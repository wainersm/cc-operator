# Introduction

Our releases are published on the [OperatorHub.io](https://operatorhub.io). Find more information on [our landing page](https://operatorhub.io/operator/cc-operator) at the Operator Hub.

# Publishing a new operator version

First of all, you should have a fork of the Operator Hub [community-operators repository](https://github.com/k8s-operatorhub/community-operators) cloned in your local host.

>Note: on the examples below let's suppose that `TARGET_RELEASE="0.7.0"`

Follow the steps:

1. Bump the `VERSION` variable in [Makefile](../Makefile) to `${TARGET_RELEASE}`. For example, `VERSION ?= 0.7.0`

2. Re-generate the [bundle](../bundle/):
   ```shell
   make bundle IMG=quay.io/confidential-containers/operator:v${TARGET_RELEASE}
   ```
3. Add the `replaces` tag under `spec` in the [base CSV(ClusterServiceVersion) file](../bundle/manifests/cc-operator.clusterserviceversion.yaml). It defines the version that this bundle should replace on an operator upgrade (see https://sdk.operatorframework.io/docs/olm-integration/generation/#upgrade-your-operator for further details). For example, the version 0.7.0 replaces 0.5.0 so:
   ```
   diff --git a/bundle/manifests/cc-operator.clusterserviceversion.yaml b/bundle/manifests/cc-operator.clusterserviceversion.yaml
   index ea81fd6..d8e6df5 100644
   --- a/bundle/manifests/cc-operator.clusterserviceversion.yaml
   +++ b/bundle/manifests/cc-operator.clusterserviceversion.yaml
   @@ -472,3 +472,4 @@ spec:
        name: Confidential Containers Community
        url: https://github.com/confidential-containers
      version: 0.7.0
   +  replaces: cc-operator.v0.5.0
   ```

4. Copy the bundle directory to the community-operators repository directory. On the example below I got the community-operators repository cloned to `../../../github.com/k8s-operatorhub/community-operators`: 
   ```shell
   cp -r bundle ../../../github.com/k8s-operatorhub/community-operators/operators/cc-operator/${TARGET_RELEASE}
   ```

5. Prepare a commit and push to your tree
   ```
   cd ../../../github.com/k8s-operatorhub/community-operators/operators/cc-operator/${TARGET_RELEASE}
   git checkout -b "new_${TARGET_RELEASE}"
   git add .
   git commit -s -m "operator cc-operator (${TARGET_RELEASE})"
   git push my-fork "new_${TARGET_RELEASE}"
   ```

6. Open a pull request to update the community-operators repository
   * In case the CI fails you will need to fix and re-do this process from step 2