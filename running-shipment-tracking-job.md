Sometimes, the `ShipmentBatchPLTrackingTask` fails due to a timeout. If this needs to be run later to fetch tracking numbers for kit shipments and we don't want customer emails to be sent, follow this procedure.

* Disable SendQueuedEmailMessages task in Django Admin Task control
* Shell into prod-celery
* `docker exec -it celery /bin/bash`
* `source /venv/bin/activate`
* `cd /app/`
* `python manage.py shell_plus`
* `from core.operations.tasks import ShipmentBatchPLTrackingTask`
* `ShipmentBatchPLTrackingTask.apply_async()`
* Watch emails get queued:
```
select * from workflow_email_message where template_id in ('29163335', '28417914') and date_processed is null and date_removed is null order by date_created desc;
```
* Once complete, mark emails as removed
```
begin;
update workflow_email_message set date_removed = now() where template_id in ('29163335', '28417914') and date_processed is null and date_removed is null;
commit;
```
* Reenable SendQueuedEmailMessages in Django Admin Task Control