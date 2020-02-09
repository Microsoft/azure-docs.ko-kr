---
title: 자산을 보호 하기 위한 보안 모범 사례-Microsoft Azure
description: 이 문서에서는 Azure에서 데이터, 응용 프로그램 및 기타 자산을 보호 하는 일련의 작업 모범 사례를 제공 합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 69e55b17814c5b5ada0813bd0de66cc3a9a591f4
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219512"
---
# <a name="azure-operational-security-best-practices"></a>Azure 운영 보안 모범 사례
이 문서에서는 Azure에서 데이터, 응용 프로그램 및 기타 자산을 보호 하는 일련의 작업 모범 사례를 제공 합니다.

모범 사례는 의견의 일치를 기반으로 하며 현재 Azure 플랫폼 기능 및 기능 집합과 함께 작동합니다. 의견 및 기술은 시간이 지남에 따라 변경 되며이 문서는 이러한 변경 내용을 반영 하기 위해 정기적으로 업데이트 됩니다.

## <a name="define-and-deploy-strong-operational-security-practices"></a>강력한 운영 보안 방법 정의 및 배포
Azure 운영 보안은 사용자가 Azure에서 자신의 데이터, 애플리케이션 및 기타 자산을 보호하는 데 사용할 수 있는 서비스, 제어 및 기능을 지칭합니다. Azure 운영 보안은 [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl), [Microsoft 보안 대응 센터](https://www.microsoft.com/msrc?rtc=1) 프로그램 및 사이버 보안 위협 상황에 대한 심층 인식을 포함하여 Microsoft 고유의 다양한 기능을 통해 얻은 지식을 통합한 프레임워크를 기반으로 합니다.

## <a name="manage-and-monitor-user-passwords"></a>사용자 암호 관리 및 모니터링
다음 표에서는 사용자 암호 관리와 관련 된 몇 가지 모범 사례를 보여 줍니다.

**모범 사례**: 클라우드에서 적절 한 수준의 암호 보호가 있는지 확인 합니다.   
**세부 정보**: Microsoft id 플랫폼 (Azure Active Directory, Active Directory 및 Microsoft 계정)의 사용자로 범위가 지정 된 [Microsoft 암호 지침](https://www.microsoft.com/research/publication/password-guidance/)의 지침을 따르세요.

**모범 사례**: 사용자 계정과 관련 된 의심 스러운 작업을 모니터링 합니다.   
**세부 정보**: Azure AD 보안 보고서를 사용 하 여 위험 및 [위험한 로그인](../../active-directory/reports-monitoring/concept-risk-events.md) [사용자](/azure/active-directory/reports-monitoring/concept-user-at-risk) 를 모니터링 합니다.

**모범 사례**: 위험 수준이 높은 암호를 자동으로 검색 하 고 재구성 합니다.   
**세부 정보**: [Azure AD ID 보호](/azure/active-directory/identity-protection/overview) 는 다음을 수행할 수 있는 Azure AD Premium P2 버전의 기능입니다.

- 조직의 id에 영향을 주는 잠재적 취약성 검색
- 조직 ID와 관련된 검색된 의심스러운 작업에 대한 자동화된 응답 구성
- 의심 스러운 인시던트를 조사 하 고 해결을 위한 적절 한 조치를 취합니다.

## <a name="receive-incident-notifications-from-microsoft"></a>Microsoft에서 인시던트 알림 받기
보안 운영 팀이 Microsoft의 Azure 인시던트 알림을 받을 수 있도록 합니다. 인시던트 알림을 통해 보안 팀은 잠재적인 보안 위험에 신속 하 게 대응 하 고 해결할 수 있도록 Azure 리소스를 손상 시킬 수 있음을 알릴 수 있습니다.

Azure 등록 포털에서 보안 작업에 알리는 세부 정보를 관리 담당자 정보에 포함할 수 있습니다. 연락처 정보는 전자 메일 주소 및 전화 번호입니다.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure 구독을 관리 그룹으로 구성
조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. [Azure 관리 그룹](/azure/governance/management-groups/create) 은 구독을 초과 하는 범위 수준을 제공 합니다. 구독을 관리 그룹 이라고 하는 컨테이너에 구성 하 고 관리 되는 조건을 관리 그룹에 적용 합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 조건을 자동으로 상속합니다.

관리 그룹 및 구독의 유연한 구조를 디렉터리에 빌드할 수 있습니다. 각 디렉터리에는 루트 관리 그룹 이라는 단일 최상위 관리 그룹이 제공 됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 루트 관리 그룹을 사용 하면 디렉터리 수준에서 전역 정책 및 RBAC 할당을 적용할 수 있습니다.

다음은 관리 그룹을 사용 하는 몇 가지 모범 사례입니다.

**모범 사례**: 새 구독은 추가 될 때 정책 및 사용 권한과 같은 거 버 넌 스 요소를 적용 하는지 확인 합니다.   
**세부 정보**: 루트 관리 그룹을 사용 하 여 모든 Azure 자산에 적용 되는 엔터프라이즈 수준의 보안 요소를 할당 합니다. 정책 및 사용 권한은 요소의 예입니다.

**모범 사례**: 조각화 전략을 사용 하 여 최상위 수준 관리 그룹을 정렬 하 여 각 세그먼트 내에서 제어 및 정책 일관성을 위한 지점을 제공 합니다.   
**세부 정보**: 루트 관리 그룹 아래의 각 세그먼트에 대해 단일 관리 그룹을 만듭니다. 루트 아래에 다른 관리 그룹을 만들지 마십시오.

**모범 사례**: 운영 및 보안을 hampers 하는 혼동을 피하기 위해 관리 그룹 깊이를 제한 합니다.   
**세부 정보**: 루트를 포함 하 여 계층을 세 수준으로 제한 합니다.

**모범 사례**: 루트 관리 그룹을 사용 하 여 전체 엔터프라이즈에 적용할 항목을 신중 하 게 선택 합니다.   
**세부 정보**: 루트 관리 그룹 요소에 모든 리소스에 대 한 명확한 적용을 적용 하 고 낮은 영향을 주는지 확인 합니다.

좋은 후보는 다음과 같습니다.

- 비즈니스 영향이 명확한 규정 요구 사항 (예: 데이터 주권 관련 된 제한 사항)
- 감사 효과가 있는 정책 또는 신중 하 게 검토 된 RBAC 권한 할당이 포함 된 작업에 대 한 잠재적 음수가 0이 아닌 요구 사항에 영향을 미치는 요구 사항

**모범 사례**: 루트 관리 그룹을 적용 하기 전에 모든 엔터프라이즈 수준의 변경 사항을 신중 하 게 계획 하 고 테스트 합니다 (정책, RBAC 모델 등).   
**세부 정보**: 루트 관리 그룹의 변경 내용은 Azure의 모든 리소스에 영향을 줄 수 있습니다. 엔터프라이즈 전체에서 일관성을 유지 하는 강력한 방법을 제공 하는 반면 오류 또는 잘못 된 사용은 프로덕션 작업에 부정적인 영향을 미칠 수 있습니다. 테스트 랩 또는 프로덕션 파일럿에서 루트 관리 그룹에 대 한 모든 변경 내용을 테스트 합니다.

## <a name="streamline-environment-creation-with-blueprints"></a>청사진을 사용 하 여 환경 만들기 간소화
[Azure 청사진 서비스를](/azure/governance/blueprints/overview) 사용 하면 클라우드 설계자와 중앙 정보 기술 그룹이 조직의 표준, 패턴 및 요구 사항을 구현 하 고 준수 하는 반복 가능한 Azure 리소스 집합을 정의할 수 있습니다. Azure 청사진을 사용 하면 개발 팀이 기본 제공 구성 요소 집합을 사용 하 여 새 환경을 빠르게 빌드하고 조직의 규정 준수 내에서 이러한 환경을 만들 수 있습니다.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>스토리지 서비스에서 예기치 않은 동작 변경 모니터링
클라우드 환경에서 호스팅되는 분산 애플리케이션의 문제를 진단하고 해결하는 과정은 기존 환경보다 복잡할 수 있습니다. 애플리케이션은 PaaS 또는 IaaS 인프라, 온-프레미스, 모바일 디바이스 또는 이들 중 일부가 조합된 환경에 배포될 수 있습니다. 애플리케이션의 네트워크 트래픽은 공용 네트워크와 프라이빗 네트워크로 전송될 수 있으며, 애플리케이션이 여러 가지 스토리지 기술을 사용할 수도 있습니다.

따라서 애플리케이션이 사용하는 스토리지 서비스를 지속적으로 모니터링해 응답 속도 저하 등의 예기치 않은 동작 변경을 파악해야 합니다. 로깅을 사용하여 자세한 데이터를 수집하고 문제를 심층 분석합니다. 모니터링 및 로깅을 통해 얻은 진단 정보를 토대로 애플리케이션에 발생한 문제의 근본 원인을 확인할 수 있습니다. 그러면 문제를 해결하고 해당 문제를 완화하기 위한 적절한 단계를 결정할 수 있습니다.

[Azure 스토리지 분석](../../storage/common/storage-analytics.md)은 로깅을 수행하며 Azure Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고, 사용량 추세를 분석하고, 스토리지 계정에 대한 문제를 진단하는 것이 좋습니다.

## <a name="prevent-detect-and-respond-to-threats"></a>위협 방지, 검색 및 대응
[Azure Security Center](../../security-center/security-center-intro.md) 를 통해 Azure 리소스의 보안을 향상 하 고 제어 하는 향상 된 기능을 제공 하 여 위협을 예방, 감지 및 대응 하는 데 도움이 됩니다. Azure 구독 전반에 걸친 통합 보안 모니터링 및 정책 관리 기능을 제공하는 Azure Security Center를 활용하면 일반적으로는 찾아내기가 어려운 위협을 검색할 수 있으며 다양한 보안 솔루션을 활용할 수 있습니다.

Security Center의 무료 계층은 Azure 리소스에 대 한 제한 된 보안을 제공 합니다. 표준 계층에서는 이러한 기능이 온-프레미스 및 기타 클라우드로 확대 제공됩니다. Security Center 표준은 보안 취약점을 찾아 해결 하 고, 액세스 및 응용 프로그램 제어를 적용 하 여 악의적인 활동을 차단 하 고, 분석 및 인텔리전스를 사용 하 여 위협을 감지 하 고, 공격을 받는 경우 신속 하 게 대응 합니다. 처음 60일 동안 추가 비용 없이 Security Center 표준을 사용해 볼 수 있습니다. [Azure 구독을 Security Center Standard로 업그레이드](../../security-center/security-center-get-started.md)하는 것이 좋습니다.

Security Center를 사용 하 여 모든 Azure 리소스의 보안 상태를 중앙에서 볼 수 있습니다. 적합한 보안 제어 기능이 마련되어 있으며 올바르게 구성되어 있는지를 한눈에 확인하고 주의가 필요한 리소스를 빠르게 확인할 수 있습니다.

또한 Security Center은 포괄적인 엔드포인트 검색 및 응답 (EDR) 기능을 제공 하는 [Microsoft DEFENDER ATP (Advanced Threat Protection)](../../security-center/security-center-wdatp.md)와 통합 됩니다. Microsoft Defender ATP 통합을 사용 하 여 비정상적인 상태를 찾을 수 있습니다. Security Center에서 모니터링 하는 서버 엔드포인트에 대 한 고급 공격을 감지 하 고 대응할 수도 있습니다.

거의 모든 엔터프라이즈 조직은 다양 한 신호 수집 장치에서 로그 정보를 통합 하 여 새로운 위협을 식별 하는 데 도움이 되는 SIEM (보안 정보 및 이벤트 관리) 시스템을 보유 하 고 있습니다. 그런 다음 데이터 분석 시스템에서 로그를 분석 하 여 모든 로그 수집 및 분석 솔루션에서 발생 하는 모든 의미의 의미를 파악 하는 데 도움이 됩니다.

[Azure 센티널](/azure/sentinel/overview) 은 확장 가능한 클라우드 기본, siem (보안 정보 및 이벤트 관리) 및 대화 충성도 (security orchestration 자동화 된 응답) 솔루션입니다. Azure 센티널은 경고 검색, 위협 가시성, 자동 관리 검색 및 자동화 된 위협 응답을 통해 지능형 보안 분석 및 위협 인텔리전스를 제공 합니다.

위협 방지, 검색 및 대응에 대 한 몇 가지 모범 사례는 다음과 같습니다.

**모범 사례**: 클라우드 기반 SIEM을 사용 하 여 SIEM 솔루션의 속도 및 확장성을 높입니다.   
**세부 정보**: [Azure 센티널](/azure/sentinel/overview) 의 기능과 기능을 조사 하 여 현재 온-프레미스에서 사용 하 고 있는 기능의 기능과 비교 합니다. 조직의 SIEM 요구 사항을 충족 하는 경우 Azure 센티널을 채택 하는 것이 좋습니다.

**모범 사례**: 조사 우선 순위를 지정할 수 있도록 가장 심각한 보안 취약성을 찾습니다.   
**세부 정보**: [Azure 보안 점수](../../security-center/security-center-secure-score.md) 를 검토 하 여 Azure Security Center에 기본 제공 되는 azure 정책 및 이니셔티브에서 얻은 권장 사항을 확인 하세요. 이러한 권장 사항은 보안 업데이트, endpoint protection, 암호화, 보안 구성, 누락 WAF, 인터넷에 연결 된 Vm 등의 주요 위험을 해결 하는 데 도움이 됩니다.

인터넷 보안 (CI) 컨트롤용 센터를 기반으로 하는 보안 점수를 사용 하 여 조직의 Azure 보안을 외부 원본에 대해 벤치 마크 할 수 있습니다. 외부 유효성 검사는 팀의 보안 전략의 유효성을 검사 하 고 보강 하는 데 도움이 됩니다.

**모범 사례**: 컴퓨터, 네트워크, 저장소 및 데이터 서비스, 응용 프로그램의 보안 상태를 모니터링 하 여 잠재적인 보안 문제를 검색 하 고 우선 순위를 지정 합니다.  
**세부 정보**: 가장 높은 우선 순위 항목을 사용 하 여 Security Center 시작의 [보안 권장 사항을](../../security-center/security-center-recommendations.md) 따릅니다.

**모범 사례**: Security Center 경고를 SIEM (보안 정보 및 이벤트 관리) 솔루션에 통합 합니다.   
**세부 정보**: SIEM을 사용 하는 대부분의 조직에서는이를 분석가 응답이 필요한 보안 경고에 대 한 중앙 clearinghouse로 사용 합니다. Security Center에서 생성 된 처리 된 이벤트는 Azure Monitor를 통해 사용할 수 있는 로그 중 하나인 Azure 활동 로그에 게시 됩니다. Azure Monitor는 모니터링 데이터를 SIEM 도구에 라우팅하는 것에 대한 통합된 파이프라인을 제공합니다. 지침은 [Security Center의 보안 솔루션 통합](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) 을 참조 하세요. Azure 센티널를 사용 하는 경우 [연결 Azure Security Center](../../sentinel/connect-azure-security-center.md)을 참조 하세요.

**모범 사례**: Azure 로그를 SIEM과 통합 합니다.   
**세부 정보**: [Azure Monitor를 사용 하 여 데이터를 수집 하 고 내보낼 수](/azure/azure-monitor/overview#integrate-and-export-data)있습니다. 이 방법은 보안 인시던트 조사를 사용 하도록 설정 하는 데 중요 하며 온라인 로그 보존은 제한적입니다. Azure 센티널를 사용 하는 경우 [연결 데이터 원본](../../sentinel/connect-data-sources.md)을 참조 하세요.

**모범 사례**: EDR (엔드포인트 검색 및 응답) 기능을 공격 조사에 통합 하 여 조사 및 구하기 프로세스를 가속화 하 고 가양성을 줄입니다.   
**세부 정보**: Security Center 보안 정책을 통해 [Microsoft DEFENDER ATP 통합을 사용 하도록 설정](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) 합니다. 위협 구하기 및 인시던트 대응을 위해 Azure 센티널을 사용 하는 것이 좋습니다.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>엔드투엔드 시나리오 기반 네트워크 모니터링
고객은 가상 네트워크, ExpressRoute, Application Gateway, 부하 분산 장치 등의 네트워크 리소스를 결합하여 Azure에서 엔드투엔드 네트워크를 빌드합니다. 모니터링은 각 네트워크 리소스에서 사용할 수 있습니다.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)는 지역별 서비스입니다. 이 서비스에서 제공하는 진단 및 시각화 도구를 사용하면 Azure 내/외부 통신에 사용되는 네트워크 시나리오 수준의 상태를 모니터링하고 진단할 수 있습니다.

사용 가능한 도구 및 네트워크 모니터링 관련 모범 사례는 다음과 같습니다.

**모범 사례**: 패킷 캡처를 사용하여 원격 네트워크 모니터링을 자동화합니다.  
**세부 정보**: VM에 로그인하지 않고 Network Watcher를 사용하여 네트워킹 문제를 모니터링 및 진단합니다. 경고를 설정하여 [패킷 캡처](../../network-watcher/network-watcher-alert-triggered-packet-capture.md)를 트리거하고 패킷 수준에서 실시간 성능 정보에 액세스합니다. 문제를 발견하면 자세히 조사하여 더 정확히 진단할 수 있습니다.

**모범 사례**: 흐름 로그를 사용하여 네트워크 트래픽에 대한 인사이트를 얻습니다.  
**세부 정보**: [네트워크 보안 그룹 흐름 로그](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)를 사용하여 네트워크 트래픽 패턴을 심층 파악합니다. 흐름 로그의 정보를 확인하면 준수, 감사 및 네트워크 보안 프로필 모니터링에 필요한 데이터를 수집할 수 있습니다.

**모범 사례**: VPN 연결 문제를 진단합니다.  
**세부 정보**: Network Watcher를 사용하면 [자주 발생하는 VPN Gateway 및 연결 문제를 진단](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)할 수 있습니다. 문제를 파악할 수 있을 뿐 아니라 자세한 로그를 사용하여 추가로 조사할 수도 있습니다.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>검증된 DevOps 도구를 사용하여 배포 보안 유지
기업과 팀의 생산성과 효율성을 높이려면 다음의 DevOps 모범 사례를 따르세요.

**모범 사례**: 서비스 빌드 및 배포를 자동화합니다.  
**세부 정보**: [IaaC(Infrastructure as code)](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)는 IT 전문가가 일상적으로 수행하는 모듈식 인프라 빌드 및 관리 작업 부담을 없애 주는 기술 및 구성 요소 세트입니다. IT 전문가는 IaaC를 통해 소프트웨어 개발자가 애플리케이션 코드를 작성하고 유지 관리하는 것과 유사한 방식으로 최신 서버 환경을 빌드하고 유지 관리할 수 있습니다.

[Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)를 사용하면 선언적 템플릿을 통해 애플리케이션을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 애플리케이션 수명 주기의 각 단계에서 애플리케이션을 반복 배포합니다.

**모범 사례**: 자동으로 Azure 웹앱 또는 클라우드 서비스에 빌드 및 배포합니다.  
**세부 정보**: Azure 웹 앱 또는 클라우드 서비스에 [자동으로 빌드 및 배포](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) 하도록 Azure DevOps Projects를 구성할 수 있습니다. Azure DevOps는 모든 코드 체크 인 후에 Azure에 빌드를 수행한 후에 이진 파일을 자동으로 배포 합니다. 패키지 빌드 프로세스는 Visual Studio의 Package 명령과 동일하며 게시 단계는 Visual Studio의 Publish 명령과 동일합니다.

**모범 사례**: Release management를 자동화 합니다.  
**세부 정보**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops)는 다단계 배포 자동화 및 릴리스 프로세스 관리를 위한 솔루션입니다. 관리되는 지속적인 배포 파이프라인을 만들어 빠르고 쉽게 자주 릴리스합니다. Azure Pipelines를 통해 릴리스 프로세스를 자동화하고, 미리 정의된 승인 워크플로를 적용할 수 있습니다. 필요에 맞게 온-프레미스 및 클라우드에 배포하고 확장하며 사용자 지정합니다.

**모범 사례**: 시작하거나 프로덕션 환경에 업데이트를 배포하기 전에 앱의 성능을 확인합니다.  
**세부 정보**: 클라우드 기반 [부하 테스트](/azure/devops/test/load-test/overview#alternatives) 를 실행 하 여 다음을 수행 합니다.

- 앱의 성능 문제를 찾습니다.
- 배포 품질을 개선합니다.
- 앱이 항상 사용 가능한 상태인지 확인합니다.
- 앱이 차기 출시 또는 마케팅 캠페인을 위한 트래픽을 처리할 수 있는지 확인합니다.

[Apache JMeter](https://jmeter.apache.org/) 는 강력한 커뮤니티 지원을 제공 하는 무료의 인기 있는 오픈 소스 도구입니다.

**모범 사례**: 애플리케이션 성능을 모니터링합니다.  
**세부 정보**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. 성능 이상을 자동으로 검색합니다. 또한 문제를 진단하고 앱을 사용하여 실제로 수행하는 작업을 파악할 수 있는 분석 도구도 포함되어 있습니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

## <a name="mitigate-and-protect-against-ddos"></a>DoS 완화 및 방지
DDoS(배포된 서비스 거부)는 애플리케이션 리소스를 고갈시키려는 공격 유형입니다. 공격 목표는 애플리케이션의 가용성과 합법적인 요청을 처리하는 기능을 약화시키는 것입니다. 최근에는 이러한 공격이 점점 정교해지고 그 규모와 영향도 점점 커지고 있습니다. 인터넷을 통해 공개적으로 연결 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.

DDoS 발생 시 복원하는 기능을 설계하고 작성하려면 다양한 오류 모드에 대한 계획과 설계가 필요합니다. Azure에서 DDoS 공격 시 복원이 가능한 서비스를 구축하기 위한 모범 사례는 다음과 같습니다.

**모범 사례**: 디자인 및 구현에서 배포 및 작업까지 애플리케이션의 전체 수명 주기에서 보안을 최우선으로 고려해야 합니다. 애플리케이션에는 양이 그다지 많지 않은 요청에서 많은 리소스를 사용하여 결국 서비스 중단으로 이어지게 만드는 버그가 있을 수 있습니다.  
**세부 정보**: Microsoft Azure에서 실행되는 서비스를 보호하기 위해 애플리케이션 아키텍처를 정확하게 이해하고 [소프트웨어 품질의 5가지 핵심 요소](https://docs.microsoft.com/azure/architecture/guide/pillars)에 집중해야 합니다. 일반적인 트래픽 볼륨, 애플리케이션과 다른 애플리케이션 간의 연결 모델, 공용 인터넷에 노출되는 서비스 엔드포인트를 알아야 합니다.

애플리케이션 자체를 대상으로 하는 서비스 거부 공격을 처리할 수 있도록 애플리케이션의 복원력을 높이는 것이 가장 중요합니다. [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl)부터 보안 및 개인 정보 보호 기능이 Azure 플랫폼에 기본 제공됩니다. SDL은 모든 개발 단계에서 보안 문제를 해결하고 Azure를 지속적으로 업데이트하여 더욱 안전하게 보호합니다.

**모범 사례**: 증폭되는 부하, 특히 DDoS 공격 시 증폭되는 부하 수요를 충족할 수 있도록 애플리케이션이 [수평으로 확장 가능](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)하도록 설계합니다. 애플리케이션이 서비스의 단일 인스턴스에 종속된 경우 단일 실패 지점이 생깁니다. 여러 인스턴스를 프로비전하면 시스템에 복원성 및 확장성이 증가하게 됩니다.  
**세부 정보**: [Azure App Service](/azure/app-service/app-service-value-prop-what-is)의 경우 여러 인스턴스를 제공하는 [App Service 계획](../../app-service/overview-hosting-plans.md)을 선택합니다.

Azure Cloud Services의 경우 각각의 역할을 [여러 인스턴스](../../cloud-services/cloud-services-choose-me.md)를 사용하도록 구성합니다.

[Azure Virtual Machines](/azure/virtual-machines/windows/overview)의 경우 VM 아키텍처가 둘 이상의 VM을 포함하는지와 각 VM이 [가용성 집합](/azure/virtual-machines/virtual-machines-windows-manage-availability)에 포함되는지 확인합니다. 자동 크기 조정 기능을 활용할 수 있는 Virtual Machine Scale Sets를 사용하는 것이 좋습니다.

**모범 사례**: 애플리케이션에서 보안 방어를 계층화하면 공격이 성공할 가능성이 줄어듭니다. Azure 플랫폼의 기본 제공 기능을 사용하여 안전한 애플리케이션 디자인을 구현합니다.  
**세부 정보**: 애플리케이션의 크기(노출 영역)가 커지면 공격 위험도 증가합니다. 부하 분산 장치([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) 및 [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal))에 필요하지 않은 노출된 IP 주소 공간 및 수신 대기 포트를 종료하기 위해 허용 목록을 사용하여 노출 영역을 줄일 수 있습니다.

[네트워크 보안 그룹](../../virtual-network/security-overview.md)은 공격 표면을 줄이기 위한 또 다른 방법입니다. [서비스 태그](../../virtual-network/security-overview.md#service-tags) 및 [애플리케이션 보안 그룹](../../virtual-network/security-overview.md#application-security-groups)을 사용하여 보안 규칙을 만드는 복잡성을 최소화하고 애플리케이션 구조의 기본 확장으로 네트워크 보안을 구성할 수 있습니다.

가능하면 [가상 네트워크](../../virtual-network/virtual-networks-overview.md)에 Azure 서비스를 배포해야 합니다. 이렇게 하면 서비스 리소스가 개인 IP 주소를 통해 통신할 수 있습니다. 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다.

[서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md) 사용 시에는 서비스 트래픽이 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 프라이빗 주소를 원본 IP 주소로 사용하도록 전환됩니다.

종종 고객의 온-프레미스 리소스가 Azure의 해댱 리소스와 함께 공격당하는 모습을 볼 수 있습니다. 온-프레미스 환경을 Azure에 연결하는 경우 공용 인터넷에 노출되는 온-프레미스 리소스를 최소화합니다.

Azure에는 네트워크 공격으로부터 리소스를 보호하는 다음의 두 가지 DDoS [서비스](../../virtual-network/ddos-protection-overview.md)가 제공됩니다.

- 기본 보호는 기본적으로 추가 비용 없이 Azure에 통합됩니다. 전역 배포 Azure 네트워크의 규모 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 레이어 공격을 방어합니다. 사용자가 구성하거나 애플리케이션을 변경하지 않아도 사용이 가능한 기본 보호 기능을 활용하면 Azure DNS 등의 PaaS 서비스를 포함한 모든 Azure 서비스를 보호할 수 있습니다.
- 표준 보호는 네트워크 공격에 대한 고급 DDoS 완화 기능을 제공합니다. 특정 Azure 리소스를 보호하도록 자동으로 조정됩니다. 가상 네트워크를 만드는 동안 간단하게 보호를 사용하도록 설정할 수 있습니다. 생성 후에도 설정 가능하며 애플리케이션 또는 리소스를 변경할 필요가 없습니다.

## <a name="enable-azure-policy"></a>Azure Policy 사용
[Azure Policy](/azure/governance/policy/overview) 는 정책을 만들고 할당 하 고 관리 하는 데 사용 하는 Azure의 서비스입니다. 이러한 정책은 리소스에 대 한 규칙 및 효과를 적용 하므로 이러한 리소스는 회사 표준 및 서비스 수준 계약을 준수 합니다. Azure Policy는 할당된 정책의 비준수에 대해 리소스를 평가하여 이 요구를 충족합니다.

Azure Policy 사용 하 여 조직의 작성 된 정책을 모니터링 하 고 적용 합니다. 이를 통해 하이브리드 클라우드 워크 로드에서 보안 정책을 중앙에서 관리 하 여 회사 또는 규정 보안 요구 사항을 준수할 수 있습니다. [정책을 만들고 관리 하 여 규정 준수를 적용](../../governance/policy/tutorials/create-and-manage.md)하는 방법을 알아봅니다. 정책 요소에 대 한 개요는 [Azure Policy 정의 구조](../../governance/policy/concepts/definition-structure.md) 를 참조 하세요.

Azure Policy를 채택 하 고 나면 다음 몇 가지 보안 모범 사례를 따라야 합니다.

**모범 사례**: 정책은 여러 유형의 효과를 지원 합니다. [Azure Policy 정의 구조](../../governance/policy/concepts/definition-structure.md#policy-rule)에서이에 대해 읽을 수 있습니다. 비즈니스 작업은 **거부** 효과 및 **수정 효과에** 의해 부정적인 영향을 받을 수 있으므로 **감사** 효과부터 시작 하 여 정책에서 부정적인 영향을 미치는 위험을 제한할 수 있습니다.   
**세부 정보**: [감사 모드에서 정책 배포를 시작한](../../governance/policy/concepts/definition-structure.md#policy-rule) 후 나중에 **거부** 또는 **재구성**으로 진행 합니다. **거부** 또는 **재구성**으로 이동 하기 전에 감사 효과의 결과를 테스트 하 고 검토 합니다.

자세한 내용은 [규정 준수를 적용 하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)를 참조 하세요.

**모범 사례**: 정책 위반에 대 한 모니터링을 담당 하는 역할을 식별 하 고 올바른 수정 작업이 신속 하 게 수행 되도록 합니다.   
**세부 정보**: [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) 또는 [명령줄](../../governance/policy/how-to/get-compliance-data.md#command-line)을 통해 할당 된 역할 모니터를 준수 합니다.

**모범 사례**: Azure Policy는 조직의 작성 된 정책을 기술 하 여 표현한 것입니다. 모든 Azure 정책을 조직 정책에 매핑하여 혼동을 줄이고 일관성을 높일 수 있습니다.   
**세부 정보**: Azure [정책 설명](../../governance/policy/concepts/definition-structure.md#display-name-and-description) 또는 azure 정책 [이니셔티브](../../governance/policy/concepts/definition-structure.md#initiatives) 설명에서 조직 정책에 대 한 참조를 추가 하 여 조직의 설명서 또는 azure 정책 자체의 문서 매핑

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD 위험 보고서 모니터링
대부분의 보안 침해는 공격자가 사용자의 ID를 도용하여 환경에 대한 액세스 권한을 얻을 때 발생합니다. 손상된 ID를 검색하는 것은 쉬운 작업이 아닙니다. Azure AD는 적응형 기계 학습 알고리즘 및 추론을 사용하여 사용자 계정과 관련된 의심스러운 동작을 감지합니다. 검색 된 각 의심 스러운 동작은 [위험 검색](../../active-directory/reports-monitoring/concept-risk-events.md)이라고 하는 레코드에 저장 됩니다. 위험 검색은 Azure AD 보안 보고서에 기록 됩니다. 자세한 내용은 [위험에 노출 된 사용자 보안 보고서](../../active-directory/reports-monitoring/concept-user-at-risk.md) 및 [위험한 로그인 보안 보고서](../../active-directory/reports-monitoring/concept-risky-sign-ins.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
[Azure 보안 모범 사례 및 패턴](best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.

Azure 보안 및 관련 Microsoft 서비스에 대한 보다 일반적인 정보를 제공하는 다음 리소스도 확인할 수 있습니다.
* [Azure 보안 팀 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure Security 관련 최신 정보를 확인할 수 있습니다.
* [Microsoft 보안 응답 센터](https://technet.microsoft.com/library/dn440717.aspx) - Azure와 관련된 문제를 비롯한 Microsoft 보안 취약점을 보고하거나 secure@microsoft.com으로 이메일을 보낼 수 있습니다.
