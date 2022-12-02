# Solution Explorer Operator

Openshift operator that handles solution explorer deployments.


|          | Project Info                |
| -------- | --------------------------- |
| License: | Apache License, Version 2.0 |

## Installation

Here are some instructions to get the operator up and running on OpenShift:

1. Clone this repository to your host machine:

   ```sh
   git clone https://github.com/RedHat-Middleware-Workshops/tutorial-web-app-operator.git
   ```

2.  Run the kubectl:

   ```sh
   #create resources (including the solution-explorer namespace) with kustomize
   kubectl apply -k deploy
   ```

## Deploying a Solution Explorer Instance

To deploy a basic instance of Solution Explorer you will need to create a `Webapp` instance using a manifest like this:

```yaml
apiVersion: "integreatly.org/v1alpha1"
kind: "WebApp"
metadata:
  name: "tutorial-web-app-operator"
  labels:
    app: "tutorial-web-app"
spec:
  app_label: "tutorial-web-app"
  template:
    path: "/home/tutorial-web-app-operator/deploy/template/tutorial-web-app.yml"
    parameters:
      OPENSHIFT_OAUTHCLIENT_ID: "tutorial-web-app"
      OPENSHIFT_HOST: ""
      SSO_ROUTE: ""
```

If you are using OpenShift you will need to add the information of your cluster in the specified parameters.

## Development
Following sections cover the basics on how to get started with development of the operator.

### Building

```sh
#builds image: quay.io/redhatintegration/tutorial-web-app-operator:latest
make image/build

#custom image params: registry.io/myusername/image-name:dev
make image/build REG=custom-registry.io ORG=myusername IMAGE=image-name TAG=dev
```

### Running tests

```
make test/unit
```

### Updating Web App Image Version
Update web app image version in the following files:
* Update [WebAppImage](pkg/handlers/webhandler.go) `WebAppImage = "quay.io/redhatintegration/tutorial-web-app:<version>"`
  * Image version that the deployment config gets reconciled to (**Must be updated as would override the version used by the template**)
* Update [tutorial-web-app.yml template](deploy/template/tutorial-web-app.yml) `image: quay.io/redhatintegration/tutorial-web-app:<version>`
  * Image version that gets deployed on initial processing of the template file

### Release

Update operator version files:

* Bump [operator version](version/version.go) 
```Version = "<version>"```
* Bump [makefile TAG](Makefile)
```TAG=<version>```
* Bump [operator image version](deploy/operator.yaml)
```image: quay.io/redhatintegration/tutorial-web-app-operator:v<version>```

Commit changes and open a pull request to master.

When the PR is merged, create a new Github release from master for v<version> e.g. v0.0.10.
This will trigger a ci build of the tag, and a new image pushed to quay.io with that tag.
