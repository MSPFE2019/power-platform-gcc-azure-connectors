# Azure Log Analytics custom connector for GCC

This package is based on [MSPFE2019/Azure-Log-Analytics-Connector](https://github.com/MSPFE2019/Azure-Log-Analytics-Connector) and calls the **commercial** Azure Log Analytics REST API at `https://api.loganalytics.io`.

It is intended for a Power Platform GCC environment that is explicitly permitted to query a commercial Azure Log Analytics workspace. It is **not** an Azure Government connector.

## Requirements

- A Power Apps or Power Automate GCC environment.
- A commercial Azure subscription with an Azure Log Analytics workspace.
- The workspace GUID.
- Permission to query the workspace, such as Log Analytics Reader or an equivalent role.
- An Entra ID app registration in the tenant that owns the commercial workspace.
- A client secret for the app registration.
- Admin consent for:
  - **Log Analytics API**: delegated `Data.Read` for interactive user authentication.
  - **Microsoft Graph**: delegated `User.Read` if the OAuth consent flow requests it.
- The Power Platform maker must be allowed to create custom connectors and use premium connectors.

## Important data boundary

This connector sends queries to the commercial `api.loganalytics.io` service. GCC hosting does not turn the commercial Log Analytics workspace into Azure Government. Do not send or retrieve regulated data through this connector unless the organization's security authority has approved the cross-cloud data path.

## How to configure

### 1. Register the Entra ID application

1. Sign in to the commercial Azure portal at `https://portal.azure.com`.
2. Open **Microsoft Entra ID** > **App registrations** > **New registration**.
3. Use a name such as `Power Platform GCC Log Analytics`.
4. Select **Accounts in this organizational directory only**.
5. Leave **Redirect URI** blank and register the application.
6. Copy the **Application (client) ID** and **Directory (tenant) ID**.
7. Open **Certificates & secrets** > **New client secret**.
8. Copy the secret **Value** immediately; it cannot be retrieved later.

### 2. Assign permissions

1. Open **API permissions** > **Add a permission** > **APIs my organization uses**.
2. Select **Log Analytics API**.
3. Add delegated **Data.Read**.
4. Add Microsoft Graph delegated **User.Read** if requested by the sign-in flow.
5. Select **Grant admin consent** and confirm.
6. Grant the application or signed-in user access to the target workspace through Azure **Access control (IAM)**.

### 3. Import the custom connector

1. Open the GCC maker portal:
   - Power Apps: `https://make.gov.powerapps.us`
   - Power Automate: `https://gov.flow.microsoft.us`
2. Open **Custom connectors** and choose **New custom connector** > **Import an OpenAPI file**.
3. Upload `apiDefinition.swagger.json`.
4. On **General**, confirm the host is `api.loganalytics.io`.
5. On **Security**, select **OAuth 2.0** and Azure Active Directory.
6. Enter the client ID, client secret, and tenant ID from the app registration.
7. Use the commercial resource URL `https://api.loganalytics.io/`.
8. Do not replace the authorization or token URLs with `.us` endpoints; this package intentionally targets the commercial API.
9. Create the connector and create a new connection from its **Test** tab.

### 4. Test the connection

Use a low-impact query first:

```kusto
union withsource=TableName *
| take 1
```

Then test:

- **Run a Kusto query** with the workspace GUID and query body.
- **List all tables** to confirm workspace access.
- **Get table schema** with a known table name.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| OAuth redirect or token error | The client ID, secret, tenant, or redirect configuration is wrong. Recreate the connection after correcting the Security tab. |
| `403 Forbidden` | The user or app lacks Log Analytics Reader/query permission on the workspace, or admin consent is missing. |
| `404 Not Found` | The workspace GUID is wrong, or the workspace is not accessible through the commercial API. |
| Connection works in commercial Power Platform but not GCC | Verify that GCC outbound access and tenant policy allow the commercial Log Analytics endpoint. |
| Compliance review fails | This connector uses a commercial endpoint; use an approved Azure Government-native integration instead. |

## Security

Never commit client secrets, access tokens, workspace secrets, or query results. Rotate the client secret before its expiry and use environment-specific connections.
