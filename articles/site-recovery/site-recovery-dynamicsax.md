---
title: Azure Site Recovery를 사용하여 다중 계층 Dynamics AX 배포에 대한 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Dynamics AX에 대한 재해 복구를 설정하는 방법을 설명합니다.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: b97bf56c23dfa96acf7cb5af5ac28b4270de117d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281486"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>다중 계층 Dynamics AX 애플리케이션에 대한 재해 복구 설정   




 Dynamics AX는 여러 위치 간 프로세스를 표준화하고 리소스를 관리하며 규정 준수를 간소화하려는 기업들에게 가장 인기 있는 ERP 솔루션 중 하나입니다. 애플리케이션은 조직에 매우 중요하므로 재해 발생 시 최소한의 시간으로 애플리케이션이 가동될 수 있어야 합니다.

현재 Dynamics AX는 기본적으로 재해 복구 기능을 제공하지 않습니다. Dynamics AX는 Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server 및 Reporting Services 등의 여러 서버 구성 요소로 이루어져 있습니다. 이러한 각 구성의 재해 복구를 수동으로 관리하려면 비용이 많이 들 뿐만 아니라 오류가 발생하기 쉽습니다.

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Dynamics AX 애플리케이션을 위한 재해 복구 솔루션을 만드는 방법에 대해 설명합니다. 또한 한 번의 클릭 복구 계획을 사용한 계획된/계획되지 않은 테스트 장애 조치, 지원되는 구성 및 필수 조건도 이 문서에서 다룹니다.



## <a name="prerequisites"></a>필수 조건

Site Recovery를 사용하여 Dynamics AX 애플리케이션의 재해 복구를 구현하려면 다음 필수 조건을 완료해야 합니다.

• 온-프레미스 Dynamics AX 배포를 설치합니다.

• Azure 구독에 Site Recovery 자격 증명 모음을 만듭니다.

• Azure가 복구 사이트인 경우 VM에서 Azure Virtual Machine Readiness Assessment 도구를 실행합니다. Azure Virtual Machines와 Site Recovery 서비스 간에 호환되어야 합니다.

## <a name="site-recovery-support"></a>Site Recovery 지원

이 문서를 작성하기 위해 Windows Server 2012 R2 Enterprise에서 Dynamics AX 2012 R3가 있는 VMware 가상 머신이 사용되었습니다. Site Recovery 복제는 애플리케이션에 상관없이 실행되므로 여기서 제시하는 권장 사항은 다음 시나리오에서도 적용될 것입니다.

### <a name="source-and-target"></a>원본 및 대상

**시나리오** | **보조 사이트로** | **Azure로**
--- | --- | ---
**Hyper-V** | 예 | 예
**VMware** | 예 | 예
**물리적 서버** | 예 | 예

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Site Recovery를 사용하여 Dynamics AX 애플리케이션의 재해 복구를 활성화
### <a name="protect-your-dynamics-ax-application"></a>Dynamics AX 애플리케이션 보호
완전한 애플리케이션 복제 및 복구가 가능하려면 Dynamics AX의 각 구성 요소를 보호해야 합니다.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Active Directory 및 DNS 복제 설정 

Dynamics AX 애플리케이션이 작동하려면 재해 복구 사이트에 Active Directory가 필요합니다. 다음은 고객의 온-프레미스 환경의 복잡도에 따라 권장되는 두 가지 옵션입니다.

**옵션 1**

고객은 온-프레미스 사이트 전체에 적은 수의 애플리케이션과 단 하나의 도메인 컨트롤러를 사용하고 있으며, 전체 사이트를 한 번에 장애 조치(failover)할 계획입니다. 이 경우 Site Recovery 복제를 사용하여 도메인 컨트롤러 컴퓨터를 보조 사이트로 복제하는 것을 권장합니다(사이트-사이트 시나리오와 사이트-Azure 시나리오에 모두 적용 가능).

**옵션 2**

