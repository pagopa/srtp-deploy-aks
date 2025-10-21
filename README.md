# SRTP Deploy AKS

This repository is used to deploy all SRTP services on AKS using **Helm** and **ArgoCD**.  
Each environment has its own configuration under the `helm` directory.

## Environments

We manage three environments:

- DEV
- UAT
- PROD

Each one has its own folder under `helm/`:

```
helm/
├── dev/
├── uat/
└── prod/
```

## How to add a new service

1. **Create a directory** under the environment where you want to deploy the service.  
   Example:

   ```
   helm/dev/top/rtp-myservice
   ```

2. **Add the `Chart.yaml` file** inside that directory:

   ```yaml
   apiVersion: v2
   name: rtp-myservice
   description: My SRTP microservice
   type: application
   version: 1.0.0
   appVersion: 1.0.0
   dependencies:
     - name: microservice-chart
       version: 7.1.1
       repository: "https://pagopa.github.io/aks-microservice-chart-blueprint"
   ```

3. **Build Helm dependencies**

   Run inside the service directory:

   ```bash
   helm dependency update
   ```

   This command generates a `Chart.lock` file and a `charts` folder.  
   The `charts` folder can be deleted, since it’s ignored by `.gitignore`.

4. **Add the values files**

    - In `helm/_global/`, create a file named after the microservice:  
      `service-name.yaml` → shared global configuration
    - Inside the service directory, create the environment-specific file:  
      `values.yaml` → environment-specific configuration (for example `dev`, `uat`, or `prod`).


## Deployment

Deployments are handled automatically through **Azure DevOps pipelines** integrated with **ArgoCD**.  

When running the pipeline:

- Choose the environment (`dev`, `uat`, `prod`)
- Set the ArgoCD target branch (`main` or your feature branch`)
- Select the application(s) to deploy

ArgoCD will fetch the corresponding Helm chart and apply it to the target AKS cluster.
