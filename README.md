# Kubernetes postgres-controller

  <img src="https://raw.githubusercontent.com/signifly/mysql-controller/master/img/k8s-logo.png" width="100"> + <img src="https://raw.githubusercontent.com/signifly/mysql-controller/master/img/mysql-logo.png" width="100">

A simple k8s controller to create MySQL databases. Once you install the controller and point it at your existing MySQL database instance, you can create `MysqlDatabase` resource in k8s and the controller will create a database in your MySQL instance, create a user that with access to this database and optionally run extra SQL commands.

Example resources:

```yaml
kind: Secret
apiVersion: v1
type: Opaque
metadata:
  name: databasePasswords
data:
  myFirstPassword: dnBJblBESHpWS212YWhzVA==
---
apiVersion: signifly.io/v1
kind: MysqlDatabase
metadata:
  name: app1
spec:
  dbName: db1
  dbRoleName: user1
  dbRolePassword:
    name: databasePasswords
    key: myFirstPassword
```

The controller will look up the secret within the same namespace as the `MysqlDatabase` resource.


Pull requests welcome.

### Installation

Use the included [Helm](https://helm.sh/) chart and set the host, username and password for your default MySQL instance:

```
helm upgrade --install mysql-controller ./chart --set config.mysql_instances.default.host=my-rds-instance.rds.amazonaws.com --set config.mysql_instances.default.user=root --set config.mysql_instances.default.password=admin_password
```

Or use the docker image: [signifly/mysql-controller](https://hub.docker.com/r/signifly/mysql-controller)

### Examples

See [examples](examples) to for how to run extra SQL commands and also how to drop databases when the k8s resource is deleted.

See [example-config.yaml](example-config.yaml) for example chart values file.

### Testing

To test locally, start a postgres container:

```
docker run -d -p 127.0.0.1:3306:3306 -e MYSQL_ROOT_PASSWORD=mysql -e MYSQL_ROOT_HOST="%" mysql:8.0
```

Start the controller, it will use your default kubectl configuration/context:

```
./controller.py --log-level=debug --config-file=example-config.yaml
```

Create or change some resources:

```
kubectl apply -f examples/simple.yaml
```

## Credits

This controller is a modified version of [postgres-controller](https://github.com/max-rocket-internet/postgres-controller) to work with MySQL instead.

