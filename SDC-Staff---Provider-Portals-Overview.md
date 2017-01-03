###Common Templates and Controls
Common Jade Templates are used throughout the Provider and Staff Portals.  These templates are a composite group of controls which enable a standardized view into our data.  Most of these templates have a JavaScript "Controller" which facilitates inter-control behaviors based on business rules as well as multiple configuration options.  

Common Control Wrappers are single control Jade Templates which provide a common look and feel supporting standardized validation indicators for required fields, max and min values, invalid dates, etc.
Although many of the templates are simple types (textbox, numeric text box, date, email) several templates are complex supporting data types such as Credit Cards (Number, Expiration, CVC) and Patient Identity (patient type, birthdate and first/last name). 


###Staff Portal: A Few Details

####Layout and Layout Manipulation
The Staff Portal layout is driven by a master page called `base.jade`.  This template combines the top navigation template with the side navigation template and two content regions which are iFrames.  The related JavaScript controller, `StaffPortal.Controller.StaffPortalMasterPage` coordinates requested layout changes and communicates with the iFrame content via `onmessage` event listeners which have the ability to enable communications between iFrames.


###Order Details
The following is a full review of the Staff Portal Order Details page and its associated Jade Templates.  This page is being detailed as Order Details uses shared Jade Templates for summary views and Smart Controls which add a uniform look and feel to editable fields.

The Order Details Page is accessible from the Fulfillment Queues (when selecting a line item) and from the Case Details Page (by clicking the Order link in the gird of orders associated with the case).  Additionally, the Order Details page is the final confirmation which a user is brought to after successfully completing non-appointment orders via the Staff Portal Order Entry Wizards.

The Order Details Page facilitates editing based on user permissions.  Each editable field is a separate permission associated either directly with the user or indirectly through a user’s group membership.  An editable field is identified by a light blue underline of the field with an associated tooltip indicating the field can be edited.  Editing begins when the user double clicks the underlined editable field.

Order Details consists of the following reusable Jade Templates:

* `order_summary`
* `followup`
* `smart_edit`
* `sdc_edit`
* `case_summary`
* `payment_summary`
* `customer_summary`
* `product_summary`
* `fulfillment_shipments_summary`
* `fulfillment_scan_appointments_summary`
* `fulfillment_store_appointments_summary`
* `smart_frame`


######`order_summary`
The `order_summary` template is the primary template which collates all the others to form the Staff Portal Order Details page.  The built-in JavaScript Controller for this template is in the namespace `StaffPortal.Controller` and is called `OrderSummaryForm`.  This controller is responsible for managing the display (hiding and showing regions) and integrating the user change request with the paired API.  Via Amplify Event Messaging this controller automatically reacts to changes made by other templates updating the displayed fields accordingly.

With the proper user permissions, the `order_summary` template controls the editing of:

* Order Number
* Order Status
* Customer Full Name
* Customer Email
* Patient Identity
* Patient MRN
* Case Number
* Lab Case ID
* Order Discount Code
* SmilePay Next Charge Date
* SmilePay Credit Card

######`case_summary`

The `case_summary` template provides a simple view into case details.  The build in controller for this page is also in the namespace `StaffPortal.Controller` and it is called `CaseSummaryForm`.  Given proper user permissions, this template enables the user to edit the following case fields:

* Case Number
* Patient Tray Size
* Lab Case Id

Note that although the `case_summary` template allows the editing of fields, the associated controller, `CaseSummaryForm`, does not actually support saving the fields.  This is done by the main page controller, the `OrderSummaryForm`. This template only provides the view.

######`followup`

The `followup` template is used in several locations within the Staff Portal including on the Order Details page.  This template providers the functionality for viewing and (with permission) editing the Case Communication Status which identifies cases as Follow-up Needed, Do Not Call and Call As Needed. 

This template is a self-contained template.  This template is directly paired with a supporting API hosted on the SDC site at `/api/case_followups`.  For full functionality, this template must be provided with the case UUID and the User in the markup used for its wire-up.

######`payment_summary`

The `payment_summary` template provides a view into the payment and refund history of an order.  From here, the user (given the proper permissions) can refund payments and take payments on the order.  

