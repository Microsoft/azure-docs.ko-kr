---
title: App Service Environment v1을 구성하는 방법 - Azure
description: App Service Environment v1 구성, 관리 및 모니터링
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c0b4117f6e7b48dce1746ad6eb3dbe29c0d16af
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130622"
---
# <a name="configuring-an-app-service-environment-v1"></a>App Service Environment v1 구성

> [!NOTE]
> 이 문서는 ASE(App Service Environment) v1에 관한 내용입니다.  사용하기가 더 쉽고 더 강력한 인프라에서 실행되는 최신 버전의 App Service Environment가 있습니다. 새 버전에 대한 자세한 내용은 [App Service Environment 소개](intro.md)를 참조하세요.
> 

## <a name="overview"></a>개요
Azure App Service Environment는 크게 몇 가지 주요 구성 요소로 이루어져 있습니다.

* App Service 환경 호스티드 서비스에서 실행되는 계산 리소스
* Storage
* 데이터베이스
* 클래식(V1) 또는 Resource Manager(V2) Azure Virtual Network(VNet) 
* App Service Environment 호스티드 서비스가 실행되고 있는 서브넷

### <a name="compute-resources"></a>Compute 리소스
4개의 리소스 풀에 대해 이 계산 리소스를 사용합니다.  각 ASE(App Service Environment)에는 프런트 엔드 1개 및 가능한 작업자 풀 3개로 이루어진 집합이 있습니다. 3개의 작업자 풀을 모두 사용할 필요는 없으며 원하는 경우 하나 또는 두 개를 사용할 수 있습니다.

리소스 풀의 호스트(프런트 엔드 및 작업자)는 테넌트에 직접 액세스할 수 없습니다. 연결하거나, 해당 프로비전을 변경하거나, 관리자로 작동하는 데는 RDP(원격 데스크톱 프로토콜)를 사용할 수 없습니다.

