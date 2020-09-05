---
title: Azure Migrate/Site Recovery를 사용하여 Windows Server 2008 서버를 Azure로 마이그레이션
description: 이 문서에서는 온-프레미스 Windows Server 2008 머신을 Azure로 마이그레이션하는 방법을 설명하고 Azure Migrate를 권장합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d8cd8bf2e1a29b122fb4bac7a12454f102183fe3
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845564"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Windows Server 2008을 실행하는 서버에서 Azure로 마이그레이션

이 자습서에서는 Azure Site Recovery를 사용하여 Windows Server 2008 또는 2008 R2를 실행하는 온-프레미스 서버에서 Azure로 마이그레이션하는 방법을 보여줍니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 마이그레이션을 위한 온-프레미스 환경을 준비합니다.
> * 대상 환경을 설정합니다.
> * 복제 정책을 설정합니다.
> * 복제를 활성화합니다.
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인합니다.
> * Azure로 장애 조치(failover) 및 마이그레이션을 완료합니다.

## <a name="migrate-with-azure-migrate"></a>Azure Migrate로 마이그레이션

[Azure Migrate](../migrate/migrate-services-overview.md) 서비스를 사용하여 머신을 Azure로 마이그레이션하는 것이 좋습니다. 

- Azure Migrate는 서버 마이그레이션을 위해 특별히 빌드되었습니다.
- Azure Migrate는 온-프레미스 머신을 Azure로 검색, 평가 및 마이그레이션하기 위한 중앙 허브를 제공합니다. Azure Site Recovery는 마이그레이션이 아닌 재해 복구에만 사용해야 합니다.
- Azure Migrate는 Windows Server 2008을 실행하는 서버의 마이그레이션을 지원합니다.


## <a name="migrate-with-site-recovery"></a>Site Recovery로 마이그레이션

### <a name="supported-operating-systems"></a>지원되는 운영 체제


|운영 체제  | Environment  |
|---------|---------|
|Windows Server 2008 SP2 - 32비트 및 64비트(IA-32 및 x86-64)</br>- 표준</br>- 엔터프라이즈</br>- 데이터 센터   |     VMware VM, Hyper-V VM 및 물리적 서버    |
|Windows Server 2008 R2 SP1 - 64비트</br>- 표준</br>- 엔터프라이즈</br>- 데이터 센터     |     VMware VM, Hyper-V VM 및 물리적 서버|

> [!WARNING]
> - Server Core를 실행하는 서버의 마이그레이션은 지원되지 않습니다.
> - 마이그레이션하기 전에 최신 서비스 팩 및 Windows 업데이트가 설치되어 있는지 확인합니다.


### <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 [VMware 및 물리적 서버 마이그레이션](vmware-azure-architecture.md) 또는 [Hyper-V 가상 머신 마이그레이션](hyper-v-azure-architecture.md)에 대한 Azure Site Recovery 아키텍처를 검토하는 것이 좋습니다. 

Windows Server 2008 또는 Windows Server 2008 R2를 실행 중인 Hyper-V 가상 머신을 마이그레이션하려면 [온-프레미스 머신을 Azure로 마이그레이션](migrate-tutorial-on-premises-azure.md) 자습서의 단계를 따릅니다.

