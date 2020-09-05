---
title: SQL 컴퓨터에 대 한 Azure Security Center의 고급 데이터 보안 (미리 보기)
description: Azure Security Center에서 SQL 컴퓨터에 대 한 고급 데이터 보안을 사용 하도록 설정 하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: e0085ef5213853a1577ec039d5e360114aa7c64e
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566270"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>SQL 컴퓨터에 대 한 고급 데이터 보안 (미리 보기)

SQL 컴퓨터에 대 한 고급 데이터 보안 Azure Security Center은 Azure에서 호스트 되는 SQL Server, 다른 클라우드 환경 및 온-프레미스 컴퓨터를 보호 합니다. 이렇게 하면 하이브리드 환경을 완벽 하 게 지원 하기 위해 Azure 네이티브 SQL Server에 대 한 보호 기능이 확장 됩니다.

이 미리 보기 기능에는 잠재적인 데이터베이스 취약성을 식별 하 고 완화 하 고 데이터베이스에 대 한 위협을 나타낼 수 있는 비정상적인 활동을 검색 하기 위한 기능이 포함 되어 있습니다. 

* **취약성 평가** -잠재적인 데이터베이스 취약성을 검색, 추적 및 해결 하는 데 도움이 되는 검색 서비스입니다. 평가 검색은 SQL 컴퓨터의 보안 상태에 대 한 개요와 보안 결과에 대 한 세부 정보를 제공 합니다.

* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) -sql 삽입, 무차별 암호 대입 공격, 권한 남용 등의 위협에 대해 sql server를 지속적으로 모니터링 하는 검색 서비스입니다. 이 서비스는 의심 스러운 활동에 대 한 세부 정보, 위협에 대 한 완화 방법에 대 한 지침, Azure 센티널에서 조사를 계속 하는 옵션을 사용 하 여 Azure Security Center의 작업 기반 보안 경고를 제공 합니다.

>[!TIP]
> SQL 컴퓨터에 대 한 고급 데이터 보안은 Azure SQL Database, Azure Synapse 및 SQL Managed Instance에 사용할 수 있는 Azure Security Center의 [고급 데이터 보안 패키지](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)의 확장입니다.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>SQL 컴퓨터에 대 한 고급 데이터 보안 설정 

SQL 컴퓨터에 대 한 Azure Security Center의 고급 데이터 보안 설정에는 다음 두 단계가 포함 됩니다.

* SQL server의 호스트에 Log Analytics 에이전트를 프로 비전 합니다. Azure에 대 한 연결을 제공 합니다.

* Security Center의 가격 책정 및 설정 페이지에서 선택적 번들을 사용 하도록 설정 합니다.

이러한 두 가지 방법에 대해서는 아래에서 설명 합니다.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>1단계. SQL server의 호스트에 Log Analytics 에이전트를 프로 비전 합니다.

