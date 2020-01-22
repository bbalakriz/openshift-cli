Create config map and set it to a deployment config using a volume
===================================================================

**1. Create a configmap using a properties file:**
```sh
oc create configmap bc-app-users-cfgmap --from-file=/home/bbalasub/Desktop/application-users.properties
```

**2. Mount the properties file from the config map to a specific location on the container so that it overrides the default properties file:**

```sh
oc set volume dc/rhdm-kieserver-kieserver --add --name=bc-app-users-vol --mount-path /opt/eap/standalone/configuration/application-users.properties --sub-path application-users.properties --source='{"configMap":{"name":"bc-app-users-cfgmap","items":[{"key":"application-users.properties","path":"application-users.properties"}]}}'
```
