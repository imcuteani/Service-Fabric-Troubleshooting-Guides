# Connecting to secure clusters with PowerShell

## **Connect to Secure Clusters**


Verbose script example

```PowerShell
#For Cert based authentication
$ClusterName= "{yourclustername}.{region}.cloudapp.azure.com:19000"
$Certthumprint = "{yourCertificateThumbprint}"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $Certthumprint  `
    -FindType FindByThumbprint `
    -FindValue $Certthumprint `
    -StoreLocation CurrentUser `
    -StoreName My 
```

Compact script example

```PowerShell
#For Cert based authentication
$ClusterName= "{yourclustername}.{region}.cloudapp.azure.com:19000"
$Certthumprint = "{yourCertificateThumbprint}"

#single command - compact example
$connectArgs = @{  ConnectionEndpoint = $ClusterName;  X509Credential = $True;  StoreLocation = "CurrentUser";  StoreName = "My";  FindType = "FindByThumbprint";  FindValue = $Certthumprint; ServerCertThumbprint =$Certthumprint;  }
Connect-ServiceFabricCluster @connectArgs
```

Unsecure cluster connection

```PowerShell
#For unsecure based authentication
$ClusterName= "{yourclustername}.{region}.cloudapp.azure.com:19000"

#single command - compact example
$connectArgs = @{  ConnectionEndpoint = $ClusterName;   }
Connect-ServiceFabricCluster @connectArgs 
```

Simple AAD Authentication
```PowerShell
$ClusterName= "{yourclustername}.{region}.cloudapp.azure.com:19000"
$Certthumprint = "{yourCertificateThumbprint}"
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint $Certthumprint
```

Custom AAD Authentication
```PowerShell
$TenantID = "<guid>"
$AppIDWeb = "<guid>" #ClientID for webApp
$AppIDNative = "<guid>" #ClientID for nativeApp
[System.Uri]$Uri = New-Object System.Uri("urn:ietf:wg:oauth:2.0:oob"); #RedirectURI for nativeApp
[string]$adTenant              = "<Tenantname>"
[string]$SubscriptionId        = "<SubID>"
[string]$TokenEndpoint = "https://login.windows.net/$($adTenant)/oauth2/token"

# User Credentials
[string]$UserName              = "<username>"
[string]$Password              = "<password>"
[string]$UserAuthPayload = "resource=$($AppIDWeb)&client_id=$($AppIDNative)"+"&grant_type=password&username=$($userName)&password=$($password)&scope=openid";

$auth = Invoke-RestMethod -Uri $TokenEndpoint -body $UserAuthPayload -Headers @{ "Content-Type" = "application/x-www-form-urlencoded"; } -Method Post

$ClusterName= "{yourclustername}.{region}.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint $ClusterName -SecurityToken $auth.access_token
```


## Troubleshooting connection over a specific port

```PowerShell
Test-NetConnection -ComputerName "contosocluster.westus2.cloudapp.azure.com" -Port 19000 -InformationLevel "Detailed"

    ComputerName : contosocluster.westus2.cloudapp.azure.com
    RemoteAddress : 13.77.169.134
    RemotePort : 19000
    NameResolutionResults : 13.77.169.134
    MatchingIPsecRules :
    NetworkIsolationContext : Internet
    IsAdmin : False
    InterfaceAlias : Ethernet
    SourceAddress : 65.53.68.93
    NetRoute (NextHop) : 65.53.64.1
    TcpTestSucceeded : True
```