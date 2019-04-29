---
title: Azure Functions의 IP 주소
description: 함수 앱의 인바운드 및 아웃바운드 IP 주소를 찾는 방법과 변경되는 원인을 알아봅니다.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: 83e5a15d8a7f9c01f6a180ebceb715600b8a39db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035864"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Functions의 IP 주소

이 문서에서 함수 앱의 IP 주소와 관련하여 설명하는 항목은 다음과 같습니다.

* 함수 앱에서 현재 사용 중인 IP 주소를 찾는 방법
* 함수 앱의 IP 주소가 변경되는 이유
* 함수 앱에 액세스할 수 있는 IP 주소를 제한하는 방법
* 함수 앱 전용 IP 주소를 가져오는 방법

IP 주소는 개별 함수가 아니라 함수 앱과 연결됩니다. 들어오는 HTTP 요청은 인바운드 IP 주소를 사용하여 개별 함수를 호출할 수 없습니다. 기본 도메인 이름(functionappname.azurewebsites.net) 또는 사용자 지정 도메인 이름을 사용해야 합니다.

## <a name="function-app-inbound-ip-address"></a>함수 앱 인바운드 IP 주소

각 함수 앱에는 하나의 인바운드 IP 주소가 있습니다. 해당 IP 주소를 찾으려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 함수 앱으로 이동합니다.
3. **플랫폼 기능**을 선택합니다.
4. **속성**을 선택합니다. 그러면 **가상 IP 주소** 아래에 인바운드 IP 주소가 표시됩니다.

## <a name="find-outbound-ip-addresses"></a>함수 앱 아웃바운드 IP 주소

각 함수 앱에는 사용 가능한 아웃바운드 IP 주소 집합이 있습니다. 백 엔드 데이터베이스와 같이 함수의 아웃바운드 연결은 사용 가능한 아웃바운드 IP 주소 중 하나를 원본 IP 주소로 사용합니다. 지정된 연결에서 사용할 IP 주소는 미리 알 수 없습니다. 따라서 백 엔드 서비스에서 모든 함수 앱의 아웃바운드 IP 주소에 대해 방화벽을 열어야 합니다.

함수 앱에서 사용 가능한 아웃바운드 IP 주소를 찾으려면 다음을 수행합니다.

