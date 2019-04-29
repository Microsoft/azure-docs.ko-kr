---
title: Azure Security Center에서 App Services 보호 | Microsoft Docs
description: 이 문서는 Azure Security Center에서 App Services 보호를 시작하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: monhaber
ms.openlocfilehash: ea738535ae9326109a7c3fdd0b5d0c4f4691fdf0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095273"
---
# <a name="protect-app-service-with-azure-security-center"></a>Azure Security Center를 통해 App Service 보호
이 문서에서는 Azure Security Center를 사용하여 App Service 기반으로 실행 중인 애플리케이션을 모니터링 및 보호하는 데 도움이 됩니다.

App Service를 사용하면 인프라를 관리할 필요 없이 선택한 프로그래밍 언어로 웹 애플리케이션을 빌드하고 호스팅할 수 있습니다. App Service는 자동 크기 조정 및 고가용성을 제공하고, Windows 및 Linux를 모두 지원하며, GitHub, Azure DevOps 또는 Git 리포지토리에서 자동화된 배포를 지원합니다. 

종종 웹 애플리케이션의 취약성은 인터넷의 거의 모든 조직에 대한 공통 및 동적 인터페이스를 가지고 있으므로 공격자들이 종종 이를 악용합니다. App Service 기반으로 실행 중인 애플리케이션에 대한 요청은 각 요청의 해당 애플리케이션을 담당하는 전 세계 Azure 데이터센터에 배포된 여러 게이트웨이를 거칩니다. 

Azure Security Center는 VM 또는 주문형 인스턴스의 샌드박스에서 App Service를 실행하는 애플리케이션에 대해 평가 및 권장 사항을 실행할 수 있습니다. Security Center는 Azure가 클라우드 공급 기업으로서 보유한 가시성을 활용하여 App Service 내부 로그를 분석하여 종종 여러 대상에서 실행되는 공통적인 웹앱 공격을 모니터링합니다.

공격자가 정찰 단계에서 Azure에서 호스팅되는 여러 웹 사이트에서 취약성을 찾기 위한 검사를 수행하는 동안 Security Center는 클라우드 규모를 활용하여 App Service 애플리케이션에 대한 공격을 식별하고 신종 공격에 집중합니다. Security Center는 분석 및 기계 학습 모델을 사용하여, HTTP를 통해서든 관리 메서드를 통해서든 고객이 애플리케이션과 상호 작용하게 하는 모든 인터페이스를 다룹니다. 또한 Azure에서 당사자 서비스 형태인 Security Center는 이 PaaS에 대한 기본 계산 노드를 다루는 호스트 기반 보안 분석을 제공하는 고유한 입지에 있으므로 Security Center가 이미 악용된 웹 애플리케이션에 대한 공격을 탐지할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

App Service를 모니터 및 보호하려면 전용 머신에 연결된 App Service 플랜이 있어야 합니다. 이러한 플랜은 Basic, Standard, Premium, Isolated 또는 Linux입니다. Azure Security Center는 Free, Shared 또는 Consumption 플랜은 지원하지 않습니다. 자세한 내용은 [App Service 플랜](https://azure.microsoft.com/pricing/details/app-service/plans/)을 참조하세요.

## <a name="security-center-protection"></a>Security Center 보호

Azure Security Center는 App Service가 실행 중인 VM 인스턴스와 관리 인터페이스를 보호합니다. 또한 App Service에서 실행 중인 앱에서 송수신한 요청 및 응답을 모니터링합니다.

Security Center는 기본적으로 App Service와 통합되므로 배포 및 탑재 필요 없이 완전 자동 통합이 이루어집니다.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>App Service 모니터링 및 보호 사용

1. Azure에서 Security Center를 선택합니다.
2. **보안 정책**으로 이동하고 구독을 선택합니다.
3. 구독 행 마지막에서 **설정 편집**을 클릭합니다.
4. **가격 책정 계층** 아래에서 **App service** 행을 선택하고 플랜을 **사용**으로 토글합니다.

![앱 서비스 토글](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> 리소스 수량에 나열된 인스턴스 수는 가격 책정 계층 블레이드를 연 시점에서 활성 상태인 App service의 관련 인스턴스 수를 나타냅니다. 이 수는 선택한 크기 조정 옵션에 따라 변경될 수 있으므로 요금이 청구되는 인스턴스 수가 그에 따라 변경됩니다.

App Service에 대한 모니터링과 권장 사항을 사용하지 않도록 설정하려면 이 프로세스를 반복하고 **App Service** 플랜을 **사용 안 함**으로 토글합니다.



## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center의 모니터링 기능을 사용하는 방법을 살펴보았습니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md): Azure Security Center에서 보안 설정을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [App Services](security-center-virtual-machine-protection.md#app-services):  상태 요약을 사용하여 App Service 환경 목록을 확인합니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/): Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
