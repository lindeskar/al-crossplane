# GCP provider

1. Export GCP project name:

   ```shell
   read -r GCP_PROJECT && export GCP_PROJECT
   ```

1. Add GCP provider:

   ```shell
   cat <<EOF | kubectl -n crossplane-system apply -f -
   apiVersion: pkg.crossplane.io/v1
   kind: Provider
   metadata:
     name: provider-gcp
   spec:
     package: xpkg.upbound.io/upbound/provider-gcp:v0.29.0
   EOF
   ```

1. Download a service account key file (JSON) and store the file in `$HOME/Downloads/gcp-credentials.json`.

1. Create secret from GCP key file:

   ```shell
   kubectl -n crossplane-system create secret generic gcp-secret-$GCP_PROJECT --from-file=creds=$HOME/Downloads/gcp-credentials.json
   ```

1. Configure GCP provider:

   ```shell
   cat <<EOF | kubectl -n crossplane-system apply -f -
   apiVersion: gcp.upbound.io/v1beta1
   kind: ProviderConfig
   metadata:
     name: gcp-$GCP_PROJECT
   spec:
     projectID: $GCP_PROJECT
     credentials:
       source: Secret
       secretRef:
         namespace: crossplane-system
         name: gcp-secret-$GCP_PROJECT
         key: creds
   EOF
   ```

1. Create test bucket:

   ```shell
   cat <<EOF | kubectl -n crossplane-system create -f -
   apiVersion: storage.gcp.upbound.io/v1beta1
   kind: Bucket
   metadata:
     generateName: crossplane-bucket-
     labels:
       docs.crossplane.io/example: provider-gcp
   spec:
     forProvider:
       location: EU
     providerConfigRef:
       name: gcp-$GCP_PROJECT
   EOF
   ```
