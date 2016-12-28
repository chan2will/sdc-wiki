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

An Align order is currently initiated from the staff portal. If all of the criteria for initiating an order are met, a button will be displayed in the prescription section of a case's detail page. This criteria is outlined in the `can_send_to_align` method on the case model. The prescription's lab must be set to Align, the photo assessment must be approved, and the case must have at least two STL files. Once the button is pressed, the above view will be called. The view calls the Align tool `initiate_align_order` method. This method will make a series of calls to the Align integration service. 

### Create Patient

The first step in this process is to create a patient. This call will actually generate a patient record in Align's system. Most of this information can be pulled directly from the patient record in our system. The one caveat here is that we have not recorded a patient's gender until very recently. As part of the migration to add this capability, all patient records were set to unknown gender. We have a way to set the patient's gender from the staff portal, so ideally that happens before sending a case to Align. If a patient's gender is unknown, we are defaulting the gender to male before the patient is created. This is only to meet the requirements of Align's system. If the response code from our integration service is anything besides 200, the process errors out.

### Create Primary Order

If a patient is created successfully, then a primary order is created for that patient.