This template is a stand-alone template as it is directly paired with supporting APIs for refunding payments (it’s only functionality other than viewing data and supporting the make_payment template). This API is hosted on the StaffPortal site at `/api/refund_payment`.

For taking payments on an order, the `payment_summary` template will utilize the reusable `make_payment` template.

Via Amplify Event Messaging, this template alerts the `order_summary` page that payment changes have occurred so the view can make the changes needed without post-back.


######`make_payment`

The `make_payment` template is used in the payment_summary template and provides the ability for permissioned users to take payments on an order.  

This template is also self-contained and lined to a supporting API hosted by the SDC Site at `/api/make_payment/`.  This API uses the same underpinnings which is used by the Order Entry Wizards and making an initial payment.  This is good code re-use and makes for a consistent and reliable experience. 

Via Amplify Event Messaging, this template alerts the payment summary as well as the `order_summary` page that payment changes have occurred, so the associated views can make the changes needed without post-back.

######`customer_summary`

The `customer_summary` template providers a view into customer and patient specific data.  From here, a permissioned user can edit a customer name, customer email, customer shipping address, customer credit cards on file, patient identity and MRN.  This template also exposes a view of the patient’s Smile Assessment via the assessment template.

Like the `case_summary` template, the `customer_summary` controller does not support the actual saving of data.  This is done by the controller for the primary template, `order_summary`.


######`assessment`

The `assessment` template is used in several places throughout the Staff Portal including in the above-mentioned `customer_summary`.  This template provides a permissioned view into the patients Smile Assessment data.

The `assessment` template is a self-contained template paired with a supporting API hosted by the SDC site at `/api/get_assessment_data/`.

######`product_summary`

The `product_summary` template is one of the simplest templates used by `order_summary`.  This is a View Only template which provides a grid view of the products which have been ordered.  The simple controller, `ProductsSummaryForm` is only used for view configuration.


######`fulfillment_shipments_summary`

The `fulfillment_shipments_summary` is only visible with orders which have shipments associated to them.  Appointment orders will not display this section.

This template facilitates the viewing of all shipments associated with an order.  From this template, a permissioned user can also add or edit the shipment tracking number OR the return shipment tracking number (for impression kit orders).

This template is NOT self-contained, meaning that this templates controller, the `ShipmentSummaryForm`, does not support saving values it allows to be edited.  This form relies on the `order_summary` template controller for persistence of edited values.


######`fulfillment_scan_appointments_summary`

The `fulfillments_scan_appointments_summary` templates are another simple view only Jade Template.  This template will only be seen in older orders where appointments were made at a patient’s home address.  This template indicates the status, time and date of the appointment as well as the address the appointment was scheduled at.


######`fulfillment_store_appointments_summary`

The `fulfillment_store_appointments_summary` is the template which display specifics regarding a store appointment order.  Specifically, this template displays the appointment time, date and status as well as the store location.  From here, the use can also directly click into the Store Appointment Details related to this order.


######`sdc_edit`, `smart_edit` and `smart_frame`
These templates are simple and complex control wrappers providing a common look and feel for editing within the SDC Staff Portal.  We will discuss these templates in a later section.  


#####A Little About Case Status Transitions
Until recently, most case status transitions have been manual.  Recent PR’s have automated some case status transitions.

* When a treatment plan is created for a case, but no assets or links have been associated, the case status is transitioned to `SETUPWIP`.
* When an asset or link is added to a treatment plan, the case status is transitioned to `SETUPREV`.
* From the provider portal, when a treatment plan is approved, the case status transitions to `SETUPAPP`.
* From the provider portal, when a treatment plan revision is requested, the case status transitions to `SETUPWIP`.
* From the provider portal, when a treatment plan is rejected, the case status transitions to `2NDOPINION`.

Future story releases around 2nd Opinion Logic will introduce more case status transitions.


####Sidebar Navigation
Although the sidebar navigation was recently reworked for performance and scalability issues, it is evident in the code that many developers had their hands in this and everyone had a different concept in how things should work.

Sidebar sections and open section items are cached (for about 5 minutes).  Clearing this cache is made possible via the Cache Admin page within the Staff Portal.

