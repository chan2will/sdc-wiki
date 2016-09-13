> This document makes a few assumptions:

> * You have a conventional directory setup of `~/dev/scc-website` and `~/dev/scc-api`.
> * The remote repos for both are named `upstream`.
> * You have a virtual environment `scc-api` with its `setvirtualenvproject` value set to `~/dev/scc-api/smilecheck`, i.e. if you type `workon scc-api`, the virtual environment `scc-api` is activated and you are taken to `~/dev/scc-api/smilecheck`. If that is not the case, `cd` into the `smilecheck` directory and type `setvirtualenvproject`. **This is essential for this script to work properly.**

Add the following to your `~/.bash_profile` and source it.

```shell
# Prepares scc-api with any changes made to scc-website
# e.g. prepare_website feature/add-foobar-functionality
prepare_website() {
  pushd ~/dev/scc-website
  git remote update --prune
  git checkout develop
  git pull origin develop
  rm -r dist/
  grunt clean --force
  grunt build
  popd
  workon scc-api
  git remote update --prune
  git checkout -b $1 upstream/develop
  git pull upstream develop
  pushd ~/dev/scc-api && pip install -r requirements.txt && popd
  pushd ~/dev/scc-api/smilecheck && ./manage.py migrate --noinput && ./manage.py npm && popd
  pushd ~/dev/scc-website
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/cases/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/commerce/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/common/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/fonts/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/leads/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/lib/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/old-tokens/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/scripts/
  rm -r ~/dev/scc-api/smilecheck/sites/website/static/styles/
  cd dist
  rm index.html
  cp -r cases/ ~/dev/scc-api/smilecheck/sites/website/static/cases/
  cp -r commerce/ ~/dev/scc-api/smilecheck/sites/website/static/commerce/
  cp -r common/ ~/dev/scc-api/smilecheck/sites/website/static/common/
  cp -r fonts/ ~/dev/scc-api/smilecheck/sites/website/static/fonts/
  cp -r leads/ ~/dev/scc-api/smilecheck/sites/website/static/leads/
  cp -r lib/ ~/dev/scc-api/smilecheck/sites/website/static/lib/
  cp -r old-tokens/ ~/dev/scc-api/smilecheck/sites/website/static/old-tokens/
  cp -r scripts/ ~/dev/scc-api/smilecheck/sites/website/static/scripts/
  cp -r styles/ ~/dev/scc-api/smilecheck/sites/website/static/styles/
  popd
  python ~/dev/static-file-updater.py
  pushd ~/dev/scc-api/smilecheck/sites/website/static && ./djangofy.sh && popd
}
```

Create the file `~/dev/static-file-updater.py`:

```python
import os
import pwd
import re

username = pwd.getpwuid(os.getuid()).pw_name
index_html_location = '/Users/{}/dev/scc-api/smilecheck/sites/website/templates/website/index_v2.html'.format(username)
scripts_location = '/Users/{}/dev/scc-api/smilecheck/sites/website/static/scripts'.format(username)
styles_location = '/Users/{}/dev/scc-api/smilecheck/sites/website/static/styles'.format(username)

replace_scripts_js = 'scripts/scripts.*.js'
replace_vendor_js = 'scripts/vendor.*.js'
scripts_dir = os.listdir(scripts_location)
scripts_js = 'scripts/{}'.format(list(filter(lambda x: x.startswith('scripts.'), scripts_dir))[0])
vendor_js = 'scripts/{}'.format(list(filter(lambda x: x.startswith('vendor.'), scripts_dir))[0])

replace_ie_main_css = 'styles/ie-main.*.css'
replace_ie_vendor_css = 'styles/ie-vendor.*.css'
replace_main_css = 'styles/main.*.css'
replace_vendor_css = 'styles/vendor.*.css'
styles_dir = os.listdir(styles_location)
ie_main_css = 'styles/{}'.format(list(filter(lambda x: x.startswith('ie-main.'), styles_dir))[0])
ie_vendor_css = 'styles/{}'.format(list(filter(lambda x: x.startswith('ie-vendor.'), styles_dir))[0])
main_css = 'styles/{}'.format(list(filter(lambda x: x.startswith('main.'), styles_dir))[0])
vendor_css = 'styles/{}'.format(list(filter(lambda x: x.startswith('vendor.'), styles_dir))[0])

with open(index_html_location) as f:
    s = f.read()

s = re.sub(replace_scripts_js, scripts_js, s)
s = re.sub(replace_vendor_js, vendor_js, s)
s = re.sub(replace_ie_main_css, ie_main_css, s)
s = re.sub(replace_ie_vendor_css, ie_vendor_css, s)
s = re.sub(replace_main_css, main_css, s)
s = re.sub(replace_vendor_css, vendor_css, s)

with open(index_html_location, 'w') as f:
    f.write(s)
```

Invoke the script by passing in the desired feature branch name into the function, e.g.

```shell
$ prepare_website feature/add-foobar-functionality
```
