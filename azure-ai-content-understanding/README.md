# Azure AI Content Understanding custom connector

This connector exposes a starter Content Understanding analyze operation through an API-key connection. It does not assert that the bundled request/response contract or API version is current.

Microsoft currently lists the managed connector as unavailable in GCC, GCC High, and DoD. Confirm the Azure service deployment, endpoint suffix, API version, and authorization boundary before importing. A custom connector does not confer Microsoft compliance authorization.

## Requirements

- A Power Apps or Power Automate GCC environment.
- An Azure AI Content Understanding resource and analyzer approved for the target cloud and workload.
- The resource name, analyzer ID, and an API version verified against the current Microsoft service documentation and resource.
- An Azure AI resource key with permission to call the analyzer.
- The analyzer input contract and any required source-file or blob permissions.
- Permission to create custom connectors and use premium connectors.
- DLP and security approval for documents, images, extracted fields, and service responses.

## Connection user

This connector uses an API-key connection rather than a signed-in Azure user.

- Create the connection with a controlled owner, preferably a dedicated integration account or managed operational owner.
- Store the `Ocp-Apim-Subscription-Key` value only in the Power Platform connection.
- The connection owner must be authorized to use the Content Understanding resource and must manage key rotation.
- Do not use a personal subscription key for production unless governance explicitly permits it.
- Share the connection only with the required makers and service principals. Anyone allowed to use it can submit content to the analyzer.
- Use separate connections for development, test, and production.

## Important GCC boundary

The Swagger definition uses the Azure Government-style host `{resourceName}.cognitiveservices.azure.us`. Verify that the target Content Understanding service, analyzer, API version, and region are actually available and approved in that cloud. A custom connector cannot create service availability or move processing into a compliant boundary.

## How to configure

### 1. Obtain the resource and analyzer details

1. Open the approved Azure portal and select the Content Understanding resource.
2. Copy the resource name and confirm the approved endpoint suffix.
3. Identify an analyzer that is deployed and available to the connection user.
4. Record the analyzer ID and an API version supported by that resource.
5. From the resource **Keys and Endpoint** page, copy an API key.

### 2. Import the custom connector

1. Open the GCC maker portal:
   - Power Apps: `https://make.gov.powerapps.us`
   - Power Automate: `https://gov.flow.microsoft.us`
2. Open **Custom connectors** and choose **New custom connector** > **Import an OpenAPI file**.
3. Upload `apiDefinition.swagger.json`.
4. On **General**, confirm the host and HTTPS scheme match the approved resource.
5. On **Security**, confirm **API key** authentication with header name `Ocp-Apim-Subscription-Key`.
6. Create or update the connector and open its **Test** tab.
7. Create a new connection and enter the resource key.

### 3. Test the connection

1. Call **Analyze content** with a known analyzer ID and an API version verified against the current Microsoft service documentation and resource. Do not assume the bundled preview version is supported.
2. Start with a non-sensitive test document or image and the smallest valid request body.
3. Confirm whether the response is synchronous (`200`) or accepted for processing (`202`).
4. Validate the returned fields before using them in a production app or flow.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| `401` or `403` | The key is invalid, revoked, entered under the wrong connection, or not permitted to use the resource/analyzer. |
| `404` | The resource name, analyzer ID, endpoint suffix, or API version is wrong. |
| `400 Bad Request` | The analyzer input schema or content type does not match the request body. |
| `202` with no final result | The operation is asynchronous; implement the approved status/result retrieval pattern if the service requires one. |
| Service is unavailable in the target cloud | The underlying Content Understanding service or region is not provisioned there; a custom connector cannot bypass that limitation. |
| Compliance review fails | Confirm the document data path, storage locations, processing region, and service authorization with the security authority. |

## Security

Never commit subscription keys, source documents, extracted sensitive fields, analyzer definitions, or response payloads. Rotate keys before expiry and disable the old key after all environments have been updated.