- **AZURE vm에서 SQL Server** -SQL 컴퓨터가 azure vm에서 호스트 되는 경우 [Log Analytics 에이전트를 자동으로 프로 비전](security-center-enable-data-collection.md#workspace-configuration)할 수 있습니다. 또는 수동 절차에 따라 [AZURE VM을 추가할](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines)수 있습니다.

- **Azure arc에 SQL Server** -SQL Server [azure arc](https://docs.microsoft.com/azure/azure-arc/) 컴퓨터에서 호스트 되는 경우 Security Center 권장 사항을 사용 하 여 Log Analytics 에이전트를 배포할 수 있습니다. "Log Analytics 에이전트는 Windows 기반 Azure Arc 컴퓨터 (미리 보기)에 설치 되어야 합니다. 또는 [Azure Arc 설명서](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)의 수동 절차를 따를 수도 있습니다.

- **SQL Server 온-프레미스** -SQL Server azure Arc 없이 온-프레미스 Windows 컴퓨터에서 호스트 되는 경우 azure에 연결 하는 두 가지 옵션이 있습니다.
    
    - **Azure Arc 배포** -모든 Windows 컴퓨터를 Security Center에 연결할 수 있습니다. 그러나 Azure Arc는 *모든* azure 환경에서 심층 통합을 제공 합니다. Azure Arc를 설정 하는 경우 포털에서 **SQL Server – Azure arc** 페이지가 표시 되 고 해당 페이지의 전용 **보안** 탭에 보안 경고가 표시 됩니다. 따라서 첫 번째 및 권장 옵션은 [호스트에서 Azure arc를 설정](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) 하 고 위의 **azure arc에서 SQL Server**에 대 한 지침을 따르는 것입니다.
        
    - **Azure arc를 사용 하지 않고 windows 컴퓨터 연결** -azure arc를 사용 하지 않고 windows 컴퓨터에서 실행 되는 SQL Server 연결 하도록 선택 하는 경우 [Azure Monitor에 windows 컴퓨터 연결](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)의 지침을 따르세요.


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>2단계. Security Center의 가격 책정 및 설정 페이지에서 선택적 번들을 사용 하도록 설정 합니다.

1. Security Center의 사이드바에서 **가격 책정 & 설정** 페이지를 엽니다.

    - **Azure Security Center의 기본 작업 영역** 을 사용 하는 경우 ("defaultworkspace-[사용자의 구독 id]-[region]") 관련 **구독**을 선택 합니다.

    - **기본이 아닌 작업 영역**을 사용 하는 경우 관련 **작업 영역** 을 선택 합니다. 필요한 경우 필터에 작업 영역 이름을 입력 합니다.

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. **컴퓨터의 SQL server (미리 보기)** 에 대 한 옵션을 사용으로 설정/해제 합니다. 

    [![선택적 번들을 사용 하 여 가격 책정 페이지 Security Center](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    컴퓨터의 SQL server에 대 한 고급 데이터 보안이 선택한 작업 영역에 연결 된 모든 SQL server에서 사용 하도록 설정 됩니다. SQL Server 인스턴스를 처음 다시 시작한 후 보호가 완전히 활성화 됩니다.

    >[!TIP] 
    > 새 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)의 지침을 따르세요.


1. 필요에 따라 보안 경고에 대 한 전자 메일 알림을 구성 합니다. 
    Security Center 경고가 생성 될 때 전자 메일 알림을 받을 받는 사람 목록을 설정할 수 있습니다. 전자 메일에는 관련 된 모든 세부 정보와 함께 Azure Security Center의 경고에 대 한 직접 링크가 포함 됩니다. 자세한 내용은 [보안 경고에 대 한 전자 메일 알림 설정](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)을 참조 하세요.



## <a name="explore-vulnerability-assessment-reports"></a>취약성 평가 보고서 살펴보기

취약성 평가 서비스는 일주일에 한 번 데이터베이스를 검색 합니다. 검색은 서비스를 사용 하도록 설정한 요일에 실행 됩니다.

취약성 평가 대시보드는 모든 데이터베이스에 대 한 평가 결과의 개요를 제공 하 고, 정상 및 비정상 데이터베이스에 대 한 요약을 제공 하 고, 위험 분포에 따라 실패 한 검사에 대 한 전체 요약을 제공 합니다.

Security Center에서 직접 취약성 평가 결과를 볼 수 있습니다.

1. Security Center의 사이드바에서 **권장 사항** 페이지를 열고 **컴퓨터의 SQL server에 대 한 권장 사항 (미리 보기)** 을 선택 합니다. 자세한 내용은 [Security Center 권장 사항](security-center-recommendations.md)을 참조 하세요. 


    [![* * 컴퓨터의 SQL server에 대 한 취약성을 재구성 해야 함 (미리 보기) * * 권장 사항](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    이 권장 사항에 대 한 상세 뷰가 나타납니다.

    [![컴퓨터의 SQL server에 대 한 * * 취약성에 대 한 자세한 보기는 재구성 해야 합니다 (미리 보기) * * 권장 사항](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 자세한 내용은 드릴 다운:

    * 스캔 한 리소스 (데이터베이스)의 개요와 테스트 된 보안 검사 목록을 보려면 관심 있는 서버를 선택 합니다.

    * 특정 SQL 데이터베이스 별로 그룹화 된 취약점의 개요를 보려면 원하는 데이터베이스를 선택 합니다.

    각 보기에서 보안 검사는 **심각도**를 기준으로 정렬 됩니다. 특정 보안 검사를 클릭 하 여 세부 정보 창에 **설명** **,이를 수정 하는** 방법 및 **영향** 또는 **벤치 마크**와 같은 기타 관련 정보를 표시 합니다.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>컴퓨터의 SQL server에 대 한 Advanced threat protection 경고
경고는 비정상적이 고 잠재적으로 위험한 SQL 컴퓨터 액세스 또는 악용 시도에 의해 생성 됩니다. 이러한 이벤트는 경고 [참조 페이지의 SQL Database 및 SQL Data Warehouse에 대 한 경고 섹션](alerts-reference.md#alerts-sql-db-and-warehouse)에 표시 된 경고를 트리거할 수 있습니다.



## <a name="explore-and-investigate-security-alerts"></a>보안 경고 탐색 및 조사

보안 경고는 Security Center의 경고 페이지, 리소스의 보안 탭 또는 경고 전자 메일의 직접 링크를 통해 사용할 수 있습니다.

1. 경고를 보려면 Security Center의 사이드바에서 **보안 경고** 를 선택 하 고 경고를 선택 합니다.

1. 경고는 각각에 대 한 자세한 수정 단계와 조사 정보를 포함 하 여 자체 포함 되도록 설계 되었습니다. 더 광범위 한 보기에 다른 Azure Security Center 및 Azure 센티널 기능을 사용 하 여 추가로 조사할 수 있습니다.

    * 추가 조사를 위해 SQL Server의 감사 기능을 사용 하도록 설정 합니다. Azure 센티널 사용자 인 경우 Windows 보안 로그 이벤트에서 센티널로 SQL 감사 로그를 업로드 하 고 풍부한 조사 환경을 즐길 수 있습니다. [SQL Server 감사에 대해 자세히 알아보세요](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * 보안 상태를 향상 시키려면 각 경고에 표시 된 호스트 컴퓨터에 대 한 Security Center 권장 사항을 사용 합니다. 이렇게 하면 향후 공격의 위험을 줄일 수 있습니다. 

    [경고 관리 및 대응에 대 한 자세한](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)정보.


## <a name="next-steps"></a>다음 단계

관련 자료는 다음 문서를 참조 하세요.

- [SQL Database 및 SQL Data Warehouse에 대 한 보안 경고](alerts-reference.md#alerts-sql-db-and-warehouse)
- [보안 경고에 대 한 전자 메일 알림 설정](security-center-provide-security-contact-details.md)
- [Azure 센티널에 대해 자세히 알아보기](https://docs.microsoft.com/azure/sentinel/)
- [Azure Security Center의 고급 데이터 보안 패키지](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)