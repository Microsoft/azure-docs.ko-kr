---
title: Azure Security Center |의 보안 경고 Microsoft Docs
description: 이 항목에서는 보안 경고의 정의와 Azure Security Center에서 사용할 수 있는 다양 한 유형을 설명 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6fc4b40e9b65f17b0af61b601826279e99410ed1
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920751"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure Security Center의 보안 경고

Azure Security Center에는 다양 한 리소스 유형에 대 한 다양 한 경고가 있습니다. Security Center는 Azure에 배포 된 리소스에 대 한 경고를 생성 하 고 온-프레미스 및 하이브리드 클라우드 환경에 배포 된 리소스에 대 한 경고를 생성 합니다.

보안 경고는 고급 검색에 의해 트리거되고 Azure Security Center 표준 계층 에서만 사용할 수 있습니다. 평가판을 사용할 수 있습니다. [보안 정책](security-center-pricing.md)의 가격 책정 계층 선택에서 업그레이드할 수 있습니다. 가격 책정에 대한 자세한 내용은 [보안 센터 페이지](https://azure.microsoft.com/pricing/details/security-center/) 를 방문하세요.

## 오늘날의 위협 <a name="respond-threats"></a> 에 대응

지난 20년 동안 위협 환경에 중요한 변경 내용이 있었습니다. 과거에 회사는 일반적으로 "자신의 능력"을 확인하는 데 주로 관심이 있던 개별 공격자에 의한 웹 사이트 파손에 대해서만 걱정했습니다. 오늘날의 공격자는 훨씬 더 정교하며 조직적입니다. 특정 금융 및 전략적 목표가 있는 경우가 많습니다. 또한 국가 또는 조직 범죄로 자금 지원을 받으므로 더 많은 사용 가능한 리소스가 있습니다.

이러한 변경 현실에는 공격자 순위에 대 한 전례 없는 수준의 전문성이 있습니다. 공격자는 더 이상 웹 손상에 관심이 없습니다. 이제 정보, 금융 계정 및 개인 데이터를 도용 하는 데 관심이 있습니다 .이는 모두 오픈 시장에서 현금을 생성 하거나 특정 비즈니스, 정치적 또는 군사 위치를 활용 하는 데 사용할 수 있습니다. 재무 목표를 가진 공격자보다 더욱 걱정되는 것은 인프라 및 사용자에게 해를 입히기 위해 네트워크를 위반하는 공격자입니다.

이에 대한 응답으로 조직은 종종 알려진 공격 서명을 검색하여 엔터프라이즈 경계 또는 엔드포인트 방어에 집중하는 다양한 지점 솔루션을 배포합니다. 이러한 솔루션은 보안 분석가에게 심사 및 조사를 요구하는 대용량의 낮은 신뢰 경고를 생성하는 경향이 있습니다. 대부분의 조직은 이러한 경고에 응답하는 데 필요한 시간 및 전문 지식이 부족하므로 많은 경고가 해결되지 않습니다.  

또한 공격자는 다양 한 서명 기반 방어를 방해할 [클라우드 환경에 맞게 조정](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)하는 방법을 발전 하 고 있습니다. 새로운 위협 요소를 보다 신속하게 식별하고 감지 및 대응을 신속하게 처리하기 위해 새로운 접근 방식이 필요합니다.

## <a name="what-are-security-alerts"></a>보안 경고란?

경고는 리소스에 대한 위협을 탐지할 때 Security Center에서 생성하는 알림입니다. Security Center 우선 순위를 정하여 문제를 신속 하 게 조사 하는 데 필요한 정보와 함께 경고를 나열 합니다. Security Center는 공격을 해결하는 방법에 대한 권장 사항도 제공합니다.

## 는 어떻게 위협을 Security Center 검색 하나요? <a name="detect-threats"> </a>

Microsoft 보안 연구원은 지속적으로 위협을 지켜보고 있습니다. 클라우드 및 온-프레미스에 Microsoft의 글로벌 거점가 있기 때문에 광범위 한 원격 분석 집합에 액세스할 수 있습니다. 광범위 하 고 다양 한 데이터 집합 컬렉션을 통해 온-프레미스 소비자 및 엔터프라이즈 제품 뿐만 아니라 해당 온라인 서비스 새로운 공격 패턴 및 추세를 검색할 수 있습니다. 결과적으로 보안 센터는 공격자가 새롭고 더욱 정교한 악용을 릴리스하는 동안 신속하게 해당 감지 알고리즘을 업데이트할 수 있습니다. 이 방법을 통해 빠르게 움직이는 위협 환경을 지속적으로 관리할 수 있습니다.

실제 위협을 검색 하 고 가양성을 줄이기 위해 Azure 리소스와 네트워크의 로그 데이터를 수집, 분석 및 통합 Security Center 합니다. 또한 방화벽 및 엔드포인트 보호 솔루션과 같은 연결 된 파트너 솔루션 에서도 작동 합니다. Security Center는이 정보를 분석 하 여 여러 원본의 정보를 상호 연결 하 여 위협을 식별 하는 경우가 많습니다.

![보안 센터 데이터 수집 및 프레젠테이션](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

보안 센터는 서명 기반 방식을 뛰어 넘는 고급 보안 분석을 사용합니다. 수동 접근 방법을 사용하고 공격의 발전을 예측하여 식별할 수 없는 위협을 감지하도록 전체 클라우드 패브릭에 대한 이벤트를 평가하는 데 빅 데이터 및 [기계 학습](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) 기술의 돌파구를 활용합니다. 이러한 보안 분석은 다음과 같습니다.

* **통합 된 위협 인텔리전스**: microsoft 제품 및 서비스, microsoft DCU (Digital Crimes Unit), MSRC (Microsoft 보안 대응 센터) 및 외부 피드의 글로벌 위협 인텔리전스를 활용 하 여 알려진 잘못 된 행위자를 찾습니다.
* **동작 분석**: 알려진 패턴을 적용하여 악의적인 동작을 검색합니다.
* **변칙 검색**: 통계적 프로파일링을 사용하여 기록 초기 계획을 작성합니다. 잠재적 공격 벡터를 준수하는 설정된 기준에서 편차에 대해 경고합니다.

아래 섹션에서는 이러한 각 분석에 대해 자세히 설명 합니다.

### <a name="integrated-threat-intelligence"></a>통합 위협 인텔리전스

Microsoft는 방대한 글로벌 위협 인텔리전스가 있습니다. 원격 분석은 Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft DCU(Digital Crimes Unit) 및 MSRC(Microsoft 보안 대응 센터)와 같은 여러 출처에서 유입됩니다. 또한 연구원은 주요 클라우드 서비스 공급자와 다른 제 3 자의 피드 사이에서 공유 되는 위협 인텔리전스 정보를 수신 합니다. Azure Security Center는 이 정보를 사용하여 알려진 부정 행위자의 위협을 경고할 수 있습니다.

### <a name="behavioral-analytics"></a>동작 분석

동작 분석은 알려진 패턴의 컬렉션에 대해 데이터를 분석하고 비교하는 기술입니다. 그러나 이러한 패턴은 단순한 서명이 아닙니다. 대량 데이터 세트에 적용되는 복잡한 기계 학습 알고리즘을 통해 결정됩니다. 또한 전문 분석가가 악의적인 행동을 신중하게 분석하여 결정합니다. Azure Security Center는 동작 분석을 사용하여 가상 머신 로그, 가상 네트워크 디바이스 로그, 패브릭 로그, 크래시 덤프 및 기타 원본에 대한 분석에 따라 손상된 리소스를 식별할 수 있습니다.

또한 광범위 한 캠페인의 증명을 지 원하는 것을 확인 하기 위해 다른 신호와의 상관 관계가 있습니다. 이 상관 관계를 통해 설정된 손상 표시기와 일치하는 이벤트를 식별할 수 있습니다. 

### <a name="anomaly-detection"></a>변칙 검색

Azure Security Center는 이상 감지를 사용하여 위협을 식별합니다. 동작 분석(큰 데이터 집합에서 파생된 알려진 패턴에 따라 결정)과 달리 이상 감지는 더욱 "개인화"되고 배포에만 적용되는 기준에 중점을 둡니다. 배포에 대한 정상적인 작동을 확인하기 위해 기계 학습이 적용되고 보안 이벤트를 표시할 수 있는 이상값 조건을 정의하는 규칙이 생성됩니다.

## <a name="how-are-alerts-classified"></a>경고는 어떻게 분류 되나요?

Security Center는 경고에 대 한 심각도를 할당 하 여 각 경고에 참석 하는 순서의 우선 순위를 지정할 수 있도록 합니다. 따라서 리소스가 손상 되 면 즉시 가져올 수 있습니다. 심각도는 경고를 실행 하는 데 사용 되는 검색 또는 분석에 사용 되는 Security Center의 신뢰도와 경고를 발생 시킨 활동의 악의적인 의도를 가진 신뢰도 수준을 기준으로 합니다.

> [!NOTE]
> 경고 심각도는 포털과 REST API에서 다르게 표시되며, 차이점은 아래 목록에 나와 있습니다.

* **높음:** 리소스가 손상 될 확률이 매우 높습니다. 지금 즉시 리소스를 살펴보아야 합니다. Security Center는 경고 실행에 사용되는 악의적 의도와 결과 둘 다에서 신뢰성이 높습니다. 자격 증명 훔치기에 많이 사용되는 Mimikatz처럼 알려진 악의적 도구 실행을 검색하는 경고를 예로 들 수 있습니다.
* **보통 (REST API 낮음)** : 의심 스러운 활동으로 리소스가 손상 된 것을 나타낼 수 있습니다.
Security Center의 분석 또는 결과 신뢰도는 보통이며 악의적 의도의 신뢰도는 보통부터 높음 사이입니다. 이러한 검색은 일반적으로 기계 학습 이거나 변칙 기반 검색입니다. 예를 들어 비정상적인 위치에서 로그인을 시도 합니다.
* **낮음(REST API에서는 Information)** : 무해한 양성 또는 차단된 공격일 수 있습니다.
   * Security Center는 신뢰도가 높지 않기 때문에 의도가 악의적이고 작업은 악의적이지 않을 수 있습니다. 예를 들어 로그 지우기는 공격자가 흔적을 숨기려 시도할 때 발생할 수도 있지만, 대부분은 관리자가 수행하는 일상적인 작업입니다.
   * Security Center는 일반적으로 공격을 차단하더라도 살펴볼 가치가 있는 흥미로운 사례가 아닌 경우에는 사용자에게 알리지 않습니다. 
* **정보(REST API에서는 Silent)** : 보안 인시던트로 드릴다운하거나 특정 경고 ID가 있는 REST API를 사용하는 경우 정보 경고만 표시됩니다. 일반적으로 한 인시던트는 여러 경고로 구성되며, 그 중 일부는 오직 정보 제공만을 위해 단독으로 표시될 수 있지만 다른 경고의 컨텍스트에서는 자세히 살펴볼 필요가 있습니다. 
 

## <a name="continuous-monitoring-and-assessments"></a>연속 모니터링 및 평가

Microsoft 전체에서 위협 환경의 변화를 지속적으로 모니터링 하는 보안 연구 및 데이터 과학 팀을 포함 하는 것이 Azure Security Center 이점이 있습니다. 다음 이니셔티브가 포함됩니다.

* **위협 인텔리전스 모니터링**: 위협 인텔리전스에는 기존 또는 새로운 위협에 대한 메커니즘, 표시기, 영향 및 조치 가능한 조언이 포함됩니다. 이 정보는 보안 커뮤니티에서 공유되고 Microsoft는 내부 및 외부 소스에서 위협 인텔리전스 피드를 지속적으로 모니터링합니다.
* **신호 공유**: Microsoft의 클라우드 및 온-프레미스 서비스, 서버 및 클라이언트 엔드포인트 디바이스의 광범위한 포트폴리오에 대한 보안 팀의 정보는 공유 및 분석됩니다.
* **Microsoft 보안 전문가**: 과학 수사 및 웹 공격 감지와 같은 특수 보안 필드에서 활동하는 Microsoft 팀과의 지속적인 참여입니다.
* **감지 튜닝**: 알고리즘은 실제 고객 데이터 집합에 대해 실행되며 보안 연구원은 고객과 협력하여 결과의 유효성을 검사합니다. 기계 학습 알고리즘을 구체화하기 위해 참 및 거짓 긍정이 사용됩니다.

이러한 결합된 노력은 즉시 활용할 수 있는 새롭고 향상된 감지에 누적됩니다. 수행해야 할 작업이 없습니다.

## 보안 경고 유형 <a name="security-alert-types"></a>

다음 항목에서는 리소스 유형에 따라 다양 한 경고를 안내 합니다.

* [IaaS Vm 및 서버 경고](security-center-alerts-iaas.md)
* [기본 계산 경고](security-center-alerts-compute.md)
* [데이터 서비스 경고](security-center-alerts-data-services.md)

다음 항목에서는 azure에 배포 된 리소스에 대 한 추가 보호 계층을 적용 하기 위해 Security Center Azure 인프라와 통합 하 여 수집 하는 다양 한 원격 분석을 사용 하는 방법에 대해 설명 합니다.

* [서비스 계층 경고](security-center-alerts-service-layer.md)
* [Azure WAF 및 Azure DDoS Protection에 대 한 위협 감지](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>보안 인시던트 란 무엇 인가요?

보안 인시던트는 각 경고를 개별적으로 나열 하는 대신 관련 된 경고의 컬렉션입니다. Security Center는 [클라우드 스마트 경고 상관 관계](security-center-alerts-cloud-smart.md) 를 사용 하 여 서로 다른 경고와 낮은 충실도 신호의 상관 관계를 보안 인시던트에 연결 합니다.

인시던트를 사용 하 여 공격 캠페인 및 모든 관련 경고에 대 한 단일 보기를 제공 Security Center 합니다. 이 보기에서는 공격자가 수행한 작업과 영향을 받는 리소스를 빠르게 파악할 수 있습니다. 자세한 내용은 [클라우드 스마트 경고 상관 관계](security-center-alerts-cloud-smart.md)를 참조 하세요.

## <a name="security-alerts-in-azure-activity-log"></a>Azure 활동 로그의 보안 경고

보안 경고 및 인시던트는 Azure Portal에서 또는 프로그래밍 방식으로 사용할 수 있을 뿐만 아니라 [Azure 활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)에서 이벤트로 감사 됩니다. 이벤트 스키마에 대 한 자세한 내용은 [Azure 활동 로그의 보안 경고](https://go.microsoft.com/fwlink/?linkid=2114113)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 사용할 수 있는 다양 한 유형의 경고에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center FAQ](https://docs.microsoft.com/azure/security-center/security-center-faq)

