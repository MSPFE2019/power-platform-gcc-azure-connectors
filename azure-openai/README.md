# Azure OpenAI custom connector

This connector exposes chat completions and embeddings through an Azure OpenAI deployment endpoint.

It is a custom connector starter definition, not the Microsoft-managed Azure OpenAI connector. Microsoft currently lists the managed connector as unavailable in GCC, GCC High, and DoD.

## Requirements

- A Power Apps or Power Automate GCC environment.
- An Azure OpenAI resource and model deployment approved for the target cloud and workload.
- The Azure OpenAI resource name, deployment name, and supported API version.
- An Azure OpenAI API key from **Resource Management** > **Keys and Endpoint**.
- Permission to create custom connectors and use premium connectors.
- DLP and security approval for the endpoint, prompt data, completion data, and model deployment.

## Connection user

This connector uses an API-key connection rather than a signed-in Azure user.

- Create the connection with a controlled owner, preferably a dedicated integration account or managed operational owner.
- Store the API key only in the Power Platform connection; never place it in a flow, app, Swagger file, source control, or documentation.
- The connection owner must be authorized to use the Azure OpenAI resource and must be responsible for key rotation.
- Do not use a personal key for production flows unless your governance policy explicitly permits it.
- Share the connection only with the required makers and service principals. A user who can use the connection may send data through the Azure OpenAI resource.
- Create separate connections for development, test, and production.

## Important GCC boundary

A custom connector does not make Azure OpenAI GCC-authorized and does not change where prompts or responses are processed. Confirm the Azure resource, endpoint, model, and data path with the security authority before use. Do not send regulated, CUI, ITAR, CJIS, or other controlled data unless the full path is approved.

## How to configure

### 1. Obtain the Azure OpenAI details

1. Open the approved Azure portal and select the Azure OpenAI resource.
2. Copy the resource name from the resource overview.
3. Copy the model deployment name from **Model deployments**.
4. From **Resource Management** > **Keys and Endpoint**, copy one key and record the endpoint for reference.
5. Confirm that the endpoint uses the host expected by this definition: `{resourceName}.openai.azure.com`.

### 2. Import the custom connector

1. Open the GCC maker portal:
   - Power Apps: `https://make.gov.powerapps.us`
   - Power Automate: `https://gov.flow.microsoft.us`
2. Open **Custom connectors** and choose **New custom connector** > **Import an OpenAPI file**.
3. Upload `apiDefinition.swagger.json`.
4. On **General**, confirm the resource host and HTTPS scheme.
5. On **Security**, confirm **API key** authentication with header name `api-key`.
6. Create or update the connector and open its **Test** tab.
7. Create a new connection and enter the Azure OpenAI API key.

### 3. Test the connection

1. Test **Create chat completion** with the deployment name and API version supported by the resource.
2. Use a non-sensitive prompt such as `Reply with the word OK`.
3. Test **Create embeddings** only when an embeddings deployment exists.
4. After testing, save the connection and reference it from the app or flow.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| `401 Unauthorized` | The API key is wrong, expired, revoked, or entered under the wrong connection. Create a new connection after rotating the key. |
| `404 Resource not found` | The resource name, deployment name, endpoint cloud, or API version is wrong. |
| `429 Too Many Requests` | The deployment quota or rate limit was exceeded. Review capacity and retry policy. |
| Connection works outside GCC but not in GCC | GCC outbound access, tenant policy, DLP policy, or endpoint approval may block the commercial endpoint. |
| Compliance review fails | The custom connector does not provide Microsoft-managed GCC authorization. Reconfirm the complete data boundary. |

## Security

Never commit API keys, prompts containing sensitive data, model responses, or connection exports. Rotate keys before expiry and disable the old key after all environments have been updated.
