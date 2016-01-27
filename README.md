# best_practices

# Dev Environment

### Setup
```
apt-get install curl
```

### Postgres (psycopg2)
```
apt-get install python-dev libpq-dev
```

### sass, susy, compass

```
apt-get install ruby
gem install sass
gem install susy
apt-get install ruby-dev
gem install compass
```

### Node
```
curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash -
apt-get install nodejs
```

### Grunt

```
cd app_folder/static
npm install
```
To watch for changes in SASS/Coffee/JS files, simply run `grunt`. If preparing the app for production, run `grunt compress` which will run ngAnnotate & uglify.

### Docker

```
wget -qO- https://get.docker.com/ | sh
systemctl enable docker

docker-compose up
```

To rebuild the docker image after changing the system requirements

```
docker-compose build
```

List containers
```
docker ps -a
```

List images
```
docker images
```

Stop and remove all Docker containers
```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

Remove all Docker images
```
docker rmi $(docker images -q)
```

##### Mac Specific
Install [Docker Toolbox](https://www.docker.com/toolbox)

Create a new docker machine, and import boot2docker. If no previous boot2docker vm, remove --virtualbox-import-boot2docker-vm.
```
docker-machine create -d virtualbox --virtualbox-import-boot2docker-vm boot2docker-vm docker-vm
```

Export environment variables
```
docker-machine env boot2docker-vm
```

Get your machine's IP. This replaces 127.0.0.1 in the hosts file.
```
docker-machine ls
docker-machine ip boot2docker-vm
```

Start docker-compose
```
docker-compose up
```

Let it build. You should be able to access it via the IP above.

### Dump and Load Database

Pull database info from Production or Staging into a .json file
```
python manage.py dumpdata --exclude contenttypes --exclude sessions -e auth.Permission -e admin > db.json
```

Load database info into Dev environment
```
rm db.sqlite3
python manage.py migrate
python manage.py loaddata db.json
```

### Git flow

##### List Branches
```
git branch
```

##### New Branch
```
git checkout master
git branch features/new_feature
git checkout features/new_feature
git push --set-upstream origin features/new_feature
```

##### Merge Branch
```
git checkout staging
git merge features/new_feature
```

##### Delete Branch
```
git branch -d features/feature_branch
```

##### Hotfix branch
```
git checkout master
git branch hotfixes/new_hotfix
...work on hotfix...
git merge hotfixes/new_hotfix
```

##### New Release
```
git checkout master
git branch releases/release.1.2.3
```

##### Squash All Commits
```
git rebase -i --root master
// ...change all but first 'pick' to 'squash'...
git push --force
```

##### Tagging
```
git tag 0.1 // create tag
git tag -d 0.1 // locally delete tag
git push origin 0.1 // push specific tag
git push origin --tags // push all tags
```

### Register with PyPi
```
python setup.py register -r pypitest
python setup.py sdist upload -r pypitest

python setup.py register -r pypi
python setup.py sdist upload -r pypi
```

### Bug tracking

# DevOps

### OpsWorks

Repository URL: git@github.com:repo_name/chef.git
Using Berkshelf
Custom JSON:
```
{
    "platform_family": "debian",
    "platform": "ubuntu",
    "ssh_users": {
        "2001": {
            "name": "user1",
            "public_key": "ssh-rsa XXXXXXXXXX user1@comp_name",
            "sudoer": true
        },
        "2002": {
            "name": "user2",
            "public_key": "ssh-rsa XXXXXXXXXX user2",
            "sudoer": true
        },
        "2003": {
            "name": "user3",
            "public_key": "ssh-rsa XXXXXXXXXX user3",
            "sudoer": true
        }
    },
    "logentries": {
        "server_name": "Staging",
        "account_key": "~~~ACCOUNT_KEY~~~"
    },
    "nginx": {
        "certs": "/etc/nginx/certs"
    },
    "apps": {
        "XXXXX": {
            "port": 8000,
            "repo": "https://XXXXX@github.com/XXXXX/XXXXX.git",
            "branch": "staging",
            "folder": "XXXXX",
            "app_folder": "XXXXX",
            "celerybeat": true,
            "main_server": "webservers1",
            "gunicorn": {
                "maxrequests": 5000,
                "workers": 9,
                "timeout": 180
            },
            "celery": {
                "settings": "XXXXX"
            },
            "environment": {
                "APP_ENV": "staging",
                "DATABASE_URL": "postgres://username:XXXXX@XXXXX.XXXXX.us-west-1.rds.amazonaws.com:5432/db_name",
                "REDIS_URL": "",
                "DEBUG": true
            },
            "domains": [
                {
                    "domain": "staging.XXXXX.com"
                },
                {
                    "domain": "dev.XXXXX.com",
                    "key": "XXXXX.key",
                    "cert": "XXXXX.crt",
                    "ssl": true
                }
            ]
        }
    }
}
```


##### Layers
App
nginx proxy

##### Apps
XXXXX

##### Resources
Elastic IPs

##### Github Setup
Add OpsWorks Web Hook

##### AMI
ubuntu-trusty-14.04-amd64-server-20140927.manifest.xml (ami-6d6b6028)

### Chef
https://github.com/user_name/chef/

Berksfile

the .json files in root at overridden by OpsWorks

app/files/default/XXXXX.crt
app/files/default/XXXXX.key

app/templates/default/*


### Extra Server Config

### Aux Server

##### Sendy

##### Jenkins
For running Selenium tests:
apt-get install firefox xvfb

###### Plugins
GIT Plugin

###### Email Notification Settings
SMTP Server: email-smtp.us-east-1.amazonaws.com

###### XXXXX Settings
Git Repository URL: git@github.com:user_name/repo_name
Credentials: Username and Password
Branches to build: staging
Build Triggers: Build when a change is pushed to GitHub
Execute shell:
```
cd $WORKSPACE
virtualenv .
source bin/activate
pip install -r requirements.txt
rm -f db.sqlite3
python manage.py migrate
./bin/python manage.py test
```
E-mail notification: my_email@email.com

###### Install instructions
```
cd /var/lib/jenkins/jobs/project_name/workspace/

install virtualenv in .
```
