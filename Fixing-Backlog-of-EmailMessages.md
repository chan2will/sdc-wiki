Occasionally our backlog of emails gets stuck, and lots of email queue up without sending.  This has typically been a result of encoding errors in email addresses we are sending to Silverpop, but recently we did load testing and queued up thousands of emails, and the backlog rose to over 13,000 messages.  

As of March 2017, we only process about 150 email messages every 10 minutes, so if the backlog becomes too large, we will never get caught up.

You can view the list of email messages at https://smiledirectclub.com/api/tools/admin/workflow/emailmessage/.  Messages that have not been sent have a Null value in the `date_processed` column.

The following query will also get you a list of queued email messages:
```
SELECT *
FROM workflow_email_message
WHERE date_processed IS NULL
AND date_removed IS NULL
AND date_created >= '2017-MM-DD'  <--Use yesterday's date
ORDER BY id
;
```

To help speed things up, it may be helpful to SSH into prod and run the following code:

To remove any duplicates:
```
from workflow.models import EmailMessage

message_qs = EmailMessage.objects.queued()
dup_messages = []

for message in message_qs:
    qs = message_qs.filter(recipient=message.recipient, 
                           template_id=message.template_id, 
                           trigger_id=message.trigger_id)
    if qs.count() > 1:
        dup_messages.append(qs.last())
        qs.last().delete()
```

Send messages.  I usually look at the lowest ID of queued/unprocessed emails, N, and begin processing at N + 250.
```
from workflow.models import EmailMessage
from common.utils import get_system_user
from django.utils import timezone
from datetime import timedelta

user = get_system_user()
yesterday = timezone.now() - timedelta(days=1)

# by supplying the range of ids you can run multiple processes without overlapping and trying to send duplicate email messages
def send_messages(low_id, high_id):
    message_qs = EmailMessage.objects.queued().filter(date_created__gte=yesterday, id__range=(low_id, high_id)
    for message in message_qs:
        message.send(user)
```
