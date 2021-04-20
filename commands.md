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
**3. Adding a init container to a depoloyment config.**

```
spec:
  volumes:
  - name: php-src-volume               
    emptyDir: {}
  initContainers:
  - name: clone
    image: alpine/git:1.0.4           
    volumeMounts:
    - name: php-src-volume
      mountPath: /src              
    env:
    - name: GITLAB_USERNAME
      valueFrom:
        secretKeyRef:
          name: nick-gitlab-secret
          key: username
    - name: GITLAB_TOKEN
      valueFrom:
        secretKeyRef:
          name: nick-gitlab-secret
          key: password   
    command: ['/bin/sh', '-c']        
    args: ['git clone --branch sharpn-openshift https://$(GITLAB_USERNAME):$(GITLAB_TOKEN)@gitlab.its.deakin.edu.au/esolutions/software-engineering/covid-safe/api.git && mv api/* /src']


  containers:
  - name: <<NAME>>
    image: <<IMAGE_NAME>>
    volumeMounts:
    - name: php-src-volume
      mountPath: "/etc/nginx/..."
      readOnly: true



spec:
  containers:
  - image: nginx
    name: nginx-helloworld
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: www-data
  initContainers:
  - name: git-cloner
    image: alpine/git
    args:
        - clone
        - --single-branch
        - --
        - https://github.com/jmutai/hello-world-nginx.git
        - /data
    volumeMounts:
    - mountPath: /data
      name: www-data
  volumes:
  - name: www-data
    emptyDir: {}
  
  ```
