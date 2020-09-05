---
title: Azure Site Recovery를 사용하여 정부와 공용 지역 간에 Azure VM 이동
description: Azure Site Recovery를 사용하여 Azure 정부와 공용 지역 간에 Azure VM을 이동합니다.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5ca6e7fa6e02ff6c5e49185c2fb02f9bc5a16d9c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927303"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Azure Government와 공용 Azure 지역 간에 Azure VM 이동 

기존 VM의 가용성을 높이거나 관리 효율성을 개선하거나 거버넌스 상의 이유로 Government와 공용 Azure 지역 간에 IaaS VM을 이동하고자 할 수 있습니다(자세한 설명은 [여기](azure-to-azure-move-overview.md) 참조).

BCDR(비즈니스 지속성 및 재해 복구)을 위해 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 머신 및 Azure VM의 재해 복구를 관리하고 오케스트레이션하는 것은 물론, Site Recovery를 사용하여 Azure VM을 보조 지역으로 이동하는 작업을 관리할 수도 있습니다.       

이 자습서에서는 Azure Site Recovery를 사용하여 Azure Government와 공용 Azure 지역 간에 Azure VM을 이동하는 방법을 보여줍니다. 같은 방법을 동일한 지리적 클러스터 내에 있지 않은 Azure 지역 쌍 간에 VM을 이동하도록 확장할 수 있습니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 필수 조건 확인
> * 원본 VM 준비
> * 대상 Azure 지역 준비
> * 대상 Azure 지역에 데이터 복사
> * 구성 테스트
> * 이동 수행
> * 원본 Azure 지역의 리소스 삭제

