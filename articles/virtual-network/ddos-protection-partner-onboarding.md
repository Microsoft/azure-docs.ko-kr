---
title: Azure DDoS Protection 표준과 파트너
description: Azure DDoS Protection Standard에서 사용 하도록 설정 된 파트너 기회를 이해 합니다.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849683"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Azure DDoS Protection 표준과 파트너
이 문서에서는 Azure DDoS Protection 표준에서 사용 하도록 설정 된 파트너 기회에 대해 설명 합니다. 이 문서는 제품 관리자 및 비즈니스 개발 역할이 투자 경로를 이해 하 고 파트너 가치 제안을 이해 하는 데 도움을 주기 위해 작성 되었습니다.

## <a name="background"></a>배경
DDoS (배포 된 서비스 거부) 공격은 고객이 응용 프로그램을 클라우드로 이동 하는 표명의 최고 가용성 및 보안 문제 중 하나입니다. Extortion과 hacktivism를 사용 하면 DDoS 공격을 통해 일반적인 동기를 유지 하 고, 비교적 쉽고 빠르게 시작할 수 있으므로 유형, 규모 및 발생 빈도가 지속적으로 늘어납니다.

Azure DDoS Protection는 Azure 네트워킹의 글로벌 규모를 활용 하 여 가장 정교한 DDoS 위협에 대 한 대책을 제공 합니다. 이 서비스는 가상 네트워크에 배포 된 응용 프로그램 및 리소스에 대해 향상 된 DDoS 완화 기능을 제공 합니다.

기술 파트너는 DDoS 공격으로 인 한 가용성 및 안정성 문제를 해결 하기 위해 기본적으로 Azure DDoS Protection Standard를 사용 하 여 고객의 리소스를 보호할 수 있습니다.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS Protection Standard 소개
Azure DDoS Protection Standard는 계층 3 및 계층 4 DDoS 공격에 대해 향상 된 DDoS 완화 기능을 제공 합니다. 다음은 DDoS Protection Standard 서비스의 주요 기능입니다.

### <a name="adaptive-real-time-tuning"></a>적응형 실시간 조정
모든 보호 된 응용 프로그램에 대해 Azure DDoS Protection Standard는 응용 프로그램의 트래픽 프로필 패턴을 기반으로 DDoS 완화 정책 임계값을 자동으로 조정 합니다. 서비스에서는 두 개의 정보를 사용하여 이 사용자 지정을 수행합니다.

- 레이어 3 및 4의 고객별(IP별) 트래픽 패턴 자동 학습
- Azure의 크기 조정을 통해 트래픽의 상당 부분을 흡수할 수 있음을 고려하여 거짓 긍정 최소화

