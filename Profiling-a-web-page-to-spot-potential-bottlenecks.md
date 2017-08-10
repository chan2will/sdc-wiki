## Styling and Profiling

### Problem
https://www.pivotaltracker.com/story/show/128548927
> The current `/staff/scheduling/appt/<UUID>/` is too slow. It is also causing 502 gateway errors. Need to resolve ASAP.

The problem was that there was something that was slowing the page down, but we were unsure what it was. After a few wild guesses turned out to be red herrings, the best way to track down what was causing such a performance hit was to use a system profiler. 

### Solution
After looking around for a profiler solution that would be easy to implement, it turns out we have one that is already available to us via `django-extensions`, `runprofileserver`:

http://django-extensions.readthedocs.io/en/latest/runprofileserver.html

There is a very clear caveat with using this, provided by the authors:
> Without sufficient knowledge it will not only be (very) hard but you’re likely to make wrong assumptions (and fixes). As a rule of thumb, clean, well written code will help you a lot more than overzealous micro-optimizations will.

Onward. `runprofileserver` uses `hotshot` as a profiler by default, which is supported in Python 2, but it is not compatible with Python 3, so we need to tell it to use `cProfile` instead. We will also flag it with `kcachegrind`, which makes the data available for viewing in a KCacheGrind compatible tool— in our case, we will be using `qcachegrind`, a QT app available on a Mac.

### Tools Installation

Install `qcachegrind`, then run the profile server:

```
brew install qcachegrind
brew linkapps qcachegrind
mkdir /tmp/profile-data
./manage.py runprofileserver --kcachegrind --use-cprofile --prof-path=/tmp/profile-data
```

### Profile
Hit the problematic web page in question:

`http://localhost:8000/staff/scheduling/appt/{YOUR UUID HERE}/`

Once it finishes loading, CTRL-C your local server. This should have generated a file in `/tmp/profile-data` with the page in question as the title of the file. In this particular case, it generated

`staff.scheduling.appt.1a79c183-346f-422d-8faa-5e4bbbdad447.023545ms.1472158306.prof`

### Diagnostics
You can open this file in the `qcachegrind` application by either opening the application via Finder, then File > Open > etc… or by invoking it directly in the command line:

```
qcachegrind /tmp/profile-data/staff.scheduling.appt.1a79c183-346f-422d-8faa-5e4bbbdad447.023545ms.1472158306.prof
```
This will open the app with the profile loaded. 

Sorted by `Called`, the first function listed had several `pytz` calls. This is a site package outside of our own code, but something we wrote is calling it very frequently. Clicking into the second highest called function, `<cycle 1>`, we see some callers that are our own code, most notably `slot_utc_date` and `get_slots`. It seems there is a pattern of calling both of these many times, which given enough iterations, can cause the system to become very slow. 

￼![Profiler-1](http://imgur.com/Ox1Dwxl.png)

Diving down into each call, we get the location and file names of where these calls are happening:

￼![Profiler-2](http://imgur.com/2584oOo.png)

￼￼![Profiler-3](http://imgur.com/RAQGm0e.png)

We can see that in `scheduling.py`, `get_slots()` is calling `slot_utc_date` in a `for` loop that has been nested 5x:

￼￼![Sublime-1](http://imgur.com/YDFzXo7.png)

Each time `slot_utc_date` is called, we see that `localize` and `pytz` are referenced for each one of these calls. Now we see why in the first profiler image `pytz` was called so many times.

￼￼![Sublime-2](http://imgur.com/eUOcp9H.png)

### Solution
After some code was re-written to not call `slot_utc_date` in the `for` loop (https://github.com/CamelotVG/scc-api/pull/1927/files), the processing time improved by 15x. There was also some old code that was being ran that was unnecessary in `sidenav.jade` (removed `{% generate_navbar as navdict %}`), and using the Django debug toolbar, we see the numbers for Time and SQL calls dramatically decreased:

￼￼![Debug-1](http://imgur.com/tzZ9DR9.png)
