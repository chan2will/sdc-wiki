By default, emails get sent to a directory in your OS temporary files dir. To change that, open up `/smilecheck/settings/local.py` and look for the following lines:

```
# Default is to use filebased email backend
EMAIL_BACKEND = 'django.core.mail.backends.filebased.EmailBackend'
EMAIL_FILE_PATH = '/tmp/vulcan-emails'

# To see emails as console output, use this backend
# EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

# To use an actual email server and send emails locally, uncomment the Mailgun email backend.
# EMAIL_BACKEND = 'django_mailgun.MailgunBackend'
```

Uncomment the backend you want to use.