---
title: Azure Functions 네트워킹 옵션
description: Azure Functions에서 사용할 수 있는 모든 네트워킹 옵션에 대 한 개요입니다.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 7b47e7b0672716141f62e3f7df4b0d3ed95c663d
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114285"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 네트워킹 옵션

이 문서에서는 Azure Functions에 대 한 호스팅 옵션에서 사용할 수 있는 네트워킹 기능을 설명 합니다. 다음 모든 네트워킹 옵션을 사용 하면 인터넷에서 라우팅할 수 있는 주소를 사용 하지 않고 리소스에 액세스 하거나 인터넷에서 함수 앱에 액세스할 수 없도록 제한할 수 있습니다.

호스팅 모델은 서로 다른 수준의 네트워크 격리를 사용할 수 있습니다. 올바른 항목을 선택 하면 네트워크 격리 요구 사항을 충족 하는 데 도움이 됩니다.

다음 두 가지 방법으로 함수 앱을 호스트할 수 있습니다.

* 다양 한 수준의 가상 네트워크 연결 및 확장 옵션을 사용 하 여 다중 테 넌 트 인프라에서 실행 되는 계획 옵션 집합이 있습니다.
    * [소비 계획](functions-scale.md#consumption-plan)-로드에 대 한 응답으로 동적으로 확장 되 고 최소 네트워크 격리 옵션을 제공 합니다.
    * 더 포괄적인 네트워크 격리를 제공 하면서도 동적으로 확장 되는 [프리미엄 요금제](functions-scale.md#premium-plan).
    * Azure [App Service 요금제](functions-scale.md#app-service-plan)는 고정 된 규모에서 작동 하 고 프리미엄 요금제와 유사한 네트워크 격리를 제공 합니다.
* [App Service Environment](../app-service/environment/intro.md)에서 함수를 실행할 수 있습니다. 이 메서드는 가상 네트워크에 함수를 배포 하 고 전체 네트워크 제어 및 격리를 제공 합니다.

## <a name="matrix-of-networking-features"></a>네트워킹 기능 매트릭스

|                |[소비 계획](functions-scale.md#consumption-plan)|[프리미엄 요금제](functions-scale.md#premium-plan)|[App Service 계획](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[인바운드 IP 제한 & 개인 사이트 액세스](#inbound-ip-restrictions)|예 ✅|예 ✅|예 ✅|예 ✅|
|[가상 네트워크 통합](#virtual-network-integration)|❌아니요|✅예 (지역)|✅예 (지역 및 게이트웨이)|예 ✅|
|[가상 네트워크 트리거 (비 HTTP)](#virtual-network-triggers-non-http)|❌아니요| 예 ✅ |예 ✅|예 ✅|
|[하이브리드 연결](#hybrid-connections) (Windows에만 해당)|❌아니요|예 ✅|예 ✅|예 ✅|
|[아웃 바운드 IP 제한](#outbound-ip-restrictions)|❌아니요| ❌아니요|❌아니요|예 ✅|

## <a name="inbound-ip-restrictions"></a>인바운드 IP 제한

IP 제한을 사용 하 여 앱에 대 한 액세스가 허용 되거나 거부 되는 IP 주소의 우선 순위를 지정 하는 목록을 정의할 수 있습니다. 목록은 IPv4 및 IPv6 주소를 포함할 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 암시적 "deny all"이 있습니다. IP 제한은 모든 함수 호스팅 옵션에서 작동 합니다.

> [!NOTE]
> 네트워크 제한을 사용 하는 경우 가상 네트워크 내 에서만 포털 편집기를 사용 하거나, 사용 중인 컴퓨터의 IP 주소를 안전 받는 사람 목록에서 Azure Portal에 액세스 하는 데 사용할 수 있습니다. 그러나 모든 컴퓨터에서 **플랫폼 기능** 탭의 모든 기능에 액세스할 수 있습니다.

자세히 알아보려면 [Azure App Service 고정 액세스 제한](../app-service/app-service-ip-restrictions.md)을 참조 하세요.

## <a name="private-site-access"></a>프라이빗 사이트 액세스

개인 사이트 액세스는 Azure virtual network와 같은 개인 네트워크 에서만 앱에 액세스할 수 있도록 하는 것을 말합니다.

* 개인 사이트 액세스는 서비스 끝점이 구성 될 때 [프리미엄](./functions-premium-plan.md), [소비](functions-scale.md#consumption-plan)및 [App Service](functions-scale.md#app-service-plan) 계획에서 사용할 수 있습니다.
    * 서비스 끝점은 앱 별로 구성 될 수 **있습니다 > ** **네트워킹** > **액세스 제한 구성** > **규칙 추가**를 구성 합니다. 이제 가상 네트워크를 규칙 유형으로 선택할 수 있습니다.
    * 자세한 내용은 [가상 네트워크 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)을 참조 하세요.
    * 서비스 끝점을 사용 하는 경우 가상 네트워크 통합이 구성 된 경우에도 함수에서 인터넷에 대 한 전체 아웃 바운드 액세스를 계속 유지 합니다.
* 개인 사이트 액세스는 ILB (내부 부하 분산 장치)를 사용 하 여 구성 된 App Service Environment 내 에서도 사용할 수 있습니다. 자세한 내용은 [App Service Environment를 사용 하 여 내부 부하 분산 장치 만들기 및 사용](../app-service/environment/create-ilb-ase.md)을 참조 하세요.

## <a name="virtual-network-integration"></a>가상 네트워크 통합

가상 네트워크 통합을 사용 하면 함수 앱이 가상 네트워크 내의 리소스에 액세스할 수 있습니다. 이 기능은 프리미엄 요금제와 App Service 계획에서 모두 사용할 수 있습니다. 앱이 App Service Environment에 있는 경우 이미 가상 네트워크에 있으며 가상 네트워크를 통합 하 여 동일한 가상 네트워크에 있는 리소스에 연결할 필요가 없습니다.

가상 네트워크 통합을 사용 하 여 가상 네트워크에서 실행 되는 데이터베이스 및 웹 서비스에 대 한 앱 액세스를 설정할 수 있습니다. 가상 네트워크 통합을 사용 하면 VM의 응용 프로그램에 대 한 공용 끝점을 노출할 필요가 없습니다. 대신 개인, 비 인터넷 라우팅 가능 주소를 사용할 수 있습니다.

가상 네트워크 통합에는 다음과 같은 두 가지 형식이 있습니다.

+ **지역 가상 네트워크 통합 (미리 보기)** : 동일한 지역의 가상 네트워크와 통합할 수 있습니다. 이 유형의 통합에는 동일한 지역의 가상 네트워크에 있는 서브넷이 필요 합니다. 이 기능은 아직 미리 보기 상태 이지만, Windows에서 실행 되는 함수 앱에 대해 지원 되며, 다음 문제/해결 방법 테이블 뒤에 설명 된 주의 사항이 있습니다.
+ **게이트웨이 필요 가상 네트워크 통합**: 원격 지역의 가상 네트워크 또는 클래식 가상 네트워크와 통합할 수 있습니다. 이러한 유형의 통합을 위해서는 VNet에 가상 네트워크 게이트웨이를 배포 해야 합니다. 이는 Windows에서 실행 되는 함수 앱에 대해서만 지원 되는 지점 및 사이트 간 VPN 기반 기능입니다.

앱은 한 번에 한 가지 유형의 가상 네트워크 통합 기능만 사용할 수 있습니다. 여러 시나리오에는 두 가지 모두 유용 하지만 다음 표에서는 각각을 사용 해야 하는 위치를 나타냅니다.

| 문제  | 해결 방법 |
|----------|----------|
| 동일한 지역에서 RFC 1918 주소 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)에 도달 하려고 합니다. | 지역 가상 네트워크 통합 |
| 클래식 가상 네트워크 또는 다른 지역의 가상 네트워크에 있는 리소스에 연결 하려고 합니다. | 게이트웨이는 가상 네트워크 통합 필요 |
| Azure Express 경로에서 RFC 1918 끝점에 도달 하려고 합니다. | 지역 가상 네트워크 통합 |
| 서비스 끝점에서 리소스에 연결 하려고 합니다. | 지역 가상 네트워크 통합 |

두 기능 모두 Express 경로에서 비 RFC 1918 주소에 도달할 수 있습니다. 이렇게 하려면 현재 App Service Environment를 사용 해야 합니다.

지역 가상 네트워크 통합을 사용 하면 가상 네트워크를 온-프레미스 끝점에 연결 하거나 서비스 끝점을 구성할 수 없습니다. 이는 별도의 네트워킹 구성입니다. 지역 가상 네트워크 통합을 사용 하면 응용 프로그램에서 이러한 연결 형식에 대 한 호출을 수행할 수 있습니다.

사용 된 버전에 관계 없이 가상 네트워크 통합은 함수 앱에 가상 네트워크의 리소스에 대 한 액세스를 제공 하지만 가상 네트워크에서 함수 앱에 대 한 개인 사이트 액세스를 부여 하지 않습니다. 개인 사이트 액세스는 Azure virtual network와 같은 개인 네트워크 에서만 앱에 액세스할 수 있도록 하는 것을 의미 합니다. 가상 네트워크 통합은 앱에서 가상 네트워크로 아웃 바운드 호출을 수행 하기 위한 것입니다.

가상 네트워크 통합 기능:

* Standard, Premium 또는 PremiumV2 App Service 계획이 필요 합니다.
* TCP 및 UDP를 지원 합니다.
* App Service 앱 및 함수 앱에서 작동

다음을 포함 하 여 가상 네트워크 통합에서 지원 하지 않는 몇 가지 항목이 있습니다.

* 드라이브 탑재
* Active Directory 통합
* NetBIOS

Azure Functions의 가상 네트워크 통합은 App Service 웹 앱과 공유 인프라를 사용 합니다. 두 가지 유형의 가상 네트워크 통합에 대해 자세히 알아보려면 다음을 참조 하세요.

* [지역 가상 네트워크 통합](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [게이트웨이는 가상 네트워크 통합 필요](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

가상 네트워크 통합을 사용 하는 방법에 대 한 자세한 내용은 [Azure 가상 네트워크와 함수 앱 통합](functions-create-vnet.md)을 참조 하세요.

## <a name="connecting-to-service-endpoint-secured-resources"></a>서비스 끝점 보안 리소스에 연결

> [!NOTE]
> 지금은 다운스트림 리소스에 대 한 액세스 제한을 구성한 후 새 서비스 끝점을 함수 앱에서 사용할 수 있게 되는 데 최대 12 시간이 걸릴 수 있습니다. 이 시간 동안 앱에서 리소스를 완전히 사용할 수 없게 됩니다.

높은 수준의 보안을 제공 하기 위해 서비스 끝점을 사용 하 여 여러 Azure 서비스를 가상 네트워크로 제한할 수 있습니다. 그런 다음 해당 가상 네트워크와 함수 앱을 통합 하 여 리소스에 액세스 해야 합니다. 이 구성은 가상 네트워크 통합을 지 원하는 모든 요금제에서 지원 됩니다.

[가상 네트워크 서비스 끝점에 대해 자세히 알아보세요.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>가상 네트워크에 대 한 저장소 계정 제한

함수 앱을 만들 때 Blob, 큐 및 테이블 저장소를 지 원하는 범용 Azure Storage 계정을 만들거나 연결 해야 합니다. 현재이 계정에 대 한 가상 네트워크 제한을 사용할 수 없습니다. 함수 앱에 사용 하는 저장소 계정에서 가상 네트워크 서비스 끝점을 구성 하는 경우 앱이 중단 됩니다. 이 기능은 현재 프리미엄 계획 및 가상 네트워크 통합을 사용 하 여 사용할 수 있습니다.

[저장소 계정 요구 사항에 대해 자세히 알아보세요.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Key Vault 참조 사용 

Key Vault 참조를 사용 하면 코드를 변경할 필요 없이 Azure Functions 응용 프로그램에서 Azure Key Vault의 암호를 사용할 수 있습니다. Azure Key Vault는 중앙 집중화 된 비밀 관리를 제공 하 고 액세스 정책 및 감사 기록에 대 한 모든 권한을 제공 하는 서비스입니다.

Key Vault 서비스 끝점을 사용 하 여 보호 되는 경우 현재 [Key Vault 참조가](../app-service/app-service-key-vault-references.md) 작동 하지 않습니다. 가상 네트워크 통합을 사용 하 여 Key Vault에 연결 하려면 응용 프로그램 코드에서 Key Vault를 호출 해야 합니다.

## <a name="virtual-network-triggers-non-http"></a>가상 네트워크 트리거 (비 HTTP)

현재 다음 두 가지 방법 중 하나로 가상 네트워크 내에서 HTTP가 아닌 트리거 함수를 사용할 수 있습니다. 
+ 프리미엄 계획에서 함수 앱을 실행 하 고 가상 네트워크 트리거 지원을 사용 하도록 설정 합니다.
+ App Service 계획 또는 App Service Environment에서 함수 앱을 실행 합니다.

### <a name="premium-plan-with-virtual-network-triggers"></a>가상 네트워크 트리거를 사용 하는 프리미엄 계획

프리미엄 계획에서 실행 하는 경우 HTTP가 아닌 트리거 함수를 가상 네트워크 내에서 실행 되는 서비스에 연결할 수 있습니다. 이렇게 하려면 함수 앱에 대 한 가상 네트워크 트리거 지원을 사용 하도록 설정 해야 합니다. **가상 네트워크 트리거 지원** 설정은 **함수 앱 설정**아래 [Azure Portal](https://portal.azure.com) 에 있습니다.

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

다음 Azure CLI 명령을 사용 하 여 가상 네트워크 트리거를 사용 하도록 설정할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

가상 네트워크 트리거는 함수 런타임의 버전 2.x 이상에서 지원 됩니다. 다음과 같은 HTTP가 아닌 트리거 형식이 지원 됩니다.

| 내선 번호 | 최소 버전 |
|-----------|---------| 
|[WebJobs. 확장명](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 이상 |
|[WebJobs. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 이상|
|[WebJobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 이상|
|[WebJobs. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 이상|
|[WebJobs. Microsoft.azure.webjobs.extensions.durabletask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 이상|

> [!IMPORTANT]
> 가상 네트워크 트리거 지원을 사용 하도록 설정 하면 위의 트리거 유형만 응용 프로그램을 사용 하 여 동적으로 확장 됩니다. 위에 나열 되지 않은 트리거는 계속 사용할 수 있지만 준비 인스턴스 개수 이상으로 크기가 조정 되지 않습니다. 트리거의 전체 목록은 [트리거 및 바인딩](./functions-triggers-bindings.md#supported-bindings) 을 참조 하세요.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>가상 네트워크 트리거를 사용 하 여 계획 및 App Service Environment App Service

함수 앱이 App Service 계획 또는 App Service Environment에서 실행 되는 경우 HTTP가 아닌 트리거 함수를 사용할 수 있습니다. 함수가 올바르게 트리거되기 위해서는 트리거 연결에 정의 된 리소스에 대 한 액세스 권한이 있는 가상 네트워크에 연결 되어 있어야 합니다. 

예를 들어 가상 네트워크의 트래픽만 허용 하도록 Azure Cosmos DB를 구성 하려는 경우를 가정해 보겠습니다. 이 경우 해당 가상 네트워크와 가상 네트워크 통합을 제공 하는 App Service 계획으로 함수 앱을 배포 해야 합니다. 이렇게 하면 해당 Azure Cosmos DB 리소스가 함수를 트리거할 수 있습니다. 

## <a name="hybrid-connections"></a>하이브리드 연결

[하이브리드 연결](../service-bus-relay/relay-hybrid-connections-protocol.md) 는 다른 네트워크의 응용 프로그램 리소스에 액세스 하는 데 사용할 수 있는 Azure Relay의 기능입니다. 이를 통해 앱에서 애플리케이션 엔드포인트에 액세스할 수 있습니다. 응용 프로그램에 액세스 하는 데 사용할 수 없습니다. 하이브리드 연결는 Windows에서 실행 되는 모든 기능에 사용할 수 있습니다.

Azure Functions에서 사용 되는 것 처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합과 관련이 있습니다. 즉, TCP 수신 대기 포트에 액세스 하는 동안에는 하이브리드 연결의 끝점이 모든 운영 체제 및 응용 프로그램에 있을 수 있습니다. 하이브리드 연결 기능은 응용 프로그램 프로토콜이 무엇 인지 또는 사용자가 액세스 하 고 있는지 인식 하지 못합니다. 네트워크 액세스만 제공 합니다.

자세히 알아보려면 [하이브리드 연결에 대 한 App Service 설명서](../app-service/app-service-hybrid-connections.md)를 참조 하세요. 이러한 동일한 구성 단계는 Azure Functions을 지원 합니다.

>[!IMPORTANT]
> 하이브리드 연결은 Windows 요금제 에서만 지원 됩니다. Linux는 지원 되지 않습니다.

## <a name="outbound-ip-restrictions"></a>아웃 바운드 IP 제한

아웃 바운드 IP 제한은 App Service Environment에 배포 된 함수에만 사용할 수 있습니다. App Service Environment 배포 되는 가상 네트워크에 대 한 아웃 바운드 제한을 구성할 수 있습니다.

프리미엄 계획의 함수 앱 또는 가상 네트워크와 App Service 계획을 통합 하는 경우 앱은 여전히 인터넷에 대 한 아웃 바운드 호출을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

네트워킹 및 Azure Functions에 대 한 자세한 내용은 다음을 확인 하세요.

* [Virtual network 통합 시작에 대 한 자습서를 따르세요.](./functions-create-vnet.md)
* [기능 네트워킹 FAQ를 참조 하십시오.](./functions-networking-faq.md)
* [App Service/함수와의 가상 네트워크 통합에 대해 자세히 알아보기](../app-service/web-sites-integrate-with-vnet.md)
* [Azure의 가상 네트워크에 대 한 자세한 정보](../virtual-network/virtual-networks-overview.md)
* [추가 네트워킹 기능을 사용 하 고 App Service 환경 제어](../app-service/environment/intro.md)
* [하이브리드 연결를 사용 하 여 방화벽 변경 없이 개별 온-프레미스 리소스에 연결](../app-service/app-service-hybrid-connections.md)
