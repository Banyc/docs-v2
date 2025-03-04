---
title: Manage InfluxDB users in Chronograf
description: >
  Enable authentication and manage InfluxDB OSS and InfluxDB Enterprise users in Chronograf.
aliases:
  - /chronograf/v1.10/administration/user-management/
menu:
  chronograf_1_10:
    name: Manage InfluxDB users
    weight: 60
    parent: Administration
---

The **Chronograf Admin** provides InfluxDB user management for InfluxDB OSS and InfluxDB Enterprise users.

{{% note %}}
***Note:*** For details on Chronograf user authentication and management, see [Managing security](/chronograf/v1.10/administration/managing-security/).
{{% /note %}}

{{% note %}}
#### Disabled administrative features
If connected to **InfluxDB OSS v2.x** or **InfluxDB Cloud**, all InfluxDB administrative
features are disabled in Chronograf. Use the InfluxDB OSS v2.x or InfluxDB Cloud user
interfaces, CLIs, or APIs to complete administrative tasks.
{{% /note %}}

**On this page:**

* [Enable authentication](#enable-authentication)
* [InfluxDB OSS user management](#influxdb-oss-user-management)
* [InfluxDB Enterprise user management](#influxdb-enterprise-user-management)

## Enable authentication

Follow the steps below to enable authentication.
The steps are the same for InfluxDB OSS instances and InfluxDB Enterprise clusters.

{{% note %}}
_**InfluxDB Enterprise clusters:**_
Repeat the first three steps for each data node in a cluster.
{{% /note %}}

### Step 1: Enable authentication.

Enable authentication in the InfluxDB configuration file.
For most Linux installations, the configuration file is located in `/etc/influxdb/influxdb.conf`.

In the `[http]` section of the InfluxDB configuration file (`influxdb.conf`), uncomment the `auth-enabled` option and set it to `true`, as shown here:

```
[http]
  # Determines whether HTTP endpoint is enabled.
  # enabled = true

  # The bind address used by the HTTP service.
  # bind-address = ":8086"

  # Determines whether HTTP authentication is enabled.
  auth-enabled = true #
```

### Step 2: Restart the InfluxDB service.

Restart the InfluxDB service for your configuration changes to take effect:

```
~# sudo systemctl restart influxdb
```

### Step 3: Create an admin user.

Because authentication is enabled, you need to create an [admin user](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#user-types-and-privileges) before you can do anything else in the database.
Run the `curl` command below to create an admin user, replacing:

* `localhost` with the IP or hostname of your InfluxDB OSS instance or one of your InfluxDB Enterprise data nodes
* `chronothan` with your own username
* `supersecret` with your own password (note that the password requires single quotes)

{{< keep-url >}}
```
~# curl -XPOST "http://localhost:8086/query" --data-urlencode "q=CREATE USER chronothan WITH PASSWORD 'supersecret' WITH ALL PRIVILEGES"
```

A successful `CREATE USER` query returns a blank result:

```
{"results":[{"statement_id":0}]}   <--- Success!
```

### Step 4: Edit the InfluxDB source in Chronograf.

If you've already [connected your database to Chronograf](/chronograf/v1.10/introduction/installation/#connect-chronograf-to-your-influxdb-instance-or-influxdb-enterprise-cluster), update the connection configuration in Chronograf with your new username and password.
Edit existing InfluxDB database sources by navigating to the Chronograf configuration page and clicking on the name of the source.

## InfluxDB OSS User Management

On the **Chronograf Admin** page:

* View, create, and delete admin and non-admin users
* Change user passwords
* Assign admin and remove admin permissions to or from a user

![InfluxDB OSS user management](/img/chronograf/1-6-admin-usermanagement-oss.png)

InfluxDB users are either admin users or non-admin users.
See InfluxDB's [authentication and authorization](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#user-types-and-privileges) documentation for more information about those user types.

{{% note %}}
Chronograf currently does not support assigning InfluxDB database `READ`or `WRITE` access to non-admin users.

As a workaround, grant `READ`, `WRITE`, or `ALL` (`READ` and `WRITE`) permissions to non-admin users with the following curl commands, replacing anything inside `< >` with your own values:

#### Grant `READ` permission:
```sh
curl --request POST "http://<InfluxDB-IP>:8086/query?u=<username>&p=<password>" \
  --data-urlencode "q=GRANT READ ON <database-name> TO <non-admin-username>"
```

#### Grant `WRITE` permission:
```sh
curl --request POST "http://<InfluxDB-IP>:8086/query?u=<username>&p=<password>" \
  --data-urlencode "q=GRANT WRITE ON <database-name> TO <non-admin-username>"
```

#### Grant `ALL` permission:
```sh
curl --request POST "http://<InfluxDB-IP>:8086/query?u=<username>&p=<password>" \
  --data-urlencode "q=GRANT ALL ON <database-name> TO <non-admin-username>"
```

In all cases, a successful `GRANT` query returns a blank result:

```sh
{"results":[{"statement_id":0}]}  # <--- Success!
```

Remove `READ`, `WRITE`, or `ALL` permissions from non-admin users by replacing `GRANT` with `REVOKE` in the curl commands above.
{{% /note %}}

## InfluxDB Enterprise user management using the UI


To create, manage, and delete users, click **Admin {{< icon "crown" >}}** in the left navigation bar. 

To create a user do the following:

1. Select the **Users** tab.
2. Click **+ Create User**.
3. Add a user name.
4. Add a password.
5. Click **Create**.
6. Assign a role to the user in the `Roles` section. To create a role see [Roles](#roles).
7. Click **Apply Changes**. 

To make changes to a user simply click on the username, make any changes and click **Apply Changes**. To delete a user click **Delete User**.

### User types

Admin users have the following permissions by default:

* [CreateDatabase](#createdatabase)
* [CreateUserAndRole](#createuserandrole)
* [DropData](#dropdata)
* [DropDatabase](#dropdatabase)
* [ManageContinuousQuery](#managecontinuousquery)
* [ManageQuery](#managequery)
* [ManageShard](#manageshard)
* [ManageSubscription](#managesubscription)
* [Monitor](#monitor)
* [ReadData](#readdata)
* [WriteData](#writedata)

Non-admin users have no permissions by default.
Assign permissions and roles to both admin and non-admin users.

### Permissions

#### AddRemoveNode
Permission to add or remove nodes from a cluster.

**Relevant `influxd-ctl` arguments**:
[`add-data`](/{{< latest "enterprise_influxdb" >}}/administration/cluster-commands/#add-data),
[`add-meta`](/{{< latest "enterprise_influxdb" >}}/administration/cluster-commands/#add-meta),
[`join`](/{{< latest "enterprise_influxdb" >}}/administration/cluster-commands/#join),
[`remove-data`](/{{< latest "enterprise_influxdb" >}}/administration/cluster-commands/#remove-data),
[`remove-meta`](/{{< latest "enterprise_influxdb" >}}/administration/cluster-commands/#remove-meta), and
[`leave`](/{{< latest "enterprise_influxdb" >}}/administration/cluster-commands/#leave)

**Pages in Chronograf that require this permission**: NA

#### CopyShard
Permission to copy shards.

**Relevant `influxd-ctl` arguments**:
[`copy-shard`](/{{< latest "enterprise_influxdb" >}}/administration/cluster-commands/#copy-shard)

**Pages in Chronograf that require this permission**: NA

#### CreateDatabase
Permission to create databases, create [retention policies](/{{< latest "influxdb" "v1" >}}/concepts/glossary/#retention-policy-rp), alter retention policies, and view retention policies.

**Relevant InfluxQL queries**:
[`CREATE DATABASE`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#create-database),
[`CREATE RETENTION POLICY`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#create-retention-policies-with-create-retention-policy),
[`ALTER RETENTION POLICY`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#modify-retention-policies-with-alter-retention-policy), and
[`SHOW RETENTION POLICIES`](/{{< latest "influxdb" "v1" >}}/query_language/explore-schema/#show-retention-policies)

**Pages in Chronograf that require this permission**: Dashboards, Data Explorer, and Databases on the Admin page

#### CreateUserAndRole
Permission to manage users and roles; create users, drop users, grant admin status to users, grant permissions to users, revoke admin status from users, revoke permissions from users, change user passwords, view user permissions, and view users and their admin status.

**Relevant InfluxQL queries**:
[`CREATE USER`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#user-management-commands),
[`DROP USER`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#general-admin-and-non-admin-user-management),
[`GRANT ALL PRIVILEGES`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#user-management-commands),
[`GRANT [READ,WRITE,ALL]`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#non-admin-user-management),
[`REVOKE ALL PRIVILEGES`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#user-management-commands),
[`REVOKE [READ,WRITE,ALL]`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#non-admin-user-management),
[`SET PASSWORD`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#general-admin-and-non-admin-user-management),
[`SHOW GRANTS`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#non-admin-user-management), and
[`SHOW USERS`](/{{< latest "influxdb" "v1" >}}/administration/authentication_and_authorization/#user-management-commands)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards, Users and Roles on the Admin page

#### DropData
Permission to drop data, in particular [series](/{{< latest "influxdb" "v1" >}}/concepts/glossary/#series) and [measurements](/{{< latest "influxdb" "v1" >}}/concepts/glossary/#measurement).

**Relevant InfluxQL queries**:
[`DROP SERIES`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#drop-series-from-the-index-with-drop-series),
[`DELETE`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#delete-series-with-delete), and
[`DROP MEASUREMENT`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#delete-measurements-with-drop-measurement)

**Pages in Chronograf that require this permission**: NA

#### DropDatabase
Permission to drop databases and retention policies.

**Relevant InfluxQL queries**:
[`DROP DATABASE`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#delete-a-database-with-drop-database) and
[`DROP RETENTION POLICY`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#delete-retention-policies-with-drop-retention-policy)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards, Databases on the Admin page

#### KapacitorAPI
Permission to access the API for InfluxKapacitor Enterprise.
This does not include configuration-related API calls.

**Pages in Chronograf that require this permission**: NA

#### KapacitorConfigAPI
Permission to access the configuration-related API calls for InfluxKapacitor Enterprise.

**Pages in Chronograf that require this permission**: NA

#### ManageContinuousQuery
Permission to create, drop, and view [continuous queries](/{{< latest "influxdb" "v1" >}}/concepts/glossary/#continuous-query-cq).

**Relevant InfluxQL queries**:
[`CreateContinuousQueryStatement`](/{{< latest "influxdb" "v1" >}}/query_language/continuous_queries/),
[`DropContinuousQueryStatement`](/{{< latest "influxdb" "v1" >}}/query_language/continuous_queries/#deleting-continuous-queries), and
[`ShowContinuousQueriesStatement`](/{{< latest "influxdb" "v1" >}}/query_language/continuous_queries/#listing-continuous-queries)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards

#### ManageQuery
Permission to view and kill queries.

**Relevant InfluxQL queries**:
[`SHOW QUERIES`](/{{< latest "influxdb" "v1" >}}/troubleshooting/query_management/#list-currently-running-queries-with-show-queries) and
[`KILL QUERY`](/{{< latest "influxdb" "v1" >}}/troubleshooting/query_management/#stop-currently-running-queries-with-kill-query)

**Pages in Chronograf that require this permission**: Queries on the Admin page

#### ManageShard
Permission to copy, delete, and view [shards](/{{< latest "influxdb" "v1" >}}/concepts/glossary/#shard).

**Relevant InfluxQL queries**:
[`DropShardStatement`](/{{< latest "influxdb" "v1" >}}/query_language/manage-database/#delete-a-shard-with-drop-shard),
[`ShowShardGroupsStatement`](/{{< latest "influxdb" "v1" >}}/query_language/spec/#show-shard-groups), and
[`ShowShardsStatement`](/{{< latest "influxdb" "v1" >}}/query_language/spec/#show-shards)

**Pages in Chronograf that require this permission**: NA

#### ManageSubscription
Permission to create, drop, and view [subscriptions](/{{< latest "influxdb" "v1" >}}/concepts/glossary/#subscription).

**Relevant InfluxQL queries**:
[`CREATE SUBSCRIPTION`](/{{< latest "influxdb" "v1" >}}/query_language/spec/#create-subscription),
[`DROP SUBSCRIPTION`](/{{< latest "influxdb" "v1" >}}/query_language/spec/#drop-subscription), and
[`SHOW SUBSCRIPTIONS`](/{{< latest "influxdb" "v1" >}}/query_language/spec/#show-subscriptions)

**Pages in Chronograf that require this permission**: Alerting

#### Monitor
Permission to view cluster statistics and diagnostics.

**Relevant InfluxQL queries**:
[`SHOW DIAGNOSTICS`](/{{< latest "influxdb" "v1" >}}/administration/server_monitoring/#show-diagnostics) and
[`SHOW STATS`](/{{< latest "influxdb" "v1" >}}/administration/server_monitoring/#show-stats)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards

#### ReadData
Permission to read data.

**Relevant InfluxQL queries**:
[`SHOW FIELD KEYS`](/{{< latest "influxdb" "v1" >}}/query_language/explore-schema/#show-field-keys),
[`SHOW MEASUREMENTS`](/{{< latest "influxdb" "v1" >}}/query_language/explore-schema/#show-measurements),
[`SHOW SERIES`](/{{< latest "influxdb" "v1" >}}/query_language/explore-schema/#show-series),
[`SHOW TAG KEYS`](/{{< latest "influxdb" "v1" >}}/query_language/explore-schema/#show-tag-keys),
[`SHOW TAG VALUES`](/{{< latest "influxdb" "v1" >}}/query_language/explore-schema/#show-tag-values), and
[`SHOW RETENTION POLICIES`](/{{< latest "influxdb" "v1" >}}/query_language/explore-schema/#show-retention-policies)

**Pages in Chronograf that require this permission**: Admin, Alerting, Dashboards, Data Explorer, Host List

#### WriteData
Permission to write data.

**Relevant InfluxQL queries**: NA

**Pages in Chronograf that require this permission**: NA

### Roles
Roles are groups of permissions. Assign roles to one or more users.

To create a role, do the following:

1. Click **{{< icon "crown" "v2" >}} Admin** in the left navigation bar. You will be taken to the `InfluxDB Admin` page. 
2. Select the **Roles** tab.
3. Click **+ Create Role**. 
4. Give the role a name.
5. Click **Create**.
6. Assign users to the role in the `Users` section
7. Add permissions to the role in the `Permissions` section.  You will see a list of databases and all permissions for that database. Select
the permissions you want for the role.
8. Click **Apply Changes**. 

The role with all permissions will appear in the list.



