When a new workflow is created that will involve staff members, we typically take the following approach:

1. Create a "panel" that will go into the Order/Case/Patient/Account etc. details page that can handle the new workflow. Generally this is some combination of fields, forms, lists, etc.
2. Ensure that any new statuses exist (so that these objects can be accessed from their master status list)
3. If it is not a status-based workflow, then make sure that the objects can be accessed as needed (this might involve creating a Workqueue, or relying on search)

When a new workflow has been tested and we are confident that it will continue to exist for some time, we can create some enhancements:

1. We can create a workflow-specific details page, much like the Aligner Order Wizard, to handle this workflow. This is best utilized by combining it with a custom Workqueue that will, upon click, direct the user directly to the appropriate workflow-specific details page.
2. We can create a button in search results for certain types of objects.  For instance, if we created a custom workflow details page for "Changing Patient Name", then a search for "John Doe" that resulted in a list of one patient would have a single button on the row titled "Change Patient Name".  If there are a lot of such buttons, we may want hierarchical options.