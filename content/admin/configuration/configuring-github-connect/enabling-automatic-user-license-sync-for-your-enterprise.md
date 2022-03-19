#pragma once

// Baselib Network Address

#include "Baselib_ErrorState.h"
#include "Baselib_Alignment.h"
#include "Internal/Baselib_EnumSizeCheck.h"

#include <string.h>

#ifdef __cplusplus
BASELIB_C_INTERFACE
{
#endif

// Address family.
typedef enum
{
    Baselib_NetworkAddress_Family_Invalid = 0,
    Baselib_NetworkAddress_Family_IPv4 = 1,
    Baselib_NetworkAddress_Family_IPv6 = 2
} Baselib_NetworkAddress_Family;
BASELIB_ENUM_ENSURE_ABI_COMPATIBILITY(Baselib_NetworkAddress_Family);

// Fixed size address structure, large enough to hold IPv4 and IPv6 addresses.
typedef struct Baselib_NetworkAddress
{
    union
    {
        uint8_t data[16];
        uint8_t ipv6[16]; // in network byte order
        uint8_t ipv4[4];  // in network byte order
    };
    BASELIB_ALIGN_AS(2) uint8_t port[2]; // in network byte order
    uint8_t family;

    // This struct consists entirely of uint8_t and as such its size would be 19.
    // Since we align the port to 2 though, padding is automatically added. However, right now our binding generator does not read this out.
    // So to be a bit safer we add this manual padding.
    uint8_t _padding;
} Baselib_NetworkAddress;

// Max length of any string representing an IP address
static const uint32_t Baselib_NetworkAddress_IpMaxStringLength = 46;

// Binary encode string representation of an address.
//
// Possible error codes:
//  - Baselib_ErrorCode_InvalidArgument - One or more of the input parameters are invalid
BASELIB_API void Baselib_NetworkAddress_Encode(
    Baselib_NetworkAddress*         dstAddress,
    Baselib_NetworkAddress_Family   family,
    const char                      ip[],
    uint16_t                        port,
    Baselib_ErrorState*             errorState
);

// Decode binary representation of an address.
//
// family, ipAddressBuffer, and port are all optional arguments.
// passing zero as  ipAddressBufferLen is the same as passing an ipAddressBuffer nullptr.
//
// Possible error codes:
//  - Baselib_ErrorCode_InvalidArgument - srcAddress is null or otherwise invalid.
//  - Baselib_ErrorCode_InvalidBufferSize - ipAddressBuffer is too small to hold decoded ip address.
BASELIB_API void Baselib_NetworkAddress_Decode(
    const Baselib_NetworkAddress*   srcAddress,
    Baselib_NetworkAddress_Family*  family,
    char                            ipAddressBuffer[],
    uint32_t                        ipAddressBufferLen,
    uint16_t*                       port,
    Baselib_ErrorState*             errorState
);

// Returns zero initialized network address struct
static inline Baselib_NetworkAddress Baselib_NetworkAddress_Empty(void)
{
    Baselib_NetworkAddress address;
    memset(&address, 0, sizeof(address));
    return address;
}

typedef enum
{
    Baselib_NetworkAddress_AddressReuse_DoNotAllow = 0,

    // Allow multiple sockets to be bound to the same address/port.
    // All sockets bound to the same address/port need to have this flag set.
    Baselib_NetworkAddress_AddressReuse_Allow = 1,
} Baselib_NetworkAddress_AddressReuse;
BASELIB_ENUM_ENSURE_ABI_COMPATIBILITY(Baselib_NetworkAddress_AddressReuse);

#ifdef __cplusplus
}
#endif
title: Enabling automatic user license sync for your enterprise
intro: 'You can manage license usage across your {% data variables.product.prodname_enterprise %} environments by automatically syncing user licenses from {% data variables.product.product_location %} to {% data variables.product.prodname_ghe_cloud %}.'
redirect_from:
  - /enterprise/admin/installation/enabling-automatic-user-license-sync-between-github-enterprise-server-and-github-enterprise-cloud
  - /enterprise/admin/configuration/enabling-automatic-user-license-sync-between-github-enterprise-server-and-github-enterprise-cloud
  - /admin/configuration/enabling-automatic-user-license-sync-between-github-enterprise-server-and-github-enterprise-cloud
  - /admin/configuration/managing-connections-between-github-enterprise-server-and-github-enterprise-cloud/enabling-automatic-user-license-sync-between-github-enterprise-server-and-github-enterprise-cloud
  - /admin/configuration/managing-connections-between-your-enterprise-accounts/enabling-automatic-user-license-sync-between-github-enterprise-server-and-github-enterprise-cloud
permissions: Enterprise owners can enable automatic user license synchronization.
versions:
  ghes: '*'
type: how_to
topics:
  - Enterprise
  - GitHub Connect
  - Licensing
shortTitle: Automatic user license sync
---
## About license synchronization

{% data reusables.enterprise-licensing.about-license-sync %} For more information, see "[About {% data variables.product.prodname_github_connect %}](/admin/configuration/configuring-github-connect/about-github-connect#data-transmission-for-github-connect)."

If you enable automatic user license sync for your enterprise, {% data variables.product.prodname_github_connect %} will automatically synchronize license usage between {% data variables.product.prodname_ghe_server %} and {% data variables.product.prodname_ghe_cloud %} weekly.

If you use multiple {% data variables.product.prodname_ghe_server %} instances, you can enable automatic license sync between each of your instances and the same organization or enterprise account on {% data variables.product.prodname_ghe_cloud %}.

{% data reusables.enterprise-licensing.view-consumed-licenses %}

You can also manually upload {% data variables.product.prodname_ghe_server %} user license information to {% data variables.product.prodname_ghe_cloud %}. For more information, see "[Syncing license usage between {% data variables.product.prodname_ghe_server %} and {% data variables.product.prodname_ghe_cloud %}](/billing/managing-your-license-for-github-enterprise/syncing-license-usage-between-github-enterprise-server-and-github-enterprise-cloud)."

{% data reusables.enterprise-licensing.verified-domains-license-sync %}

## Enabling license synchronization

Before enabling license synchronization on {% data variables.product.product_location %}, you must enable {% data variables.product.prodname_github_connect %}. For more information, see "[Managing {% data variables.product.prodname_github_connect %}](/admin/configuration/configuring-github-connect/managing-github-connect)."

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.github-connect-tab %}
1. Under "Server can sync user license count and usage", use the drop-down menu and select **Enabled**.
  ![Drop-down menu to enable automatic user license sync](/assets/images/enterprise/site-admin-settings/enable-user-license-drop-down.png)