1. [Azure Resource Explorer](https://resources.azure.com)에 로그인합니다.
2. **구독 > {사용자 구독} > 공급자 > Microsoft.Web > 사이트**를 차례로 선택합니다.
3. JSON 패널에서 함수 앱의 이름으로 끝나는 `id` 속성이 있는 사이트를 찾습니다.
4. `outboundIpAddresses` 및 `possibleOutboundIpAddresses`를 확인합니다. 

`outboundIpAddresses` 집합은 현재 함수 앱에서 사용할 수 있습니다. `possibleOutboundIpAddresses` 집합에는 함수 앱에서 [다른 가격 책정 계층으로 크기 조정](#outbound-ip-address-changes)하는 경우에만 사용할 수 있는 IP 주소가 포함됩니다.

사용 가능한 아웃바운드 IP 주소를 찾는 다른 방법은 [Cloud Shell](../cloud-shell/quickstart.md)을 사용하는 것입니다.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> [소비 계획](functions-scale.md#consumption-plan)에서 실행되는 함수 앱의 크기를 조정하는 경우 새로운 아웃바운드 IP 주소 범위를 할당할 수 있습니다. 소비 계획에서 실행하는 경우 전체 데이터 센터를 허용 목록에 추가해야 합니다.

## <a name="data-center-outbound-ip-addresses"></a>데이터 센터 아웃바운드 IP 주소

함수 앱에서 사용하는 아웃바운드 IP 주소를 허용 목록에 추가해야 하는 경우, 함수 앱의 데이터 센터(Azure 지역)를 허용 목록에 추가할 수 있습니다. [모든 Azure 데이터 센터의 IP 주소를 나열하는 JSON 파일을 다운로드](https://www.microsoft.com/en-us/download/details.aspx?id=56519)할 수 있습니다. 그런 다음, 함수 앱이 실행되는 지역에 적용되는 JSON 조각을 찾습니다.

예를 들어, 유럽 서부 JSON 조각은 다음과 같습니다.

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 이 파일이 업데이트되는 시기와 IP 주소가 변경되는 시기에 대한 내용은 [다운로드 센터 페이지](https://www.microsoft.com/en-us/download/details.aspx?id=56519)의 **세부 정보** 섹션을 펼쳐보세요.

## <a name="inbound-ip-address-changes"></a>인바운드 IP 주소 변경

인바운드 IP 주소가 **변경될 수 있는 경우**는 다음과 같습니다.

- 함수 앱을 삭제하고 다른 리소스 그룹에서 다시 만듭니다.
- 리소스 그룹 및 지역 조합에서 마지막 함수 앱을 삭제하고 다시 만듭니다.
- [인증서 갱신](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates) 중인 경우와 같이 SSL 바인딩을 삭제합니다.

함수 앱이 [소비 계획](functions-scale.md#consumption-plan)에서 실행되는 경우 인바운드 IP 주소는 위에서 나열한 것과 같은 작업을 수행하지 않은 경우에도 변경될 수 있습니다.

## <a name="outbound-ip-address-changes"></a>아웃바운드 IP 주소 변경

함수 앱의 사용 가능한 아웃바운드 IP 주소 집합이 변경될 수 있는 경우는 다음과 같습니다.

* 인바운드 IP 주소를 변경할 수 있는 작업을 수행합니다.
* App Service 계획의 가격 책정 계층을 변경합니다. 모든 가격 책정 계층에 대해 앱에서 사용할 수 있는 모든 가능한 아웃바운드 IP 주소 목록은 `possibleOutboundIPAddresses` 속성에 있습니다. [아웃바운드 IP 찾기](#find-outbound-ip-addresses)를 참조하세요.

함수 앱이 [소비 계획](functions-scale.md#consumption-plan)에서 실행되는 경우 아웃바운드 IP 주소는 위에서 나열한 것과 같은 작업을 수행하지 않은 경우에도 변경될 수 있습니다.

의도적으로 아웃바운드 IP 주소를 강제로 변경하려면 다음을 수행합니다.

1. 표준 및 프리미엄 v2 가격 책정 계층 사이에서 App Service 계획의 크기를 위 또는 아래로 조정합니다.
2. 10분 동안 기다립니다.
3. 시작한 위치의 크기로 다시 조정합니다.

## <a name="ip-address-restrictions"></a>IP 주소 제한

함수 앱에 대한 액세스를 허용하거나 거부하려는 IP 주소의 목록을 구성할 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 참조하세요.

## <a name="dedicated-ip-addresses"></a>전용 IP 주소

고정 전용 IP 주소가 필요한 경우 [App Service Environment](../app-service/environment/intro.md)(App Service 계획의 [격리 계층](https://azure.microsoft.com/pricing/details/app-service/))를 사용하는 것이 좋습니다. 자세한 내용은 [App Service Environment IP 주소](../app-service/environment/network-info.md#ase-ip-addresses) 및 [App Service Environment로의 인바운드 트래픽을 제어하는 방법](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)을 참조하세요.

함수 앱이 App Service Environment에서 실행되는지 확인하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 함수 앱으로 이동합니다.
3. **개요** 탭을 선택합니다.
4. App Service 계획 계층이 **App Service 계획/가격 책정 계층** 아래에 표시됩니다. App Service Environment 가격 책정 계층은 **격리**입니다.
 
또는 [Cloud Shell](../cloud-shell/quickstart.md)을 사용할 수 있습니다.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service Environment `sku`는 `Isolated`입니다.

## <a name="next-steps"></a>다음 단계

IP는 일반적으로 함수 앱 크기 조정 변경으로 인해 변경됩니다. [함수 앱 크기 조정에 대해 자세히 알아보세요](functions-scale.md).
