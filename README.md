# cnwan-operator-charts

Test reporitory for cnwan-operator's helm charts.
NOTE: this documentation is a TODO and is done in a low-effort style, as it is not the definitive one.

## Usage

[Helm](https://helm.sh) must be installed to use the charts.  Please refer to
Helm's [documentation](https://helm.sh/docs) to get started.

Once Helm has been set up correctly, add the repo as follows:

  helm repo add sunsince90 https://SunSince90.github.io/cnwan-operator-charts
  helm repo update

If you had already added this repo earlier, run `helm repo update` to retrieve
the latest versions of the packages.  You can then run `helm search repo
cnwan-operator` to see the charts.

To install the cnwan-operator chart:

    helm install my-cnwan-operator sunsince90/cnwan-operator

To uninstall the chart:

    helm delete my-cnwan-operator

At any point you can run

    helm show values sunsince90/cnwan-operator

to see what you can override with `--set <key>:<value>`

Note that you put all values in just one `--set`, but in these examples those values are on separate `--set` for sack of clarity.

## Install with Service Directory

if you install this on *GKE* then you can avoid setting google cloud data.
Otherwise you need to provide `operator.googleServiceDirectory.defaultRegion` and `operator.googleServiceDirectory.projectID` with `--set`.

    kubectl create ns cnwan-operator-system

    helm install my-operator sunsince90/cnwan-operator \
    --set operator.namespaceListPolicy=allowlist \
    --set operator.serviceAnnotations="{traffic-profile}" \
    --set operator.serviceRegistry=ServiceDirectory \
    --set-file operator.googleServiceDirectory.serviceAccount=path/to/gcloud-credentials.json \
    -n cnwan-operator-system

## Install with etcd

### With etcd dependency

This will install etcd on your cluster with the name `service-registry`.

    kubectl create ns cnwan-operator-system

    helm install my-operator sunsince90/cnwan-operator \
    --set operator.serviceAnnotations="{traffic-profile}" \
    --set operator.serviceRegistry=etcd \
    --set operator.etcd.install=true \
    -n cnwan-operator-system

    # Wait a few minutes until service registry gets into ready state
    watch kubectl get pods -n cnwan-operator-system


### Without etcd

If you already have etcd running somewhere -- remember to specify the correct endpoints:

    kubectl create ns cnwan-operator-system

    helm install my-operator sunsince90/cnwan-operator \
    --set operator.serviceAnnotations="{traffic-profile}" \
    --set operator.serviceRegistry=etcd \
    --set operator.etcd.endpoints:{"address:port} \
    -n cnwan-operator-system
