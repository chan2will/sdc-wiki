This document will outline the current state of our Django app's interactions with the Align integration service. The Django app, as well as Align's servers, both interact with a third party integration service located [here](https://github.com/CamelotVG/align-integration). All of our interactions with Align should take place in the context of this service. To that end we have a tool in our Django app that provides a central location for all of these interactions. This tool is located in `/scc-api/smilecheck/tools/align/`. 

## Instantiation

Here is an example view.

```
from tools.align import Align

@waffle_flag('initiate-align-order')
def initiate_align_order(request, uuid):
    case = get_object_or_404(Case, uuid=uuid)

    try:
        align = Align()
        status_code, errors = align.initiate_align_order(request, case)

        if status_code == 200:
            return HttpResponse('Success', status=200)
        else:
            return HttpResponse('There was a problem sending this case to Align', status=status_code)
    except Exception as ex:
        logger.exception(ex)
        return HttpResponseNotFound(str(ex))
```

Upon instantiation of the `Align` class, a token will either be generated or retrieved from Redis. all further calls will use this same token, so no further auth should be required. For local use, it is a good idea to enable this variable so that a token can be cached: `SDC_CACHING_ENABLED = True`. Redis will need to be running for this to work. Once you have instantiated the class, you are free to call any class methods in order to initiate an actual call to the service.

## Initiate Align Order

An Align order is currently initiated from the staff portal. If a case has a prescription, and that prescription is set to Align labs, an additional panel will display in the staff portal above the prescription page. Several criteria will be displayed on the left side of this panel showing requirements for sending a case to Align labs. If all of the boxes are checked, then a button should be displayed on the right to initiate the process. This criteria is outlined in the `can_send_to_align` property on the Case model. The prescription's lab must be set to Align, the photo assessment must be approved, and the case must have at least two STL files. Once the button is pressed, the `staff-align-initiate-order` URL with call the `initiate_align_order` view. The view calls the Align tool `initiate_align_order` method. This method will make a series of calls to the Align integration service. 

### Create Patient

The first step in this process is to create a patient. This call will generate a patient record in Align's system. Most of this information can be pulled directly from the patient record in our system. The one caveat here is that we have not recorded a patient's gender until very recently. As part of the migration to add this capability, all patient records were set to unknown gender. We have a way to set the patient's gender from the staff portal, so ideally that happens before sending a case to Align. If a patient's gender is unknown, we are defaulting the gender to male before the patient is created. This is only to meet the requirements of Align's system. If the response code from our integration service is anything besides 200, the process errors out. A case not is creating regardless of the outcome, to provide some history or actions taken.

### Create Primary Order

If a patient is created successfully, then a primary order is created for that patient. This method will be called using the patient id from Align's system, as this order will be linked to their patient record. The order type is currently hardcoded to `IAS` meaning this is a primary order. We are currently only requesting primary orders. The treatment option is pulled from the photo assessment and sent. The chief patient concern is currently hardcoded. A POST is then made to our integration service that will handle creating the order in Align's system.

### Upload Assets

If a primary order is created successfully, then all the relevant assets are uploaded to a case. No actual asset information is passed to the integration service, only the relevant ids.

## Return values from Align

Once all of these steps have been taken, several actions will happen before our API initiates anything else. Align will submit a sales order record back to our system, stored in the `cases_align_orders` table. They will submit multiple records for each case, one for the primary order, and one for the secondary order. We are not currently doing anything with the secondary order record, though that will be used in the future should we want to request Align begin work on it. The most recent record should always be used for a given type, as Align may submit multiple sales orders per case. You can use the properties on the `Case` model, `align_sales_order_number` and `align_patient_id`, to determine the correct values. After the sales order records are created, Align will also submit a treatment plan back to us, which the integration service is responsible for storing. The status will also be updated, so the case will enter the provider portal programmatically.

## Provider Portal Actions

A few things happen currently based on a provider's actions.

### Case Acceptance

If a provider accepts a case, then we generate a screenshot that will be sent to Align later, once aligners are purchased. The template builds a canvas from the html in the DOM, and then converts that canvas into a base 64 string that can be submitted with the form.
```
script.
  $('.provider-case-message-form').submit(function (e) {
    e.preventDefault();
    
    if ($('input[name="action"]').val() === 'approve') {
      html2canvas($('.patient-info-comments'), {
        background: '#fff',  
        onrendered: function (canvas) {
          image_data = canvas.toDataURL('image/jpeg');
          $('input[name="acceptance_screenshot"]').val(image_data);
          $('.provider-case-message-form').unbind('submit').submit();
        }
      });
    } else {
      $('.provider-case-message-form').unbind('submit').submit();
    }
  });
```

After that, the Align tool decodes that string, and sends it with the `Asset` creation method, which stores the file-like object in Truevault. All of this happens in the `create_acceptance_screenshot` method in the Align tools class. This screenshot will be stored for use later. Once a customer actually purchases Aligners, a ClinCheck acceptance call will be sent to Align's system letting them know a case is ready to manufacture.

```            if waffle.flag_is_active(request, 'set-align-order-status'):
                case = self.case

                if case.prescription.lab == 'AL':
                    align = Align()
                    align.set_case_status(request.user, case, 'approve')
```

The `set_case_status` method, passing action approve, will grab the relevant case asset and pass it along. Once this happens, we are done with our programmatic interactions. ALign will manufacture and ship the order to us, and from there it goes through the normal fulfillment process.

### Case Revision

If a provider requests a revision, a Clincheck Revision request is immediately sent to Align. This includes the comment from the provider indicating what is wrong with the treatment plan. Align will submit a new treatment plan, which will automatically get sent back to the provider portal and the cycle will begin again.

## Adhoc Scripts

These scripts are not used much now, but if a case needs to be altered manually, you can use these queries and scripts to do so. Export a CSV from the relevant query and then feed to the adhoc script.

### ClinCheck Accept

```
--Query for CC accept
SELECT DISTINCT TO_CHAR(co.date_order, 'YYYY-MM-DD HH24:MI')   AS "Order Date"
  ,cp2.first_name || ' ' || cp2.last_name             AS "Patient Name"
--  ,co.updated_by                                      AS "Updated By"
  ,cc.case_number                                     AS "Case Number"
  ,pp.account_name                                    AS "Provider Name"
  ,cp.name                                            AS "Product Name"
  ,vws.status_name                                    AS "Order Status"
  ,TO_CHAR(co.date_status, 'YYYY-MM-DD HH24:MI')      AS "Order Status Date"
  ,cc.id      										  AS "Case ID"
  ,cp2.medical_record_number                          AS "Medical Record Number"
  ,ca.vault_id										  AS "Vault ID"
  ,ca.external_key									  AS "External Key"
FROM   commerce_order      co
  JOIN commerce_order_item coi ON coi.order_id=co.id
  JOIN commerce_product    cp  ON coi.product_id=cp.id
  JOIN cases_patient       cp2 ON co.patient_id=cp2.id
  JOIN cases_case          cc  ON cc.id=co.case_id
  JOIN cases_prescription  cp3 ON cp3.case_id=cc.id
  JOIN providers_provider  pp  ON pp.id=cp3.provider_id
  JOIN vw_workflow_status  vws ON co.current_status_id=vws.status_code
  LEFT JOIN (SELECT c.id, c.case_id, c.vault_id, c.external_key
	FROM cases_case_asset c
	INNER JOIN (select max(date_created) as date_created, case_id
					from cases_case_asset
					where date_removed is null
					and kind = 'CCACCEPTSCREEN'
					group by case_id) ca
	ON ca.case_id = c.case_id 
	where ca.date_created = c.date_created	) as ca
	ON ca.case_id = cc.id
WHERE cp.category='ALIGNER'
  AND cp3.lab='AL'
  AND co.date_order IS NOT NULL
  AND co.date_removed IS NULL
  AND coi.date_removed IS NULL
  AND cp3.date_removed IS NULL
  AND cc.current_status_id = 'WAITALGNSHIP'
  AND cp2.medical_record_number in ()
  AND cc.case_number in ()
  AND cc.id IN ()
  AND vws.status_name = 'Processing'
  AND co.date_created > ''
  ORDER BY "Order Date" desc
```

```import csv, requests
from collections import OrderedDict

from core.profiles.models import User
from tools.align import Align


class Job(object):
    '''
    This script is an extremely manual process for doing batch cc accepts for Align Cases. It is extremely brittle and
    should not be run without considerable modification to the Align tool upload_assets method. It also requires input
    from a CSV file with a very specific structure. I'm saving it here just in case we need it again in the future.
    '''
    def __init__(self, logger):
        self.logger = logger
        self.result = OrderedDict()

    def execute(self, backwards=False):
        if backwards:
            return self.backwards_func()
        else:
            return self.forwards_func()

    def forwards_func(self):
        total_count = 0
        failed_case_ids = []

        align = Align()

        with open('<path to csv exported from above query>', 'rt') as file:
            cases = list(csv.reader(file))
            cases.pop(0)
            # cases = cases[0:20]

            for row in cases:
                try:
                    case_id = row[7].strip()
                    mrn = row[8].strip()

                    vault_id = row[9]

                    if vault_id:
                        vault_id = vault_id.strip()
                    else:
                        vault_id = '00000000-0000-0000-0000-000000000000'

                    external_key = row[10]

                    if external_key:
                        external_key = external_key.strip()
                    else:
                        external_key = '00000000-0000-0000-0000-000000000000'

                    set_case_status_path = 'http://align-prd.api.smiledirectclub.com/orders/' + str(case_id) + '/accept'

                    headers = {
                        'Authorization': 'Bearer {}'.format(align.token)
                    }

                    request_data = {
                        'caseId': case_id,
                        'mrn': mrn,
                        'vault_id': vault_id,
                        'external_key': external_key
                    }

                    response = requests.put(
                        set_case_status_path,
                        data=request_data,
                        headers=headers
                    )

                    if response.status_code != 200:
                        self.logger.info('Bad Upload: {}'.format(case_id))
                        self.logger.info('Response: {}'.format(response.json()))
                        self.logger.info('')
                        failed_case_ids.append(case_id)
                    else:
                        self.logger.info('Upload: {}'.format(case_id))
                        self.logger.info('Response: {}'.format(response.json()))
                        self.logger.info('')

                    total_count += 1
                except Exception as e:
                    self.logger.info(e)


        self.logger.info('Failed Case IDs: {}'.format(failed_case_ids))

    def backwards_func(self):
        pass
```

### Clincheck Revision

```
--Query for CC Revisions
SELECT DISTINCT c.id, pa.medical_record_number, m.body, n.comment
FROM cases_case c
JOIN cases_patient pa ON pa.id = c.patient_id
JOIN cases_prescription pr ON pr.case_id = c.id
JOIN cases_case_message m ON m.case_id = c.id
JOIN cases_case_note n ON n.case_id = c.id
WHERE pr.lab = 'AL'
--AND n.comment LIKE 'Bad Align response during Set Case Status. Action: revise. Status code: 503%'
--AND pa.medical_record_number = 'M84b7ad7f6efd1'
AND c.case_number IN ('C9d90c51086f07')
LIMIT 1
```

```import csv, requests
from collections import OrderedDict

from core.profiles.models import User
from tools.align import Align


class Job(object):
    '''
    This script is an extremely manual process for doing batch cc accepts for Align Cases. It is extremely brittle and
    should not be run without considerable modification to the Align tool upload_assets method. It also requires input
    from a CSV file with a very specific structure. I'm saving it here just in case we need it again in the future.
    '''
    def __init__(self, logger):
        self.logger = logger
        self.result = OrderedDict()

    def execute(self, backwards=False):
        if backwards:
            return self.backwards_func()
        else:
            return self.forwards_func()

    def forwards_func(self):
        total_count = 0
        failed_case_ids = []

        align = Align()

        with open('<path to csv exported from above query>', 'rt') as file:
            cases = list(csv.reader(file))
            cases.pop(0)
            # cases = cases[0:1]

            for row in cases:
                try:
                    case_id = row[0].strip()
                    mrn = row[1].strip()
                    comment = row[2].strip() or 'Revision Requested.'

                    set_case_status_path = 'http://align-prd.api.smiledirectclub.com/orders/' + str(case_id) + '/modify'

                    headers = {
                        'Authorization': 'Bearer {}'.format(align.token)
                    }

                    request_data = {
                        'caseId': case_id,
                        'mrn': mrn,
                        'comments': comment
                    }

                    response = requests.put(
                        set_case_status_path,
                        data=request_data,
                        headers=headers
                    )

                    if response.status_code != 200:
                        self.logger.info('Bad Upload: {}'.format(case_id))
                        self.logger.info('Response: {}'.format(response.json()))
                        self.logger.info('')
                        failed_case_ids.append(case_id)
                    else:
                        self.logger.info('Upload: {}'.format(case_id))
                        self.logger.info('Response: {}'.format(response.json()))
                        self.logger.info('')

                    total_count += 1
                except Exception as e:
                    self.logger.info(e)


        self.logger.info('Failed Case IDs: {}'.format(failed_case_ids))

    def backwards_func(self):
        pass
```

### Upload Align Assets

```
--Query for Align Upload
SELECT c.id, pa.medical_record_number, ao.pid
FROM cases_case c
JOIN cases_patient pa ON pa.id = c.patient_id
JOIN cases_prescription pr ON pr.case_id = c.id
FULL OUTER JOIN cases_align_orders ao ON ao.case_id = c.id
WHERE pr.lab = 'AL'
AND pa.medical_record_number IN ()
AND c.id IN (97345, 98613)
```

```
import csv, requests
from collections import OrderedDict

from core.profiles.models import User
from tools.align import Align


class Job(object):
    '''
    This script is an extremely manual process for doing batch uploads of assets to Align. It is extremely brittle and
    should not be run without considerable modification to the Align tool upload_assets method. It also requires input
    from a CSV file with a very specific structure. I'm saving it here just in case we need it again in the future.
    '''
    def __init__(self, logger):
        self.logger = logger
        self.result = OrderedDict()

    def execute(self, backwards=False):
        if backwards:
            return self.backwards_func()
        else:
            return self.forwards_func()

    def forwards_func(self):
        total_count = 0
        failed_case_ids = []
        request_user = User.objects.get(pk=-1)

        align = Align()

        with open('path to csv export from above query', 'rt') as file:
            cases = list(csv.reader(file))
            cases.pop(0)
            # cases = cases[0:10]

            for row in cases:
                try:
                    pId = row[11].strip()
                    mrn = row[8].strip()
                    case_id = row[7].strip()

                    upload_path = 'http://align-prd.api.smiledirectclub.com/assets/upload'

                    headers = {
                        'Authorization': 'Bearer {}'.format(align.token)
                    }

                    request_data = {
                        'pId': pId,
                        'mrn': mrn,
                        'caseId': case_id
                    }

                    response = requests.post(
                        upload_path,
                        data=request_data,
                        headers=headers
                    )

                    if response.status_code != 200:
                        self.logger.info('Bad Upload: {}'.format(case_id))
                        self.logger.info('Response: {}'.format(response.json()))
                        self.logger.info('')
                        failed_case_ids.append(case_id)
                    else:
                        self.logger.info('Upload: {}'.format(case_id))
                        self.logger.info('Response: {}'.format(response.json()))
                        self.logger.info('')

                    total_count += 1
                except Exception as e:
                    self.logger.info(e)


        self.logger.info('Failed Case IDs: {}'.format(failed_case_ids))

    def backwards_func(self):
        pass
```