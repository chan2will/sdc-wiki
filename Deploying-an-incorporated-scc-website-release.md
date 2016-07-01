### Deploying an incorporated scc-website (Spartax) release

#### Prepare `scc-website`

Change directories to the `scc-website` repo locally, and update to the latest `develop` branch:

```
git checkout develop
git remote update origin --prune
git pull origin develop
```

Delete `dist` folder:

```
rm -rf dist/
```

Prep and build the website using `grunt`:

```
grunt clean --force
grunt build
```

Now we're ready to switch to the `scc-api` environment and repo.


#### Prepare `scc-api`


In `scc-api`, create and update a new branch to ingest the new `scc-website` changes:

```
git remote update upstream --prune
git checkout -b feature/name-of-new-release-branch upstream/develop
```

#### Djangofy process

Now we need to do some manual tweaking, and the easiest way to do this is using Finder:

```
open ~/dev/scc-api/smilecheck/sites/website/static
```

We have some files and a folder in here that are never to be deleted: assigning a red labeled tag to them is helpful in visually flagging them as important files not to be removed. They are:

* `djangofy.sh`
* `favicon.ico`
* `robots.txt`
* `website/`

Delete the other folders:

* `cases/`
* `commerce/`
* `common/`
* `fonts/`
* `leads/`
* `lib/`
* `old-tokens/`
* `scripts/`
* `styles/`

Now open the `dist` folder from `scc-website` in Finder:

```
open ~/dev/scc-website/dist
```

1. Delete `index.html`  
2. Copy all folders
3. Paste into scc-api's `static` folder
4. Open `~/dev/scc-api/smilecheck/sites/website/templates/website/index.html` in a text editor
5. Replace css static filenames with names from `static/styles` folder:
   * `ie-main.XXXXXXXX.css`
   * `ie-vendor.XXXXXXXX.css`
   * `main.XXXXXXXX.css`
   * `vendor.XXXXXXXX.css`
6. Replace javascript static filenames with names from `static/scripts` folder:
   * `scripts.XXXXXXXX.js`
   * `vendor.XXXXXXXX.js`

Then run `djangofy.sh`:

```
scc-api/smilecheck/sites/website/static
$ ./djangofy.sh
```

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
5. Copy tickets from pivotal into description or use diff to notate what has changed
6. `Publish`
7. Create a new PR
8. base: `master` => compare: `release`
9. Title should be `Release/YYYYMMDD`
10. Merge PR
11. Confirm merge
12. Delete branch
13. Post release changes to Slack's `#smilecheck` channel
