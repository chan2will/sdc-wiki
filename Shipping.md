https://smiledirectclub.com/api/operations/shipping/
To view current building batches and batches that had issues.

Shipments (evaluation kits and retake kits) will get gathered into batches based on their statuses. This task  is where this task is housed in core.commerce.operations

After file gets sent to Phoenix, the batch status should be 'Processing'. If you see a batch in 'Ready' status then that usually means that the file wasn't sent to Phoenix and needs to be sent (Usually caused by FTP connection errors). We have a nightly task that runs to grab tracking information from Phoenix. If every item in the batch has tracking information, then the batch gets marked complete and it doesn't show up on the above webpage anymore. We have had some issues grabbing this data (especially from people in Hawaii and Alaska) and haven't spent much time looking into it. 

If a Shipment file doesn't transmit to Phoenix (our shipping partner), the batch status will be in 'READY'. In order to transmit, ssh into prod and open shell_plus. 

* from celery.utils.log import get_task_logger
* from core.operations.data_exchange import get_vendor_file_maker

task_logger = get_task_logger('tasks.operations.ShipmentBatchPLTask')

batch = ShipmentBatch.objects.active().select_related('fulfiller').get(id=1739)
* Fill in with whatever the ID is of the batch that you want to send. 

file_maker = get_vendor_file_maker(batch=batch)
success = file_maker.transmit(logger=task_logger)

You should see a message saying that the file was transmitted and the batch status should be updated. 




