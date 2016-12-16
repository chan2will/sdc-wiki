### Deploying a production release

#### Test and prepare release build on staging

Test on local server- make an order, make sure everything still works properly. If so:

1. Push to remote
2. Create pull request
3. Merge PR
4. Create `release` branch on `scc-api` (this starts a build on jenkins for `vulcan_release_branch`)

#### Release to production from staging

1. Check `#kamino` in Slack and copy vulcan's SHA from its last `staging` release:
```
DEPLOYED: STAGING Vulcan with SHA <copy-this-SHA-that-exists-here> ---- GITHUB
```
2. Go to jenkins => `prod-deploy vulcan`
3. `Build with parameters` and paste SHA
4. If there are ad-hoc scripts to run, or fixtures that have been updated, run pyrunner:  
    a. Go to jenkins => `prod-pyrunner`  
    b. `Build with parameters` and paste SHA  
5. Monitor the progress on jenkins. On successful deployment, a slack message will be posted to #kamino.

#### Bookkeeping

1. In GitHub, click on `releases`
2. `Draft a new release`
3. Point it to `release`
4. Tag it as `YYYYMMDD`
5. In Jira, go to the Releases, click the release number, then click the release notes link. Copy and paste these notes into GitHub's textarea.
6. `Publish`
7. Create a new PR
8. base: `master` => compare: `release`
9. Title should be `Release/YYYYMMDD`
10. Merge PR
11. Confirm merge
12. Delete `release` branch
13. Inform the QA team that the release has happened-- they will do smoke tests and inform the world
