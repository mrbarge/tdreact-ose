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
  --context-dir=services/users --name=users \
  -e APP_SCRIPT=entrypoint-ose.sh \
  -e DATABASE_URL=postgres://$USER:$PASS@users-db:5432/users_dev \
  -e FLASK_ENV=development \
  -e APP_SETTINGS=project.config.DevelopmentConfig
```

And create a route:

```bash
oc create route edge --service=users
```

We may want to add a readiness check on the service to not accept
incoming connections until the database pod is active.

Depending upon what commands may be available in the pod, there
are different ways to conduct this check. In lieu of tools like
nc, socat, telnet (etc), we can do a simple
check in python. Here's how that readiness proble would look:

```bash
oc set probe dc/users --readiness --\
   python -c 'import socket; s = \
   socket.socket(socket.AF_INET, socket.SOCK_STREAM); \
   s.connect(("users-db",5432));'
```
