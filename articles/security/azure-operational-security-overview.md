---
title: Azure 운영 보안 개요 | Microsoft Docs
description: 이 문서에서는 Azure 운영 보안 기능에 대한 개요를 제공합니다.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: 38054d6ee3799296887726954ef1f096945aeaeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586852"
---
# <a name="azure-operational-security-overview"></a>Azure 운영 보안 개요

[Azure 운영 보안](https://docs.microsoft.com/azure/security/azure-operational-security)은 사용자가 Microsoft Azure에서 자신의 데이터, 애플리케이션 및 기타 자산을 보호할 수 있는 서비스, 제어 및 기능을 나타냅니다. Azure 운영 보안은 Microsoft SDL(Security Development Lifecycle), Microsoft 보안 대응 센터 프로그램, 사이버 보안 위협 상황에 대한 심층 인식 등 Microsoft 고유의 다양한 기능을 통해 얻은 지식을 통합하는 프레임워크입니다.

## <a name="azure-management-services"></a>Azure 관리 서비스

IT 운영 팀은 이러한 시스템의 안정성과 보안을 포함하여 데이터 센터 인프라, 애플리케이션 및 데이터를 관리하는 역할을 담당합니다. 그러나 점점 더 복잡해지는 IT 환경에서 보안 정보를 얻으려면 조직에서 여러 보안 및 관리 시스템의 데이터를 급하게 맞추어야 합니다.

[Microsoft Azure Monitor 로그](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 는 보호할 온-프레미스 및 클라우드 인프라 및 관리 하도록 도와주는 클라우드 기반 IT 관리 솔루션입니다. 핵심 기능은 Azure에서 실행되는 다음 서비스를 통해 제공됩니다. Azure는 온-프레미스 및 클라우드 인프라를 관리하고 보호하는 여러 서비스를 제공합니다. 각 서비스는 특정 관리 기능을 제공합니다. 이러한 서비스를 결합하여 다양한 관리 시나리오를 해결할 수 있습니다. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)는 관리되는 원본에서 중앙 데이터 저장소로 데이터를 수집합니다. 이 데이터에는 이벤트, 성능 데이터 또는 API를 통해 제공되는 사용자 지정 데이터가 포함될 수 있습니다. 데이터를 수집한 후에는 경고, 분석 및 내보내기에 사용할 수 있습니다. 

다양한 원본의 데이터를 통합하고 Azure 서비스의 데이터를 기존 온-프레미스 환경과 결합할 수 있습니다. Azure Monitor 로그도 명확 하 게 구분 데이터의 컬렉션 해당 데이터에 대해 수행 하는 작업에서 모든 작업은 모든 종류의 데이터를 사용할 수 있도록 합니다.

### <a name="automation"></a>Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)은 클라우드 및 엔터프라이즈 환경에서 일반적으로 장기간 실행되고, 오류가 발생하기 쉬우며, 자주 반복되는 수동 작업을 자동화할 수 있는 방법을 제공합니다. 시간을 절약하고 일반 관리 작업의 안정성을 향상시키며, 이러한 작업을 정기적으로 자동으로 수행되도록 예약합니다. Runbook을 사용하는 프로세스를 자동화하거나 원하는 상태 구성을 사용하여 구성 관리를 자동화할 수 있습니다.

### <a name="backup"></a>Backup

[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)은 Microsoft 클라우드에서 데이터를 백업(또는 보호)하고 복원하는 데 사용할 수 있는 Azure 기반 서비스입니다. 기존의 온-프레미스 또는 오프사이트 백업 솔루션을 신뢰할 수 있고 안전하며 가격 경쟁력이 있는 클라우드 기반 솔루션으로 대체합니다. 

Azure Backup에서는 컴퓨터, 서버 또는 클라우드에 적절히 다운로드하고 배포하는 구성 요소를 제공합니다. 배포하는 구성 요소 또는 에이전트는 보호하려는 대상에 따라 달라집니다. 온-프레미스 또는 클라우드에서 데이터를 보호하는지 여부에 관계없이 모든 Azure Backup 구성 요소는 Azure에서 Azure Recovery Services 자격 증명 모음에 데이터를 백업하는 데 사용할 수 있습니다. 

자세한 내용은 [Azure Backup 구성 요소 표](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use)를 참조하세요.

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery)는 온-프레미스 가상 컴퓨터 및 실제 컴퓨터를 Azure 또는 보조 사이트로 복제하는 작업을 오케스트레이션하여 비즈니스 연속성을 제공합니다. 기본 사이트를 사용할 수 없는 경우 사용자가 계속 작업할 수 있도록 보조 위치에 장애 조치(Failover)하고, 시스템이 다시 정상 상태가 되면 장애 복구합니다. Azure Security Center를 사용하여 좀 더 지능적이고 효과적인 위협 검색을 수행할 수 있습니다.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure AD(Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario)는 포괄적인 ID 서비스입니다.

