# Crossplane lab

## Install Crossplane

1. Install Helm chart:

   ```shell
   helm repo add crossplane-stable https://charts.crossplane.io/stable && helm repo update

   helm install crossplane crossplane-stable/crossplane --namespace crossplane-system --create-namespace
   ```

1. Install providers

   - [GCP](./gcp-provider.md)