이 자습서의 나머지 부분에서는 온-프레미스 VMware 가상 머신 및 Windows Server 2008 또는 2008 R2를 실행 중인 물리적 서버를 마이그레이션할 수 있는 방법을 보여줍니다.
> [!TIP]
> VMware VM을 Azure로 마이그레이션할 에이전트가 없는 방법을 찾으시나요? [여기를 클릭](https://aka.ms/migrateVMs-signup)


### <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- 구성 서버, 추가 프로세스 서버 및 Windows Server 2008 SP2 서버를 마이그레이션하는 데 사용되는 모바일 서비스는 Azure Site Recovery 소프트웨어의 버전 9.19.0.0 이상을 실행해야 합니다.

- 애플리케이션 일치 복구 지점 및 다중 VM 일관성 기능은 Windows Server 2008 SP2를 실행하는 서버의 복제에 대해 지원되지 않습니다. Windows Server 2008 SP2 서버는 크래시 일치 복구 지점으로 마이그레이션되어야 합니다. 기본적으로 5분마다 크래시 일치 복구 지점이 생성됩니다. 구성된 애플리케이션 일치 스냅샷 빈도로 복제 정책을 사용하면 애플리케이션 일치 복구 지점의 부족으로 인해 복제 상태가 위험으로 전환될 수 있습니다. 거짓 긍정을 방지하려면 복제 정책에서 애플리케이션 일치 스냅샷 빈도를 "꺼짐"으로 설정합니다.

- 마이그레이션되는 서버에는 작업할 모바일 서비스에 대한 .NET Framework 3.5 서비스 팩 1이 있어야 합니다.

- 서버에 동적 디스크가 있는 경우 특정 구성에서 장애 조치된 서버의 이러한 디스크가 오프라인 상태로 표시되거나 외부 디스크와 같이 표시되어 있음을 확인할 수 있습니다. 동적 디스크에서 미러된 볼륨에 대한 미러된 집합 상태가 "중복 실패"로 표시되어 있음을 확인할 수도 있습니다. 수동으로 이러한 디스크를 가져오고 다시 활성화하여 diskmgmt.msc에서 이 문제를 해결할 수 있습니다.

- 마이그레이션되는 서버에는 vmstorfl.sys 드라이버가 있어야 합니다. 드라이버가 마이그레이션되는 서버에 없는 경우 장애 조치(failover)가 실패할 수 있습니다. 
  > [!TIP]
  >드라이버가 "C:\Windows\system32\drivers\vmstorfl.sys"에 있는지 확인합니다. 드라이버를 찾을 수 없는 경우 현재 위치에서 더미 파일을 만들어 문제를 해결할 수 있습니다. 
  >
  > 명령 프롬프트(실행 > cmd)를 열고 다음 "copy nul c:\Windows\system32\drivers\vmstorfl.sys"를 실행합니다.

- 장애 조치(failover)되거나 Azure로 테스트 장애 조치(failover)된 후 32비트 운영 체제를 실행하는 Windows Server 2008 SP2 서버로 RDP를 즉시 실행할 수 없습니다. Azure Portal에서 장애 조치(failover)된 가상 머신을 다시 시작하고 연결을 다시 시도합니다. 여전히 연결할 수 없는 경우 서버가 원격 데스크톱 연결을 허용하도록 구성되어 있는지 확인하고, 연결을 차단하는 방화벽 규칙 또는 네트워크 보안 그룹이 없는지 확인합니다. 
  > [!TIP]
  > 서버를 마이그레이션하기 전에 테스트 장애 조치(failover)를 하는 것이 좋습니다. 마이그레이션하는 각 서버에서 적어도 하나의 성공적인 테스트 장애 조치(failover)를 수행했는지 확인합니다. 테스트 장애 조치(failover)의 일부로 테스트 장애 조치(failover) 머신에 연결하고 작업이 예상대로 작동하는지 확인합니다.
  >
  >테스트 장애 조치(failover) 작업은 중단되지 않으며 원하는 격리된 네트워크에서 가상 머신을 만들어 마이그레이션을 테스트할 수 있도록 합니다. 장애 조치(failover) 작업과 달리 테스트 장애 조치(failover) 작업 중 데이터 복제는 계속해서 진행됩니다. 마이그레이션할 준비가 되기 전에 원하는 만큼 테스트 장애 조치(failover)를 수행할 수 있습니다. 
  >
  


### <a name="get-started"></a>시작하기

다음 작업을 수행하여 Azure 구독 및 온-프레미스 VMware/물리적 환경을 준비합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. 온-프레미스 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 준비


### <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **관리 도구** > **Backup 및 Site Recovery**를 클릭합니다.
3. **이름**에서 **W2K8-migration**이라는 이름을 지정합니다. 구독이 두 개 이상인 경우 적절한 구독을 선택합니다.
4. 리소스 그룹 **w2k8migrate**를 만듭니다.
5. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
6. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정**을 클릭하고 **만들기**를 클릭합니다.

   ![새 자격 증명 모음 만들기 옵션을 보여주는 스크린샷](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

**대시보드**의 **모든 리소스** 아래와 주 **Recovery Services 자격 증명 모음** 페이지에 새 자격 증명 모음이 추가됩니다.


### <a name="prepare-your-on-premises-environment-for-migration"></a>마이그레이션을 위한 온-프레미스 환경 준비

- VMware에서 실행 중인 Windows Server 2008 가상 머신을 마이그레이션하려면 [VMware에서 온-프레미스 구성 서버를 설정](vmware-azure-tutorial.md#set-up-the-source-environment)합니다.
- 구성 서버를 VMware 가상 머신으로 설정할 수 없는 경우 [온-프레미스 실제 서버 또는 가상 머신에서 구성 서버를 설정](physical-azure-disaster-recovery.md#set-up-the-source-environment)합니다.

### <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 리소스 관리자 배포 모델을 지정합니다.
3. Site Recovery가 호환되는 Azure Storage 계정 및 네트워크가 하나 이상 있는지 확인합니다.


### <a name="set-up-a-replication-policy"></a>복제 정책 설정

1. 새 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** >  **+복제 정책**을 클릭합니다.
2. **복제 정책 만들기**에서 정책 이름을 지정합니다.
3. **RPO 임계값**에서 RPO(복구 목표 시점) 제한을 지정합니다. 복제 RPO가 이 제한을 초과하면 경고가 생성됩니다.
4. **복구 시점 보존**에서 각 복구 시점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 복제된 서버는 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다. Premium Storage에 복제된 컴퓨터의 경우 최대 24시간 보존이 지원되고 표준 스토리지에 복제된 경우 72시간 보존이 지원됩니다.
5. **앱 일치 스냅샷 빈도**에서 **꺼짐**을 지정합니다. **확인** 을 클릭하여 정책을 만듭니다.

정책은 구성 서버와 자동으로 연결됩니다.

> [!WARNING]
> 복제 정책의 앱 일치 스냅샷 빈도 설정에서 **꺼짐**을 지정합니다. Windows Server 2008을 실행하는 서버를 복제하는 동안 크래시 일관성 복구 시점만 지원됩니다. 앱 일치 스냅샷 빈도에 대해 다른 값을 지정하면 앱 일치 복구 시점의 부족으로 인해 서버의 복제 상태가 위험으로 전환되어 잘못된 경고가 발생합니다.

   ![복제 정책 만들기 옵션을 보여주는 스크린샷](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>복제 사용

마이그레이션할 Windows Server 2008 SP2 / Windows Server 2008 R2 SP1 서버에 대해 [복제를 사용](physical-azure-disaster-recovery.md#enable-replication)합니다.
   
   ![물리적 머신을 추가하는 옵션을 보여주는 스크린샷](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![복제를 사용하도록 설정하는 옵션을 보여주는 스크린샷](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>테스트 마이그레이션 실행

초기 복제가 완료되고 서버 상태가 **보호됨**으로 전환된 후 복제 서버의 테스트 장애 조치(failover)를 수행할 수 있습니다.

모든 것이 예상대로 작동하는지 확인할 수 있도록 Azure에 대해 [테스트 장애 조치(Failover)](tutorial-dr-drill-azure.md)를 실행합니다.

   ![테스트 장애 조치(failover) 명령을 보여주는 스크린샷](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Azure로 마이그레이션

마이그레이션할 컴퓨터에 대해 장애 조치(Failover)를 실행합니다.

1. **설정** > **복제된 항목**에서 컴퓨터 > **장애 조치(Failover)** 를 클릭합니다.
2. **장애 조치(Failover)** 에서 장애 조치할 **복구 시점**을 선택합니다. 최신 복구 시점을 선택합니다.
3. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 서버를 종료하려고 합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
4. Azure VM이 예상대로 Azure에 표시되는지 확인합니다.
5. **복제된 항목**에서 서버를 마우스 오른쪽 단추로 클릭하고 **마이그레이션 완료**를 클릭합니다. 다음을 수행합니다.

    - 마이그레이션 프로세스가 완료되고, 서버에 대한 복제가 중지되고, 서버에 대한 Site Recovery 청구가 중지됩니다.
    - 이 단계는 복제 데이터를 정리합니다. 마이그레이션된 VM을 삭제하지 않습니다.

   ![전체 마이그레이션 명령을 보여주는 스크린샷](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **진행 중인 장애 조치(failover)는 취소하지 마세요**. 장애 조치(failover)가 시작되기 전에 서버 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 서버는 계속 복제되지 않습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> Azure Migrate에 대한 [일반적인 질문을 검토](../migrate/resources-faq.md)합니다.