-   클라우드 서비스로 IAM(ID 및 액세스 관리) 사용
-   중앙 액세스 관리, SSO(Single Sign-On) 및 보고 제공
-   Salesforce, Google Apps, Box, Concur 등을 포함하여 Azure Marketplace에서 [수천 개의 애플리케이션](https://azure.microsoft.com/marketplace/active-directory/)에 대한 통합된 액세스 관리 지원

또한 Azure AD에는 다음을 비롯해 전체 [ID 관리 기능](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)이 포함되어 있습니다.

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [셀프 서비스 암호 관리](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [권한 있는 계정 관리](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [애플리케이션 사용 현황 모니터링](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [풍부한 감사](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [보안 모니터링 및 경고](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Azure Active Directory를 사용하면 파트너 및 고객(비즈니스 또는 소비자)을 위해 게시하는 모든 애플리케이션에 동일한 ID 및 액세스 관리 기능이 제공됩니다.  이렇게 하면 운영 비용을 크게 줄일 수 있습니다.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에서 통합된 보안 모니터링 및 정책 관리를 제공하며, 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되고 보안 솔루션의 광범위한 환경에서 작동합니다.

가상 머신의 보안 설정에 대한 가시성을 제공하고 위협을 모니터링하여 Azure의 [VM(가상 머신) 데이터를 보호](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine)합니다. Security Center는 다음의 목적으로 가상 머신을 모니터링할 수 있습니다.

- 권장된 구성 규칙으로 운영 체제 보안 설정
- 시스템 보안 및 누락된 중요 업데이트
- 엔드포인트 보호 권장 사항
- 디스크 암호화 유효성 검사
- 네트워크 기반 공격

Security Center에서는 [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 사용합니다. RBAC는 Azure에서 사용자, 그룹 및 서비스에 할당될 수 있는 [기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 제공합니다.

Security Center는 리소스 구성을 평가하여 보안 문제 및 취약성을 식별합니다. Security Center에서는 리소스가 속한 구독이나 리소스 그룹에 대한 소유자, 참가자 또는 독자 역할을 할당 받은 경우에만 리소스와 관련된 정보를 볼 수 있습니다.

>[!Note]
>Security Center의 역할 및 허용된 작업에 대해 알아보려면 [Azure Security Center의 권한](https://docs.microsoft.com/azure/security-center/security-center-permissions)을 참조하세요.

Security Center에서는 Microsoft Monitoring Agent를 사용하며, 이 Azure Monitor 서비스를 사용 하는 동일한 에이전트입니다. 이 에이전트에서 수집된 데이터는 VM의 지리적 위치를 고려하여 Azure 구독 또는 새 작업 영역과 연결된 기존 Log Analytics [작업 영역](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) 중 하나에 저장됩니다.

## <a name="azure-monitor"></a>Azure Monitor

클라우드 앱의 성능 문제는 비즈니스에 영향을 미칠 수 있습니다. 상호 연결된 여러 구성 요소와 자주 제공되는 릴리스를 사용하면 언제든지 성능 저하가 발생할 수 있습니다. 앱을 개발하는 경우 사용자는 일반적으로 테스트에서 찾지 못한 문제를 발견합니다. 이러한 문제에 대해 즉시 알고 있어야 하며 문제를 진단하고 수정하는 도구가 있어야 합니다.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) - Azure에서 실행되는 서비스를 모니터링하는 기본 도구입니다. 또한 서비스의 처리량과 주변 환경에 대한 인프라 수준 데이터를 제공합니다. Azure에서 모든 앱을 관리하고 리소스를 확장 또는 축소할지 여부를 결정하려면 Azure Monitor에서 시작해야 합니다.

모니터링 데이터를 사용하여 애플리케이션에 대해 깊이 이해할 수도 있습니다. 이러한 정보를 통해 애플리케이션 성능이나 유지 관리를 개선하거나 그렇지 않으면 수동 개입이 필요한 작업을 자동화하는 데 도움이 될 수 있습니다. 

Azure Monitor에는 다음 구성 요소가 포함되어 있습니다.

### <a name="azure-activity-log"></a>Azure 동작 로그

[Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 정보를 제공합니다. 활동 로그는 구독에 대한 제어 평면 이벤트를 보고하기 때문에 이전에는 "감사 로그" 또는 "작업 로그"라고 했습니다.

### <a name="azure-diagnostic-logs"></a>Azure 진단 로그

[Azure 진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

Windows 이벤트 시스템 로그는 VM에 대한 진단 로그의 한 범주입니다. Blob, 테이블 및 큐 로그는 스토리지 계정에 대한 진단 로그의 범주입니다.

진단 로그는 [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)와 다릅니다. 활동 로그는 구독에 있는 리소스에서 수행된 작업에 대한 자세한 정보를 제공합니다. 진단 로그는 리소스 자체에서 수행하는 작업에 대한 정보를 제공합니다.

### <a name="metrics"></a>메트릭

Azure Monitor는 Azure에서 워크로드의 상태와 성능에 대한 정보를 얻을 수 있는 원격 분석 기능을 제공합니다. Azure 원격 분석 데이터의 가장 중요한 유형은 대부분의 Azure 리소스에서 내보내는 [메트릭](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)(성능 카운터라고도 함)입니다. Azure Monitor는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 및 사용하는 몇 가지 방법을 제공합니다.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure Diagnostics는 배포된 애플리케이션에서 진단 데이터를 수집할 수 있도록 합니다. 다양한 원본에서 진단 확장을 사용할 수 있습니다. [Azure 클라우드 서비스 역할](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), Microsoft Windows를 실행하는 [Azure Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) 및 [Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)에서 현재 지원됩니다.

## <a name="azure-network-watcher"></a>Azure Network Watcher

고객은 Virtual Network, Azure ExpressRoute, Azure Application Gateway, 부하 분산 장치 등의 개별 네트워크 리소스를 오케스트레이션하고 구성하여 Azure에서 종단 간 네트워크를 구축합니다. 모니터링은 각 네트워크 리소스에서 사용할 수 있습니다.

종단 간 네트워크는 복잡하게 구성되고 리소스 간에 상호 작용할 수 있으므로 [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)를 통해 시나리오 기반 모니터링이 필요한 복잡한 시나리오를 발생합니다.

Network Watcher는 Azure 네트워크의 모니터링 및 진단을 간소화합니다. Network Watcher에서 제공되는 진단 및 시각화 도구를 사용하여 다음을 수행할 수 있습니다.

- Azure Virtual Machine에서 원격 패킷 캡처
- 흐름 로그를 사용하여 네트워크 트래픽을 이해합니다.
- Azure VPN Gateway 및 연결 진단

Network Watcher는 현재 다음과 같은 기능을 제공합니다.

- [토폴로지](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): 리소스 그룹의 네트워크 리소스 간 다양한 연결 및 상호 연결에 대한 보기를 제공합니다.
- [변수 패킷 캡처](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): 가상 머신의 내부 및 외부 패킷 데이터를 캡처합니다. 고급 필터링 옵션과 세밀한 조정 컨트롤(예: 시간 및 크기 제한을 설정하는 기능)이 다양하게 제공됩니다. 패킷 데이터는 Blob 저장소 또는 .cap 형식의 로컬 디스크에 저장할 수 있습니다.
- [IP 흐름 확인](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): 흐름 정보의 5-튜플 패킷 매개 변수(대상 IP, 원본 IP, 대상 포트, 원본 포트 및 프로토콜)에 따라 패킷을 허용하거나 거부하는지 확인합니다. 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙과 그룹이 반환됩니다.
- [다음 홉](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): 잘못 구성된 사용자 정의 경로를 진단할 수 있도록 Azure 네트워크 패브릭에서 라우팅되는 패킷의 다음 홉을 결정합니다.
- [보안 그룹 보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): VM에 적용되는 효과적이고 실용적인 보안 규칙을 가져옵니다.
- [네트워크 보안 그룹의 NSG 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): 그룹의 보안 규칙으로 허용되거나 거부되는 트래픽과 관련된 로그를 캡처할 수 있습니다. 흐름은 원본 IP, 대상 IP, 원본 포트, 대상 포트 및 프로토콜의 5-튜플 정보로 정의됩니다.
- [Virtual Network 게이트웨이 및 연결 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): 가상 네트워크 게이트웨이 및 연결 문제를 해결하는 기능을 제공합니다.
- [네트워크 구독 제한](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): 제한 대비 네트워크 리소스 사용량을 볼 수 있습니다.
- [진단 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): 리소스 그룹의 네트워크 리소스에 대해 진단 로그를 사용하거나 사용하지 않도록 설정할 수 있는 단일 창을 제공합니다.

자세한 내용은 [Network Watcher 구성](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)을 참조하세요.

## <a name="cloud-service-provider-access-transparency"></a>클라우드 서비스 공급자 액세스 투명성

[Microsoft Azure에 대한 고객 Lockbox](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)는 Microsoft 지원 엔지니어가 문제를 해결하기 위해 데이터에 액세스해야 하는 드문 경우에서 명시적인 제어를 제공하는 Azure Portal로 통합된 서비스입니다. Microsoft 지원 엔지니어가 이 문제를 해결하기 위해 관리자 권한이 필요한 디버깅 원격 액세스 문제와 같은 경우는 거의 없습니다. 이러한 경우 Microsoft 엔지니어는 서비스로 제한된 액세스를 사용하여 제한된, 시간 제한 권한 부여를 제공하는 Just-In-Time 액세스 서비스를 사용합니다.  
Microsoft는 항상 액세스에 대한 고객 동의를 얻었지만 고객 Lockbox는 이제 Azure Portal에서 이러한 요청을 검토 및 승인하거나 거부하는 기능을 제공합니다. Microsoft 지원 엔지니어는 요청을 승인할 때까지 액세스 권한이 부여되지 않습니다.

## <a name="standardized-and-compliant-deployments"></a>표준화된 준수 배포

클라우드 설계자와 중앙 정보 기술 그룹은 [Azure Blueprints](../governance/blueprints/overview.md)를 통해 조직의 표준, 패턴 및 요구 사항을 구현하고 준수하는 반복 가능한 Azure 리소스 집합을 정의할 수 있습니다.  
따라서 DevOps 팀은 새 환경을 신속하게 빌드 및 구축하고 조직의 규정 준수를 유지 관리하는 인프라를 사용하여 빌드한다는 것을 신뢰할 수 있습니다. Blueprints는 다음과 같은 다양한 리소스 템플릿 및 기타 아티팩트의 배포를 조정하는 명확한 방법을 제공합니다. 

- 역할 할당
- 정책 할당
- Azure 리소스 관리자 템플릿
- 리소스 그룹

## <a name="devops"></a>DevOps

[DevOps(Developer Operations)](https://www.visualstudio.com/learn/what-is-devops/) 애플리케이션을 개발하기 전에 팀에서 소프트웨어 프로그램 및 코드 작성에 대한 비즈니스 요구 사항을 수집했습니다. 그런 다음 별도의 QA 팀이 격리된 개발 환경에서 프로그램을 테스트했습니다. 요구사항이 충족되면 QA 팀에서 배포할 작업 코드를 해제했습니다. 배포 팀은 네트워킹 및 데이터베이스와 같은 그룹으로 더 세분화되었습니다. 소프트웨어 프로그램이 독립된 팀으로 "사전 협의 없이 던져질(thrown over the wall)" 때마다 병목 현상이 추가되었습니다.

DevOps를 사용하면 팀에서 보다 안전하고 뛰어난 품질의 솔루션을 보다 빠르고 저렴하게 전달할 수 있습니다. 고객은 소프트웨어 및 서비스를 사용할 때 동적이고 안정적인 환경을 기대합니다. 팀은 소프트웨어 업데이트를 신속하게 반복하고 업데이트의 영향을 측정해야 합니다. 또한 새로운 개발 반복으로 신속하게 대응하여 문제를 해결하거나 더 많은 가치를 제공해야 합니다.  

Microsoft Azure와 같은 클라우드 플랫폼은 기존 병목 현상을 제거하고 인프라를 범용 상품화(commoditize)하도록 지원했습니다. 모든 비즈니스에서 소프트웨어는 비즈니스 결과의 차별화 요소이자 핵심 요소입니다. 어떤 조직, 개발자 또는 IT 작업자도 DevOps로의 변화를 피할 수 없습니다.

성숙한 DevOps 전문가는 다음 방법 중 몇 가지를 채택합니다. 이러한 방법에는 [사람들이 비즈니스 시나리오를 기반으로 하여 전략을 수립하는 것이 포함됩니다](https://www.visualstudio.com/learn/what-is-devops-culture/). 도구를 사용하면 다음과 같은 다양한 방법을 자동화할 수 있습니다.

- [민첩한 계획 수립 및 프로젝트 관리](https://www.visualstudio.com/learn/what-is-agile/) 기술은 업무를 스프린트로 계획 및 분리하고, 팀 역량을 관리하며, 팀에서 변화하는 비즈니스 요구 사항에 신속하게 적응할 수 있도록 하는 데 사용됩니다.
- [버전 제어(일반적으로 Git 포함)](https://www.visualstudio.com/learn/what-is-git/)를 사용하면 전 세계 어디에 있든지 간에 팀에서 소스를 공유하고 소프트웨어 개발 도구와 통합하여 릴리스 파이프라인을 자동화할 수 있습니다.
- [연속 통합](https://www.visualstudio.com/learn/what-is-continuous-integration/)은 진행 중인 코드를 병합 및 테스트하여 결함을 초기에 찾아내도록 합니다.  다른 이점으로 병합 문제를 해결하는 데 걸리는 시간을 줄이고 개발 팀에게 신속한 피드백을 제공할 수 있습니다.
- 프로덕션 및 테스트 환경에 대한 소프트웨어 솔루션의 [지속적인 업데이트](https://www.visualstudio.com/learn/what-is-continuous-delivery/)를 통해 조직에서 버그를 신속하게 수정하고 끊임없이 변화하는 비즈니스 요구 사항에 대응할 수 있습니다.
- 애플리케이션 상태뿐만 아니라 고객 사용을 위한 프로덕션 환경을 포함하여 실행 중인 애플리케이션의 [모니터링](https://www.visualstudio.com/learn/what-is-monitoring/)을 사용하면 조직에서 가설을 세우고 전략을 신속하게 유효성을 검사하거나 반증할 수 있습니다.  자세한 데이터가 캡처되어 다양한 로깅 형식으로 저장됩니다.
- 안전하고 안정적인 애플리케이션 호스팅 플랫폼을 제공하도록 지원하기 위해 [IaC(Infrastructure as Code)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/)를 사용하여 네트워크 및 가상 머신의 만들기 및 해제에 대한 자동화 및 유효성 검사를 사용하도록 설정하는 것이 좋습니다.
- [마이크로 서비스](https://www.visualstudio.com/learn/what-are-microservices/) 아키텍처는 비즈니스 사용 사례를 재사용 가능한 작은 서비스로 격리하는 데 사용됩니다.  이 아키텍처는 확장성과 효율성을 가능하게 합니다.

## <a name="next-steps"></a>다음 단계

보안 및 감사 솔루션에 대한 자세한 내용은 다음 문서를 참조하세요.

- [보안 및 규정 준수](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)
