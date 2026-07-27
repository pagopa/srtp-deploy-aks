#### List of Changes

- Added and aligned Kubernetes `hostAliases` configuration for SRTP Helm values across `dev`, `uat`, and `prod` environments.
- Configured environment-specific aliases for RTP API endpoints:
  - `api-rtp.dev.cstar.pagopa.it` -> `20.71.68.43`
  - `api-rtp.uat.cstar.pagopa.it` -> `20.31.11.237`
  - `api-rtp.cstar.pagopa.it` -> `20.103.162.3`
- Configured environment-specific aliases for Keycloak / MC Shared endpoints where used by the services:
  - `api-mcshared.dev.cstar.pagopa.it` -> `20.71.68.43`
  - `api-mcshared.uat.cstar.pagopa.it` -> `20.31.11.237`
  - `api-mcshared.cstar.pagopa.it` -> `20.103.162.3`
- Configured Cosmos DB Mongo endpoint aliases for services that use the Cosmos connection string:
  - `cstar-d-itn-srtp-cosmos-account.mongo.cosmos.azure.com` -> `4.232.26.70`
  - `cstar-u-itn-srtp-cosmos-account.mongo.cosmos.azure.com` -> `4.232.42.68`
  - `cstar-p-itn-srtp-cosmos-account.mongo.cosmos.azure.com` -> `10.20.0.4`
- Added missing `deployment.hostAliases` sections where needed, including `rtp-payees` for RTP API resolution.
- Updated `rtp-sender-v2` non-production values to include active host alias configuration.

Affected services:

- `rtp-activator`
- `rtp-consumer`
- `rtp-payees`
- `rtp-sender`
- `rtp-sender-v2` in `dev` and `uat`

#### Motivation and Context

The SRTP services need deterministic hostname resolution from within AKS pods for upstream RTP APIs, Keycloak / MC Shared endpoints, and Cosmos DB Mongo endpoints.

This change pins the required hostnames to the expected environment-specific IP addresses through Kubernetes `hostAliases`, ensuring that the applications continue to use the original hostnames from their configuration and connection strings while resolving them to the required network endpoints inside the pods.

For Cosmos DB, the applications still connect using the Mongo connection string hostname. The `hostAliases` entry only overrides pod-level DNS resolution and does not change the connection string format.

#### How Has This Been Tested?

- Verified the updated YAML files through IDE validation: no errors reported.
- Checked the configured hostnames and IPs against the provided DNS resolution outputs for `dev` and `uat`.
- Confirmed that `dev` and `uat` Keycloak aliases no longer use the production IP.
- Confirmed that `dev` and `uat` Cosmos aliases use the resolved Cosmos endpoint IPs.

Runtime deployment validation was not performed in this workspace. Suggested follow-up validation:

- Render the affected Helm charts and verify that the generated Kubernetes `Deployment` manifests include the expected `hostAliases`.
- Deploy to the target environment and verify pod-level hostname resolution for RTP API, Keycloak, and Cosmos DB endpoints.

#### Screenshots (if appropriate):

N/A

#### Types of changes

- [x] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)

#### Checklist:

- [ ] My change requires a change to the documentation.
- [ ] I have updated the documentation accordingly.

