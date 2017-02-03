**New Method**

There is now a Django management command for populating a User, Case, etc. ready to order aligners.  In the console, use the following command:
* `$ ./manage.py create_case <new_user_email_address@mail.com> <password>`

Example: `$ ./manage.py create_case sdc_test_user_123@mail.com examplepassword`

The code for this management command is in `cases.management.commands.create_case`.


**Old Method**

1. Make an account on the commerce website.
2. Complete photo assessment
3. Logout, Login to `/staff`
4. Look for the case corresponding to the user you just created in `Waiting on Eval Kit Fullfillment`
5. Click the case to reveal a sidebar
6. Scroll down and add a prescription. This will require having access to a provider account. This can be done by creating a separate user in the django admin, and then linking it to a provider (also in the admin). Look at the staging db, or mirror one of the other providers for relevant user/provider information. `Aaron Addams` is a good provider on staging to emulate as he was created for testing.
7. Photo Assessment Approval, click Start Review, approve all images and overall status, select both, submit
8. View Case, set to waiting on materials, submit
9. Status materials received, submit
10. Status materials accepted, submit
11. Status setup in progress, submit
12. Status setup ready for review, submit
13. Status setup approved, submit
14. At this point the patient should be status `Waiting on Aligner Purchase` (`current_status_id == WAITALGNPURCH` from `cases_case` table)
15. Login to the commerce website, click purchase your aligners CTA
16. At this point the case should be `current_status_id == WAITALGNSHIP`. You can reset this value to `WAITALGNPURCH` in the db to go back through the checkout flow as much as you want
