# Identify Provider

## HTPasswd

#### Create htpasswd file 

**httpd-tools** packages provides htpasswd. 

```bash
htpasswd <flags> <path to file> <user> <passwd>

Flags:

-c Create new htpasswd file, Only need to use it once.
-b Dont prompt for the passwd take it from cmd line
-B Force bcrypt encryption to store passwd
-m Use md5 to store passwd (default)
-D Delete user from the file
-v verify passwd from user 
```

Examples:

```bash
htpasswd -c -B -b /tmp/htpasswd admin admin_pass
htpasswd -B -b /tmp/htpasswd dev dev_pass
htpasswd -b /tmp/htpasswd operator ops_pass
```


#### Upload htpasswd file as secret

```bash
oc create secret generic localuser-srt --from-file htpasswd=<path to htpasswd> -n openshift-config
```

#### Modify OAuth resource and add identity provider

```bash
oc get oauth cluster -o yaml > oauth.yaml
```

Add below below section in oauth spec

```yaml
...
spec:
  identityProviders :
    - name: localuser
      type: HTPassed                <<--
      mappingMethod: claim          <<--
      htpasswd:
        fileData:
          name: localuser-srt       <<-- secret name
```

update the resource defination

```bash
oc replace -f oauth.yaml 
```

Moniter if pod in openshift-authentication to read new configuration

```bash
oc get pod -n openshift-authentication -w
```

Verify if you are able to login

```bash
oc login -u admin -p admin_pass
oc whoami
```

#### Set admin role to users

Set cluster-admin role to admin user

```bash
oc adm policy add-cluster-role-to-user  cluster-admin admin
```

List current users

```bash
oc get users
```

List current identifies

```bash
oc get identity
```

#### Extract htpassd to add/remove user

Extract secret file

```bash
oc extract secret/localuser-srt -n openshift-config --to <dest path> --confirm
```

Add manager user and remove operator user

```bash
htpasswd -b -B /tmp/htpasswd manager mgr_pass
htpassed -D /tmp/htpasswd operator 
```

Upload the file back

```bash
oc set data secret/localuser-srt --from-file htpasswd=<path to htpasswd> -n openshift-config
```

Delete identity and user resource 

```bash
oc delete identity "localuser:manager"
oc delete user "localuser:manager"
```