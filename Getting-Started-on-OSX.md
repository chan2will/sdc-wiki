## Install a variety of developer tools

```bash
xcode-select --install  # install XCode developer tools
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"  # install HomeBrew
brew install git  # git used for source control
brew install python3  # we use python 3, not 2; pip3 is installed with python3
brew install postgresql  # we use postgres as our database (follow the instructions this command spits out too)
brew install redis  # we use redis as a kv store/queue (follow the instructions for this one as well)
brew install node  # installs node and npm
brew install libjpeg  # for photo assessment manipuation
```

To start the postgres server now and on all subsequent system startups ([source](https://chartio.com/resources/tutorials/how-to-start-postgresql-server-on-mac-os-x):
```
mkdir -p ~/Library/LaunchAgents
ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
```

## Install VirtualEnv, so you can separate your Python runtime for SmileCheck

```bash
pip3 install virtualenv virtualenvwrapper  # this runs on your system python
mkdir ~/.virtualenvs  # this is where the virtual environment files will live
echo 'export WORKON_HOME=~/.virtualenvs' >> ~/.bash_profile  # env var to let virtualenv know where to keep files
echo 'VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3' >> ~/.bash_profile  # point it to correct python version
echo 'source /usr/local/bin/virtualenvwrapper.sh' >> ~/.bash_profile  # script that sets up aliases
. ~/.bash_profile  # reload our bash_profile file with these new env vars
mkvirtualenv -p $(which python3) smilecheck  # this creates the virtual environment
```

Use `workon smilecheck` to activate your virtual environment and `deactivate` to quit your virtual environment.

## Git setup

```bash
ssh-keygen -t rsa -C "first.last@smiledirectclub.com"  # generate a public/private keypair
cat ~/.ssh/id_rsa.pub | pbcopy  # shortcut to copy it to clipboard
git config --global user.name "Firstname Lastname"  # set your name on commit messages
git config --global user.email "first.last@smiledirectclub.com"  # set your email on commit messages
```

* Now you can add your SSH key to your GitHub account [here](https://github.com/settings/ssh)
* Also make sure to confirm your work e-mail on your GitHub account

## Set up local repos
Start by forking the repos you're going to be working on ([https://github.com/CamelotVG](https://github.com/CamelotVG))

_After_ you've forked the repo(s) on GitHub, clone a local copy:
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
```bash
cd smilecheck
python manage.py npm
```

## Set up the database and run the site
```bash
cd ~/dev/scc-api/smilecheck
brew services start postgresql  #start postgresql service, if not already started
python manage.py migrate
python manage.py pyrunner
python manage.py runserver
```

## Useful applications for development
```bash
install chrome browser: https://www.chrome.com
install pyCharm: https://www.jetbrains.com/pycharm/
install pgAdmin: https://www.pgadmin.org/download/
```