#####Sections
Sidebar sections are the expandable blocks in the sidebar that when clicked will either open or close.  When open, the section items will be displayed.  Clicking a section item will either bring the user to a queue, a tool, or a details page.  When a section is first clicked and API call will be made to fetch the section items and the JavaScript Controller of the sidebar will populate the section items.  Consecutive requests to open already opened sections will use JavaScript cached results from the prior request.

Sidebar sections are cached by user, server-side.  This cache can be manually cleared via the Cache Admin Page in the staff portal. 

#####User Queue
User Queues include Photo Assessments, Fulfillments, Cases, Providers, and SmileShop Sections.  User Queue Items which appear when a section is opened always include a Counter representing how many items are in a specific queue.

Opening a User Queue section is expensive as calculating the queue counts can be as resource intensive as returning the actual rows of the queue. User Queue Section Items are query based but coded directly in Django. 

#####Work Queue
Work Queues are strictly database driven, meaning the section item and the queue SQL exist in the StaffPortal database in the workqueue tables.  Work Queue section items do not support a Count display like user queues.
Current Work Queue sections include the sections RevisionRequested, SmileLab and Scheduling.

These queues were designed to enable users to develop a reporting query, insert it into the database, and automagically have it appear in the staff portal.

There have been requests to add counts to these Work Queues.  This is a major undertaking as there is no concept in this type of queue for SQL to be used to get section item names.

#####User Links
User Links are represented by New Evaluation Order and New Aligner Order.  Although these appear similar to User Queue and Work Queue Sections, these sidebar items do not "open" to show section items, instead the user is redirected to specific URL, specifically the Order Entry Wizards.

#####User Tools
User Tools represent the sidebar single section called Quick Tools.  These are built directly into the products Jade Templates and when accessed a built-in modal popup is displayed.  Current Tools include "Apply Discount" and "Zip Code Check"

#####Admin Pages
Admin Pages are accessed by the sidebar section "Admin".  These are permissioned pages.  If a user does not have any admin page access, this section will not appear.  This section currently contains admin pages for User Administration and Cache Administration.  A placeholder exists for Provider Administration which will be released with Jira story `ENG-305`.

####Search
Staff Portal Search currently spans Patient Name, Customer Name, Customer Email, Customer Phone, Patient Phone, Lab Case ID, Shipment Tracking Number, Case Number, Order Number and Patient MRN.  

Search is made possible by a Materialized View called `cases_search_mv` which is refreshed regularly via a celery task OR manually when an order is placed via the Staff Portal.  This means that orders placed via the Commerce Website may not be immediately available when searching the Staff Portal, the celery refresh of the materialized view will need to run first.  


###Jade Template Control Wrappers
Jade template control wrappers encapsulate simple and complex data entry forms which are type specific.  This enables various simple, or complex, form reuse throughout the SDC Portals making viewing, editing and validating, on the client side, simple and synergistic throughout the site.  These the JavaScript code supporting these common typed controls have been designed in a way to greatly reduce regression issues when adding new form types or altering existing ones.

####`instance_dto` – The Data Transfer Object for control instances
Throughout this document `instance_dto` will be referred to.  The `instance_dto` (DTO meaning Data Transfer Object) contains all information relevant to the specific instance of a control.  This includes type, initial value, read only, include clipboard, etc.  Each instance of a smart control will have its own unique `instance_dto`.

####`smart_frame` – Jade Template and JavaScript Controller
The `smart_frame` template and controller provides a uniform look and feel throughout various sections in our Portals.  This look and feel includes a configurable title bar including icon, the frame border, and configurable buttons for form cancel and submit. 

A template which is encapsulated within the frames is wired up through the templating "extends" directive.  
`| {% extends "sdc/base/smart_frame.jade" %}`

Via an implied contract (standardized type handle methods and standardized `instance_dto` frame delegates), the `smart_frame` template identifies and communicates with the encapsulated smart controls (`sdc_edit` and `smart_edit` templates).  

When the smart frame wraps a collection of smart controls (`sdc_edit` and smart_edit) the following features are automatically enabled:

