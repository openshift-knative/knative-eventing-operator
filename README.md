# knative-eventing-operator

If you don't already have it, install
[operator-sdk](https://github.com/operator-framework/operator-sdk/)

Before doing anything else, grab your dependencies:

    $ dep ensure -v

## Create a Release

Verify that [version.go](version/version.go) matches the contents of
[deploy/resources](deploy/resources/) and then run the following to
build and push an image for the operator to
[quay.io](https://quay.io/repository/openshift-knative/knative-eventing-operator).

    ./hack/release.sh

## Create a CatalogSource for [OLM](https://github.com/operator-framework/operator-lifecycle-manager)

The OLM requires special manifests that the operator-sdk can help
generate. First we extract the CRD's from the upstream manifest[s]
contained within [deploy/resources](deploy/resources/):

    ./hack/extract-crds.sh

Update [deploy/role.yaml](deploy/role.yaml/) to match the RBAC
policies in the upstream manifest[s].

And then generate a basic `ClusterServiceVersion` passing in a version
that corresponds to those manifests:

    operator-sdk olm-catalog gen-csv --csv-version $VERSION

Some post-editing of the file it generates is required:

* Add fields to address any warnings it reports
* Add `description` and `displayName` fields for all owned CRD's
* Add `args: ["--olm", "--install"]` to the operator's container spec.
* Add a `replaces` field referencing the previous CSV

With the above in place, the [catalog.sh](hack/catalog.sh) script
should yield a valid `CatalogSource` for you to publish.

### Using OLM on Minikube

You can test the operator using
[minikube](https://kubernetes.io/docs/setup/minikube/) after
installing OLM on it:

    minikube start
    kubectl apply -f https://github.com/operator-framework/operator-lifecycle-manager/releases/download/0.9.0/olm.yaml

Once all the pods in the `olm` namespace are running, install the
operator like so:
    
    ./hack/catalog.sh | kubectl apply -n olm -f -

Interacting with OLM is possible using `kubectl` but the OKD console
is "friendlier". If you have docker installed, use [this
script](https://github.com/operator-framework/operator-lifecycle-manager/blob/master/scripts/run_console_local.sh)
to fire it up on <http://localhost:9000>.
