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

