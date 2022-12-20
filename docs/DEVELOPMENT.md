# Introduction
These instructions should help you to build a custom version of the operator with your
changes

## Prerequisites
- Golang (1.18.x)
- Operator SDK version (1.23.x+)
- podman and podman-docker or docker
- Access to Kubernetes cluster (1.24+)
- Container registry to store images


## Set Environment Variables
```
export QUAY_USER=<userid>
export IMG=quay.io/${QUAY_USER}/cc-operator
```

## Viewing available Make targets
```
make help
```

## Building Operator image
```
make docker-build
make docker-push
```

## Deploying Operator

Ensure KUBECONFIG points to target Kubernetes cluster
```
make install && make deploy
```

## Create Custom Resource (CR)
```
kubectl create -f config/samples/ccruntime.yaml
```

## Uninstalling Operator

Ensure KUBECONFIG points to target Kubernetes cluster
```
make uninstall && make undeploy
```

## Using Kind Kubernetes cluster

You can use a [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/) cluster running on non-TEE hardware 
for development purposes.

Kind version `v0.16.0` have been successfully tested on the following Linux distros.
- `CentOS Stream 8`
- `RHEL9`
- `Ubuntu 20.04`
- `Ubuntu 22.04`

>**Note**: Only `kata-clh` runtimeclass works with Kind cluster.

# Contributing

Your contributions to this project should sticky with the [contributing guidelines](https://github.com/confidential-containers/community/blob/main/CONTRIBUTING.md) of the Confidential Containers organization.

## Continuous Integration (CI)

Your pull request (PR) is going to be tested on our CI and all the required checkers must pass.

Currently it is be executed two categories of checkers:

 * [Github actions](https://docs.github.com/en/actions) for build and testing, and static checking
 * [Jenkins](https://www.jenkins.io) jobs for end-to-end (e2e) testing

### Github actions

Some checkers can run as [Github Actions](https://docs.github.com/en/actions) because they do not require special
environments. In general they will build and run unit/integration tests as well as execute static analysis tools.

Those checker will be automatically triggered upon PR opening if you are a member in the Github organization. Otherwise,
you should ask a member to approve the execution of the checkers on your behalf.

### End-to-end tests

The end-to-end (e2e) jobs run mostly on the Kata Containers's [Jenkins instance](http://jenkins.katacontainers.io/) and they aim to
test the operator as it would be deployed on close-to-production environments by our end users.

Unlike the Github actions, they are not triggered automatically even if the PR came from a member. The reason being to allow a human to review it first as a security measurement, apart from that most of them require scarce hardware. Thus,
to run the jobs a member on the Github organization should make the **/test** comment on your PR.

The following table shows the jobs currently executed and the commands to trigger them individually if needed:

| Github checker | Environment | Runtimeclass | Trigger command (regex) | Jenkins job |
|---------|-------------|--------------|------------------|-----|
| jenkins-ci-clh-tdx-x86_64                                  | CentOS 8 x86_64 (Intel TDX) | kata-clh-tdx | `/test` | N/A |
| jenkins-ci-tdx-x86_64                                      | CentOS 8 x86_64 (Intel TDX) | kata-clh | `/test` | N/A |
| tests-e2e-ubuntu-20.04-x86_64-containerd_kata-qemu         | Ubuntu 20.04 x86_64 | kata-qemu | `/(re)?test(-kata-qemu)?(-ubuntu)?` | [confidential-containers-operator-main-ubuntu-20.04-x86_64-containerd_kata-qemu-PR](http://jenkins.katacontainers.io/job/confidential-containers-operator-main-ubuntu-20.04-x86_64-containerd_kata-qemu-PR) |
| tests-e2e-ubuntu-20.04-x86_64-containerd_kata-clh          | Ubuntu 20.04 x86_64 | kata-clh | `/(re)?test(-kata-clh)?(-ubuntu)?` |[confidential-containers-operator-main-ubuntu-20.04-x86_64-containerd_kata-clh-PR](http://jenkins.katacontainers.io/job/confidential-containers-operator-main-ubuntu-20.04-x86_64-containerd_kata-clh-PR) |
| tests-e2e-ubuntu-20.04_sev-x86_64-containerd_kata-qemu-sev | Ubuntu 20.04 x86_64 (AMD SEV) | kata-qemu-sev | `/(re)?test(-kata-qemu-sev)?(-ubuntu)?` | [confidential-containers-operator-main-ubuntu-20.04_sev-x86_64-containerd_kata-qemu-sev-PR](http://jenkins.katacontainers.io/job/confidential-containers-operator-main-ubuntu-20.04_sev-x86_64-containerd_kata-qemu-sev-PR) |
| cc-operator-e2e-ubuntu-20.04-s390x-containerd_kata-qemu    | Ubuntu 20.04 s390x  | kata-qemu | `/(re)?test(-s390x)?(-kata-qemu)?(-ubuntu)?`| [confidential-containers-operator-main-ubuntu-20.04-s390x-containerd_kata-qemu-PR](http://jenkins.katacontainers.io/job/confidential-containers-operator-main-ubuntu-20.04-s390x-containerd_kata-qemu-PR) |

>**Note**: *jenkins-ci-clh-tdx-x86_64* and *jenkins-ci-tdx-x86_64* run behind a firewall at Intel's infraestructure. The community does not has access to their console logs, so in case of failure, please contact Arron Wang (@arronwy) or any other Intel member for assistance.