######Cancel Edit
A cancel button (`CANCEL`) will appear on the lower right portion of the frame.  Clicking will reset all encapsulated `sdc_edit` and `smart_edit` controls, closing the edit form and setting the value back to the initial value.  This then will cause the submit button to become disabled and the change indicator to disappear.

######Submit Edit
A submit button (`OK`) will appear on the lower right portion of the frame, initially disabled.  When an encapsulated `sdc_edit` or `smart_edit` control reports a change in value, the submit button automatically becomes enabled.
NOTE:  The smart button does not automatically have and associated click handler.  The associated `smart_frame` controller object exposes a method to get the submit button selector (`getOKSelector`) which can then be used by the consuming template to associate the button with various events.

######Change Indicator
When an encapsulated `sdc_edit` or `smart_edit` control becomes dirty (a change in value occurs) the smart frame will display "You Have Pending Changes" next to the enabled OK button will be displayed.  The tooltip of this label will display all changes found in the encapsulated controls.  This display of changes is defined in the `smart_frame` controller in the method _addPendingChangesTooltip.

> NOTE:  The method addPendingChangesTooltip needs refactoring.  This method currently has control type specific code which it should not have.  Example:  When detecting a patient identity value change, the JSON object value is parsed here for display.  This must be converted to a Type Handler Method which the `smart_frame` calls into.  Doing this will fully remove the type consciousness in the `smart_frame` and if the type handler classes conform to the same method/property contract communication between frame and control is quite simple. 

The following code snippet is an example of what is required for setting up a `smart_frame`:

```
instance_dto.smart_frame = SDC.Controller.SmartFrameControl.InstanceManager.Create(instance_id);
$(instance_dto.smart_frame.getOkSelector()).on("click", _saveOrderSummaryChangesTrigger);
instance_dto.smart_frame.initialize({title: 'Order Details', title_icon_class: 'fa fa-money', debug_layout: false });
```

####`sdc_edit` and `smart_edit`
`sdc_edit` and `smart_edit` are our Jade templates; with their associated JavaScript controllers we refer to these as Smart Controls.  These templates/controls add a uniform level of consistency in both viewing and behavior throughout SDC Portals.   

The `sdc_edit` controls/templates are the latest version of smart controls.  These were originally derived from the older implementations of the `smart_edit` templates and controllers, but have been greatly refined reducing base class functionality and adding it to the type hander classes with the use of delegates in an observer style pattern. Legacy `smart_edit` templates and controllers are being phased out but are still in use today in the SDC Portals.  

The legacy Smart Controls (`smart_edit`) rely on a base controller which provides default actions for most event handling, but in turn calls into the type handlers for the override event handler implementation.  The legacy base controller is also the primary access point to both the read-only markup and the edit form markup, which is configured via direct selector access in the type handler classes.

The newer `sdc_edit` Smart Controls still use one base controller, which is a JavaScript Event Broker event broker, that communicates with type specific handlers via a defined (implied) contract.  This base controller is the primary access point for the type handlers to access the read-only markup via delegate methods placed inside the `instance_dto`.  Additionally, with `sdc_edit` controls, the type handle class defines the input form markup which adds a great deal of diversity for supporting many complex data input forms.  In general, code supporting `sdc_edit` controls (specifically type handlers) is much less complex, much less wordy and much more robust than the legacy `smart_edit` type handlers.


####`sdc_edit` - Jade Template and JavaScript Controller
`sdc_edit` is comprised of a simple Jade template (`sdc_edit.jade`) and two supporting JavaScript files (`sdc_edit.js` and `sdc_edit_types.js`)

The Jade template (`sdc_edit.jade`) provides the READ-ONLY view of the control (including the value label, value postfix label, clipboard icon and validation error messages) with a specific content region (accessible via `instance_dto` delegates) for the type handler to insert the edit form.  This template also includes the Jade Template Instance Initializer which packages the provided template parameters into the `instance_dto` for consumption by the base controller as well as the type handlers.  This initialization routine executes during the document ready event.

