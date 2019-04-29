---
title: Azure Site Recovery를 사용하여 다중 계층 SharePoint 애플리케이션에 대한 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery 기능을 사용하여 다중 계층 SharePoint 애플리케이션에 대한 재해 복구를 설정하는 방법을 설명합니다.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 5f477cf20b817d7a6c8be856636bf1e3755b5424
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472114"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 다중 계층 SharePoint 애플리케이션에 대한 재해 복구 설정

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 SharePoint 애플리케이션을 보호하는 방법을 자세히 설명합니다.


## <a name="overview"></a>개요

Microsoft SharePoint는 그룹 또는 부서가 정보를 구성, 공동 작업 및 공유하도록 도울 수 있는 강력한 애플리케이션입니다. SharePoint는 인트라넷 포털, 문서 및 파일 관리, 공동 작업, 소셜 네트워크, 엑스트라넷, 웹 사이트, 엔터프라이즈 검색 및 비즈니스 인텔리전스를 제공합니다. 또한 시스템 통합, 프로세스 통합 및 워크플로 자동화 기능도 갖추고 있습니다. 일반적으로 조직은 이를 가동 중지 시간과 데이터 손실에 민감한 계층 1 애플리케이션으로 여깁니다.

오늘날, Microsoft SharePoint는 기본적으로 재해 복구 기능을 제공하지 않습니다. 재해의 형식 및 규모에 관계 없이 복구에는 팜을 복구할 수 있는 대기 데이터 센터 사용이 포함됩니다. 로컬 중복 시스템 및 백업으로는 기본 데이터 센터의 작동 중단으로부터 복구할 수 없는 시나리오에서는 대기 데이터 센터가 필요합니다.

좋은 재해 복구 솔루션은 SharePoint와 같은 복잡한 애플리케이션 아키텍처에 대한 복구 계획의 모델링을 허용해야 합니다. 또한 다양한 계층 간 애플리케이션 매핑을 처리하도록 사용자 정의된 단계를 추가함으로써 재해 이벤트 시 한 번의 클릭으로 낮은 RTO의 장애 조치를 제공하는 기능도 갖추어야 합니다.

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 SharePoint 애플리케이션을 보호하는 방법을 자세히 설명합니다. 그리고 3계층 SharePoint 애플리케이션을 Azure로 복제하는 방법, 재해 복구 연습을 수행하는 방법 및 애플리케이션을 Azure로 장애 조치하는 방법에 대한 모범 사례를 설명합니다.

볼 수는 아래 비디오를 다중 계층 응용 프로그램을 Azure로 복구 하는 방법에 대 한 합니다.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목을 이해해야 합니다.

