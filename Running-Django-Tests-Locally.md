For when you want to run your tests locally, here are some nice aliases:

```
alias pmt="SCCENVIRONMENT=test ./manage.py test --keepdb --noinput --verbosity=2"
alias pmt4="SCCENVIRONMENT=test ./manage.py test --keepdb --noinput --verbosity=2 --parallel=4"
alias pmtc="SCCENVIRONMENT=test ./manage.py test --noinput --verbosity=2"
alias pmt4c="SCCENVIRONMENT=test ./manage.py test --noinput --verbosity=2 --parallel=4"
```

`--keepdb` will store the test database for the next time your run the tests.
`--parallel=4` will use 4 cores.

You can also run tests just in an app by adding the app name after the "pmt*".  For example: `pmt reports`.

To run an individual test, you'll need the complete path to the class containing the test (as if you were going to import it within a Python script) and the name of the test. For example, to run the `test_lead_scan_eligible_true` test by itself, you would enter:

```
python manage.py test core.leads.tests.test_api.LeadAPITestCase.test_lead_scan_eligible_true --keepdb
```
or
```
pmt core.leads.tests.test_api.LeadAPITestCase.test_lead_scan_eligible_true
```