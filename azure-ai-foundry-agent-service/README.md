# Azure AI Foundry Agent Service custom connector

> **Important lifecycle warning:** Do not use this definition as evidence that the classic Azure AI Foundry/Agent Service API is current. Classic and preview API surfaces can be deprecated or changed. Verify the current Microsoft Foundry documentation, service availability, project API, API version, endpoint, and retirement notices immediately before import and again before production use.

This is an Entra ID OAuth starter definition targeting an Azure Government-style endpoint and project API. It is not a Microsoft-supported connector and makes no claim that the endpoint or API contract is currently available.

Microsoft currently lists the managed connector as unavailable in GCC. Confirm the service endpoint, API version, project API, and Azure Government availability before importing. If the service is not available in the target cloud, this connector cannot make it available.

## Requirements

- A Power Apps or Power Automate GCC environment.
- An Azure AI Foundry resource and project approved and provisioned in the target government cloud.
- The resource name, project name, API version, and agent/project API contract.
- A current, source-verified API contract; the version in the Swagger is a placeholder and must not be treated as a compatibility guarantee.
- An Entra ID application registration in the same government tenant, or an approved multi-tenant registration.
- A redirect URI supported by Power Platform custom connectors.
- An identity authorized to access the AI Foundry project and invoke agents.
- Permission to create custom connectors and use premium connectors.
- DLP and security approval for instructions, input data, tool calls, and agent responses.

## Connection user

This connector uses Microsoft Entra ID OAuth 2.0. The connection is made by a signed-in user, not by the client secret alone.

- Use a dedicated integration user or approved service identity for unattended production flows when supported by the target service.
- The connection user must be licensed and permitted to create/use connections in the GCC environment.
- Grant the connection user the least-privilege Azure AI Foundry project role required to invoke the agent.
- Confirm the user can obtain a token for the scope `https://cognitiveservices.azure.us/.default`.
- Do not share a personal interactive connection with production flows unless governance explicitly permits it.
- If the connection owner leaves the organization or loses access, dependent apps and flows may stop working. Document an owner and an operational backup.

## Important GCC boundary

The definition uses `login.microsoftonline.us` and `{resourceName}.services.ai.azure.us`. These values are provisional starter values. Verify the service, project API, tenant authority, API version, and data processing boundary with Microsoft documentation and the security authority before use. A custom connector cannot make an unavailable service GCC-authorized.

## How to configure

### 1. Register the Entra ID application

1. Sign in to the approved Azure Government portal and open **Microsoft Entra ID** > **App registrations**.
2. Select **New registration** and use a name such as `Power Platform GCC AI Foundry`.
3. Select the account type approved for the tenant.
4. Add the redirect URI required by the Power Platform custom connector OAuth configuration after the connector provides it.
5. Copy the **Application (client) ID** and **Directory (tenant) ID**.
6. Add only the API permissions required by the target AI Foundry project/API.
7. Grant admin consent when required by tenant policy.
8. Do not commit a client secret. If the target API requires one, store it only in the connector security configuration or approved secret store.

### 2. Import and configure the custom connector

1. Open the GCC maker portal:
   - Power Apps: `https://make.gov.powerapps.us`
   - Power Automate: `https://gov.flow.microsoft.us`
2. Open **Custom connectors** and choose **New custom connector** > **Import an OpenAPI file**.
3. Upload `apiDefinition.swagger.json`.
4. On **General**, set the approved resource name and confirm the project path.
5. On **Security**, select **OAuth 2.0** and configure Microsoft Entra ID using the government authorization and token URLs in the Swagger definition.
6. Enter the client ID, tenant ID, client secret if required, and scope `https://cognitiveservices.azure.us/.default`.
7. Save the connector, copy the generated redirect URI, and add it to the Entra app registration.
8. Update and save the connector again, then create a connection from the **Test** tab.
9. Sign in as the approved connection user and consent to the requested permissions.

### 3. Test the connection

1. Confirm the resource name and project name identify the approved project.
2. Call **Create agent response** with a minimal, non-sensitive request body supported by the project API.
3. Verify the response and audit record in the target environment.
4. Test again with the intended flow identity before production deployment.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| Redirect URI error | The URI generated by the custom connector was not added exactly to the Entra app registration. |
| `AADSTS` token error | The authority, tenant ID, client ID, scope, consent, or connection user is wrong. |
| `401 Unauthorized` | The token audience/scope is wrong or the connection user lacks access to the project. |
| `403 Forbidden` | The connection user is missing the required AI Foundry project or Azure role. |
| `404` | The resource name, project name, endpoint, API path, or API version is wrong. |
| Service is unavailable in the target cloud | The underlying Agent Service or project API is not provisioned there; a custom connector cannot bypass that limitation. |
| Flow fails after the owner changes roles | Recreate the connection with an approved operational owner and update dependent apps/flows. |

## Security

Never commit client secrets, refresh tokens, prompts containing sensitive data, tool payloads, or agent responses. Use least privilege, separate environment connections, conditional access, and an operational connection owner.
