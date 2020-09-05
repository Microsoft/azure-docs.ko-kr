---
title: Logic Apps REST API를 사용 하 여 통합 서비스 환경 (ISEs) 만들기
description: 에서 Vnet (Azure virtual network)에 액세스할 수 있도록 Logic Apps REST API를 사용 하 여 ISE (통합 서비스 환경)를 만듭니다 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 427b488fe6673bef505fccdaa7185d69437bceaf
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231319"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Logic Apps REST API를 사용하여 ISE(통합 서비스 환경) 만들기

이 문서에서는 논리 앱 및 통합 계정에 [Azure virtual network](../virtual-network/virtual-networks-overview.md)에 대 한 액세스 권한이 필요한 시나리오에 대 한 Logic Apps REST API 통해 [ISE ( *통합 서비스 환경* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) )를 만드는 방법을 보여 줍니다. ISE는 "글로벌" 다중 테넌트 Logic Apps 서비스와 별도로 유지되는 전용 스토리지 및 기타 리소스를 사용하는 전용 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. 또한 ISE는 자체 고정 IP 주소를 제공합니다. 이러한 IP 주소는 공용 다중 테넌트 서비스에서 논리 앱이 공유하는 고정 IP 주소와는 별개입니다.

[샘플 Azure Resource Manager 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) 또는 [Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)를 사용 하 여 ISE를 만들 수도 있습니다.

> [!IMPORTANT]
> ISE에서 실행되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 사용량 기반 가격 책정 플랜과 다른 가격 책정 플랜을 사용합니다. ISE의 가격 책정 및 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조하세요. 가격 책정 요금은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Portal ISE를 만들 때 [ise에 대 한 액세스를 가능 하 게 하](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) 는 동일한 [필수 구성 요소](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) 및 요구 사항

* HTTPS PUT 요청을 사용 하 여 Logic Apps REST API를 호출 하 여 ISE를 만드는 데 사용할 수 있는 도구입니다. 예를 들어 [Postman](https://www.getpostman.com/downloads/)을 사용 하거나이 작업을 수행 하는 논리 앱을 빌드할 수 있습니다.

## <a name="send-the-request"></a>요청 보내기

Logic Apps REST API 호출 하 여 ISE를 만들려면 HTTPS PUT 요청을 만듭니다.

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 버전을 사용 하려면 ISE 커넥터에 대 한 HTTP PUT 요청을 직접 수행 해야 합니다.

배포는 일반적으로 완료 하는 데 2 시간 이내에 수행 됩니다. 배포에 최대 4시간이 걸리는 경우가 간혹 있습니다. 배포 상태를 확인 하려면 [Azure Portal](https://portal.azure.com)의 Azure 도구 모음에서 알림 아이콘을 선택 합니다. 그러면 알림 창이 열립니다.

> [!NOTE]
> 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이렇게 지연되면 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 대기해야 할 수도 있습니다.
>
> 가상 네트워크를 삭제하면 Azure에서 서브넷이 해제되기까지 일반적으로 최대 2시간이 걸리지만 더 오래 걸릴 수도 있습니다. 
> 가상 네트워크를 삭제할 때는 그 때까지 연결된 리소스가 없는지 확인해야 합니다. 
> [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조하세요.

## <a name="request-header"></a>요청 헤더

요청 헤더에 다음 속성을 포함 합니다.

* `Content-type`:이 속성 값을로 설정 `application/json` 합니다.

* `Authorization`: 사용 하려는 Azure 구독 또는 리소스 그룹에 대 한 액세스 권한이 있는 고객에 대 한 전달자 토큰으로이 속성 값을 설정 합니다.

<a name="request-body"></a>

## <a name="request-body"></a>요청 본문

다음은 ISE를 만들 때 사용할 속성을 설명 하는 요청 본문 구문입니다. 위치에 설치 된 자체 서명 된 인증서를 사용할 수 있도록 하는 ISE를 만들려면 `TrustedRoot` `certificates` ise 정의의 섹션 내에 개체를 포함 합니다 `properties` . 기존 ISE의 경우 개체에 대 한 패치 요청을 보낼 수 있습니다 `certificates` . 자체 서명 된 인증서를 사용 하는 방법에 대 한 자세한 내용은 [다른 서비스와 시스템에 대 한 아웃 바운드 호출을 위한 보안 액세스 및 데이터 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)를 참조 하세요.

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>요청 본문 예제

이 예제 요청 본문은 샘플 값을 보여 줍니다.

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경에 리소스 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