The controller class located in `sdc_edit.js` provides its type handler class delegate level access (by inserting delegate methods into `instance_dto`) for accessing the read-only form and validation messages. This base controller also enables the uniform functionality for Unique Check Validation as well as Required Field Validation, thus simplifying the individual type handlers.  JavaScript event handling (such as on change or on blur) is wired into this base controller, but unlike `smart_edit` controls, there are few event default actions.  Instead, event captured by the base controller are passed through to the type handlers IF they have implemented the appropriate contracted method.

The type handers (`sdc_edit_types.js`) provide the UI for editing the specific data type AND they must conform to a specific contract enabling interrogation by the `smart_frame`. The type handler is inserted into the `sdc_edit` controller during initialization via a factory method which bases its decision on the type attribute within the jade templates inclusion.

The `sdc_edit` Jade Template and JavaScript Controller supports the following data types:

#####Text
The text type handler supports a simple text entry field.  Supporting attributes include Required and Max Length.  Via the base controller class, Unique Checks via API are available and used for items such as Lab Case Id, Order Number and Case Number.

```
| {% include "sdc/base/sdc_edit.jade" with template_id="order_number" initial_value=order.number clipboard=true tooltip="Order Number" type="text" readonly=readonly max_length=20 required=true unique=true only %}
```

#####Full Name
The full name type handler display the non-editable full name as "<first name> <last name>" where the editing controls are configured as separate entry fields for first name and last name.  Both data entry fields will auto capitalize both first and last names.  Both entry fields are required.  Multi-part names are not directly supported, although both first and last name do allow for spaces.  The value when getting or setting in this control is a JSON object representing both first and last name.

```
| {% include "sdc/base/sdc_edit.jade" with template_id="customer_full_name" initial_value=object.user.json clipboard=true tooltip="Customer Full Name" type="fullname" readonly=readonly required=true control_group="customer_full_name" only %}
```


#####Patient Identity
Patient Identity type handler data includes Type (Self, Legal or Other), Patient Full Name, and Patient Birthdate.  This is the most complex of the `sdc_edit` data type handlers.  The value when getting or setting in this control is a JSON object representing patient type, both patient first and last name and birthdate.

```
| {% include "sdc/base/sdc_edit.jade" with template_id="patient_identity" initial_value=order.patient.json clipboard=true type="patient_identity" required=true readonly=readonly control_group="patient_identity" only %}
```


#####Date
The date type type handler uses `moment.js` for many date functions in JavaScript.  Supporting attributes include Required and Earliest Date.  

```
| {% include "sdc/base/sdc_edit.jade" with template_id="date_next_charge" initial_value=subscription.date_next_charge tooltip="Date of Next Recurring Payment" earliest_date="today" type="date" required=true readonly=readonly control_group="date_next_charge" only %}
```


#####Numeric
The numeric type handler has been developed but the PR has not yet been merged.  This prevents input of anything except numbers and control keys (for moving the cursor around). Supporting attributes include Required and Max Length.


####`smart_edit` - Jade Template and JavaScript Controller
`smart_edit` is comprised of a simple Jade template (`smart_edit.jade`) and two supporting JavaScript files (`smart_edit.js` and `smart_edit_types.js`)

The Jade template (`smart_edit.jade`) provides the entire view of the control including the edit form, value label, value postfix label, clipboard icon and validation error messages.  Via direct selector access, type handlers must choose the variations of the input form they which to display.  This template will change as new complex type handlers are created, thus adding a level of instability to this code with each release.  **Future development should use the `sdc_edit` pattern for all future work and an effort should be made to convert the remaining `smart_edit` types to the `sdc_edit` pattern.**

The Jade template (`smart_edit.jade`) also includes the Jade Template Instance Initializer which packages the provided template parameters into the `instance_dto` for consumption by the base controller as well as the type handlers.  This initialization routine executes during the document ready event.

The controller class located in `smart_edit.js` provides many default actions based on simple text input, thus there is not type handler for "text" as this is the default action.  Like the more modern `sdc_edit` controller, this base controller also enables the uniform functionality for Unique Check Validation as well as Required Field Validation, thus simplifying the individual type handlers.  As a JavaScript Event Broker, this base controller will also call into type handler methods, but will still surround these with default actions.


The `smart_edit` Jade Template and JavaScript Controller supports the following data types:

