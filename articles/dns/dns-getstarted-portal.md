---
title: Quickstart - Create a DNS zone and record using the Azure Portal
description: Learn how to create a DNS zone and record in Azure DNS. This is a step-by-step guide to create and manage your first DNS zone and record using the Azure portal.
services: dns
author: KumudD
manager: jeconnoc

ms.service: dns
ms.topic: quickstart
ms.date: 6/8/2018
ms.author: kumud
#Customer intent: As an administrator or developer, I want to learn how to configure Azure DNS so I can connect to my web server using a friendly name.
---

# Quickstart: Configure Azure DNS for name resolution using the Azure Portal

 You can configure Azure DNS to resolve a host name in your public domain. For example, if you purchased the contoso.com domain name from a domain name registrar, you could configure Azure DNS to host the contoso.com domain and resolve www.contoso.com to point to your web server or web app.

In this quickstart, you will create a test domain, and then create a host address record named 'www' to point to an IP address of your choice.

It is important to know that all the names and IP addresses used in this quickstart are examples only, and are not meant to represent a real-world scenario. However, where applicable, real-world alternatives are also described.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI 2.0](dns-getstarted-cli.md).
--->

A DNS zone is used to contain the DNS entries for a particular domain. To start hosting your domain in Azure DNS, you need to create a DNS zone for that domain name. Each DNS entry (or record) for your domain is then created inside this DNS zone. This is described in the following steps.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Create a DNS zone

1. Sign in to the Azure portal.
2. On the Hub menu, click **Create a resource > Networking >** and then click **DNS zone** to open the **Create DNS zone** page.

    ![DNS zone](./media/dns-getstarted-portal/openzone650.png)

4. On the **Create DNS zone** page enter the following values, then click **Create**:


   | **Setting** | **Value** | **Details** |
   |---|---|---|
   |**Name**|contoso.xyz|The name of the DNS zone for this example. You can use any value you want for this test, as long as it not already configured on the Azure DNS servers. A real-world value would be a domain that you bought from a domain name registrar.|
   |**Subscription**|[Your subscription]|Select a subscription to create the DNS zone in.|
   |**Resource group**|**Create new:** dns-test|Create a resource group. The resource group name must be unique within the subscription you selected. |
   |**Location**|East US||

## Create a DNS record

The following example walks you through the process of creating new address record ('A' record). 'A' records are used to resolve a host name to an IP v4 address.

1. With the DNS Zone created, in the Azure portal **Favorites** pane, click **All resources**. Click the **contoso.xyz** DNS zone in the All resources page. If the subscription you selected already has several resources in it, you can enter **contoso.xyz** in the **Filter by name…** box to easily access the DNS Zone.

1. At the top of the **DNS zone** page, select **+ Record set** to open the **Add record set** page.

1. On the **Add record set** page, enter the following values, and click **OK**. In this example, you are creating an A record.

   |**Setting** | **Value** | **Details** |
   |---|---|---|
   |**Name**|www|Name of the record. This is the name of the host that you want to resolve to an IP address.|
   |**Type**|A| Type of DNS record to create.|
   |**TTL**|1|Time-to-live of the DNS request.|
   |**TTL unit**|Hours|Measurement of time for TTL value.|
   |**IP address**|10.10.10.10| This value is the IP address that the 'A' record resolves to. This is just a test value for this example. For a real-world example, you would enter the public IP address for your web server.|


Since in this example you didn't actually purchase a real domain name, there is no need to configure Azure DNS as the name server with your domain name registrar. But in a real-world scenario, you would want anyone on the Internet to be able to resolve your host name to connect to your web server or app. For more information about that real-world scenario, see [Delegate a domain to Azure DNS](dns-delegate-domain-azure-dns.md).


## Test the name resolution

Now that you have a test zone, with a test 'A' record in it, you can test the name resolution with a tool called nslookup. 

1. First, you need to note the Azure DNS name servers to use with nslookup. 

   The name servers for your zone are listed on the DNS zone Overview page. Copy the name of one of the name servers:

   ![zone](./media/dns-getstarted-portal/viewzonens500.png)

2. Now, open a command prompt and run the following command:

   ```
   nslookup www.contoso.xyz <name server>
   
   For example:

   nslookup www.contoso.xyz ns1-08.azure-dns.com
   ```

You should see something like to the following screenshot:

![nslookup](media/dns-getstarted-portal/nslookup.PNG)

## Clean up resources

To delete all resources created in this quickstart, you can delete the **dns-test** resource group. To do so, select the resource group that contains the dns zone and click **Delete**.


## Next steps

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain](./dns-web-sites-custom-domain.md)