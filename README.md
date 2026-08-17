# Power Platform GCC Azure custom connectors

These are starter custom connector definitions for Azure services whose Microsoft-managed Power Platform connectors are unavailable in GCC:

- Azure OpenAI
- Azure AI Foundry Agent Service
- Azure AI Content Understanding
- Azure Log Analytics (commercial API for GCC tenants)

## Important GCC boundary warning

These definitions do **not** make a service GCC-authorized, create an Azure Government endpoint, or change where data is processed. Before importing one into a GCC environment, confirm that the target Azure resource, endpoint, model, and tenant authorization are approved for the workload. Do not point a GCC connector at a commercial resource for regulated, CUI, ITAR, CJIS, or other controlled data.

The Microsoft-managed connector availability pages currently list all three services as unavailable in Power Apps and Power Automate GCC. These custom connectors are therefore integration starting points, not Microsoft-supported substitutes.

## Import

1. Open the GCC Power Apps or Power Automate maker portal.
2. Create a custom connector from the matching `apiDefinition.swagger.json`.
3. Review the generated connection parameters and security settings.
4. Set the API version and endpoint values appropriate for the approved Azure resource.
5. Test with non-sensitive data before deployment.

The `apiProperties.json` files provide branding metadata only. Custom connectors are environment-specific and must be imported separately into each environment.

## Connector-specific notes

### Azure OpenAI

Uses an Azure OpenAI deployment endpoint and `api-key` authentication. The connector exposes chat completions and embeddings. Use only an endpoint approved for the environment.

### Azure AI Foundry Agent Service

Uses Microsoft Entra ID OAuth 2.0 against the US Government authority. The service must actually be provisioned and available in the target cloud; a connector cannot bypass Azure service-region restrictions.

### Azure AI Content Understanding

Uses an Azure AI Content Understanding endpoint and API-key authentication. The Microsoft-managed connector is not available in GCC, and the underlying service/region must be separately approved before use.

### Azure Log Analytics

The Log Analytics package is a GCC-usable custom connector that calls the **commercial** `api.loganalytics.azure.com` API. It is not an Azure Government endpoint. Do not use it for data that must remain inside an Azure Government, GCC High, DoD, CUI, ITAR, or CJIS boundary unless your security and compliance authority has approved that data path. Azure Government deployments require a separately verified government endpoint and service contract; do not substitute one cloud's host for the other.

See the package README for the required app registration, API permissions, OAuth configuration, and test query.

## References

- [Power Platform connector catalog](https://learn.microsoft.com/en-us/connectors/)
- [Azure OpenAI connector availability](https://learn.microsoft.com/en-us/connectors/azureopenai/)
- [Azure AI Foundry Agent Service connector availability](https://learn.microsoft.com/en-us/connectors/azureagentservice/)
- [Azure AI Content Understanding connector availability](https://learn.microsoft.com/en-us/connectors/contentunderstanding/)
- [Custom connectors overview](https://learn.microsoft.com/en-us/connectors/custom-connectors/)
