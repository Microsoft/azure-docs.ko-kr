---
title: 'Terraform: VNet 통합 및 Private Endpoint로 안전하게 연결된 프런트 엔드 웹앱 및 백 엔드 웹앱 배포'
description: App Service용 terraform 공급자를 사용하여 Private Endpoint 및 VNet 통합으로 안전하게 연결된 두 개의 웹앱을 배포하는 방법을 알아봅니다.
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 76591b9f397bd84e5afac19a56dd2c6467f4650f
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962233"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Private Endpoint 및 VNet 통합으로 안전하게 연결된 두 개의 웹앱 만들기

이 문서에서는 다음 단계에 따라 두 개의 웹앱(프런트 엔드 및 백 엔드)을 안전하게 연결하는 [Private Endpoint](../networking/private-endpoint.md) 및 지역 [VNet 통합](../web-sites-integrate-with-vnet.md)을 사용하는 예를 보여줍니다.
- VNet 배포
- 통합을 위한 첫 번째 서브넷 만들기
- 프라이빗 엔드포인트에 대한 두 번째 서브넷을 만듭니다. 네트워크 정책을 사용하지 않도록 설정하려면 특정 매개 변수를 설정해야 합니다.
- Private Endpoint 기능에 필요한 최소 SKU인 PremiumV2 형식의 App Service 계획 1개 배포
- 특정 앱 설정을 통해 프런트 엔드 웹앱을 만들어 프라이빗 DNS 영역을 사용합니다. [자세한 정보](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- 프런트 엔드 웹앱을 통합 서브넷에 연결
- 백 엔드 웹앱 만들기
- 웹앱 privatelink.azurewebsites.net에 대한 프라이빗 링크 영역 이름으로 DNS 프라이빗 영역을 만듭니다.
- 이 영역을 VNet에 연결
- 엔드포인트 서브넷에 백 엔드 웹앱에 대한 프라이빗 엔드포인트를 만들고 이전에 만든 DNS 프라이빗 영역에 DNS 이름(웹 사이트 및 SCM)을 등록합니다.

## <a name="how-to-use-terraform-in-azure"></a>Azure에서 terraform을 사용하는 방법

[Azure 설명서](/azure/developer/terraform/)로 이동하여 Azure에서 terraform을 사용하는 방법을 알아봅니다.

## <a name="the-complete-terraform-file"></a>전체 terraform 파일

이 파일을 사용하려면 frontwebapp 및 backwebapp 리소스의 이름 속성을 변경해야 합니다(웹앱 이름은 전 세계적으로 고유한 DNS 이름이어야 함). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>다음 단계


> [Azure에서 Terraform을 사용하는 방법에 대해 자세히 알아보기](/azure/developer/terraform/)