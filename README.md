# OpenShift-ed Test Driven React App

Running notes on porting the existing app to an OpenShift deployment
environment.

## The database

```bash
USER=myusergoeshere
PASS=mypassgoeshere
oc new-app -e POSTGRESQL_USER=$USER -e POSTGRESQL_PASSWORD=$PASS \
  -e POSTGRESQL_DATABASE=users --name=users-db centos/postgresql-95-centos7
```

## Users service

First, a new entrypoint script was created that does not check for 
the users-db database service via nc (as it is not installed as 
part of the s2i builder image).

Then:

```bash
oc new-app centos/python-36-centos7~https://github.com/mrbarge/tdreact \
  --context-dir=services/users -e APP_SCRIPT=manage.py
```
