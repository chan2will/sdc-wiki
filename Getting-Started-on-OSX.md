## Install a variety of developer tools

```bash
xcode-select --install  # install XCode developer tools
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"  # install HomeBrew
brew install git  # git used for source control
brew install python3 python3-pip # we use python 3, not 2
brew install postgresql  # we use postgres as our database (follow the instructions this command spits out too)
brew install redis  # we use redis as a kv store/queue (follow the instructions for this one as well)
brew install libjpeg  # for photo assessment manipuation
```

## Install VirtualEnv, so you can separate your Python runtime for SmileCheck

```bash
pip3 install virtualenv virtualenvwrapper  # this runs on your system python
mkdir ~/.virtualenvs  # this is where the virtual environment files will live
echo 'export WORKON_HOME=~/.virtualenvs' >> ~/.bashrc  # env var to let virtualenv know where to keep files
echo 'VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3' >> ~/.bashrc  # point it to correct python version
echo 'source /usr/local/bin/virtualenvwrapper.sh' >> ~/.bashrc  # script that sets up aliases
. ~/.bashrc  # reload our bashrc file with these new env vars
mkvirtualenv -p $(which python3) smilecheck  # this creates the virtual environment
```

Use `workon smilecheck` to activate your virtual environment and `deactivate` to quit your virtual environment.

## Git setup

```bash
ssh-keygen -t rsa -C "first.last@smilecareclub.com"  # generate a public/private keypair
cat ~/.ssh/id_rsa.pub | pbcopy  # shortcut to copy it to clipboard
git config --global user.name "Firstname Lastname"  # set your name on commit messages
git config --global user.email "first.last@smilecareclub.com"  # set your email on commit messages
```

* Now you can add your SSH key to your GitHub account [here](https://github.com/settings/ssh)
* Also make sure to confirm your work e-mail on your GitHub account

## Set up local repos
Start by forking the repos you're going to be working on ([https://github.com/CamelotVG](https://github.com/CamelotVG))
```bash
mkdir ~/dev
cd ~/dev
git clone git@github.com:<your-github-username>/<repo-name>.git
cd ~/dev/<repo-name>
git remote add upstream git@github.com:CamelotVG/<repo-name>.git
git remote update
git branch -u upstream/develop
```

## Activate your virtual environment and install dependencies
```bash
workon scc
cd ~/dev/scc-api
pip install -r requirements.txt
```
Install npm modules
(as of 2/24/2016, although this will likely become site-specific soon, in which case you'll want to find the site's `package.json` and `npm install`):
```bash
cd smilecheck
npm install
```

## Set up the database and run the site
```bash
cd ~/dev/scc-api/smilecheck
python manage.py migrate
python manage.py pyrunner
python manage.py runserver
```