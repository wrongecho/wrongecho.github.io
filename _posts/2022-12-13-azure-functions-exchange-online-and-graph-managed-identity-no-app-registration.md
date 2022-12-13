## Connecting to Exchange Online & MS Graph PowerShell with Managed Identities using Azure Functions

Azure Functions are part of Azure's PaaS "Serverless" compute services that enable you to easily run code in a variety of languages (Node, JavaScript, Python, PowerShell) in the cloud.

When using PowerShell modules such as Exchange Online or MS Graph, my go-to method for authentication has been to setup an app registration, assign the appropriate permissions and then connect using the certificate.

I wanted to investigate whether it was possible for a function app to authenticate to both Exchange and MS Graph using just an Azure Managed Identity - it is!

> A quick explanation: it is my understanding that *everything* in Azure is a service principal. There are just varying degrees of what Azure manages for you. In this situation, I'm aiming for Azure to manage the entire service principal - rather than having to do it myself via an app registration - hence "System Assigned Managed Identity".

### Setup Managed Identity

First, you'll need to turn on the System Assigned managed identity on the *Identity* tab. This will allow the function to call `Connect-AzAccount -Identity`.

![identity.png](/img/2022-12-13-func-managed-id/identity.png)

Now that you have a Managed Identity, you need to assign some permissions.

### Exchange

For Exchange, this means registering the service principal so it can be assigned roles. We can use the `New-MgServicePrincipalAppRoleAssignment` command for this:

```
 # Managed Identity - Get this from the Identity tab
$managedIdentity = "aaa-bbb-ccc"

# App Role ID - Exchange Online App ID (Exchange.ManageAsApp)
$appRoleID = "dc50a0fb-09a3-484d-be87-e023b12c6440"

# Resource ID -  Get the Service Principal associated with the Exchange Online resource
## you'll sometimes see this app ID in the browser when accessing Exchange admin center
$resourceID = (Get-MgServicePrincipal -Filter 'AppId eq "00000002-0000-0ff1-ce00-000000000000"'.Id)

# Put it together to generate a service principal for Exchange
New-MgServicePrincipalAppRoleAssignment -ServicePrincipalId $managedIdentity -PrincipalId $managedIdentity -AppRoleId $appRoleID -ResourceId $resourceID
```

Once this is complete, the managed identity is now able to be assigned Exchange-related roles in Azure AD. I've assigned it Exchange Recipient Administrator:

![exchange-sp.png](/img/2022-12-13-func-managed-id/exchange-sp.png)

### Graph

Thankfully, assigning permissions for MS Graph is a little more straightforward - just add the service principal to the role.

![exchange-sp.png](/img/2022-12-13-func-managed-id/exchange-sp.png)

### Connecting in PowerShell

First, check that your profile.ps1 file includes the `Connect-AzAccount -Identity` line and that you're defining `ExchangeOnlineManagement` and `Microsoft.Graph` in your requirements.psd1 file.

Then, import your modules:
```
Import-Module Az.Accounts
Import-Module ExchangeOnlineManagement
Import-Module Microsoft.Graph.Authentication -UseWindowsPowershell # UseWindowsPowershell seems to prevent some weird conflicts when importing additional modules like ExchangeOnline
Import-Module Microsoft.Graph.Users -UseWindowsPowershell # Optional
```

To connect to Exchange Online, just use the ManagedIdentity param and specify your onmicrosoft domain:
`Connect-ExchangeOnline -ManagedIdentity -Organization 'yourdomain.onmicrosoft.com'`

To connect to MS Graph, you need to retrieve an access token using the Az.Accounts module:
`Connect-MgGraph -AccessToken (Get-AzAccessToken -ResourceUrl "https://graph.microsoft.com/").Token`

And we're done!