고객은 많은 수의 애플리케이션을 사용하고 Active Directory 포리스트를 실행 중이며, 애플리케이션을 한 번에 몇 개씩 장애 조치(failover)할 계획입니다. 이 경우 추가 도메인 컨트롤러를 재해 복구 사이트(보조 사이트 또는 Azure)에 설정하는 것을 권장합니다.

 자세한 내용은 [재해 복구 사이트에서 도메인 컨트롤러를 사용하도록 설정](site-recovery-active-directory.md)을 참조하세요. 이어지는 본 문서 내용에서는 재해 복구 사이트에서 도메인 컨트롤러를 사용할 수 있다고 가정합니다.

### <a name="2-set-up-sql-server-replication"></a>2. SQL Server 복제 설정
SQL 계층 보호를 위해 권장되는 옵션에 대한 기술 지침은 [SQL Server 및 Azure Site Recovery를 통한 애플리케이션 복제](site-recovery-sql.md)를 참조하세요.

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Dynamics AX 클라이언트 및 Application Object Server VM에 대해 보호 사용
VM이 [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 또는 [VMware](site-recovery-vmware-to-azure.md)에 배포되었는지 여부에 따라 관련 Site Recovery 구성을 수행합니다.

> [!TIP]
> 크래시 일관성 주기를 15분으로 구성하는 것이 좋습니다.
>

다음 스냅숏에서는 VMware 사이트-Azure 보호 시나리오에서 Dynamics 구성 요소 VM의 보호 상태를 보여 줍니다.

![보호된 항목](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. 네트워킹 구성
**VM 계산 및 네트워크 설정 구성**

Dynamics AX 클라이언트 및 Application Object Server VM의 경우 Site Recovery에서 네트워크 설정을 구성하여 장애 조치 후에 VM 네트워크가 올바른 재해 복구 네트워크에 연결되도록 합니다. 이러한 계층에 대한 재해 복구 네트워크가 SQL 계층으로 라우팅할 수 있는지 확인합니다.

다음 스냅숏에 나온 것처럼 복제된 항목에서 VM을 선택하여 네트워크 설정을 구성할 수 있습니다.

* Application Object Server 서버의 경우 정확한 가용성 집합을 선택합니다.

* 고정 IP를 사용하는 경우 **대상 IP** 텍스트 상자에 VM이 사용할 IP를 지정합니다.

    ![네트워크 설정](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. 복구 계획 만들기

Site Recovery에서 복구 계획을 만들어 장애 조치 프로세스를 자동화할 수 있습니다. 복구 계획에 앱 계층 및 웹 계층을 추가합니다. 앱 계층 전에 프런트 엔드가 종료되도록 다른 그룹에 정렬합니다.

1. 구독에서 Site Recovery 자격 증명 모음을 선택하고 **복구 계획** 타일을 클릭합니다.

2. **+ 복구 계획**을 선택하고 이름을 지정합니다.

3. **원본** 및 **대상**을 선택합니다. 대상으로 Azure 또는 보조 사이트를 지정할 수 있습니다. Azure를 선택하는 경우 배포 모델을 지정해야 합니다.

    ![복구 계획 만들기](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. 복구 계획에 대한 Application Object Server와, 클라이언트 VM을 선택하고 ✓를 선택합니다.

    ![항목 선택](./media/site-recovery-dynamics-ax/selectvms.png)

    복구 계획 예:

    ![복구 계획 세부 정보](./media/site-recovery-dynamics-ax/recoveryplan.png)

다음 단계를 추가하여 Dynamics AX 애플리케이션에 대한 복구 계획을 사용자 지정할 수 있습니다. 앞의 스냅숏은 단계를 모두 추가한 후 전체 복구 계획을 보여 줍니다.


* **SQL Server 장애 조치(failover) 단계**: SQL Server 관련 복구 단계에 대한 자세한 내용은 [SQL Server 및 Azure Site Recovery를 통한 애플리케이션 복제](site-recovery-sql.md)를 참조하세요.

* **장애 조치(failover) 그룹 1** 애플리케이션 개체 서버 VM을 장애 조치합니다.
선택한 복구 지점이 데이터베이스 PIT에 최대한 근접하면서도 앞서지 않도록 합니다.

* **스크립트**: 부하 분산 장치(E-A만)를 추가합니다.
Application Object Server VM 그룹이 나온 뒤 스크립트를 추가(Azure Automation을 통해)하여 부하 분산 장치를 추가합니다. 스크립트를 사용하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [다중 계층 애플리케이션의 재해 복구를 위해 부하 분산 장치를 추가하는 방법](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)을 참조하세요.

* **장애 조치(failover) 그룹 2**: Dynamics AX 클라이언트 VM을 장애 조치합니다. 복구 계획의 일부로 웹 계층 VM을 장애 조치합니다.


### <a name="perform-a-test-failover"></a>테스트 장애 조치(failover) 수행

테스트 장애 조치 중인 Active Directory와 관련한 자세한 내용은 "Active Directory 재해 복구 솔루션" 도우미 가이드를 참조하세요.

테스트 장애 조치 중 Active Directory 특정 정보에 대한 자세한 내용은 [SQL Server 및 Azure 사이트 복구를 통한 애플리케이션 복제](site-recovery-sql.md)를 참조하세요.

1. Azure Portal로 이동하여 Site Recovery 자격 증명 모음을 선택합니다.

2. Dynamics AX에 대해 만든 복구 계획을 선택합니다.

3. **테스트 장애 조치**를 선택합니다.

4. 테스트 장애 조치(failover) 프로세스를 시작할 가상 네트워크를 선택합니다.

5. 보조 환경이 가동되면 유효성 검사를 수행할 수 있습니다.

6. 유효성 검사가 완료되면 **유효성 검사 완료**를 선택합니다. 테스트 장애 조치(failover) 환경이 정리됩니다.

테스트 장애 조치 수행에 대한 자세한 내용은 [Site Recovery에서 Azure로 테스트 장애 조치](site-recovery-test-failover-to-azure.md)를 참조하세요.

### <a name="perform-a-failover"></a>장애 조치 수행

1. Azure Portal로 이동하여 Site Recovery 자격 증명 모음을 선택합니다.

2. Dynamics AX에 대해 만든 복구 계획을 선택합니다.

3. **장애 조치**를 선택하고 **장애 조치**를 선택합니다.

4. 대상 네트워크를 선택하고 **✓**를 선택하여 장애 조치 프로세스를 시작합니다.

장애 조치 수행에 대한 자세한 내용은 [Site Recovery에서 장애 조치](site-recovery-failover.md)를 참조하세요.

### <a name="perform-a-failback"></a>장애 복구(failback) 수행

장애 복구 중인 SQL Server와 관련한 사항은 [SQL Server 및 Azure Site Recovery를 통한 애플리케이션 복제](site-recovery-sql.md)를 참조하세요.

1. Azure Portal로 이동하여 Site Recovery 자격 증명 모음을 선택합니다.

2. Dynamics AX에 대해 만든 복구 계획을 선택합니다.

3. **장애 조치**를 선택하고 **장애 조치**를 선택합니다.

4. **방향 변경**을 선택합니다.

5. 데이터 동기화 및 VM 만들기 중 적절한 옵션을 선택합니다.

6. **✓**를 선택하여 장애 복구 프로세스를 시작합니다.


장애 복구 수행에 대한 자세한 내용은 [Azure에서 온-프레미스로의 VMware VM 장애 복구](site-recovery-failback-azure-to-vmware.md)를 참조하세요.

## <a name="summary"></a>요약
Site Recovery를 사용하여 Dynamics AX 애플리케이션에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 중단이 발생할 경우 어디서나 몇 초 이내에 장애 조치(failover)를 시작하고 몇 분 만에 애플리케이션을 가동할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Site Recovery로 엔터프라이즈 워크로드를 보호하는 방법에 대해 자세히 알아보려면 [어떤 워크로드를 보호할 수 있습니까?](site-recovery-workload.md) 를 참조하세요.
