# Version 3.0.1

## Changes

* Kubeval does not currently evaluate ```cloud.google.com``` schemas. This causes non K8s native manifests to fail deployment. Added some ignore logic to check manifests for ```apiVersion: cloud.google.com``` and not run kubeval on those files.

# Version 3.0.0

## Backwards Incompatible Changes
* kubectl - Due to a bug in kubectl, you'll need to delete the gitlab-registry secret in your namespace before you deploy. Make sure to do it right before you do a deploy to make it non-impactful. To do this, run ```kubectl delete secret gitlab-registry -n=yournamespace```.

* Deploy Token Usage - Previous versions used an actual GitLab username because GitLab didn't have persistent deploy tokens until recently. Now that this feature is in GitLab, we're going to stop using the shared credentials as this is much more secure. Create a deploy token at Settings->Repository->Deploy Tokens and make one named gitlab-deploy-token with read_registry access. As long as it's named gitlab-deploy-token, that's all you should have to do.

* Canary Usage - Delete your canary manifest templates. We will create them automatically now. Make sure track: stable is present as labels in your deployments.

## New Features
* DEPLOY - Canary manifests are now dynamically created in canary stages.
* DEPLOY - Automatically insert secrets as environment variables into Kubernetes manifest using {{SECRETS}} command in manifest templates. Make sure {{SECRETS}} is indented right, so it looks something like:
```
env:
  {{SECRETS}}
```
* DEPLOY - Deploy script now uses [kubeval](https://github.com/garethr/kubeval) to look for manifest errors before manifests are applied.
* DEPLOY - Deploy script now leaves a copy of the post-processed files in /tmp/kubernetes for easier debugging and artifact grabbing
* BUILD - Added BUILDARGS_FROM feature. Set BUILDARGS_FROM=production in your gitlab-ci stage, and it will make your GitLab secrets become build arguments for your Docker container. EXAMPLE: If you set BUILDARGS_FROM=production and have SECRET_VAR1 and PRODUCTION_VAR2 defined, it will automatically create build arguments named var1 and var2. *It also turns on KDH_INSERT_ARGS and will dynamically insert build arguments into your Dockerfile before build.* This is for applications that require all the environment variables to exist at buildtime as a sanity check.
* BUILD - Set KDH_INSERT_ARGS=true as a variable in your gitlab ci build stage and k8s-deploy-helper will automatically insert the ARG statements into your Dockerfile.

## Bug Fixes
* BUILD - BUILDARGS_ can now handle spaces correctly

## Changes
* Deployment events will not fire until after a deployment has been registered as rolled out successfully.

# Version 2.0.3

Docker Image: quay.io/lifechurch/k8s-deploy-helper:2.0.3

## What's New?
* Add ability to pass an variable to destroy in order to use destroy for namespaces with multiple apps

# Version 2.0.2

Docker Image: quay.io/lifechurch/k8s-deploy-helper:2.0.2

## What's New?
* Add DataDog [deployment event](https://github.com/lifechurch/k8s-deploy-helper#deploy-events) support

# Version 2.0.1

Docker Image: quay.io/lifechurch/k8s-deploy-helper:2.0.1

## What's New?
* Add KDH_SKIP_LATEST flag to skip pulling the latest docker image and using it as cache-from.

# Version 2.0.0

Docker Image: quay.io/lifechurch/k8s-deploy-helper:2.0.0

## What's New?
* Canary Deploys 

# Version 1.0.0

Docker Image: quay.io/lifechurch/k8s-deploy-helper:1.0.0

## What's New?
* Initial release
