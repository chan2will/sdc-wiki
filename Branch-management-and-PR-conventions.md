Always work on a fresh branch based off of `upstream`'s `develop` branch.

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

After finishing the work, push up to `origin` (assuming you have not renamed it):
```shell
git push origin feature/my-new-feature
```

Then you can make a pull request to Camelot's `develop` branch:  
https://help.github.com/articles/creating-a-pull-request/#creating-the-pull-request

Be descriptive in the details section, and leave a link to the Pivotal ticket by supplying the id within square brackets, like

```shell
Fixed the widget to supply the baz to the bar. Updated styling supplied by marketing.

[Finishes #123456789]
```
