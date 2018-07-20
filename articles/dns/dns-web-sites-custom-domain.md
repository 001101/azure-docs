---
title: Tutorial - Create custom DNS records for a web app
description: In this tutorial you create custom domain DNS records for web app using Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/20/2018
ms.author: victorh
#Customer intent: As an experienced network administrator I want to create DNS records in Azure DNS, so I can host a web app in a custom domain.
---

# Tutorial: Create DNS records in a custom domain for a web app 

You can use Azure DNS to host a custom domain for your web apps. For example, you can create an Azure web app and have your users access it using either contoso.com or www.contoso.com as a fully qualified domain name (FQDN).

> [!NOTE]
> Contoso.com is used as an example throughout this tutorial. Substitute your own domain name for contoso.com.

To do this, you have to create three records:

* A root "A" record pointing to contoso.com
* A root "TXT" record for verification
* A "CNAME" record for the www name that points to the A record

Keep in mind that if you create an A record for a web app in Azure, the A record must be manually updated if the underlying IP address for the web app changes.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create an A and TXT record for your custom domain
> * Create a CNAME record for your custom domain
> * Test the new records


If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## Prerequisites

- [Create an App Service app](../app-service/app-service-web-get-started-html.md), or use an app that you created for another tutorial.

- Create a DNS zone in Azure DNS, and delegate the zone in your registrar to Azure DNS.

   1. To create a DNS zone, follow the steps in [Create a DNS zone](dns-getstarted-create-dnszone.md).
   2. To delegate your zone to Azure DNS, follow the steps in [DNS domain delegation](dns-domain-delegation.md).

After creating a zone and delegating it to Azure DNS, you can then create records for your custom domain.

## Create an A and TXT record

An A record is used to map a name to its IP address. In the following example, assign @ as an A record using your web app IPv4 address.

### Get the IPv4 address

In the left navigation of the app page in the Azure portal, select **Custom domains**. 

![Custom domain menu](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

In the **Custom domains** page, copy the app's IPv4 address:

![Portal navigation to Azure app](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### Create the A record

```powershell
New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### Create the TXT record

App Service uses this record only at configuration time to verify that you own the custom domain. You can delete this TXT record after your custom domain is validated and configured in App Service.

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup -Name "@" -RecordType "txt" -Ttl 600 -DnsRecords (New-AzureRmDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## Create a CNAME record for your custom domain

If your domain is already managed by Azure DNS (see [DNS domain delegation](dns-domain-delegation.md), you can use the following the example to create a CNAME record for contoso.azurewebsites.net.



Open Azure PowerShell and create a new CNAME record. This example creates a record set type CNAME with a "time to live" of 600 seconds in DNS zone named "contoso.com" with the alias for the web app contoso.azurewebsites.net.

### Create the record

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" -Name "www" -RecordType "CNAME" -Ttl 600 -DnsRecords (New-AzureRmDnsRecordConfig -cname "contoso.azurewebsites.net")
```

The following example is the response:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## Test the new records

You can validate the records were created correctly by querying the "www.contoso.com"  and "contoso.com" using nslookup, as shown below:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```

## Clean up resources

You can keep the **myresourcegroup** resource group if you intend to do the next tutorial. Otherwise, delete the **myresourcegroup** resource group to delete the resources created in this tutorial.

## Next steps

Now that you've created your custom domain, you can map hostnames to your web app. Use the information in the App Service tutorial to add hostnames *www.contoso.com* and *contoso.com* to your web app.

> [!div class="nextstepaction"]
> [Configuring a custom domain name for App Service](../app-service/app-service-web-tutorial-custom-domain.md)
