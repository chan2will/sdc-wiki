##`git bisect`

###Why would you need it?
`git bisect` can be extremely helpful for tracking down changes that were created, but you're not exactly sure where or when. An example-- I tried to create a new prescription on the staff portal, and after clicking the button, a modal window opened and immediately closed. I knew it used to work properly at some point, but it wasn't now, and I confirmed it on dev as well.

###The basics
`git bisect` will take 2 given commits: 1 "good" working one, and 1 "bad" broken one. It will then take the median of the two, and wait for you to tell it if it is "good" or "bad". Once you continue to label each try as "good" or "bad", you arrive shortly at the end of all possible commits, and it will tell you what the first bad commit was.

###Where to start
To find the first "good" one, you can look at your git log, and pick an entry from several days ago, when you think it was working. i.e.

```shell
git log

0ee794a - (daveboling/bug/fix-taboola-and-outbrain) Fix missing UTM for Taboola and Outbrain (3 days ago) <Dave Boling>
72e190a - (daveboling/develop) Merge pull request #1450 from DeCastri/feature/wizard-new-aligner (5 days ago) <Julie Barnick>
813ace0 - (refs/bisect/bad) resolved merge conflicts (5 days ago) <Ted DeCastri>
dfbbaf6 - (tag: 20160510, refs/bisect/good-dfbbaf6cbaff57cbea1b6c8a62c3c6c9595d17c5) Merge pull request #1527 from julierae/feature/workflow (6 days ago) <Julie Barnick>
af2ecea - Add Waiting on STL Files Case Status (6 days ago) <Julie Barnick>
3a69747 - Merge pull request #1525 from katyjustiss/feature/Admin-changes (6 days ago) <Julie Barnick>
a348fc8 - Merge pull request #1524 from solesby/fix/echosign-serializer (6 days ago) <Julie Barnick>
25bb7b1 - Fix echosign tests and authentication return codes (6 days ago) <Adam Solesby>
3cae9b1 - Add improvements to echosign admin (6 days ago) <KatyJustiss>
```

So you can check it out directly:

`git checkout 3cae9b1`

Then reload your page and see if your issue works. If it does, great. This will be your "good" commit. Then take the most recent one (`0ee794a` in this case) and that will be your "bad" one.

###Bisecting
Git bisect must be invoked at the top level directory, i.e. above "smilecheck". For me, that is

`/Users/eric/dev/scc-api`

and begin with

`git bisect start`

followed by 

`git bisect good 3cae9b1`

followed by

`git bisect bad 0ee794a`

and it will bisect and tell you what commit you are on. Reload your page and see if it works. In this particular case, a database change has occurred, and I get a Django ProgrammingError page. This is not the problem I am hunting down; this commit has happened far before my problem I am trying to fix. So although it would make sense to call this a bad one, it is actually a "good" commit because the expected modal behavior that is prematurely closing is not present here. So we say

`git bisect good`

and it brings us to another commit. We reload our browser, and we see the site-- and it fails, just as we see in dev. So that's

`git bisect bad`

We get another commit, and reload our browser. It works!

`git bisect good`

We do this until git finally responds with

`first known bad commit -- 813ace087f52b251ed6372fa2363e48e4e1680d7`

###Finding and fixing the bug
So now we know where a bug was introduced. We can go directly to this in github with the link

[https://github.com/CamelotVG/scc-api/commit/813ace087f52b251ed6372fa2363e48e4e1680d7](https://github.com/CamelotVG/scc-api/commit/813ace087f52b251ed6372fa2363e48e4e1680d7)

and see that it is a pretty long commit that is resolving merge conflicts, where things can naturally get hairy. After narrowing down a few suspect files, I found some duplicated javascript imports that are indeed the culprit:

[https://github.com/CamelotVG/scc-api/pull/1534/commits/1b63fa42ba5177ffea78e1f2c364eb1ff9f133a6](https://github.com/CamelotVG/scc-api/pull/1534/commits/1b63fa42ba5177ffea78e1f2c364eb1ff9f133a6)

Since we're done with git bisect, we say

`git bisect reset`

and you're ready to create a new branch or check out an existing one.
