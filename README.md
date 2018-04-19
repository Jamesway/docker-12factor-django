# 12 Factor Django with Docker

## The problem
When building a web app we'd like to employ the 12 factor app approach, so we need to get secrets out of settings.py and into the environment or an .env file. Also docker supports .env files so, we're heading in the right direction. [Cookiecutter]() is one way to get this all wired up, but is a little more opinionated than I like. Also, until I personally see the need, for certain tools, I don't add them to my stack.

Where we would like to end up is:
- use docker to start a 12 factor django project
- runserver with docker/docker-compose
- git pull the src + pipenv install to get all packages and a working dev copy of the app
- be deployable to Google App Engine and Heroku (wip)

## Usage

### Start a project
Right now I'm using a different django-admin image for kicking off startproject, but ideally, I'd like to combine it with my pipenv image
```
docker run --rm -v $(pwd)/code jamesway/12factor-django-admin startproject [project_name]
```

### Install Django into the local project directory
```
cd [project_name]
docker run --rm -v $(pwd):/code jamesway/python36-pipenv install django
```

### Adjust config
You should find an .env file with secrets and configs to change accordingly.
There should be a .gitingore for the .env. **IF there isn't add one!!!**

### runserver
Don't forget the ports
```
# inside the project root
docker run --rm -p 8000:8000 -v $(pwd):/code jamesway/python36-pipenv run manage.py runserver 0:8000
```

### Installing packages
```
# install normally
docker run --rm -v $(pwd):/code jamesway/python36-pipenv install python-dotenv

# install a development package
docker run --rm -v $(pwd):/code jamesway/python36-pipenv install pytest --dev

# install all non-development packages from pipfile
docker run --rm -itv $(pwd):/code jamesway/python36-pipenv install

# installing everything including dev packages from pipfile
docker run --rm -itv $(pwd):/code jamesway/python36-pipenv install --dev
```
