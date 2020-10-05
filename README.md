# Deprecation notice!
Use the repo from OpsMx account instead
https://github.com/OpsMx/install-oes3.0

# OES3.0 installation instructions

## Instructions to install the entire application

Run below commands inside k8s directory

### Install
`
kubectl create -R -f . -n <namespace>
`

### Uninstall
`
kubectl delete -R -f . -n <namespace>
`

## Instructions to update specific service

Let's consider I already have all the services in a namespace and I just want to update any particular service that I've been working on, just follow below Instructions

For instance, I'm a developer who's working on oes-gate service, first I'll build the image using a particular jenkins job and get the new image.

`
cd oes-gate
`

### Update deployment of a particular service

Edit the image section of deployment i.e oes-gate-deployment.yaml

`
kubectl apply -f oes-gate-deployment.yaml
`

### Update configuration of a particular service
If I've updated my oes-gate configuration files, I'd have to update my config map i.e oes-gate-cm.yaml, then run below command

`
kubectl apply -f oes-gate-cm.yaml
`

Configuration file doesn't get updated in a pod, unless pod restarts after the updation of config map. So, ensure to restart the pod.

> **Tip**: Be very careful with indentation, yaml is very sensitive`

## Additional Info
- *prerequisites* directory has image pull secrets to fetch images from opsmx11 registry & flame(docker.flame.org/opsmx) registry. *prerequisites* directory also has an rbac yaml that gives necessary access to oes init container
- oes-ui, oes-gate, sapor are automatically configured using an init container
- init container fetches the LoadBalancer IP of oes-gate and substitutes in app-config.json file of oes-ui
- init container fetches the LoadBalancer IP of oes-ui and configure CORS regex section of gate.yaml
- init container checks if spin-deck service is installed in the same namespace, if yes it will fetch the LoadBalancer IP of spinnaker Gate and configures it in sapor
- Currently, platform service comes up before database when entire application is installed, and platform service is failing to connect to DB and ends up in failed state. Suggested workaround is to restart platform service after DB is up and running. One way to restart pod is by killing it. Anyway, an init container will be added to platform pod so that it will wait till DB is up and running.
