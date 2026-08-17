# Azure AI Foundry Agent Service custom connector

This is an Entra ID OAuth starter definition targeting an Azure Government-style endpoint.

Microsoft currently lists the managed connector as unavailable in GCC. Confirm the service endpoint, API version, project API, and Azure Government availability before importing. If the service is not available in the target cloud, this connector cannot make it available.

The custom connector OAuth client registration must be completed in the GCC tenant. Do not commit client secrets.
