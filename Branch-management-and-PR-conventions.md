Start off with your own fork of `scc-api` repo by forking it from the page:  
<img src="http://i.imgur.com/umwdmL2.png" width="200">  

> In the following, `upstream` will refer to `scc-api` repo on CamelotVG's account, and `origin` will refer to the `scc-api` repo on your own personal account.

Then from your own fork, work on a fresh branch based off of `upstream`'s `develop` branch.

```shell
git remote update --prune
git checkout -b feature/my-new-feature upstream/develop
```

Standard naming conventions:
```shell
feature/my-branch-name
bug/my-branch-name
chore/my-branch-name
```

After finishing the work, test it locally to make sure it passes all tests:

```shell
SCCENVIRONMENT=test ./manage.py test --noinput --verbosity=2
```

...then if the tests pass, push your branch up to `origin` (assuming you have not renamed it):
```shell
git push origin feature/my-new-feature
```

Then you can make a pull request to Camelot's `develop` branch:  
https://help.github.com/articles/creating-a-pull-request/#creating-the-pull-request

The title of the pull request will be the name of the branch, therefore you will not need to change the title of the pull request.

Fill out the given pull request template with appropriate info and end it with a link to the Pivotal tracker ID

The pull request will then be reviewed by SDC.  Either it will be accepted and then merged with the code base or there will comments added noting areas of correction.

If there are comments made about your code, and you need to update/fix it, make the corrections, then re-push to the same branch. The pull request will automatically detect the new code on the CamelotVG repo, and will re-run the unit tests.