![적응 실시간 튜닝](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>공격 분석, 원격 분석, 모니터링 및 경고
Azure DDoS Protection은 사용자 개입 없이 DDoS 공격을 식별 하 고 완화 합니다.

- 보호 된 리소스가 Azure Security Center에서 설명 하는 구독에 있는 경우 DDoS Protection 표준에서는 DDoS 공격이 감지 되 고 보호 된 응용 프로그램에 대해 완화 될 때마다 Security Center에 경고를 자동으로 보냅니다.
- 또는 보호 된 공용 IP에 대 한 활성 완화가 있는 경우 알림을 받으려면 DDoS attack에서 메트릭에 대 한 [경고를 구성할](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) 수 있습니다.
- 또한 다른 DDoS 메트릭에 대 한 경고를 생성 하 고 공격 범위, 손실 되는 트래픽, 공격 벡터, 상위 참가자 및 기타 세부 정보를 이해 하도록 [공격 분석을 구성할](manage-ddos-protection.md#configure-ddos-attack-analytics) 수 있습니다.

![DDoS 메트릭](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DRR (DDoS 신속한 응답)
DDoS Protection 표준 고객은 활성 공격 동안 [신속한 대응 팀](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) 에 액세스할 수 있습니다. DRR은 공격 조사, 공격 중 사용자 지정 완화 및 공격 후 분석에 도움이 됩니다.

### <a name="sla-guarantee-and-cost-protection"></a>SLA 보장 및 비용 보호
DDoS Protection Standard 서비스는 99.99% SLA를 적용 하며, 비용 보호는 문서화 된 공격 중 규모 확장을 위한 리소스 크레딧을 제공 합니다. 자세한 내용은 [Azure DDoS Protection에 대 한 SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)를 참조 하세요.

## <a name="featured-partner-scenarios"></a>주요 파트너 시나리오
다음은 Azure DDoS Protection 표준과 통합 하 여 얻을 수 있는 주요 이점입니다.

- 파트너의 제공 서비스 (부하 분산 장치, 웹 응용 프로그램 방화벽, 방화벽 등)는 백 엔드에서 Azure DDoS Protection Standard에 의해 자동으로 보호 됩니다 (레이블).
- 파트너는 Azure DDoS Protection 표준 공격 분석과 원격 분석에 대 한 액세스를 제공 하 여 통합 된 고객 환경을 제공 하는 자체 제품과 통합할 수 있습니다.  
- 파트너는 DDoS 관련 문제에 대 한 Azure 빠른 응답이 없을 때도 신속한 응답 지원을 DDoS 수 있습니다.
- 파트너의 보호 된 응용 프로그램은 DDoS 공격의 경우 DDoS SLA 보장 및 비용 보호를 통해 지원 됩니다.

## <a name="technical-integration-overview"></a>기술 통합 개요
Azure DDoS Protection 표준 파트너 기회는 Azure Portal, Api 및 CLI/PS를 통해 제공 됩니다.

### <a name="integrate-with-ddos-protection-standard"></a>DDoS Protection 표준과 통합
파트너가 Azure DDoS Protection 표준과의 통합을 구성 하려면 다음 단계를 수행 해야 합니다.
1. 원하는 (파트너) 구독에 DDoS Protection 계획을 만듭니다. 단계별 지침은 [DDoS 표준 보호 계획 만들기](manage-ddos-protection.md#create-a-ddos-protection-plan)를 참조 하세요.
   > [!NOTE]
   > 지정 된 테넌트에 대해 1 개의 DDoS Protection 계획만 만들어야 합니다. 
2. 부하 분산 장치, 방화벽, 웹 응용 프로그램 방화벽 등의 파트너 구독에 공용 엔드포인트을 사용 하 여 서비스를 배포 합니다. 
3. 첫 번째 단계에서 만든 DDoS Protection 계획을 사용 하 여 공용 엔드포인트이 있는 서비스의 가상 네트워크에서 Azure DDoS Protection Standard를 사용 하도록 설정 합니다. Stpe 단계별 지침은 [Enable DDoS Standard Protection plan](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network) 항목을 참조 하세요.
   > [!IMPORTANT] 
   > 가상 네트워크에서 Azure DDoS Protection Standard를 사용 하도록 설정 하면 해당 가상 네트워크 내의 모든 공용 Ip가 자동으로 보호 됩니다. 이러한 공용 Ip의 원본은 Azure (클라이언트 구독) 또는 Azure 외부에 있을 수 있습니다. 
4. 필요에 따라 응용 프로그램별 고객 관련 대시보드의 표준 원격 분석과 공격 분석 Azure DDoS Protection 통합 합니다. 원격 분석 사용에 대 한 자세한 내용은 [DDoS Protection 원격 분석 사용](manage-ddos-protection.md#use-ddos-protection-telemetry)을 참조 하세요. 공격 분석을 구성 하는 방법에 대 한 자세한 내용은 [DDoS 공격 분석 구성](manage-ddos-protection.md#configure-ddos-attack-analytics) 을 참조 하세요.

### <a name="onboarding-guides-and-technical-documentation"></a>온 보 딩 가이드 및 기술 문서

- [Azure DDoS Protection 제품 페이지](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection 설명서](ddos-protection-overview.md)
- [Azure DDoS Protection API 참조](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 가상 네트워크 API 참조](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>도움 받기

- Azure DDoS Protection 표준과 응용 프로그램, 서비스 또는 제품 통합에 대 한 질문이 있는 경우 [Azure 보안 커뮤니티](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)에 연락 하세요.
- [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/)에 대 한 토론을 따르세요.

### <a name="get-to-market"></a>시장 출시

- Microsoft와 파트너 관계를 위한 기본 프로그램은 [Microsoft 파트너 네트워크](https://partner.microsoft.com/)입니다. – Microsoft Graph 보안 통합은 [ISV (MPN 독립 소프트웨어 공급 업체)](https://partner.microsoft.com/saas-solution-guide) 트랙에 속합니다.
- Microsoft [Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) 은 Microsoft 보안 파트너를 위한 특수 프로그램으로, 보안 제품을 보강 하 고 microsoft 보안 제품에 대 한 통합의 고객 검색 기능을 개선 하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계
기존 파트너 통합 보기:

- [Barracuda WAF-서비스로](https://www.barracuda.com/waf-as-a-service)
- [Radware의 Azure Cloud WAF](https://www.radware.com/resources/microsoft-azure/)
