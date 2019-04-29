---
title: Azure 네트워킹 옵션 함수
description: Azure Functions에서 사용 가능한 모든 네트워킹 옵션의 개요
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437687"
---
# <a name="azure-functions-networking-options"></a>Azure 네트워킹 옵션 함수

이 문서에서는 Azure Functions에 대 한 호스팅 옵션에서 사용할 수 있는 네트워킹 기능을 설명합니다. 다음과 같은 네트워킹 옵션의 모든 인터넷 라우팅 가능 주소를 사용 하지 않고 리소스에 액세스 하거나 함수 앱에 대 한 인터넷 액세스를 제한 하는 일부 기능을 제공 합니다. 

호스팅 모델은 서로 다른 수준의 네트워크 격리를 사용할 수 있는 경우 올바른 선택 네트워크 격리 요구 사항을 충족 하는 데 도움이 됩니다.

여러 가지 방법으로 함수 앱을 호스트할 수 있습니다.

* 다양 한 수준의 가상 네트워크 연결 및 확장 옵션을 사용 하 여 다중 테 넌 트 인프라를에서 실행 되는 계획 옵션의 집합이 있는:
    * 합니다 [소비 계획](functions-scale.md#consumption-plan), 부하에 동적으로 조정 하며 최소한의 네트워크 격리 옵션을 제공 합니다.
    * 합니다 [프리미엄 요금제](functions-scale.md#premium-plan-public-preview), 또한 확장 되는 동적으로 보다 포괄적인 네트워크 격리를 제공 하는 동안.
    * Azure [App Service 계획](functions-scale.md#app-service-plan), 고정된 크기에서 작동 하며 프리미엄 요금제에 유사한 네트워크 격리를 제공 합니다.
* 함수를 실행할 수 있습니다는 [App Service Environment](../app-service/environment/intro.md)합니다. 이 메서드는 가상 네트워크에 함수를 배포 하 고 전체 네트워크 제어 및 격리를 제공 합니다.

## <a name="matrix-of-networking-features"></a>네트워킹 기능 매트릭스

|                |[소비 계획](functions-scale.md#consumption-plan)|⚠ [프리미엄 요금제](functions-scale.md#premium-plan-public-preview)|[App Service 계획](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[인바운드 IP 제한](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[가상 네트워크 통합](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[가상 네트워크 통합 (Azure ExpressRoute 및 서비스 끝점)의 미리 보기](#preview-version-of-virtual-network-integration)|❌No|⚠예|⚠예|✅Yes|
|[VNet](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[개인 사이트 액세스](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ 이 미리 보기 기능을 사용 하 여 프로덕션에 없습니다.

## <a name="inbound-ip-restrictions"></a>인바운드 IP 제한

우선 순위에 따른 IP 주소 목록을 허용/거부 된 앱에 대 한 액세스를 정의 하려면 IP 제한을 사용할 수 있습니다. 목록에는 IPv4 및 IPv6 주소를 포함할 수 있습니다. 하나 이상의 항목의 경우 목록 끝에 있는 암시적 "모두 거부"입니다. IP 제한은 모든 함수 호스팅 옵션을 사용 하 여 작동합니다.

> [!NOTE]
> Azure portal 편집기를 사용 하려면 포털을 실행 중인 함수 앱에 직접 액세스할 수 있어야 합니다. 또한 포털에 액세스 하려면 사용 중인 장치에 해당 IP 허용 목록에 있어야 합니다. 네트워크 제한 사항이 사용 하 여 액세스할 수 있습니다 기능에는 **플랫폼 기능** 탭 합니다.

자세한 내용은 참조 하세요 [Azure App Service에 정적 액세스 제한](../app-service/app-service-ip-restrictions.md)합니다.

## <a name="virtual-network-integration"></a>가상 네트워크 통합

가상 네트워크 통합 함수 앱을 가상 네트워크 내의 리소스에 액세스할 수 있습니다. 이 기능은 프리미엄 요금제와 App Service 계획에서 사용할 수 있습니다. App Service Environment의 앱 인 경우 가상 네트워크를 이미 있고 동일한 가상 네트워크의 리소스를 연결할 가상 네트워크 통합을 사용 하 여 필요 하지 않습니다.

가상 네트워크 통합 가상 네트워크의 리소스에 함수 앱 액세스를 부여 하지만 허용 하지 않습니다 [개인 사이트 액세스](#private-site-access) 가상 네트워크에서 함수 앱으로 합니다.

앱에서 데이터베이스 및 가상 네트워크에서 실행 되는 웹 서비스에 액세스할 수 있도록 가상 네트워크 통합을 사용할 수 있습니다. 가상 네트워크 통합을 사용 하 여 VM의 응용 프로그램에 대 한 공개 끝점을 노출할 필요가 없습니다. 개인, 비 인터넷 라우팅 가능 주소를 대신 사용할 수 있습니다.

Virtual network 통합의 일반 공급 버전 함수 앱을 가상 네트워크에 연결 하는 VPN gateway에 의존 합니다. App Service 계획에서 호스트 하는 함수에 제공 됩니다. 이 기능을 구성 하는 방법에 알아보려면 참조 [Azure virtual network와 앱 통합](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration)합니다.

### <a name="preview-version-of-virtual-network-integration"></a>가상 네트워크 통합 미리 보기 버전

새 버전의 가상 네트워크 통합 기능이 미리 보기로 제공에서 됩니다. 지점-사이트 간 VPN에 종속 되지 않기입니다. ExpressRoute에서 리소스에 액세스를 지원 또는 서비스 끝점입니다. PremiumV2를 확장 하는 App Service 계획 및 프리미엄 요금제에서 제공 됩니다.

다음은이 버전의 일부 특성입니다.

* 게이트웨이를 사용할 필요가 없습니다.
* ExpressRoute가 연결된 가상 네트워크와의 통합을 제외하고는 추가 구성 없이 ExpressRoute 연결을 통해 리소스에 액세스할 수 있습니다.
* 함수 실행 리소스 서비스 끝점 보호를 사용할 수 있습니다. 이렇게 하려면 가상 네트워크 통합을 위해 사용 하는 서브넷에서 서비스 끝점을 사용 하도록 설정 합니다.
* 서비스 끝점 보안 리소스를 사용 하는 트리거를 구성할 수 없습니다. 
* 함수 앱 및 가상 네트워크 모두 동일한 지역에 있어야 합니다.
* 새로운 기능에는 Azure Resource Manager를 통해 배포 된 가상 네트워크에 서브넷을 사용 하지 않는 필요 합니다.
* 기능 미리 보기 상태인 동안에 프로덕션 워크 로드 지원 되지 않습니다.
* 경로 테이블 및 전역 피어 링 아직 사용할 수 없는 기능을 사용 하 여 합니다.
* 함수 앱의 잠재적인 인스턴스마다 하나의 주소 사용 됩니다. 할당 후 서브넷 크기를 변경할 수 없으므로, 최대 배율 크기를 쉽게 지원할 수 있는 서브넷을 사용 합니다. 예를 들어, 80 인스턴스로 확장할 수 있는 프리미엄 계획을 지원 하려면 것이 좋습니다는 `/25` 126 호스트 주소를 제공 하는 서브넷입니다.

가상 네트워크 통합 미리 보기 버전을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure virtual network를 사용 하 여 함수 앱을 통합](functions-create-vnet.md)합니다.

## <a name="hybrid-connections"></a>하이브리드 연결

[하이브리드 연결](../service-bus-relay/relay-hybrid-connections-protocol.md) 다른 네트워크의 응용 프로그램 리소스에 액세스 하는 데 사용할 수 있는 Azure Relay의 기능입니다. 이를 통해 앱에서 애플리케이션 엔드포인트에 액세스할 수 있습니다. 응용 프로그램 액세스에 사용할 수 없습니다. 하이브리드 연결이에서 실행 되는 함수에 사용할 수는 [App Service 계획](functions-scale.md#app-service-plan) 와 [App Service Environment](../app-service/environment/intro.md)합니다.

Azure Functions를 사용 하는 대로 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합에 상호 연결 합니다. 즉, 모든 운영 체제 및 모든 응용 프로그램에 하이브리드 연결 끝점 수 있는 TCP 수신 대기 포트에 액세스할 때는으로 합니다. 하이브리드 연결 기능 알고 또는 응용 프로그램 프로토콜이 무엇 인지 또는 액세스 하는 중 처리 하지 않습니다. 네트워크 액세스를 제공 하기만 하면 됩니다.

자세한 내용은 참조는 [하이브리드 연결에 대 한 App Service 설명서](../app-service/app-service-hybrid-connections.md), App Service 계획에서 함수를 지 합니다.

## <a name="private-site-access"></a>개인 사이트 액세스

개인 사이트 액세스 로부터 개인 네트워크와 같은 Azure virtual network 내 에서만에서 액세스할 수 있는 앱을 만드는 가리킵니다. 개인 사이트 액세스는 내부 부하 분산 장치 (ILB)를 사용 하 여 구성 된 App Service Environment에만 사용할 수 있습니다. 자세한 내용은 [만들기 및 App Service Environment를 사용 하 여 내부 부하 분산 장치를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.

여러 가지 방법으로 다른 호스팅 옵션에 대 한 가상 네트워크 리소스에 액세스할 수 있습니다. 하지만 App Service Environment 가상 네트워크를 통해 발생 하는 함수에 대 한 트리거를 허용 하는 유일한 방법은 됩니다.

## <a name="next-steps"></a>다음 단계
네트워킹 및 Azure Functions에 대 한 자세한 내용을 보려면: 

* [가상 네트워크 통합을 사용 하 여 시작 하는 방법에 대 한 자습서를 수행 합니다.](./functions-create-vnet.md)
* [읽기 네트워킹 FAQ 함수](./functions-networking-faq.md)
* [가상 네트워크 통합 된 앱 서비스/기능에 자세히 알아보기](../app-service/web-sites-integrate-with-vnet.md)
* [Azure에서 가상 네트워크에 자세히 알아보기](../virtual-network/virtual-networks-overview.md)
* [추가 네트워킹 기능 및 App Service Environment를 사용 하 여 컨트롤을 사용 하도록 설정](../app-service/environment/intro.md)
* [하이브리드 연결을 사용 하 여 방화벽 변경 없이 개별 온-프레미스 리소스에 연결](../app-service/app-service-hybrid-connections.md)
