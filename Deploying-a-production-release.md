### Deploying a production release

#### Prepare `scc-website` if necessary

If there were any code changes in the `scc-website` repo, add them programmatically to a new `scc-api` feature branch by following the guide here:

https://github.com/CamelotVG/scc-api/wiki/Automatically-incorporate-scc-website-changes-into-an-scc-api-branch

#### Test and prepare release build

Test on local server- make an order, make sure everything still works properly. If so:

1. Push to remote
2. Create pull request
3. Merge PR
4. Create `release` branch on `scc-api` (this starts a build on jenkins for `vulcan_release_branch`)
5. Check staging, test again
6. Check `#kamino` in Slack and copy vulcan's SHA
7. Go to jenkins => `prod-deploy vulcan`
8. `Build with parameters` and paste SHA


#### Bookkeeping

1. In GitHub, click on `releases`
2. `Draft a new release`
3. Point it to `release`
4. Tag it as `YYYYMMDD`
5. Copy tickets from pivotal into description or use diff to notate what has changed:  
    `alias release-notes='git log upstream/master..upstream/develop --no-merges --graph --pretty=format:"%s"'`
6. `Publish`
7. Create a new PR
8. base: `master` => compare: `release`
9. Title should be `Release/YYYYMMDD`
10. Merge PR
11. Confirm merge
12. Delete branch
13. Post release changes to Slack's `#smilecheck` channel
