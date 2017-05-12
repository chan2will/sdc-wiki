1. Get access to the google spreadsheet that allows you to add a record for the new status/status option
2. Add the record to the spreadsheet
3. Run `python manage.py pyrunner -xf 0001_master_data.py` and verify that your changes are present in the `workflow_status` and `workflow_status_option` tables.
4. Do your local dev with this data
5. If you run tests and find that some are failing due to invalid status reasons (something like `AssertionError: 500 != 200 : {'detail': 'StatusOption matching query does not exist.'}`), you will need to update your test fixtures.
6. You can do so with this command `rm ~/dev/scc-api/smilecheck/workflow/fixtures/0001_initial.json && ./manage.py dumpdata --indent=4 workflow --exclude workflow.EmailTemplate --exclude workflow.EmailMessage --exclude workflow.EmailMessageHistory >> ~/dev/scc-api/smilecheck/workflow/fixtures/0001_initial.json`
7. This should update `workflow/fixtures/0001_initial.json` to reflect the current state of the db.
8. If your tests are now passing, make sure to push up those changes with the rest of your code.
