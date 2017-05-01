# When adding changes to the case journal in the staff portal

Listed below are two options and some considerations to consider before choosing the way forward.  I will use `model_field` as our example field to track.

## One option is to use the `audit_fields()` method by return a list of fields to track.  
```
audit_fields(self):
    return ['model_field_id']
```

This will generate a default message in the case journal when the field changes and is saved.  Something like, `Model Field changed from <value1> to <value2>.`

If your data can be set to null, the `audit_fields()` method will leave blank spaces in the response for the `None` value in the Case Journal.  Eg:  'Model Field changed from <value1> to'.  This is obviously not desirable.  

## An alternative with a customizable message is to use `FieldTracker` to detect changes to the field value.

Set up tracking like so:

`tracker = FieldTracker(fields=['model_field_id'])`

Once the field is currently being tracked, in the `save()` method of the model you can write to the case journal:
```
if self.tracker.has_changed('model_field_id'):
    msg = 'Model field changed to {}'.format(self.model_field)
    self.add_note(msg, request_user=kwargs.get('request_user', None))
```

## Note:
If the field is referencing a model with a PK id such as `models.ForeignKey(ModelField...`, the field name will need `_id` appended to it.  Otherwise, if the field is referencing `models.CharField(...`, `_id` does not need to be appended to the name.  Check this by tracing `tracker.changed()` and viewing the results to know for sure if `_id` needs appended to the field name.