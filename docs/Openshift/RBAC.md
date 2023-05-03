# Role-based Access Control (RBAC)

Role-based access control (RBAC) is a technique for managing access to resources in a computer system. In Red Hat OpenShift, RBAC determines if a user can perform certain actions within the cluster or project. There are two types of roles that can be used depending on the user's level of responsibility: cluster and local.

#### RBAC objects

* **Rule**  Use to allowed action for object or grp of objects
* **Role**  Set of rule assigned to user or grp. User or grp can be assign to multiple role
* **Binding**  Assign role to user/grp. 

#### RBAC scope

* **Cluster Role** User or group has cluster level role
* **Local Role** User or group has project/namespace level role  

## Manage RBAC

For cluster role we need to user __oc adm policy__ and for local role we can user __oc policy__. <br/>

To add a cluster role to a user, use the add-cluster-role-to-user subcommand:
```bash
oc adm policy add-cluster-role-to-user cluster-role username
```

For example, to change a regular user to a cluster administrator, use the following command:

```bash
oc adm policy add-cluster-role-to-user cluster-admin username
```

To remove a cluster role from a user, use the remove-cluster-role-from-user subcommand:

```bash
oc adm policy remove-cluster-role-from-user cluster-role username
```

For example, to change a cluster administrator to a regular user, use the following command:

```bash
oc adm policy remove-cluster-role-from-user cluster-admin username
```

Rules are defined by an action and a resource. For example, the create user rule is part of the cluster-admin role.

You can use the oc adm policy who-can command to determine if a user can execute an action on a resource. For example:

```bash 
oc adm policy who-can delete user
```

## Default roles

* **admin**  Project admin.
* **basic-user** Project read access.
* **cluster-admin** Cluster admin with access to all resource of cluster.
* **cluster-status** User you can get cluster status information.
* **edit** Users with this role can create, change, and delete common application resources from the project, such as services and deployments. These users cannot act on management resources such as limit ranges and quotas, and cannot manage access permissions to the project.
* **view** User can only view resources.
* **self-provisioner** User can create new project. This is a cluster role.


## User Types

* **Local Users**<br/>
Regular user that can login via cli or dashboard.
* **System Users**<br/>
System users are defined automatically when the infrastructure is defined. System users include a cluster administrator (with access to everything), a per-node user, users for routers and registries, and various others. An anonymous system user is used by default for unauthenticated requests.<br/>
Examples of system users include: system:admin, system:openshift-registry, and system:node:node1.example.com.


* **Service Accounts**<br/>
These are special system users associated with projects. Some service account users are created automatically when the project is first created. Project administrators can create more for the purpose of defining access to the contents of each project. Service accounts are often used to give extra privileges to pods or deployments. Service accounts are represented with the ServiceAccount object.<br/>
Examples of service account users include system:serviceaccount:default:deployer and system:serviceaccount:foo:builder.



## Sample commands:

```bash 
oc get clusterrolebinding -o wide

oc describe clusterrolebindings self-provisioners

oc adm groups new <grp name>

oc adm groups new-users <grp name> <user name>

oc get groups -o wide 

oc get rolebindings -o wide

oc adm policy add-cluster-role-to-group  --rolebinding-name self-provisioners  self-provisioner system:authenticated:oauth
```