1. [Azure에 가상 머신 복제](site-recovery-vmware-to-azure.md)
2. 방법: [복구 네트워크 디자인](site-recovery-network-design.md)
3. [Azure로 테스트 장애 조치 수행](site-recovery-test-failover-to-azure.md)
4. [Azure로 장애 조치 수행](site-recovery-failover.md)
5. 방법: [도메인 컨트롤러 복제](site-recovery-active-directory.md)
6. 방법: [SQL Server 복제](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint 아키텍처

SharePoint은 계층된 토폴로지 및 서버 역할을 사용하여 한 개 이상의 서버에 배포되어 특정 목적 및 목표를 충족하는 팜 디자인을 구현할 수 있습니다. 수 많은 동시 사용자 및 콘텐츠 항목을 지원하는 일반적인 큰 규모의 많은 처리량을 요구하는 SharePoint 서버 팜은 확장성 전략의 일부로 서비스 그룹화를 사용합니다. 이 접근 방식에는 전용 서버에서 서버를 실행하고, 이러한 서비스를 함께 그룹화한 다음, 서버를 그룹으로 확장하는 것이 포함됩니다. 다음과 같은 토폴로지는 3계층 SharePoint 서버 팜에 대한 서비스 및 서버 그룹화를 보여 줍니다. 다양한 SharePoint 토폴로지에 대한 자세한 지침은 SharePoint 문서 및 제품 라인 아키텍처를 참조하세요. SharePoint 2013 배포에 대한 자세한 내용은 [이 문서](https://technet.microsoft.com/library/cc303422.aspx)에서 확인할 수 있습니다.



![배포 패턴 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery 지원

이 문서를 작성하기 위해 Windows Server 2012 R2 Enterprise가 있는 VMware 가상 머신이 사용되었습니다. SharePoint 2013 Enterprise Edition 및 SQL server 2014 Enterprise Edition이 사용되었습니다. Site Recovery 복제는 애플리케이션을 제한하지 않으므로 여기서 제시하는 권장 사항은 다음 시나리오에서도 유지됩니다.

### <a name="source-and-target"></a>원본 및 대상

**시나리오** | **보조 사이트로** | **Azure로**
--- | --- | ---
**Hyper-V** | 예 | 예
**VMware** | 예 | 예
**물리적 서버** | 예 | 예
**Azure** | 해당 없음 | 예

### <a name="sharepoint-versions"></a>SharePoint 버전
다음 SharePoint Server 버전이 지원됩니다.

* SharePoint Server 2013 Standard
* SharePoint Server 2013 Enterprise
* SharePoint Server 2016 Standard
* SharePoint Server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>주의할 사항

공유 디스크 기반 클러스터를 애플리케이션에서 모든 계층으로 사용하는 경우 Site Recovery 복제를 사용하여 해당 가상 머신을 복제할 수 없습니다. 애플리케이션에서 제공하는 기본 복제를 사용한 다음 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 모든 계층을 장애 조치합니다.

## <a name="replicating-virtual-machines"></a>가상 머신 복제

[이 지침](site-recovery-vmware-to-azure.md)에 따라 가상 머신을 Azure로 복제합니다.

* 복제가 완료되면 각 계층의 각 가상 머신으로 이동하여 '복제된 항목 &gt; 설정 &gt; 속성 &gt; Compute 및 네트워크'에서 동일한 가용성 집합을 선택했는지 확인합니다. 예를 들어 웹 계층에 VM이 3개 있는 경우 VM 3개가 모두 Azure의 동일한 가용성 집합에 속해 있도록 구성되었는지 확인합니다.

    ![가용성 집합 설정](./media/site-recovery-sharepoint/select-av-set.png)

* Active Directory 및 DNS 보호에 대한 지침은 [Active Directory 및 DNS 보호](site-recovery-active-directory.md) 문서를 참조하세요.

* SQL Server에서 실행되는 데이터베이스 계층 보호에 관한 지침은 [SQL Server 보호](site-recovery-active-directory.md) 문서를 참조하세요.

## <a name="networking-configuration"></a>네트워킹 구성

### <a name="network-properties"></a>네트워크 속성

* 앱 및 웹 계층 VM의 경우 Azure Portal에서 네트워크 설정을 구성하여 장애 조치 후에 VM이 올바른 DR 네트워크에 연결되도록 합니다.

    ![네트워크 선택](./media/site-recovery-sharepoint/select-network.png)


* 고정 IP를 사용하는 경우 **대상 IP** 필드에 가상 머신에서 사용할 IP를 지정합니다.

    ![고정 IP 설정](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS 및 트래픽 라우팅

인터넷 연결 사이트의 경우 Azure 구독에서 [‘우선 순위’ 형식의 Traffic Manager 프로필을 만듭니다](../traffic-manager/traffic-manager-create-profile.md) . 그런 다음, 다음과 같은 방법으로 DNS 및 Traffic Manager 프로필을 구성합니다.


| **Where** | **원본** | **대상**|
| --- | --- | --- |
| 공용 DNS | SharePoint 사이트에 대한 공용 DNS <br/><br/> 예: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| 온-프레미스 DNS | sharepointonprem.contoso.com | 온-프레미스 팜의 공용 IP |


Traffic Manager 프로필에서 [기본 및 복구 엔드포인트를 만듭니다](../traffic-manager/traffic-manager-configure-priority-routing-method.md). 온-프레미스 엔드포인트에 대해 외부 엔드포인트를, Azure 엔드포인트에 대해 공용 IP를 사용합니다. 우선 순위가 온-프레미스 엔드포인트에 더 높게 설정되었는지 확인합니다.

Traffic Manager가 가용성 사후 장애 조치(Failover)를 자동으로 감지하기 위해서 테스트 페이지를 SharePoint 웹 계층의 특정 포트(예: 800)에 호스트합니다. 이는 SharePoint 사이트 중에서 익명 인증을 사용하도록 설정할 수 없을 경우에 해결 방법입니다.

다음 설정으로 [Traffic Manager 프로필을 구성](../traffic-manager/traffic-manager-configure-priority-routing-method.md)합니다.

* 라우팅 방법 - ‘우선 순위’
* DNS TTL(Time to live) - ‘30초’
* 엔드포인트 모니터 설정 - 익명 인증을 사용하도록 설정할 수 있는 경우 특정 웹 사이트 엔드포인트를 지정할 수 있습니다. 또는 특정 포트(예: 800)에서 테스트 페이지를 사용할 수 있습니다.

## <a name="creating-a-recovery-plan"></a>복구 계획 만들기

복구 계획을 사용하면 다중 계층 애플리케이션에서 다양한 계층의 장애 조치를 시퀀싱하여 애플리케이션의 일관성을 유지할 수 있습니다. 다중 계층 웹 애플리케이션에 대한 복구 계획을 만드는 동안 아래 단계를 수행합니다. [복구 계획 만들기에 대한 자세한 정보](site-recovery-runbook-automation.md#customize-the-recovery-plan)

### <a name="adding-virtual-machines-to-failover-groups"></a>장애 조치 그룹에 가상 머신 추가

1. 앱 및 웹 계층 VM을 추가하여 복구 계획을 만듭니다.
2. ‘사용자 지정’을 클릭하여 VM을 그룹화합니다. 기본적으로 모든 VM은 ‘그룹 1’에 속합니다.

    ![RP 사용자 지정](./media/site-recovery-sharepoint/rp-groups.png)

3. 다른 그룹(그룹 2)을 만들고 웹 계층 VM을 새 그룹으로 이동합니다. 앱 계층 VM은 ‘그룹 1’의 일부여야 하며, 웹 계층 VM은 ‘그룹 2’의 일부여야 합니다. 이렇게 해야 앱 계층 VM이 먼저 부팅되고, 그 뒤에 웹 계층 VM이 이어집니다.


### <a name="adding-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가

아래 ‘Azure로 배포’ 단추를 클릭하면 가장 일반적으로 사용되는 Azure Site Recovery 스크립트를 Automation 계정에 배포할 수 있습니다. 게시된 스크립트를 사용하는 경우 스크립트에 있는 지침을 따라야 합니다.

[![Azure에 배포](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. ‘그룹 1’에 사전 작업 스크립트를 추가하여 SQL 가용성 그룹을 장애 조치(Failover)합니다. 샘플 스크립트에 게시된 ‘ASR-SQL-FailoverAG’ 스크립트를 사용합니다. 반드시 스크립트에서 제공된 지침을 따르고 스크립트에 필요한 사항을 적절히 변경합니다.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. 사후 작업 스크립트를 추가하여 웹 계층(그룹 2)의 장애 조치된 가상 머신에 부하 분산 장치를 연결합니다. 샘플 스크립트에 게시된 ‘ASR-AddSingleLoadBalancer’ 스크립트를 사용합니다. 반드시 스크립트에서 제공된 지침을 따르고 스크립트에 필요한 사항을 적절히 변경합니다.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Azure에서 새 팜을 가리키도록 DNS 레코드를 업데이트하는 수동 단계를 추가합니다.

    * 인터넷 연결 사이트의 경우 사후 장애 조치에 DNS 업데이트가 필요하지 않습니다. Traffic Manager를 구성하려면 ‘네트워킹 지침’ 섹션에 설명된 단계를 따릅니다. 이전 섹션에 설명된 대로 Traffic Manager 프로필이 설정된 경우 스크립트를 추가하여 Azure VM에서 더미 포트(예제에서는 800)를 엽니다.

    * 내부 연결 사이트의 경우 새로운 웹 계층 VM의 부하 분산 장치 IP를 가리키도록 DNS 레코드를 업데이트하는 수동 단계를 추가합니다.

4. 백업에서 검색 애플리케이션을 복원하거나 새 검색 서비스를 시작하는 수동 단계를 추가합니다.

5. Search Service 애플리케이션을 백업에서 복원하는 것은 아래 단계를 수행합니다.

    * 이 메서드는 Search Service 애플리케이션의 백업이 치명적인 이벤트 이전에 수행되었으며, DR 사이트에서 백업을 사용할 수 있다고 가정합니다.
    * 이는 백업을 예약하고(예: 하루 한 번) DR 사이트에 백업을 배치하는 복사 절차를 사용하여 손쉽게 달성할 수 있습니다. 복사 절차에는 AzCopy(Azure 복사)와 같은 스크립트를 사용한 프로그램 또는 DFSR(분산 파일 서비스 복제)를 설정하는 것이 포함될 수 있습니다.
    * 이제 SharePoint 팜이 실행되고 있으며, 중앙 관리 ‘Backup 및 복원’으로 이동하여 복원을 선택합니다. 복원은 지정된 백업 위치를 조사합니다(값을 업데이트해야 할 수도 있음). 복원하려는 Search Service 애플리케이션 백업을 선택합니다.
    * 검색이 복원됩니다. 복원은 해당 서버에 할당된 동일한 토폴로지(동일한 서버의 수)와 동일한 하드 드라이브 문자를 찾는다는 점에 유의하세요. 자세한 내용은 [‘SharePoint 2013의 Search Service 애플리케이션 복원’](https://technet.microsoft.com/library/ee748654.aspx) 문서를 참조하세요.


6. 새로운 Search Service 애플리케이션으로 시작은 다음 단계를 수행합니다.

    * 이 메서드는 DR 사이트에서 “검색 관리” 데이터베이스의 백업을 사용할 수 있다고 가정합니다.
    * 다른 Search Service 애플리케이션 데이터베이스는 복제되지 않으므로 다시 만들어야 합니다. 이렇게 하려면 중앙 관리로 이동한 다음 Search Service 애플리케이션을 삭제합니다. 검색 인덱스를 호스트하는 모든 서버에서 인덱스 파일을 삭제합니다.
    * Search Service 애플리케이션을 다시 만들고 데이터베이스를 다시 만듭니다. GUI를 통해 모든 작업을 수행할 수는 없기 때문에 이 서비스 애플리케이션을 다시 만드는 준비된 스크립트를 사용하는 것이 좋습니다. 예를 들어 인덱스 드라이브 위치의 설정 및 검색 토폴로지의 구성은 SharePoint PowerShell cmdlet을 사용해야만 가능합니다. Windows PowerShell cmdlet Restore-SPEnterpriseSearchServiceApplication을 사용하고 로그 전송 및 복제된 검색 관리 데이터베이스, Search_Service__DB를 지정합니다. 이 cmdlet은 검색 구성, 스키마, 관리되는 속성, 규칙 및 원본을 제공하고 다른 구성 요소의 기본 설정을 만듭니다.
    * Search Service 애플리케이션을 다시 만들고 나면 각 콘텐츠 원본에 대한 전체 크롤링을 시작하여 Search Service를 복원해야 합니다. 온-프레미스 팜에서 검색 권장 사항과 같은 분석 정보가 일부 손실됩니다.

7. 모든 단계가 완료되면 복구 계획을 저장하고 최종 복구 계획이 다음과 같이 표시됩니다.

    ![저장된 RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>테스트 장애 조치 수행
[이 지침](site-recovery-test-failover-to-azure.md)에 따라 테스트 장애 조치를 수행합니다.

1.  Azure Portal로 이동하여 복구 서비스 자격 증명 모음을 선택합니다.
2.  SharePoint 애플리케이션에 대해 만든 복구 계획을 클릭합니다.
3.  '테스트 장애 조치'를 클릭합니다.
4.  복구 지점 및 Azure 가상 네트워크를 선택하여 테스트 장애 조치 프로세스를 시작합니다.
5.  보조 환경이 가동 중이면 유효성 검사를 수행할 수 있습니다.
6.  유효성 검사가 완료되면 복구 계획에서 ‘테스트 장애 조치(Failover) 정리’를 클릭할 수 있으며, 테스트 장애 조치(Failover) 환경이 정리됩니다.

AD 및 DNS에 대한 테스트 장애 조치(Failover) 수행에 관한 지침은 [AD 및 DNS에 대한 테스트 장애 조치(Failover) 고려 사항](site-recovery-active-directory.md#test-failover-considerations) 문서를 참조하세요.

SQL Always ON 가용성 그룹에 대한 테스트 장애 조치(Failover) 수행에 관해서는 [SQL Server Always On에 대한 테스트 장애 조치(Failover) 수행](site-recovery-sql.md#steps-to-do-a-test-failover) 문서를 참조하세요.

## <a name="doing-a-failover"></a>장애 조치 수행
장애 조치(Failover)를 수행할 때 [이 지침](site-recovery-failover.md)을 따릅니다.

1.  Azure Portal로 이동하여 Recovery Services 자격 증명 모음을 선택합니다.
2.  SharePoint 애플리케이션에 대해 만든 복구 계획을 클릭합니다.
3.  '장애 조치'를 클릭합니다.
4.  복구 지점을 선택하여 장애 조치 프로세스를 시작합니다.

## <a name="next-steps"></a>다음 단계
Site Recovery를 사용하여 [다른 애플리케이션을 복제](site-recovery-workload.md)하는 것에 대해 자세히 알아볼 수 있습니다.
