---
title: Azure Site Recovery를 사용하여 Azure에 물리적 온-프레미스 서버에 대한 재해 복구 설정 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용하여 Azure에 온-프레미스 Windows 및 Linux 서버에 대한 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: a6be2fbbf946f13213f95b8876cfdddfa2ffbe67
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098170"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Azure에 온-프레미스 물리적 서버에 대한 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 Azure에 대한 온-프레미스 물리적 Windows 및 Linux 서버의 재해 복구를 설정하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure 및 온-프레미스 필수 조건 설정
> * Site Recovery에 대한 Recovery Services 자격 증명 모음 만들기 
> * 원본 및 대상 복제 환경 설정
> * 복제 정책 만들기
> * 서버에 대해 복제 사용

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 이 시나리오에 대한 [아키텍처 및 구성 요소](physical-azure-architecture.md)를 이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](vmware-physical-secondary-support-matrix.md)을 검토합니다.
- 복제할 서버가 [Azure VM 요구 사항](vmware-physical-secondary-support-matrix.md#replicated-vm-support)을 준수해야 합니다.
- Azure를 준비합니다. Azure 구독, Azure Virtual Network 및 저장소 계정이 필요합니다.
- 복제하려는 각 서버에서 모바일 서비스를 자동으로 설치하기 위해 계정을 준비합니다.

시작하기 전에 다음 사항을 확인합니다.

- Azure로 장애 조치한 후에는 물리적 서버를 온-프레미스 물리적 컴퓨터에 장애 복구할 수 없습니다. VMware VM에만 장애 복구할 수 있습니다. 
- 이 자습서는 가장 간단한 설정을 사용하여 Azure에 대한 물리적 서버 재해 복구를 설정합니다. 다른 옵션에 대해 알아보려면 방법 가이드를 읽어보시기 바랍니다.
    - Site Recovery 구성 서버를 포함하여 [복제 원본](physical-azure-set-up-source.md)을 설정합니다.
    - [복제 대상](physical-azure-set-up-target.md)을 설정합니다.
    - [복제 정책](vmware-azure-set-up-replication.md) 및 [복제 사용](vmware-azure-enable-replication.md)을 구성합니다.


### <a name="set-up-an-azure-account"></a>Azure 계정 설정

Microsoft [Azure 계정](https://azure.microsoft.com/)을 얻습니다.

- [평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
- [Site Recovery 가격 책정](site-recovery-faq.md#pricing)에 대해 알아보고 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/site-recovery/)를 가져옵니다.
- Site Recovery에 대해 [지원되는 지역](https://azure.microsoft.com/pricing/details/site-recovery/)을 알아보세요.

### <a name="verify-azure-account-permissions"></a>Azure 계정 권한 확인

Azure 계정에 Azure로 VM을 복제하기 위한 권한이 있는지 확인합니다.

- Azure에 컴퓨터를 복제하는 데 필요한 [권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)을 검토합니다.
- [역할 기반 액세스](../role-based-access-control/role-assignments-portal.md) 권한을 확인하고 수정합니다. 



### <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

[Azure 네트워크](../virtual-network/quick-create-portal.md)를 설정합니다.

- Azure VM은 장애 조치 후 처음 만들 때 이 네트워크에 배치됩니다.
- 네트워크가 Recovery Services 자격 증명 모음과 같은 지역에 있어야 합니다.


## <a name="set-up-an-azure-storage-account"></a>Azure 저장소 계정을 설정

[Azure Storage 계정](../storage/common/storage-quickstart-create-account.md)을 설정합니다.

- Site Recovery는 온-프레미스 컴퓨터를 Azure Storage에 복제합니다. 장애 조치(failover)가 발생한 후에 저장소에서 Azure VM을 만듭니다.
- 저장소 계정은 Recovery Services 자격 증명 모음과 동일한 영역에 있어야 합니다.


### <a name="prepare-an-account-for-mobility-service-installation"></a>모바일 서비스 설치를 위한 계정 준비

복제하려는 각 서버에 모바일 서비스가 설치되어야 합니다. Site Recovery는 사용자가 서버에 대해 복제를 사용하도록 설정하면 이 서비스를 자동으로 설치합니다. 자동으로 설치하려는 경우 Site Recovery가 서버에 액세스하는 데 사용할 계정을 준비해야 합니다.

- 도메인 또는 로컬 계정을 사용할 수 있습니다.
- Windows VM에서는 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정합니다. 그러려면 레지스터의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 값이 1인 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가합니다.
- CLI에서 설정을 사용하지 않도록 설정하기 위한 레지스트리 항목을 추가하려면 다음을 입력합니다. ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux에서 계정은 원본 Linux 서버의 루트여야 합니다.


## <a name="create-a-vault"></a>자격 증명 모음 만들기

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>보호 목표 선택

복제할 항목 및 복제 대상을 선택합니다.

1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 **Azure에** > **가상화되지 않음/기타**를 선택합니다.

## <a name="set-up-the-source-environment"></a>원본 환경 설정

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
구성 서버 컴퓨터에서 시스템 시계가 [시간 서버](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)와 동기화되었는지 확인합니다. 서로 일치해야 합니다. 15분 빠르거나 늦은 경우 설치가 실패할 수 있습니다.

#### <a name="verify-connectivity"></a>연결 확인
컴퓨터에서 사용자 환경을 기반으로 다음 URL에 액세스할 수 있는지 확인합니다. 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP 주소 기반 방화벽 규칙은 HTTPS(443) 포트를 통해 위에 나열된 모든 Azure URL에 대한 통신을 허용해야 합니다. IP 범위를 간소화하고 제한하려면 URL 필터링을 수행하는 것이 좋습니다.

- **상업용 IP** - [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다. AAD, 백업, 복제 및 저장소 URL을 지원하기 위해 구독의 Azure 지역에 대한 IP 주소 범위를 허용합니다.  
- **정부 IP** - AAD, 백업, 복제 및 저장소 URL을 지원하기 위해 모든 USGov 지역(버지니아, 텍사스, 애리조나 및 아이오와)의 [Azure Government 데이터 센터 IP 범위](https://www.microsoft.com/en-us/download/details.aspx?id=57063) 및 HTTPS(443) 포트를 허용합니다.  

#### <a name="run-setup"></a>설치 프로그램 실행
로컬 관리자 권한으로 통합 설치를 실행하여 구성 서버를 설치합니다. 프로세스 서버 및 마스터 대상 서버도 기본적으로 구성 서버에 설치됩니다.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

등록이 완료되면 자격 증명 모음의 **설정** > **서버** 페이지에 구성 서버가 표시됩니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델을 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>복제 정책 만들기

1. 새 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** > **+복제 정책**을 클릭합니다.
2. **복제 정책 만들기**에서 정책 이름을 지정합니다.
3. **RPO 임계값**에서 RPO(복구 지점 목표) 제한을 지정합니다. 이 값은 데이터 복구 지점 생성 횟수를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다. Premium Storage에 복제된 컴퓨터의 경우 최대 24시간 보존이 지원되고 표준 스토리지에 복제된 경우 72시간 보존이 지원됩니다.
5. **애플리케이션 일치 스냅숏 빈도**에서 애플리케이션 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(분)를 지정합니다. **확인** 을 클릭하여 정책을 만듭니다.

    ![복제 정책](./media/physical-azure-disaster-recovery/replication-policy.png)


정책은 구성 서버와 자동으로 연결됩니다. 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy**인 경우 장애 복구(failback) 정책 **rep-policy-failback**이 만들어집니다. 이 정책은 Azure에서 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.

## <a name="enable-replication"></a>복제 사용

각 서버에 대해 복제를 사용하도록 설정합니다.

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
10. **속성** > **속성 구성** 에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
11. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. 
12. **복제 사용**을 클릭합니다. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.


추가하는 서버를 모니터링하려면 **구성 서버** > **마지막 연락**에서 마지막으로 검색한 시간을 확인할 수 있습니다. 예약된 검색 시간 동안 기다리지 않고 컴퓨터를 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[재해 복구 훈련 실행](tutorial-dr-drill-azure.md)
