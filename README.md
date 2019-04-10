# knative-eventing-operator

If you don't already have it, install
[operator-sdk](https://github.com/operator-framework/operator-sdk/)

Before doing anything else, grab your dependencies:

    $ dep ensure

## Create a release

Update [the resource files](deploy/resources/) with the proper
[quay.io](https://quay.io/organization/openshift-knative) images,
verify that [version.go](version/version.go) is correct, and then run
these commands:

    $ VERSION="vX.Y.Z"      # ensure this matches version/version.go!
    $ operator-sdk build quay.io/openshift-knative/knative-eventing-operator:$VERSION
    $ docker push quay.io/openshift-knative/knative-eventing-operator:$VERSION
    $ git tag $VERSION
    $ git push --tags
