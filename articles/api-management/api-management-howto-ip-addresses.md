---
title: Azure API Management 서비스의 IP 주소 | Microsoft Docs
description: Azure API Management 서비스의 IP 주소를 검색 하는 방법 및 변경 되는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: cab63a17ddfed49684cc37609d9f2ae5bde9bdd3
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689378"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API Management의 IP 주소

이 문서에서는 Azure API Management 서비스의 IP 주소를 검색 하는 방법을 설명 합니다. 서비스가 가상 네트워크에 있는 경우 IP 주소는 공용 또는 개인 일 수 있습니다.

IP 주소를 사용 하 여 방화벽 규칙을 만들거나, 백 엔드 서비스로 들어오는 트래픽을 필터링 하거나, 아웃 바운드 트래픽을 제한할 수 있습니다.

## <a name="ip-addresses-of-api-management-service"></a>API Management 서비스의 IP 주소

Developer, Basic, Standard 또는 Premium 계층의 모든 API Management 서비스 인스턴스에는 공용 IP 주소가 있으며이 주소는 해당 서비스 인스턴스에만 단독으로 사용 되며 다른 리소스와 공유 되지 않습니다. 

Azure Portal에서 리소스의 개요 대시보드에서 IP 주소를 검색할 수 있습니다.

![API Management IP 주소](media/api-management-howto-ip-addresses/public-ip.png)

다음 API 호출을 사용 하 여 프로그래밍 방식으로 가져올 수도 있습니다.

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

공용 IP 주소는 응답의 일부입니다.

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

[다중 지역](api-management-howto-deploy-multi-region.md)배포의 경우 각 지역 배포에는 하나의 공용 IP 주소가 있습니다.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNet의 API Management 서비스에 대한 IP 주소

API Management 서비스는 가상 네트워크 내에 있는 경우 공용 및 개인 이라는 두 가지 유형의 IP 주소를 갖게 됩니다.

공용 IP 주소는 포트 `3443`에서 내부 통신에 사용 되며 (예: Azure Resource Manager를 통해) 구성을 관리 합니다. 외부 VNet 구성에서는 런타임 API 트래픽에도 사용 됩니다. API Management에서 공용 (인터넷 연결) 백 엔드로 요청이 전송 되 면 공용 IP 주소가 요청의 원본으로 표시 됩니다.

개인 VIP (가상 IP) 주소는 네트워크 내에서 API Management 엔드포인트 (게이트웨이, 개발자 포털 및 직접 API 액세스를 위한 관리 평면)에 연결 하는 데 사용 됩니다. 네트워크 내에서 DNS 레코드를 설정 하는 데 사용할 수 있습니다.

Azure Portal 및 API 호출의 응답에 두 형식의 주소가 모두 표시 됩니다.

![VNet IP 주소에 API Management](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management는 vnet 외부 연결에 대한 공용 IP 주소와 VNet 내의 연결에 대한 개인 IP 주소를 사용 합니다.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>소비 계층 API Management 서비스의 IP 주소

API Management 서비스가 소비 계층 서비스인 경우 전용 IP 주소가 없습니다. 소비 계층 서비스는 결정적 IP 주소 없이 공유 인프라에서 실행 됩니다. 

트래픽 제한 목적으로 Azure 데이터 센터의 IP 주소 범위를 사용할 수 있습니다. 정확한 단계 [는 Azure Functions 설명서 문서](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) 를 참조 하세요.

## <a name="changes-to-the-ip-addresses"></a>IP 주소에 대한 변경 내용

API Management의 개발자, 기본, 표준 및 프리미엄 계층에서 VIP (공용 IP 주소)는 서비스의 수명 동안 정적 이며 다음과 같은 경우는 예외입니다.

* 서비스가 삭제된 다음 다시 생성되었습니다.
* 서비스 구독이 [일시 중단](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)되거나 [경고](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)된 다음(예: 미지불) 복원되었습니다.
* Azure Virtual Network 서비스에서 추가 되거나 제거 됩니다.
* API Management 서비스가 외부 및 내부 VNet 배포 모드 사이에서 전환 되었습니다.

[다중 지역 배포](api-management-howto-deploy-multi-region.md)에서는 지역이 비워진 후 복원 되는 경우 지역 IP 주소가 변경 됩니다.
