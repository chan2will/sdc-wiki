For when you want to run your tests locally, here are some nice aliases from HPT, delivered by Eric Hamiter.

```
alias pmt="SCCENVIRONMENT=test ./manage.py test --keepdb --noinput --verbosity=2"
alias pmt4="SCCENVIRONMENT=test ./manage.py test --keepdb --noinput --verbosity=2 --parallel=4"
alias pmtc="SCCENVIRONMENT=test ./manage.py test --noinput --verbosity=2"
alias pmt4c="SCCENVIRONMENT=test ./manage.py test --noinput --verbosity=2 --parallel=4"
```

--keepdb will store the test database for the next time your run the tests.
--parallel=4 will use 4 cores.

You can also run tests just in an app by adding the app name after the "pmt*".  For example: "pmt reports".