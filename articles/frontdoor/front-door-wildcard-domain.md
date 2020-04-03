---
title: Azure Front Door - Support for wildcard domains
description: This article helps you understand how Azure Front Door supports mapping and managing wildcard domains in the list of custom domains
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
---

# Wildcard domains

Other than apex domains and subdomains, you can map a wildcard domain name to your list of frontend hosts or custom domains in your Front Door profile. Having wildcard domains in your Front Door configuration simplifies traffic routing behavior for multiple subdomains for an API, application, or website from the same routing rule. Use a wildcard domain to avoid having to modify the configuration to add or specify each subdomain separately. As an example, you can define the routing for `customer1.contoso.com`, `customer2.contoso.com`, and `customerN.contoso.com` using the same routing rule by adding the wildcard domain `*.contoso.com`.

Key scenarios that are improved with support for wildcard domains include:

- You don't need to onboard each subdomain in your Front Door profile and then enable HTTPS to bind a certificate for each subdomain.
- You are no longer required to change your production Front Door configuration if an application adds a new subdomain. Without the wildcard domain, you had to add the subdomain, bind a certificate to it, attach a web application firewall (WAF) policy, and add the domain to different routing rules.

> [!NOTE]
> Currently, wildcard domains are only supported via API, PowerShell, and CLI. Support for adding and managing wildcard domains in the Azure portal is not available.

## Add wildcard domains

You can add a wildcard domain under the frontend hosts or domains section. Similar to subdomains, Front Door validates that there is CNAME record mapping for your wildcard domain. This DNS mapping can be a direct CNAME record mapping like `*.contoso.com` mapped to `contoso.azurefd.net`. Mapping can also be accomplished via afdverify temporary mapping. For example, `afdverify.contoso.com` mapped to `afdverify.contoso.azurefd.net` validates the CNAME record map for the wildcard.

> [!NOTE]
> Azure DNS supports wildcard records.

You can add as many single level subdomains of the wildcard domain in frontend hosts, up to the maximum limit of the frontend hosts. This functionality might be required for defining a different route for a subdomain than the rest of the domains, from the wildcard domain. It might also be required for having a different WAF policy for a specific subdomain.

To illustrate, `*.contoso.com` allows adding `foo.contoso.com` without having to again prove domain ownership. However, it doesn't allow `foo.bar.contoso.com` because it is not a single level subdomain of `*.contoso.com`. To add `foo.bar.contoso.com` without additional domain ownership validation, `*.bar.contosonews.com` needs to be added.

### Limitations

- If a wildcard domain is added to a Front Door profile, then the same cannot be added to any other Front Door profile.
- If a wildcard domain is added to a Front Door profile, then any subdomains of that wildcard domain cannot be added to another Front Door profile or an Azure Content Delivery Network (CDN) from Microsoft profile.
- If a subdomain of a wildcard domain is added to a Front Door profile or an Azure CDN from Microsoft profile, then the wildcard domain cannot be added to other Front Door profiles.
- If two profiles (Front Door or Azure CDN from Microsoft) have various subdomains of a root domain, then wildcard domains can't be added to either of the profiles.

## Certificate binding for wildcard domains and their subdomains

For accepting HTTPS traffic on your wildcard domain, you must enable HTTPS on the wildcard domain. The certificate binding for a wildcard domain requires a wildcard certificate. That is, the subject name of the certificate should also have the wildcard domain.

> [!NOTE]
> Currently, only using your own custom SSL certificate option is available for enabling HTTPS for wildcard domains. Front Door managed certificates cannot be used for wildcard domains.

You can choose to use the same wildcard certificate from your Key Vault or from Front Door managed certificates for the subdomains.

If a subdomain is added for a wildcard domain and the wildcard domain already has a certificate associated with it, then HTTPS for this subdomain cannot be disabled. By default, the subdomain uses the certificate binding for the wildcard domain, unless it is overridden by a different Key Vault certificate or a Front Door managed certificate.

## Web application firewall policies for wildcard domains and their subdomains

WAF policies can be attached to a wildcard domain, similar to other domains. A different WAF policy can be applied to a subdomain of a wildcard domain. For the subdomains, you must specify the WAF policy to be used even if it is the same policy as the wildcard domain. Subdomains do *not* automatically inherit the WAF policy from the wildcard domain.

If you have a scenario where you don't want a WAF to run for a subdomain, you can create a blank WAF policy with no managed or custom rulesets.

## Routing rules for wildcard domains and their subdomains

When configuring a routing rule, you can select a wildcard domain as a frontend host. You can also have different route behavior for wildcard domains and subdomains. As described in [how Front Door does route matching](front-door-route-matching.md), the most specific match for the domain across different routing rules is chosen at runtime.

> [!CAUTION]
> You must have matching path patterns across your routing rules or your clients will see failures. For example, you have two routing rules like Route 1 (`*.foo.com/*` mapped to Backend Pool A) and Route 2 (`bar.foo.com/somePath/*` mapped to Backend Pool B). Then, a request arrives for `bar.foo.com/anotherPath/*`. Front Door selects Route 2 based on a more specific domain match, only to find no matching path patterns across the routes.

## Next steps

- Learn how to [create a Front Door](quickstart-create-front-door.md).
- Learn how to [add a custom domain on Front Door](front-door-custom-domain.md).
- Learn how to [enable HTTPS on a custom domain](front-door-custom-domain-https.md).