> [!IMPORTANT]
> 이 자습서에서는 Azure Government와 공용 Azure 지역 간에, 또는 일반적인 Azure VM용 재해 복구 솔루션에서 지원되지 않는 Azure 지역 쌍 간에 Azure VM을 이동하는 방법을 보여줍니다. 경우에 따라 원본 및 대상 Azure 지역 쌍이 [지원됩니다](./azure-to-azure-support-matrix.md#region-support). 이동에 대해서는 이 [문서](azure-to-azure-tutorial-migrate.md)를 참조하세요. 가용성 세트를 다른 Azure 지역의 영역 고정 VM으로 이동하여 가용성을 개선해야 하는 요구 사항이 있는 경우 [여기](move-azure-VMs-AVset-Azone.md) 자습서를 참조하세요.

> [!IMPORTANT]
> DR 시나리오의 경우 데이터 대기 시간을 염두에 두고 Azure 지역 쌍을 정의하는 것이 중요하므로, 지원되지 않는 Azure 지역 쌍 간에 이 방법을 사용하여 DR을 구성하는 것은 바람직스럽지 않습니다.

## <a name="verify-prerequisites"></a>필수 조건 확인

> [!NOTE]
> 이 시나리오에 대한 [아키텍처 및 구성 요소](physical-azure-architecture.md)를 이해해야 합니다. 이 아키텍처는 **VM을 실제 서버로 처리하여** Azure VM을 이동하는 경우에 사용합니다.

- 모든 구성 요소에 대한 [지원 요구 사항](vmware-physical-secondary-support-matrix.md)을 검토합니다.
- 복제할 서버가 [Azure VM 요구 사항](vmware-physical-secondary-support-matrix.md#replicated-vm-support)을 준수해야 합니다.
- 복제하려는 각 서버에서 모바일 서비스를 자동으로 설치하기 위해 계정을 준비합니다.

- Azure에서 대상 지역으로 장애 조치(failover)한 후에는 원본 지역에 대해 직접 장애 복구를 수행할 수 없습니다. 대상에 다시 복제하도록 설정해야 합니다.

### <a name="verify-azure-account-permissions"></a>Azure 계정 권한 확인

Azure 계정에 Azure로 VM을 복제하기 위한 권한이 있는지 확인합니다.

- Azure에 컴퓨터를 복제하는 데 필요한 [권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)을 검토합니다.
- [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 권한을 확인하고 수정합니다. 

### <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

대상 [Azure 네트워크](../virtual-network/quick-create-portal.md)를 설정합니다.

- Azure VM은 장애 조치 후 처음 만들 때 이 네트워크에 배치됩니다.
- 네트워크가 Recovery Services 자격 증명 모음과 같은 지역에 있어야 합니다.


### <a name="set-up-an-azure-storage-account"></a>Azure Storage 계정을 설정

[Azure Storage 계정](../storage/common/storage-account-create.md)을 설정합니다.

- Site Recovery는 온-프레미스 컴퓨터를 Azure Storage에 복제합니다. 장애 조치가 발생한 후에 스토리지에서 Azure VM을 만듭니다.
- 스토리지 계정은 Recovery Services 자격 증명 모음과 동일한 영역에 있어야 합니다.


## <a name="prepare-the-source-vms"></a>원본 VM 준비

### <a name="prepare-an-account-for-mobility-service-installation"></a>모바일 서비스 설치를 위한 계정 준비

복제하려는 각 서버에 모바일 서비스가 설치되어야 합니다. Site Recovery는 사용자가 서버에 대해 복제를 사용하도록 설정하면 이 서비스를 자동으로 설치합니다. 자동으로 설치하려는 경우 Site Recovery가 서버에 액세스하는 데 사용할 계정을 준비해야 합니다.

- 도메인 또는 로컬 계정을 사용할 수 있습니다.
- Windows VM에서는 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정합니다. 그러려면 레지스터의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 값이 1인 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가합니다.
- CLI에서 설정을 사용하지 않도록 설정하기 위한 레지스트리 항목을 추가하려면 다음을 입력합니다. ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux에서 계정은 원본 Linux 서버의 루트여야 합니다.


## <a name="prepare-the-target-region"></a>대상 Azure 지역 준비

1. Azure 구독에서 재해 복구에 사용되는 대상 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

2. 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery를 사용하여 대상에 데이터를 복사하는 경우 Site Recovery는 동일한 크기 또는 가장 비슷한 크기를 대상 VM에 선택합니다.

3. 원본 네트워킹 레이아웃에서 식별된 모든 구성 요소의 대상 리소스를 만듭니다. 그래야만 대상 Azure 지역으로 전환 후 원본에서 사용하던 모든 기능을 VM에서 그대로 사용할 수 있습니다.

    > [!NOTE]
    > 원본 VM을 복제하도록 설정하면 Azure Site Recovery가 자동으로 가상 네트워크를 검색하고 만들지만, 네트워크를 미리 만들어 두었다가 복제 설정을 위한 사용자 흐름에서 VM에 할당할 수도 있습니다. 하지만 그 외의 리소스는 아래 설명처럼 대상 Azure 지역에 수동으로 만들어야 합니다.

     다음 문서를 참조하여 원본 VM 구성에 따라 가장 일반적으로 사용되는 네트워크 리소스를 만드세요.

    - [네트워크 보안 그룹](../virtual-network/manage-network-security-group.md)
    - [부하 분산 장치](../load-balancer/index.yml)
    - [공용 IP](../virtual-network/virtual-network-public-ip-address.md)
    
    그 외의 네트워킹 구성 요소는 네트워킹 [설명서](../index.yml?pivot=products&panel=network)를 참조하세요.

4. 대상 Azure 지역으로 최종 전환하기 전에 구성을 테스트하려면 수동으로 대상 Azure 지역에 [비-프로덕션 네트워크를 만듭니다](../virtual-network/quick-create-portal.md). 이렇게 하면 프로덕션과의 간섭을 최소화할 수 있으므로 권장하는 방법입니다.

## <a name="copy-data-to-the-target-region"></a>대상 Azure 지역에 데이터 복사
아래 단계는 Azure Site Recovery를 사용하여 대상 Azure 지역에 데이터를 복사하는 방법을 안내합니다.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>원본 지역을 제외한 모든 지역에 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **관리 도구** > **Backup 및 Site Recovery**를 클릭합니다.
3. **이름**에서 **ContosoVMVault**라는 이름을 지정합니다. 구독이 여러 개인 경우 적절한 구독을 선택합니다.
4. **ContosoRG** 리소스 그룹을 만듭니다.
5. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
6. Recovery Services 자격 증명 모음에서 **개요** > **ConsotoVMVault** >  **+복제**를 클릭합니다.
7. **Azure에** > **가상화되지 않음/기타**를 선택합니다.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>VM을 검색하도록 구성 서버를 설정합니다.


구성 서버를 설정하고 자격 증명 모음에 등록한 후 VM을 검색합니다.

1. **Site Recovery** > **인프라 준비** > **원본**을 클릭합니다.
2. 구성 서버가 없는 경우 **+구성 서버**를 클릭합니다.
3. **서버 추가**에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
5. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치를 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

   ![원본 설정](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>자격 증명 모음에 구성 서버 등록

시작하기 전에 다음을 수행하세요. 

#### <a name="verify-time-accuracy"></a>시간 정확도 확인
구성 서버 컴퓨터에서 시스템 시계가 [시간 서버](/windows-server/networking/windows-time-service/windows-time-service-top)와 동기화되었는지 확인합니다. 서로 일치해야 합니다. 15분 빠르거나 늦은 경우 설치가 실패할 수 있습니다.

#### <a name="verify-connectivity"></a>연결 확인
컴퓨터에서 사용자 환경을 기반으로 다음 URL에 액세스할 수 있는지 확인합니다. 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP 주소 기반 방화벽 규칙은 HTTPS(443) 포트를 통해 위에 나열된 모든 Azure URL에 대한 통신을 허용해야 합니다. IP 범위를 간소화하고 제한하려면 URL 필터링을 수행하는 것이 좋습니다.

- **상업용 IP** - [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다. AAD, 백업, 복제 및 스토리지 URL을 지원하기 위해 구독의 Azure 지역에 대한 IP 주소 범위를 허용합니다.  
- **정부 IP** - AAD, 백업, 복제 및 스토리지 URL을 지원하기 위해 모든 USGov 지역(버지니아, 텍사스, 애리조나 및 아이오와)의 [Azure Government 데이터 센터 IP 범위](https://www.microsoft.com/en-us/download/details.aspx?id=57063) 및 HTTPS(443) 포트를 허용합니다.  

#### <a name="run-setup"></a>설치 프로그램 실행
로컬 관리자 권한으로 통합 설치를 실행하여 구성 서버를 설치합니다. 프로세스 서버 및 마스터 대상 서버도 기본적으로 구성 서버에 설치됩니다.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

등록이 완료되면 자격 증명 모음의 **설정** > **서버** 페이지에 구성 서버가 표시됩니다.

### <a name="configure-target-settings-for-replication"></a>복제 대상 설정 구성

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델을 지정합니다.
3. Site Recovery가 호환되는 Azure Storage 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>복제 정책 만들기

1. 새 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** >  **+복제 정책**을 클릭합니다.
2. **복제 정책 만들기**에서 정책 이름을 지정합니다.
3. **RPO 임계값**에서 RPO(복구 목표 시점) 제한을 지정합니다. 이 값은 데이터 복구 지점 생성 횟수를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
4. **복구 시점 보존**에서 각 복구 시점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다. Premium Storage에 복제된 컴퓨터의 경우 최대 24시간 보존이 지원되고 표준 스토리지에 복제된 경우 72시간 보존이 지원됩니다.
5. **애플리케이션 일치 스냅샷 빈도**에서 애플리케이션 일치 스냅샷이 포함된 복구 지점을 만드는 빈도(분)를 지정합니다. **확인** 을 클릭하여 정책을 만듭니다.

    ![복제 정책](./media/physical-azure-disaster-recovery/replication-policy.png)


정책은 구성 서버와 자동으로 연결됩니다. 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy**인 경우 장애 복구(failback) 정책 **rep-policy-failback**이 만들어집니다. 이 정책은 Azure에서 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.

### <a name="enable-replication"></a>복제 사용

- 복제를 사용할 경우 Site Recovery에서 모바일 서비스를 설치합니다.
- 서버에 대해 복제를 사용하도록 설정하면 변경 내용이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.

1. **애플리케이션 복제** > **원본**을 클릭합니다.
2. **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **물리적 컴퓨터**를 선택합니다.
4. 프로세스 서버(구성 서버)를 선택합니다. 그런 후 **OK**를 클릭합니다.
5. **대상**에서 장애 조치(failover) 후 Azure VM을 만들 구독 및 리소스 그룹을 선택합니다. Azure(클래식 또는 리소스 관리)에서 사용할 배포 모델을 선택합니다.
6. 데이터 복제에 사용할 Azure Storage 계정을 선택합니다. 
7. 장애 조치(failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다.
8. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성**을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다. 
9. **물리적 컴퓨터**에서 **+물리적 컴퓨터**를 클릭합니다. 이름 및 IP 주소를 지정합니다. 복제하려는 컴퓨터의 운영 체제를 선택합니다. 서버가 검색된 후 나열되는 데 몇 분 정도 걸립니다. 

   > [!WARNING]
   > 이동하려는 Azure VM의 IP 주소를 입력해야 합니다.

10. **속성** > **속성 구성** 에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
11. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. 
12. **복제 사용**을 클릭합니다. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.


추가하는 서버를 모니터링하려면 **구성 서버** > **마지막 연락**에서 마지막으로 검색한 시간을 확인할 수 있습니다. 예약된 검색 시간 동안 기다리지 않고 컴퓨터를 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 클릭합니다.

## <a name="test-the-configuration"></a>구성 테스트


1. 자격 증명 모음으로 이동하여 **설정** > **복제된 항목**에서 대상 Azure 지역으로 이동할 가상 머신을 클릭하고, **+테스트 장애 조치(failover)** 아이콘을 클릭합니다.
2. **테스트 장애 조치**에서 장애 조치에 사용할 복구 지점을 선택합니다.

   - **가장 최근에 처리됨**: VM을 Site Recovery 서비스에서 처리된 최신 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 시간 목표)가 제공됩니다.
   - **최신 앱 일치**: 모든 VM을 최신 앱 일치 복구 시점으로 장애 조치합니다. 타임스탬프가 표시됩니다.
   - **Custom**: 복구 시점을 선택합니다.

3. 구성을 테스트하기 위해 Azure VM을 이동할 대상 Azure 가상 네트워크를 선택합니다. 

   > [!IMPORTANT]
   > VM(복제를 활성화할 때 설정한)을 최종적으로 이동할 대상 프로덕션 네트워크가 아닌 별도의 Azure VM 네트워크를 장애 조치(failover)에 사용하는 것이 좋습니다.

4. 이동 테스트를 시작하려면 **확인**을 클릭합니다. 진행률을 추적하려면 VM을 클릭하여 해당 속성을 엽니다. 또는 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**에서 **테스트 장애 조치** 작업을 클릭하면 됩니다.
5. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM이 실행 중이고, 규모가 적절히 조정되었으며, 적절한 네트워크에 연결되어 있는지 확인하세요.
6. 이동 테스트 과정에서 만들어진 VM을 삭제하려면 복제된 항목에서 **테스트 장애 조치(failover) 정리**를 클릭합니다. 테스트와 연관된 모든 관측 내용을 **참고**에 기록하고 저장합니다.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>대상 Azure 지역으로 이동하고 확인합니다.

1. 자격 증명 모음으로 이동하여 **설정** > **복제된 항목**에서 가상 머신을 클릭한 다음, **장애 조치(failover)** 를 클릭합니다.
2. **장애 조치(failover)** 에서 **최신**을 선택합니다. 
3. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM을 종료하려고 합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다. 
4. 작업이 완료되면 VM이 예상대로 대상 Azure 지역에 표시되는지 확인합니다.
5. **복제된 항목**에서 VM을 마우스 오른쪽 단추로 클릭하고 **커밋**을 클릭합니다. 대상 Azure 지역으로 이동하는 프로세스가 끝났습니다. 커밋 작업이 완료될 때까지 기다립니다.

## <a name="discard-the-resource-in-the-source-region"></a>원본 Azure 지역의 리소스 삭제 

- VM으로 이동합니다.  **복제 사용 안 함**을 클릭합니다.  그러면 VM에 대한 데이터 복사 프로세스가 중지됩니다.  

   > [!IMPORTANT]
   > ASR 복제 요금이 청구되지 않도록 이 단계를 수행해야 합니다.

원본 리소스를 다시 사용할 계획이 없으면 다음 단계를 진행합니다.

1. [원본 VM 준비](#prepare-the-source-vms)의 4단계에서 나열한 원본 Azure 지역의 모든 관련 네트워크 리소스를 삭제합니다. 
2. 원본 Azure 지역에서 해당 스토리지 계정을 삭제합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM을 다른 Azure 지역으로 이동했습니다. 이제 이동한 VM에 대해 재해 복구를 구성할 수 있습니다.

> [!div class="nextstepaction"]
> [마이그레이션 후 재해 복구 설정](azure-to-azure-quickstart.md)
