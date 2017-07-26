The asset transfer workflow helps upload/process files as automatically and asynchronously as possible. The goal is for all assets to be processed automatically in this manner, replacing all manual uploads in the staff portal. It currently only supports OBJ treatment plan assets (kind PROVPREV in the cases_treatment_plan_asset table). This workflow contains three major components: [The Asset Watcher](https://github.com/CamelotVG/sdc-asset-watcher), The Asset Transfer Service, and the Smilecheck Staff Portal Integration.

## [The Asset Watcher](https://github.com/CamelotVG/sdc-asset-watcher)

The asset watcher service is a python process that is currently running locally on our file server at the BT lab. It is the entry point for this workflow. It monitors the filesystem events in a directory that is setup to receive file exports from the CA software. It handles uploading those files to S3 so that they can be accessed later. Events are submitted to an SNS topic (ex: arn:aws:sns:us-west-1:866893681515:qa1-asset-watcher) that the asset transfer service subscribes to in order to perform operations based on the files that are processed. Updates to the code for this service will have to be added to the file server and the service will need to be restarted for the changes to take effect.

### Debug

#### The relevant directories that are watched can be found by connecting to the network drive.
1. From OSX, connect to the directory with command-k, and use URL smb://owbtvfs01
2. Enter your ADFS credentials
3. Navigate to PrintSetup/Assets/{env}
4. Files can be dropped into that folder to begin the process.
5. Processed files will move to the Processed directory
6. PrintSetup/Assets/LogTemp will contain logs for the python process running on the server.

#### Check on the events emitted by the watcher from the Amazon SQS interface.
1. Login to AWS
2. Navigate to the Simple Queue Service from the top left Services dropdown
3. You should see a list of queues, search for the asset transfer queue by entering '{env}-vulcan-asset-transfer-inbox'
4. Right click the record in the table to see a list of operations. From here you can send messages, purge the queue to remove any testing messages that you no longer want, or view the current contents of the queue. Here is a set of messages to show the typical structure for each event.
```
{
"type": "sdc_asset_transfer_file_created",
"file_name": "<case_number>_Step1.obj",
"uncompressed_sha1_hash": "abcdefg"
}

{
"type": "sdc_asset_transfer_file_uploaded",
"file_name": "<case_number>_Step1.obj",
"uncompressed_sha1_hash": "abcdefg"
}

{
"type": "sdc_asset_transfer_uploads_complete",
"treatment_plan_uuid": "<treatment_plan_uuid>"
}
```

#### Check on the files uploaded to S3
1. Login to AWS
2. Navigate to S3 from the top left Services dropdown
3. Navigate to sdc-asset-transfer -> {env} -> staged_assets
4. Here you can see uploaded assets.
5. The client behaves strangely when you download the assets. After a file is downloaded, the client decompresses the file but leaves the gz extension. To open the OBJ, remove the gz extension but not not attempt to decompress. The file should open normally.

## The Asset Transfer Service

The asset transfer service uses [reactive core](https://github.com/CamelotVG/sdc-reactive-core) to process events emitted from the asset watcher service. It currently exists as part of the scc-api codebase (scc-api/asset_transfer_service) to facilitate easy access to scc-api's models. It is deployed separately to an ECS cluster that will start django up in a minimal state and then initiate an event Processor. The Processor will continually look for events emitted from the watcher, and call a relevant Handler if the criteria is met in the can_handle method of the Handler. There are three handlers for the asset transfer service that react to specific events.

### Asset Created Event

This event is emitted as soon as the file is finished being copied to the BT local network storage. The event contains a type (sdc_asset_transfer_file_created), a filename, and a hash of the file. This hash will be used a unique identifier for the file as it will change when the actual contents of the file change. A file with the same name may be uploaded multiple times, so we only want to create a new record for it if the contents of the file are different. The filename string is currently parsed in order to extract the case number and step. Any errors in parsing this string will result in the event not being processed properly, and thus not being available from the staff portal interface. If the filename is parsed and the case number matches an existing case, a record will be created in the cases_asset_transfer table. This record will contain case id, case number, type (based on the file extension), file hash, filename, and the step. These records will be made available to the staff portal interface in order to be associated with a treatment plan and user later.

### Asset Uploaded Event

This event is emitted when a file has been uploaded from the BT fileserver to S3. It signifies that the file is ready to be bundled together into the treatment plan asset used by the 3d viewer downstream. It has a type (sdc_asset_transfer_file_uploaded), filename, and hash much like the creation event. This handler will set the is_uploaded column on the relevant asset transfer record to True. It will then make a check to see if all asset transfer records meet the criteria for being bundled into the treatment plan asset. The criteria for this is that all asset transfer records are is_uploaded == True and that they have been associated with a treatment plan (which happens as part of the staff portal portion). If all this criteria is met, an event wil be emitted for our third handler: Asset Uploads Complete. If this event is emitted before the creation event for a given file, a record will be created containing only the hash and the is_uploaded value. When the subsequent creation event is emitted, the existing record will be updated instead of a new record being generated.

### Asset Uploads Complete

This event is emitted when all transfer files have been processed and are ready to be bundled into a file that can be used downstream by the 3d treatment plan viewer. For this to happen, all files must have asset transfer records  that are is_uploaded == True and that they have been associated with a treatment plan (which happens as part of the staff portal portion). The handler for this event will create a NamedTemporaryFile as a zip archive, and then grab every asset transfer file from S3 and add it to the archive. It will then create a treatment plan asset from this zip file. Once this happens, everything downstream should be able to interact with the assets in the same manor that they always have.

### Debug

The asset transfer service runs on Amazon's EC2 Container Service. You can view logs for the relevant environment by following these steps.
1. Login to AWS
2. Navigate to the EC2 Container Service from the top left services menu
3. Click on the cluster for the environment that you are interested (ex: sdc-qa)
4. Find the relevant service following the naming pattern '{env}-asset-transfer'
5. Click the service name to see details for that service
6. You should see a list of running tasks, click the uuid of the relevant task
7. From the table of containers, click the arrow on the left to see a dropdown of information related to the container
8. Click 'View logs in CloudWatch' to see logging output from that instance.


## The Smilecheck Staff Portal Integration

Before the asset transfer files can be processed into a bundled treatment plan asset, they must be associated with a treatment plan. The user making that association must also be recorded. To this end, the interface on the UPLOAD TREATMENT PLAN ASSET modal has been updated to allow association of asset transfers. This is currently only available if a user selects 3D Viewer OBJ Preview from the kind dropdown. A user will then be presented with a list of available asset transfers as soon as the asset created events have been emitted from the watcher and process by the transfer service. This will allow for a user to associate files before the S3 uploads have been completed. A user can select from the list of transfers and then submit that selection. This will update the relevant records with the current treatment plan uuid as well as the current staff portal user. A check will then be made to see if all transfer operations are complete. If this is the case, an asset uploads complete event will be emitted to initiate the bundling process. If the assets are not uploaded yet, the bundling will be initiated by the uploaded handler. The upload manually button should allow for the old style of synchronous uploading.