#####Status
The status type handler supports a drop-down list for status code and a drop-down list for reasons.  Earlier versions supported status change notes, but this support is comments out as the models did not properly support it yet. Business logic such as "this status code requires a reason to be selected" is supported.  Currently this type is used in the Order Details to change Order Statuses.

```
| {% include "sdc/base/smart_edit.jade" with template_id="order_status" initial_value=order_status tooltip="Order Status" type="status" readonly=readonly type_specific_data=order_status_change|safe only %}
```


#####Tracking Number
The tracking number type handler accepts as input tracking numbers in various formats supporting several carriers (as identified by regex).  The read-only version of this control displays its data with an iconic link (a truck) which, when clicked, will redirect the user to the tracking page of the appropriate carrier.  This template is used in the fulfillment_shipments_summary template for shipment and return tracking.

```
| {% include "sdc/base/smart_edit.jade" with template_id=id initial_value=ff.shipment.tracking_code tooltip="Tracking Number" type="trackingnumber" type_specific_data=carrier_options|safe required=true readonly=readonly_tracking only %}
```


#####Credit Card 
The credit card type handler supports data entry for card number and expiration.  Collection of CVC number has been remarked out as per the powers that be.  Validation of card number occurs via the jquery.payment script provided by stripe.  This type handler will also display the card logo, when a valid card number is input.  This Smart Control is used in several places within the SDC Portals, however it is NOT used in the Staff Portal Order Entry Wizards or the Commerce site.

```
| {% include "sdc/base/smart_edit.jade" with template_id=id initial_value=None tooltip="Payment Card" type="creditcard" required=true editonly=true smartframe_hide=True only %}
```


#####Email
The email type handler is a very simple smart control which provides input validation by email regex.  

```
| {% include "sdc/base/smart_edit.jade" with template_id="customer_email" initial_value=object.user.get_primary_email clipboard=true tooltip="Customer Email" type="email" required=true readonly=readonly max_length=254  unique=true control_group="customer_email" only %}
```


#####Options
The options type handler provides support for a single drop-down list.   Examples of its use in the Staff Portal can be seen in the Patient Tray Size selector and the Refund Reason Selector.

```
| {% include "sdc/base/smart_edit.jade" with template_id=id tooltip="Patient Tray Size" type="options" readonly=readonly only %}
```


#####Currency
The currency type handler supports easy input of decimal numbers with two decimal placeholders.  It supports the concept of a maximum amount.  

```
| {% include "sdc/base/smart_edit.jade" with template_id=id initial_value=default_payment_amount maximum_amount=order.balance_due tooltip="Payment Amount" type="currency" editonly=true required=true smartframe_hide=True only %}
```


#####Discount Code
The discount code handler is directly linked to a code validation API which is triggered during the on blur event.  Input discount codes are validated by both product and period.  This template is used in Order Details allowing for the adding, removing, or editing of a single order discount code.

```
| {% include "sdc/base/smart_edit.jade" with template_id="discount_code" initial_value=order.discount.coupon_code tooltip="OrderDiscount Code" type="discount_code" type_specific_data=order.uuid readonly=readonly max_length=36 only %}
```


####Smart Control Summary
In general, `smart_edit` Smart Controls are simply more complex than they need to be and their markup is always much larger.  These have begun to be, and need to continue being, phased out with the replacements using the `sdc_edit` pattern and implied contracts.

The more modern pattern for `sdc_edit` controls still needs to expand.  The implied contract needs to be expanded so that the `smart_frame` integration allows the type handler to define the appearance of the varying changed data which is used by the smart frame as a part of the Change Indicator Tooltip.

The concept of the Smart Controls is for them to provide the SDC Portals with a common look and feel while maintaining a good level of code reuse, regarding JavaScript and Jade Templates.

Smart Controls are all built with simple JavaScript, JQuery, Ajax and Jade Templating.  Due to this intended simplicity, cross-browser support has yet to be an issue.


###Second Opinions – Logic and Implementation
Because the stories around Second Opinion Logic have been delayed after development, I think a summarization of how these all work if of value.