리소스 풀 수량 및 크기를 설정할 수 있습니다. ASE에서 P1-P4 레이블이 지정된 4개의 크기 옵션이 있습니다. 해당 크기 및 가격에 대한 자세한 내용은 [App Service 가격](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.
수량 또는 크기 변경을 크기 조정 작업이라고 합니다.  한 번에 하나의 크기 조정 작업만 진행할 수 있습니다.

**프런트 엔드**: 프런트 엔드는 ASE에 보관된 앱에 대한 HTTP/HTTPS 엔드포인트입니다. 프런트 엔드에서 워크로드를 실행하지 마세요.

* ASE는 개발/테스트 워크로드 및 하위 수준 프로덕션 워크로드에 충분한 2개의 P2로 시작합니다. P3는 보통에서 많은 정도의 프로덕션 워크로드까지에 사용하는 것이 좋습니다.
* 보통에서 많은 프로덕션 워크로드의 경우 예약된 유지 관리가 발생할 때 충분한 프런트 엔드 실행이 보장되도록 P3를 4개 이상 사용하는 것이 좋습니다. 예약된 유지 관리 작업은 한 번에 하나의 프런트 엔드를 종료합니다. 따라서 유지 관리 작업 동안 전반적으로 사용 가능한 프런트 엔드 용량이 줄어듭니다.
* 프런트 엔드에서 프로비전하는 데 최대 1시간이 걸릴 수 있습니다. 
* 추가 크기 미세 조정의 경우 프런트 엔드 풀에 대해 CPU 백분율, 메모리 백분율, 활성 요청 메트릭을 모니터링합니다. P3를 실행할 때 CPU 또는 메모리 백분율이 70%를 초과하는 경우 프런트 엔드를 더 추가합니다. 활성 요청 값 평균이 프런트 엔드당 15,000~20,000개 요청이 되는 경우에도 프런트 엔드를 더 추가해야 합니다. P3를 실행할 때 전반적인 목표는 CPU 및 메모리 백분율을 70% 미만으로 유지하고 활성 요청 평균이 프런트 엔드당 P3 요청 아래가 되는 것입니다.  

**작업자**: 작업자는 앱이 실제로 실행되는 위치입니다. App Service 계획을 강화할 때 연결된 작업자 풀의 작업자를 모두 사용합니다.

* 작업자를 즉시 추가할 수 없습니다. 준비하는 데 최대 1시간이 걸릴 수 있습니다.
* 모든 풀에 대한 계산 리소스의 크기를 조정하는 데 업데이트 도메인당 1시간 미만이 걸립니다. ASE에 20개의 업데이트 도메인이 있습니다. 10개 인스턴스를 사용하여 작업자 풀의 컴퓨팅 크기를 조정하는 경우 완료하는 데 최대 10시간이 걸릴 수 있습니다.
* 작업자 풀에서 사용되는 계산 리소스의 크기를 변경할 경우 해당 작업자 풀에서 실행 중인 앱이 콜드 부팅됩니다.

앱을 실행하지 않는 작업자 풀의 계산 리소스 크기를 변경하는 가장 빠른 방법은 다음과 같습니다.

* 작업자 수를 2로 축소합니다.  포털의 최소 축소 크기는 2입니다. 인스턴스의 할당을 취소하는 데 몇 분이 걸립니다. 
* 새 컴퓨팅 크기 및 인스턴스 수를 선택합니다. 여기서 완료하는 데 최대 2시간이 걸립니다.

앱에 더 큰 계산 리소스 크기가 필요한 경우 이전 지침을 이용할 수는 없습니다. 이러한 앱을 호스트하는 작업자 풀의 크기를 변경하는 대신 원하는 크기의 작업자로 다른 작업자 풀을 채우고 해당 풀로 앱을 이동할 수 있습니다.

* 다른 작업자 풀에서 필요한 컴퓨팅 크기의 추가 인스턴스를 만듭니다. 여기서 완료하는 데 최대 1시간이 걸립니다.
* 더 큰 크기를 필요로 하는 앱을 호스트하는 App Service 계획을 새로 구성된 작업자 풀에 다시 할당합니다. 이는 완료하는 데 1분 미만이 걸려야 하는 빠른 작업입니다.  
* 이러한 사용되지 않는 인스턴스가 더 이상 필요하지 않은 경우 첫 번째 작업자 풀의 규모를 축소합니다. 이 작업을 완료하는 데 몇 분이 걸립니다.

**자동 크기 조정**: 컴퓨팅 리소스 사용을 관리하는 데 도움이 되는 도구 중 하나는 자동 크기 조정입니다. 프런트 엔드 또는 작업자 풀에 대해 자동 크기 조정을 사용할 수 있습니다. 오전에는 특정 풀 유형의 인스턴스를 늘렸다가 저녁에는 줄이는 것과 같은 작업이 가능합니다. 또는 작업자 풀에서 사용할 수 있는 작업자 수가 특정 임계값 아래로 떨어질 경우 인스턴스를 추가할 수도 있습니다.

계산 리소스 풀 메트릭을 기준으로 자동 크기 조정 규칙을 설정하려는 경우 프로비전에 필요한 시간을 고려해야 합니다. App Service Environment의 자동 크기 조정에 대한 자세한 내용은 [App Service Environment에서 자동 크기 조정을 구성하는 방법][ASEAutoscale]을 참조하세요.

### <a name="storage"></a>Storage
ASE 각각은 500GB의 저장소로 구성됩니다. 이 공간은 ASE의 모든 앱에서 사용됩니다. 이 저장소 공간은 ASE의 일부이며 현재는 사용자의 저장소 공간을 사용하도록 전환할 수 없습니다. 가상 네트워크 라우팅 또는 보안에 대해 조정하는 경우 Azure Storage에 대한 액세스를 허용하지 않으면 ASE가 작동되지 않습니다.

### <a name="database"></a>데이터베이스
데이터베이스는 내부에서 실행 중인 앱의 환경뿐만 아니라 세부 정보를 정의하는 정보를 가집니다. 이러한 기능 또한 Azure 보유 구독의 일부입니다. 사용자가 직접 조작할 수는 없는 부분입니다. 가상 네트워크 라우팅 또는 보안에 대해 조정하는 경우 SQL Azure에 대한 액세스를 허용하지 않으면 ASE가 작동되지 않습니다.

### <a name="network"></a>네트워크
ASE와 함께 사용되는 VNet은 ASE를 만들 때 만들었거나 미리 만들었던 것일 수 있습니다. ASE를 만드는 동안 서브넷을 만드는 경우 ASE가 가상 네트워크와 동일한 리소스 그룹에 있도록 강제 적용됩니다. ASE에서 사용한 리소스 그룹과 VNet의 리소스 그룹이 달라야 하는 경우 Resource Manager 템플릿을 사용하여 ASE를 만들어야 합니다.

ASE에 사용되는 가상 네트워크에 대한 몇 가지 제한 사항이 있습니다.

* 가상 네트워크는 지역 가상 네트워크여야 합니다.
* ASE가 배포되는 8개 이상의 주소가 있는 서브넷이어야 합니다.
* 서브넷을 사용하여 ASE를 호스트하면 서브넷의 주소 범위를 변경할 수 없습니다. 따라서 서브넷에는 향후 ASE 성장을 수용하도록 주소가 64개 이상 포함되는 것이 좋습니다.
* 서브넷에 ASE 외에는 아무 것도 없을 수 있습니다.

ASE를 포함하는 호스티드 서비스와 달리 [가상 네트워크][virtualnetwork] 및 서브넷은 사용자 제어 하에 있습니다.  가상 네트워크 관리는 Virtual Network UI 또는 PowerShell을 통해 수행할 수 있습니다.  ASE는 클래식 또는 Resource Manager VNet에 배포될 수 있습니다.  포털 및 API 환경은 클래식 및 Resource Manager VNet 간에 약간 차이가 있지만 ASE 환경은 동일합니다.

ASE를 호스트하는 데 사용되는 VNet은 개인 RFC 1918 IP 주소를 사용하거나 공용 IP 주소를 사용할 수 있습니다.  RFC1918에 포함되지 않는 IP 범위(10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)를 사용하려는 경우 ASE 생성 전에 ASE에서 사용할 VNet 및 서브넷을 만들어야 합니다.

이 기능은 Azure App Service를 가상 네트워크에 두기 때문에 ASE에서 호스트되는 앱이 ExpressRoute 또는 사이트 간 VPN(가상 사설망)을 통해 사용할 수 있는 리소스에 직접 액세스할 수 있습니다. App Service Environment 내의 앱은 추가 네트워킹 기능 없이도 App Service Environment를 호스트하는 가상 네트워크에서 사용할 수 있는 리소스에 액세스할 수 있습니다. 즉, VNET 통합 또는 하이브리드 연결을 사용하여 리소스를 가져오거나 가상 네트워크에 연결할 필요가 없습니다. 이러한 기능을 계속 사용하여 가상 네트워크에 연결되지 않은 네트워크의 리소스에 액세스할 수 있습니다.

예를 들어 가상 네트워크 통합을 사용하여 구독에 있는 VNET과 통합할 수 있지만 ASE에 있는 가상 네트워크에 연결되지 않습니다. 또한 하이브리드 연결을 계속 사용하여 일반적인 상황과 마찬가지로 다른 네트워크의 리소스에 액세스할 수 있습니다.  

ExpressRoute VPN을 사용하여 구성된 가상 네트워크가 없는 경우 라우팅 일부는 ASE에 있는 VNET이 필요합니다. ASE와 호환되지 않는 일부 사용자 정의 경로(UDR) 구성이 있습니다. ExpressRoute를 사용하는 가상 네트워크에서 ASE 실행에 대한 자세한 내용은 [ExpressRoute를 사용하는 가상 네트워크에서 App Service Environment 실행][ExpressRoute]을 참조하세요.

#### <a name="securing-inbound-traffic"></a>인바운드 트래픽 보안
ASE로의 인바운드 트래픽을 제어하는 기본적인 방법에는 두 가지가 있습니다.  [App Service Environment에서 인바운드 트래픽을 제어하는 방법](app-service-app-service-environment-control-inbound-traffic.md) 에 설명된 것처럼 NSG(네트워크 보안 그룹)를 사용하여 ASE에 액세스할 수 있는 IP를 제어하고, ILB(내부 부하 분산 장치)를 사용하여 ASE를 구성할 수도 있습니다.  NSG를 사용하는 액세스를 ILB ASE로 제한하려는 경우에는 이러한 기능을 함께 사용할 수 있습니다.

ASE를 만들면 VNet에 VIP가 만들어집니다.  외부 및 내부의 두 가지 VIP 유형이 있습니다.  외부 VIP를 사용하여 ASE를 만들 경우 ASE의 앱에 인터넷 라우팅 가능 IP 주소를 통해 액세스할 수 있습니다. 내부를 선택하는 경우 ASE는 ILB로 구성되고, 인터넷에 직접 액세스할 수 없습니다.  ILB ASE는 외부 VIP가 여전히 필요하지만 Azure 관리 및 유지 관리 액세스에만 사용됩니다.  

ILB ASE를 만드는 동안 ILB ASE에서 사용되는 하위 도메인을 제공하고, 지정한 하위 도메인에 대한 고유한 DNS를 관리해야 합니다.  하위 도메인 이름을 설정했으므로 HTTPS 액세스에 사용되는 인증서를 관리해야 합니다.  ASE를 만든 후에 인증서를 제공하라는 메시지가 표시됩니다.  ILB ASE 만들기 및 사용에 대한 자세한 내용은 [App Service Environment에서 내부 부하 분산 장치 사용][ILBASE]을 참조하세요. 

## <a name="portal"></a>포털
Azure Portal의 UI를 사용하여 App Service Environment를 관리하고 모니터링할 수 있습니다. ASE가 있는 경우 사이드바에서 App Service 기호를 확인할 수 있습니다. 이 기호는 Azure Portal에서 App Service Environment를 나타내는 데 사용됩니다.

![App Service Environment 기호][1]

App Service Environment를 모두 나열하는 UI를 열려면 아이콘을 사용하거나 사이드바의 맨 아래에 있는 펼침 단추(">" 기호)를 선택하여 App Service Environment를 선택할 수 있습니다. 나열된 ASE 중 하나를 선택하여 모니터링하고 관리하는 데 사용되는 UI를 엽니다.

![App Service Environment를 모니터링 및 관리하기 위한 UI][2]

이 첫 번째 블레이드는 리소스 풀 단위당 메트릭 차트와 함께 ASE의 속성 일부를 보여 줍니다. 또한 **Essentials** 블록에 표시된 속성 일부에는 연관된 블레이드를 열 수 있는 하이퍼링크가 있습니다. 예를 들어 **Virtual Network** 이름을 선택하여 ASE가 실행되는 가상 네트워크에 연결된 UI를 열 수 있습니다. **App Service 계획** 및 **앱**은 각각 ASE에 있는 이러한 항목을 나열하는 블레이드를 엽니다.  

### <a name="monitoring"></a>모니터링
차트를 사용하면 각 리소스 풀에서 다양한 성능 메트릭을 참조할 수 있습니다. 프런트 엔드 풀의 경우 평균 CPU 및 메모리를 모니터링할 수 있습니다. 작업자 풀의 경우 사용되는 수량 및 사용할 수 있는 수량을 모니터링할 수 있습니다.

여러 App Service 계획은 작업자 풀에서 작업자를 사용할 수 있습니다. 워크로드는 프런트 엔드 서버와 동일한 방식으로 배포되지 않으므로 CPU 및 메모리 사용량은 유용한 정보를 제공하지 않습니다. 특히 타인이 해당 시스템을 사용하도록 관리하려면 작업자 및 사용 가능한 작업자의 수를 추적하는 것이 중요합니다.  

또한 차트에서 추적할 수 있는 모든 메트릭은 경고를 설정하는 데 사용할 수 있습니다. 경고 설정은 App Service 내의 다른 곳에서와 동일하게 작동합니다. 또는 **경고** UI 일부를 사용하거나 메트릭 UI를 드릴인투한 후 **경고 추가**를 선택하여 경고를 설정할 수 있습니다.

![메트릭 UI][3]

방금 설명한 메트릭은 App Service Environment 메트릭입니다. 또한 App Service 계획 수준에서 메트릭을 사용할 수 있습니다. CPU 및 메모리의 모니터링이 합리적입니다.

ASE에서 모든 App Service 계획은 전용 App Service 계획입니다. 즉, 해당 App Service 계획에 할당된 호스트에서 실행 중인 유일한 앱은 해당 App Service 계획에 있는 앱입니다. App Service 계획의 세부 정보를 확인하려면 단순히 ASE UI의 목록 중 하나 또는 이들 모두를 나열하는 **App Service 계획 찾아보기** 에서 App Service 계획을 불러옵니다.   

### <a name="settings"></a>설정
ASE 블레이드 내에 몇 가지 중요한 기능을 포함하는 **설정** 섹션이 있습니다.

**설정** > **속성**: ASE 블레이드를 불러오면 **설정** 블레이드가 자동으로 열립니다. 맨 위쪽에 **속성**이 있습니다. 여기에 **Essentials**에 표시된 것과 중복되는 여러 항목이 있지만 **아웃바운드 IP 주소**뿐만 아니라 **가상 IP 주소**가 매우 유용합니다.

![설정 블레이드 및 속성][4]

**설정** > **IP 주소**: ASE에서 IP SSL(Secure Sockets Layer) 앱을 만들 때 IP SSL 주소가 필요합니다. 그렇게 하려면 ASE에서 소유하는 IP SSL 주소가 할당될 수 있어야 합니다. 이를 위해, ASE를 만들 때 1개의 IP SSL 주소가 있지만 더 추가할 수 있습니다. SSL 연결 섹션의 [App Service 가격][AppServicePricing]에서 보여 주듯이 추가 IP SSL 주소에 대한 요금이 청구됩니다. 추가 가격은 IP SSL 가격입니다.

**설정** > **프런트 엔드 풀** / **작업자 풀**: 이러한 리소스 풀 블레이드는 각각 완벽하게 해당 리소스 풀의 크기를 조정하도록 제어를 제공하는 것 외에도 해당 리소스 풀에 대한 정보를 확인하는 기능을 제공합니다.  

각 리소스 풀에 대한 기본 블레이드는 해당 리소스 풀에 대한 메트릭을 사용하여 차트를 제공합니다. ASE 블레이드의 차트와 마찬가지로 차트로 이동하고 원하는 대로 경고를 설정합니다. 특정 리소스 풀에 대한 ASE 블레이드에서 경고를 설정하는 작업은 리소스 풀에서의 작업 수행과 동일합니다. 작업자 풀 **설정** 블레이드에서 이 작업자 풀에 실행 중인 모든 앱 또는 App Service 계획에 액세스할 수 있습니다.

![작업자 풀 설정 UI][5]

### <a name="portal-scale-capabilities"></a>포털 확장 기능
세 가지 규모 작업이 있습니다.

* IP SSL 사용에 사용할 수 있는 ASE에서 IP 주소의 수 변경
* 리소스 풀에서 사용된 계산 리소스의 크기 변경.
* 수동으로 또는 자동 크기 조정을 통해 리소스 풀에 사용되는 계산 리소스의 수 변경.

포털에서 리소스 풀에 있는 서버 수를 제어하는 세 가지 방법이 있습니다.

* 위쪽의 주 ASE 블레이드에서 크기를 조정하는 작업입니다. 프런트 엔드 및 작업자 풀을 다양한 규모 구성으로 변경할 수 있습니다. 이 변경 내용은 단일 작업으로 모두 적용됩니다.
* **설정** 아래에 있는 개별 리소스 풀 **크기 조정** 블레이드 수동 크기 조정 작업
* 개별 리소스 풀 **크기 조정** 블레이드에서 설정하는 자동 크기 조정

ASE 블레이드에서 크기 조정 작업을 사용하려면 슬라이더를 원하는 수량만큼 끌어 놓고 저장합니다. 또한 이 UI는 크기 변경을 지원합니다.  

![크기 조정 UI][6]

특정 리소스 풀에서 수동 또는 자동 크기 조정 기능을 사용하려면 **설정** > **프런트 엔드 풀** / **작업자 풀**로 적절하게 이동합니다. 그런 다음 변경하려는 풀을 엽니다. **설정** > **규모 확장** 또는 **설정** > **강화**로 이동합니다. **규모 확장** 블레이드를 사용하면 인스턴스 수량을 제어할 수 있습니다. **강화** 를 사용하면 리소스 크기를 제어할 수 있습니다.  

![크기 조정 설정 UI][7]

## <a name="fault-tolerance-considerations"></a>내결함성 고려 사항
총 55개의 최대 계산 리소스를 사용하도록 App Service Environment를 구성할 수 있습니다. 55개의 계산 리소스 중 50개만 워크로드를 호스트하는 데 사용할 수 있습니다. 원인에는 두 가지가 있습니다. 최소 2개의 프런트 엔드 계산 리소스가 있습니다.  따라서 작업자 풀 할당을 지원하기 위한 리소스는 최대 53개가 됩니다. 내결함성을 제공하기 위해 다음 규칙에 따라 추가 계산 리소스를 할당해야 합니다.

* 각 작업자 풀에 워크로드를 할당하는 데 사용할 수 없는 1개 이상의 추가 계산 리소스가 필요합니다.
* 작업자 풀에 있는 계산 리소스의 양이 특정 값을 초과하는 경우 내결함성에 다른 계산 리소스가 필요합니다. 프런트 엔드 풀에는 적용되지 않습니다.

단일 작업자 풀 내에서 내결함성 요구 사항은 작업자 풀에 할당된 X개의 리소스에 대한 지정된 값입니다.

* X가 2~20인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-1입니다.
* X가 21~40인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-2입니다.
* X가 41~53인 경우 워크로드에 사용할 수 있는 계산 리소스 양은 X-3입니다.

최소한의 공간에는 2개의 프런트 엔드 서버와 2개의 작업자가 있습니다.  다음은 위의 문을 사용하여 명확히 하는 몇 가지 예제입니다.  

* 단일 풀에 30개의 작업자가 있는 경우 그 중 28개는 워크로드를 호스트하는 데 사용될 수 있습니다.
* 단일 풀에 2개의 작업자가 있는 경우 1개는 워크로드를 호스트하는 데 사용될 수 있습니다.
* 단일 풀에 20개의 작업자가 있는 경우 19개는 워크로드를 호스트하는 데 사용될 수 있습니다.  
* 여전히 단일 풀에 21개의 작업자가 있는 경우 19개는 워크로드를 호스트하는 데 사용될 수 있습니다.  

내결함성 측면은 중요하지만 특정 임계값보다 높은 눈금을 염두에 두어야 합니다. 20개의 인스턴스에서 더 많은 용량을 추가하려는 경우 21개는 더 이상 용량을 추가하지 못하므로 22개 이상을 추가합니다. 마찬가지로, 41개 이상에서는 용량을 추가하는 다음 번호가 42입니다.  

## <a name="deleting-an-app-service-environment"></a>App Service Environment 삭제
App Service Environment를 삭제하려면 App Service Environment 블레이드의 위쪽에 있는 **삭제** 작업을 사용하면 됩니다. 이렇게 하면 이 작업을 수행할지 확인하기 위해 App Service Environment의 이름을 입력하라는 메시지가 표시됩니다. App Service Environment를 삭제하는 경우 포함된 내용도 모두 삭제됩니다.  

![App Service Environment 삭제 UI][9]  

## <a name="getting-started"></a>시작
App Service Environment를 시작하려면 [App Service Environment를 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md)을 참조하세요.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