Second Opinion Logic depends on the population of Regional Groups with Providers.  Currently every US State is represented by a like named regional group.  A provider should be a member of every group representing the states which the provider is licensed in.  

Second Opinion stories have associated adhoc scripts to place existing providers in their representative groups.  Additionally, the staff portal can be triggered to do this via the Provider Details Page (this occurs when enabling portal access) and via the Provider Admin Page (which, if groups are out of sync with licensed states, will show a button to specifically associate the provider with the groups representing their licensed states).  At this time, there is no UI to allow a portal user to Add A Licensed State to a provider. This must be done directly in the database or via scripting.

When a provider is added to a group, the position within the group is recorded as an integer value.  This position can be manipulated via the Provider Admin Page.  The position in a group indicates when a provider will be assigned a case.  Providers with a HIGHER position number will be assigned cases first.  Second Opinion Logic also uses this position, as we will discuss shortly.

The second opinion support code which automatically assigns a provider to a case resides in the case model save routine.  At the time of persistence, the code determines if this is a "new" record (meaning a new case).  If it is, we will get the provider with the greatest position value in the group representative of the patients’ home address. And create a new prescription for that provider.  At that time, we also create the initial treatment plan, reducing the need of a manual user interaction.

If no providers are found in the regional group representing the patients home address, then a prescription is NOT automatically created.

Creating a new prescription in the Staff Portal remains the same even if a prescription is auto created.  When creating a new prescription, however, the list of providers will represent all the members of the regional group associated to the patients’ home address.  If no providers exist in this group, all active providers will be listed and selectable (which is the legacy functionality).

A boolean flag has been added to the cases_prescription table indicating second_opinion.  The default value is false.

When a provider rejects a case via the SDC Provider Portal the current prescription status is set to `DEN` with a reason of `TP_REJECTED`, then a check is made on the prescription to see if the second_opinion flag is true.  If the current prescription second_opinion flag is true, the case status is set to `UNQUAL` with the reason `NOPRODUCT`.  If the current prescription second_opinion flag is false, the case set_status routine is called to set the case status to `2NDOPINION`.

In the case model, the set_status method has been updated to support second opinion logic.  When a case status is set to `2NDOPINION` via the set_status method, Second Opinion Business Logic begins when we call the prescription models method process_2nd_opinion_request() which does the following:

* Determine if a provider exists with a lower position indicator within the appropriate regional group
  * If a successor (the next provider) is not found the case status is set to `UNQUAL`.
  * If a successor is found
     * From a copy of the original, a new treatment plan is created
     * From a copy of the original, a new prescription is created (with second_opinion set to true)
     * The new treatment plan is associated to the new prescription
     * From copies of the originals, new Treatment Plan Assets and Links are created and associated with the new treatment plan
     * The original prescriptions date_removed is set to a current timestamp


###Portal User Permissions
Portal User Permissions piggy back upon the Django permissions tables.  The django_content_type record with the app_label of `staff_portal` and model `permissions`, is used to group the individual permission items located in the `auth_permission` table.  Permissions are then associated to a user either thru the users group membership or directly via the `profile_user_user_permissions` table.

The current staff portal uses field level permissions.  Meaning every editable field (in the new technology pages) as an associated permission.

Permissions are cached on the server for 1 hour.  This cache can be physically refreshed via the User Administration Page within the Staff Portal.

The user model has one property per permission making interrogation of the permission from python code or Jade templates easy.  A common example of this is:

```
- var readonly = user.StaffPermissions.can_edit_customer_email == False
```

###Caching
Caching in the Staff Portal is accomplished via the `CacheManager` python class which wraps the caching engine (currently redis) and provides standard Get/Set caching functionality with configurable expiration.  The `CacheManager` was written in such a way that changing the distributed caching engine would be easy with minimal code changes.

Caching is currently configured via the `settings.py` file for the given environment.  `local` and `test` environments do not enable caching, where `qa1`, `qa2`, `staging`, and `prod` environments all have caching enabled.

By default, when setting a cache value without an expiration explicitly set, a default of 20 minutes will be used for cache expiration.

The Get method of the Cache Manager also supports rolling expirations, although this is not used anywhere in our code today.
