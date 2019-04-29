---
title: Azure Site Recovery를 사용하여 Azure로 Azure Stack VM 복제 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용하여 Azure에 Azure Stack VM에 대한 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: a84cbba968baf50563a2c2b0e2843d64f17bb34a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124716"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Azure에 Azure Stack VM 복제

이 문서에서는 [Azure Site Recovery 서비스](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 Azure에 재해 복구 Azure Stack VM을 설정하는 방법을 알아봅니다.

Site Recovery는 BCDR(비즈니스 연속성 및 재해 복구 개선) 전략에 기여하는 서비스입니다. 이 서비스는 예상된 및 예기치 않은 중단이 발생할 때 VM 워크로드를 계속 사용할 수 있도록 합니다.

- Site Recovery는 Azure Storage로의 VM 복제를 오케스트레이션하고 관리합니다.
- 기본 사이트에서 중단이 발생하는 경우 Site Recovery를 사용하여 Azure로 장애 조치(Failover)합니다.
- 장애 조치(Failover) 시 Azure VM이 저장된 VM 데이터에서 만들어지고, 사용자는 해당 Azure VM에서 실행되는 워크로드에 계속 액세스할 수 있습니다.
- 모든 기능이 다시 작동되고 실행되면 Azure VM을 기본 사이트로 다시 장애 복구(Failback)하고 Azure Storage로 다시 복제할 수 있습니다.


이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * **1단계: Azure Stack VM 복제 준비**. VM이 Site Recovery 요구 사항을 준수하는지 확인하고 Site Recovery 모바일 서비스의 설치를 준비합니다. 이 서비스는 복제하려는 각 VM에 설치됩니다.
> * **2단계: Recovery Services 자격 증명 모음을 설정합니다**. Site Recovery에 대한 자격 증명 모음을 설정하고 복제할 항목을 지정합니다. Site Recovery 구성 요소 및 작업은 자격 증명 모음에서 구성 및 관리됩니다.
> * **3단계: 소스 복제 환경을 설정합니다**. Site Recovery 구성 서버를 설정합니다. 구성 서버는 Site Recovery에 필요한 모든 구성 요소를 실행하는 단일 Azure Stack VM입니다. 구성 서버를 설정한 후 자격 증명 모음에 등록합니다.
> * **4단계: 대상 복제 환경을 설정합니다**. 사용하려는 Azure 계정, Azure Storage 계정 및 네트워크를 선택합니다. 복제 중에 VM 데이터가 Azure Storage에 복사됩니다. 장애 조치(Failover) 후에 Azure VM은 지정된 네트워크에 가입됩니다.
> * **5단계: 복제를 사용하도록 설정합니다**. 복제 설정을 구성하고 VM에 대한 복제를 사용하도록 설정합니다. 복제를 사용하도록 설정하면 모바일 서비스가 VM에 설치됩니다. Site Recovery가 VM의 초기 복제를 수행하면 진행 중인 복제가 시작됩니다.
> * **6단계: 재해 복구 훈련 실행**: 복제가 가동 및 실행되면 훈련을 실행하여 장애 조치(Failover)가 예상대로 작동하는지 확인합니다. 훈련을 시작하려면 Site Recovery에서 테스트 장애 조치(Failover)를 실행합니다. 테스트 장애 조치(Failover)는 프로덕션 환경에 영향을 주지 않습니다.

이러한 단계가 완료되면 필요할 때 Azure로의 전체 장애 조치(Failover)를 실행할 수 있습니다.

## <a name="architecture"></a>아키텍처

![아키텍처](./media/azure-stack-site-recovery/architecture.png)

**위치**: | **구성 요소** |**세부 정보**
--- | --- | ---
**구성 서버** | 단일 Azure Stack VM에서 실행됩니다. | 각 구독에서 구성 서버 VM을 설정합니다. 이 VM은 다음 Site Recovery 구성 요소를 실행합니다.<br/><br/> - 구성 서버: 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다. - 프로세스 서버: 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고, 캐싱, 압축 및 암호화를 사용하여 최적화하며, Azure Storage로 보냅니다.<br/><br/> 복제하려는 VM이 아래에 설명된 제한을 초과하는 경우 별도의 독립 실행형 프로세스 서버를 설정할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**모바일 서비스** | 복제하려는 각 VM에 설치됩니다. | 이 문서의 단계에서는 복제가 사용되도록 설정될 경우 VM에 모바일 서비스가 자동으로 설치되도록 계정을 준비합니다. 이 서비스를 자동으로 설치하지 않으려는 경우 다양한 다른 방법을 사용할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | Azure에는 Recovery Services 저장소, 저장소 계정 및 가상 네트워크가 필요합니다. |  복제된 데이터는 저장소 계정에 저장됩니다. 장애 조치(Failover)가 발생하면 Azure VM이 Azure 네트워크에 추가됩니다. 


복제는 다음과 같이 작동합니다.

1. 자격 증명 모음에서 복제 원본 및 대상을 지정하고 구성 서버를 설정하며 복제 정책을 만들고 모바일 서비스를 배포하며 복제를 사용하도록 설정합니다.
2. 모바일 서비스가 컴퓨터에 설치되고(푸시 설치를 사용한 경우) 컴퓨터는 복제 정책에 따라 복제를 시작합니다.
3. 서버 데이터의 초기 복사본이 Azure Storage에 복제됩니다.
4. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. .hrl 파일에는 컴퓨터에 대한 추적된 변경 내용이 유지됩니다.
5. 구성 서버는 Azure를 사용하는 복제 관리를 오케스트레이션합니다(포트 HTTPS 443 아웃바운드).
6. 프로세스 서버는 원본 컴퓨터에서 데이터를 수신하고, 이를 최적화 및 암호화하며, Azure Storage로 보냅니다(포트 443 아웃바운드).
7. 컴퓨터를 복제하는 작업은 복제 관리를 위해 구성 서버와 통신합니다(포트 HTTPS 443 인바운드). 컴퓨터는 복제 데이터를 프로세스 서버로 전달합니다(포트 HTTPS 9443 인바운드 - 수정 가능).
8. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. Azure ExpressRoute 공용 피어링을 사용할 수도 있습니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN을 통한 트래픽 복제는 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

이 시나리오 설정을 위해 필요한 사항은 다음과 같습니다.

**요구 사항** | **세부 정보**
--- | ---
**Azure 구독 계정** | Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.
**Azure 계정 권한** | 사용하는 Azure 계정에는 다음 권한이 필요합니다.<br/><br/> - 복구 서비스 자격 증명 모음 만들기<br/><br/> - 시나리오에 사용하는 리소스 그룹 및 가상 네트워크에 가상 머신 만들기<br/><br/> - 지정한 저장소 계정에 쓰기<br/><br/> 다음 사항에 유의하세요.<br/><br/> - 계정을 만들면 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> - 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다.<br/><br/> - 보다 세부적인 권한이 필요한 경우 [이 문서](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)를 검토합니다. 
**Azure Stack VM** | 테넌트 구독에서 Site Recovery 구성 서버로 배포될 Azure Stack VM이 필요합니다. 


### <a name="prerequisites-for-the-configuration-server"></a>구성 서버 필수 조건

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>1단계: Azure Stack VM 준비

### <a name="verify-the-operating-system"></a>운영 체제 확인

VM에서 표에 요약된 운영 체제 중 하나가 실행되고 있는지 확인합니다.


**운영 체제** | **세부 정보**
--- | ---
**64비트 Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2(SP1)
**CentOS** | 5.2 ~ 5.11, 6.1 ~ 6.9, 7.0 ~ 7.3
**Ubuntu** | 14.04 LTS 서버, 16.04 LTS 서버. [지원되는 커널](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions) 검토

### <a name="prepare-for-mobility-service-installation"></a>모바일 서비스 설치 준비

복제하려는 모든 VM에 모바일 서비스가 설치되어 있어야 합니다. 복제가 사용되도록 설정된 경우 프로세스 서버가 VM에서 서비스를 자동으로 설치하도록 하기 위해 VM 설정을 확인합니다.

#### <a name="windows-machines"></a>Windows 컴퓨터

- 네트워크 복제를 사용하도록 설정하려는 VM과 프로세스 서버를 실행하는 컴퓨터(기본적으로 구성 서버 VM) 간에 네트워크 연결이 필요합니다.
- 복제를 사용하도록 설정하는 컴퓨터에서 관리자 권한(도메인 또는 로컬)의 계정이 필요합니다.
    - Site Recovery를 설정할 때 이 계정을 지정합니다. 그러면 복제가 사용되도록 설정된 경우 프로세스 서버가 이 계정을 사용하여 모바일 서비스를 설치합니다.
    - 이 계정은 Site Recovery에서 강제 설치를 수행할 때와 모바일 서비스를 업데이트하는 데만 사용됩니다.
    - 도메인 계정을 사용하지 않는 경우 VM에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다.
        - 레지스트리에서 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 아래에 DWORD 값 **LocalAccountTokenFilterPolicy**를 만듭니다.
        - 이 값을 1로 설정합니다.
        - 명령 프롬프트에서 이 작업을 수행하려면 다음 명령을 입력합니다. **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- 복제하려는 VM의 Windows 방화벽에서 파일 및 프린터 공유, WMI를 허용합니다.
    - 이 작업을 수행하려면 **wf.msc**를 실행하여 Windows 방화벽 콘솔을 엽니다. 마우스 오른쪽 단추로 **인바운드 규칙** > **새 규칙**을 클릭합니다. **미리 정의**를 선택하고 목록에서 **파일 및 프린터 공유**를 선택합니다. 마법사를 완료하고 연결을 허용하도록 선택한 후 **마침**을 선택합니다.
    - 도메인 컴퓨터에 대해 GPO를 사용하여 이 작업을 수행할 수 있습니다.

    
#### <a name="linux-machines"></a>Linux 컴퓨터

- Linux 컴퓨터와 프로세스 서버 간에 네트워크가 연결되어 있는지 확인합니다.
- 복제를 사용하도록 설정한 컴퓨터에서 원본 Linux 서버의 루트 사용자에 해당하는 계정이 필요합니다.
    - Site Recovery를 설정할 때 이 계정을 지정합니다. 그러면 복제가 사용되도록 설정된 경우 프로세스 서버가 이 계정을 사용하여 모바일 서비스를 설치합니다.
    - 이 계정은 Site Recovery에서 강제 설치를 수행할 때와 모바일 서비스를 업데이트하는 데만 사용됩니다.
- 원본 Linux 서버의 /etc/hosts 파일에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 있는지 확인합니다.
- 복제하려는 컴퓨터에 최신 openssh, openssh-server 및 openssl 패키지를 설치합니다.
- SSH(보안 셸)가 22 포트에서 사용되고 실행 중인지 확인합니다.
- sshd_config 구성 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정합니다.
    1. 이 작업을 수행하려면 루트 권한으로 로그인합니다.
    2. /etc/ssh/sshd_config 파일에서 **PasswordAuthentication**으로 시작하는 줄을 찾습니다. 줄의 주석 처리를 제거하고 값을 **예**로 변경합니다.
    3. **Subsystem** 으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.

        ![Linux 모바일 서비스](./media/azure-stack-site-recovery/linux-mobility.png)

    4. sshd 서비스를 다시 시작합니다.


### <a name="note-the-vm-private-ip-address"></a>VM 개인 IP 주소를 적어둡니다.

복제하려는 각 컴퓨터에 대해 다음과 같이 IP 주소를 찾습니다.

1. Azure Stack Portal에서 VM을 클릭합니다.
2. **리소스** 메뉴에서 **네트워크 인터페이스**를 클릭합니다.
3. 개인 IP 주소를 적어둡니다.

    ![개인 IP 주소](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>2단계: 자격 증명 모음을 만들기 및 복제 목표 선택

1. Azure Portal에서 **리소스 만들기** > **관리 도구** > **Backup 및 Site Recovery**를 선택합니다.
2. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 
3. **리소스 그룹**에서 리소스 그룹을 만들거나 선택합니다. **contosoRG**를 사용합니다.
4. **위치**에서 Azure 지역을 입력합니다. **유럽 서부**를 사용합니다.
5. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정** > **만들기**를 선택합니다.

   ![새 자격 증명 모음 만들기](./media/azure-stack-site-recovery/new-vault-settings.png)

   새 자격 증명 모음이 **대시보드** > **모든 리소스** 및 주 **Recovery Services 자격 증명 모음** 페이지에 표시됩니다.

### <a name="select-a-replication-goal"></a>복제 목표 선택

1. **Recovery Services 자격 증명 모음**에서 자격 증명 모음 이름을 지정합니다. **ContosoVMVault**를 사용합니다.
2. **시작**에서 Site Recovery를 선택합니다. 그런 다음, **인프라 준비**를 선택합니다.
3. **보호 목표** > **컴퓨터가 있는 위치**에서 **온-프레미스**를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
5. **컴퓨터가 가상화되어 있습니까?** 에서 **가상화되지 않음/기타**를 선택합니다. 그런 다음 **확인**을 선택합니다.

    ![보호 목표](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>3단계: 원본 환경 설정

구성 서버 컴퓨터를 설정하고 자격 증명 모음에 등록한 후 복제하려는 컴퓨터를 검색합니다.

1. **인프라 준비** > **원본**을 클릭합니다.
2. **원본 준비**에서 **+구성 서버**를 클릭합니다.

    ![원본 설정](./media/azure-stack-site-recovery/plus-config-srv.png)

3. **서버 추가**에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
5. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
6. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치 프로그램을 실행하는 경우 등록 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![원본 설정](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery 통합 설치 프로그램 실행

구성 서버를 설치 및 등록하려면 구성 서버에 사용하려는 VM에 대해 RDP 연결을 수행하고 통합 설치 프로그램을 실행합니다.

시작하기 전에 클록이 VM의 [시간 서버와 동기화](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)되었는지 확인합니다. 이 시간이 로컬 시간보다 5분 넘게 차이를 보이면 설치는 실패합니다.

이제 다음과 같이 구성 서버를 설치합니다.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 명령줄을 통해 구성 서버를 설치할 수도 있습니다. [자세히 알아보기](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> 포털에 계정 이름이 표시되는 데 15분 이상 걸릴 수 있습니다. 즉시 업데이트하려면 **구성 서버** > ***서버 이름*** > **서버 새로 고침**을 선택합니다.

## <a name="step-4-set-up-the-target-environment"></a>4단계: 대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**에서 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델을 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다. 호환되는 항목을 찾지 못하면 마법사를 완료하기 위해 하나 이상의 저장소 계정 및 가상 네트워크를 만들어야 합니다.


## <a name="step-5-enable-replication"></a>5단계: 복제 사용

### <a name="create-a-replication-policy"></a>복제 정책 만들기

1. **인프라 준비** > **복제 설정**을 클릭합니다.
2. **복제 정책 만들기**에서 정책 이름을 지정합니다.
3. **RPO 임계값**에서 RPO(복구 지점 목표) 제한을 지정합니다.
    - 복제된 데이터에 대한 복구 지점이 시간 설정에 따라 생성됩니다.
    - 이 설정은 연속되는 복제에 영향을 미치지 않습니다. 복구 지점이 생성되지 않은 상태로 임계값 제한에 도달할 경우 경고를 발생하기만 합니다.
4. **복구 지점 보존**에서 각 복구 지점이 보존되는 기간을 지정합니다. 복제된 VM은 지정된 기간 중 임의의 시점으로 복구될 수 있습니다.
5. **앱 일치 스냅숏 빈도**에서 애플리케이션 일치 스냅숏이 만들어지는 빈도를 지정합니다.

    - 앱 일치 스냅숏은 VM 내에서 앱 데이터의 시간에서 스냅숏입니다.
    - VSS(볼륨 섀도 복사본 서비스)는 스냅숏을 만들 때 VM의 앱이 일관된 상태가 되도록 합니다.
6. **확인**을 선택하여 정책을 만듭니다.


### <a name="confirm-deployment-planning"></a>배포 계획 확인

지금은 이 단계를 건너뛸 수 있습니다. **배포 계획** 드롭다운 목록에서 **예, 완료함**을 클릭합니다.



### <a name="enable-replication"></a>복제 사용

[1단계: 머신 준비](#step-1-prepare-azure-stack-vms)의 모든 태스크를 완료했는지 확인합니다. 그런 후 다음과 같이 복제를 사용하도록 설정합니다.

1. **애플리케이션 복제** > **원본**을 선택합니다.
2. **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **물리적 컴퓨터**를 선택합니다.
4. 프로세스 서버(구성 서버)를 선택합니다. 그런 후 **OK**를 클릭합니다.
5. **대상**에서 장애 조치(failover) 후 VM을 만들 구독 및 리소스 그룹을 선택합니다. 장애 조치(Failover)된 VM에 사용할 배포 모델을 선택합니다.
6. 복제된 데이터를 저장할 Azure Storage 계정을 선택합니다.
7. 장애 조치(failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다.
8. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성** 을 선택합니다. 각 컴퓨터에 대해 별도로 Azure 네트워크를 선택하려는 경우 **나중에 구성**을 선택합니다.
9. **물리적 컴퓨터**에서 **+물리적 컴퓨터**를 클릭합니다. 복제하려는 각 머신의 이름, IP 주소 및 OS 종류를 지정합니다.

    - 컴퓨터의 내부 IP 주소를 사용합니다.
    - 공용 IP 주소를 지정하는 경우 복제가 예상대로 작동하지 않을 수 있습니다.

10. **속성** > **속성 구성**에서 프로세스 서버가 컴퓨터에 모바일 서비스를 자동으로 설치하는 데 사용할 계정을 선택합니다.
11. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다.
12. **복제 사용**을 클릭합니다.
13. **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적합니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

> [!NOTE]
> VM에 대한 복제를 사용하도록 설정되면 Site Recovery에서 모바일 서비스를 설치합니다.
> 
> 변경 내용이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.
> 
> 추가하는 VM을 모니터링하려면, **구성 서버** > **마지막 연락**에서 VM을 마지막으로 검색한 시간을 확인합니다. 예약된 검색을 기다리지 않고 VM을 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 선택합니다.


## <a name="step-6-run-a-disaster-recovery-drill"></a>6단계: 재해 복구 훈련 실행

모든 것이 예상대로 작동하는지 확인할 수 있도록 Azure에 대해 테스트 장애 조치(Failover)를 실행합니다. 이 장애 조치(Failover)는 프로덕션 환경에 영향을 주지 않습니다.

### <a name="verify-machine-properties"></a>컴퓨터 속성 확인

테스트 장애 조치(failover)를 실행하기 전에 먼저 컴퓨터 속성을 확인하고 [Azure 요구 사항](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)을 준수하는지 확인합니다. 다음과 같이 속성을 확인하고 수정할 수 있습니다.

1. **보호된 항목**에서 **복제된 항목** > VM을 클릭합니다.
2. **복제된 항목** 창에 VM 정보, 상태 및 최신 사용 가능한 복구 지점의 요약이 제공됩니다. 자세한 내용을 보려면 **속성**을 클릭합니다.
3. **계산 및 네트워크**에서 필요에 따라 설정을 수정합니다.

    - Azure VM 이름, 리소스 그룹, 대상 크기, [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md) 및 관리 디스크 설정을 수정할 수 있습니다.
    - 네트워크 설정을 보고 수정할 수도 있습니다. 여기에는 장애 조치(Failover) 후에 Azure VM이 가입되는 네트워크/서브넷과 VM에 할당될 IP 주소가 포함됩니다.
1. **디스크**에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 확인합니다.
   

### <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

테스트 장애 조치(failover)를 실행하면 다음 상황이 발생합니다.

1. 필수 구성 요소 확인은 장애 조치(failover)에 필요한 모든 조건이 충족되었는지 확인하기 위해 실행합니다.
2. 장애 조치(Failover)는 지정된 복구 지점을 사용하여 데이터를 처리합니다.
    - **가장 최근에 처리됨**: 머신은 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치(Failover)됩니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 시간 목표)가 제공됩니다.
    - **최신 앱 일치**: 최신 앱 일치 복구 지점으로 머신이 장애 조치(Failover)됩니다.
    - **사용자 지정**: 장애 조치(Failover)에 사용되는 복구 지점을 선택합니다.

3. Azure VM은 처리된 데이터를 사용하여 생성됩니다.
4. 테스트 장애 조치(Failover)는 훈련 동안 만든 Azure VM을 자동으로 정리할 수 있습니다.

VM에 대해 테스트 장애 조치(Failover)를 다음과 같이 실행합니다.

1. **설정** > **복제된 항목**에서 VM > **+테스트 장애 조치(Failover)** 를 클릭합니다.
2. 이 연습에서는 **가장 최근에 처리됨** 복구 지점을 사용하도록 선택합니다. 
3. **테스트 장애 조치(Failover)** 에서 대상 Azure 네트워크를 선택합니다.
4. **확인**을 클릭하여 장애 조치(failover)를 시작합니다.
5. VM을 클릭하여 해당 속성을 열어 진행률을 추적합니다. 또는 *자격 증명 모음 이름* > **설정** > **작업** >**Site Recovery 작업**에서 **테스트 장애 조치(Failover)** 작업을 클릭합니다.
6. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM이 크기가 적당하고 올바른 네트워크에 연결되어 있으며 실행 중인지 확인합니다.
7. 이제 Azure에서 복제된 VM에에 연결할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. 테스트 장애 조치(failover) 중에 만든 Azure VM을 삭제하려면 VM에서 **테스트 장애 조치(failover) 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 연관된 모든 관측 내용을 기록하고 저장합니다.

## <a name="fail-over-and-fail-back"></a>장애 조치(Failover) 및 장애 복구

복제를 설정하고 훈련을 실행하여 모든 항목이 제대로 작동되는지 확인한 후에는 필요에 따라 컴퓨터를 Azure로 장애 조치(Failover)할 수 있습니다.

장애 조치(Failover) 후에 Azure의 컴퓨터에 연결하려는 경우, 장애 조치(Failover)를 실행하기 전에 [연결을 준비](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover)합니다.

그런 후 다음과 같이 장애 조치(Failover)를 실행합니다.


1. **설정** > **복제된 항목**에서 컴퓨터 > **장애 조치(Failover)** 를 클릭합니다.
2. 사용할 복구 지점을 선택합니다.
3. **테스트 장애 조치(Failover)** 에서 대상 Azure 네트워크를 선택합니다.
4. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. 이 설정을 사용하여 Site Recovery는 장애 조치(Failover)를 시작하기 전에 원본 컴퓨터를 종료하려고 합니다. 그렇지만 종료가 실패하는 경우에도 장애 조치(Failover)는 계속됩니다. 
5. **확인**을 클릭하여 장애 조치(failover)를 시작합니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
6. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. 장애 조치(Failover) 후에 연결할 준비가 된 경우 VM이 크기가 적당하고 올바른 네트워크에 연결되어 있으며 실행 중인지 확인합니다.
7. VM을 확인한 후에 **커밋**을 클릭하여 장애 조치를 완료합니다. 그러면 사용 가능한 복구 지점이 모두 삭제됩니다.

> [!WARNING]
> 진행 중인 장애 조치(Failover)는 취소하지 마세요. 장애 조치(failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 VM은 다시 복제되지 않습니다.


### <a name="fail-back-to-azure-stack"></a>Azure Stack으로 장애 복구(Failback)

기본 사이트가 다시 작동되고 실행되면 Azure에서 Azure Stack으로 장애 복구(failback)할 수 있습니다. 이렇게 하려면 Azure VM VHD를 다운로드하고 Azure Stack에 업로드해야 합니다.

1. VHD를 다운로드할 수 있도록 Azure VM을 종료합니다. 
2. VHD 다운로드를 시작하려면 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 설치합니다.
3. Azure Portal에서 VM으로 이동합니다(VM 이름 사용).
4. **디스크**에서 디스크 이름을 클릭하고 설정을 수집합니다.

    - 예를 들어 테스트에서 사용되는 VHD URI https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd를 분석하여 VHD 다운로드에 사용되는 다음 입력 매개 변수를 가져올 수 있습니다.
        - Storage 계정: 502055westcentralus
        - 컨테이너: wahv9b8d2ceb284fb59287
        - VHD 이름: copied-3676553984.vhd

5. 이제 Azure Storage 탐색기를 사용하여 VHD를 다운로드합니다.
6. [이러한 단계](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm)를 사용하여 VHD를 Azure Stack으로 업로드합니다.
7. 기존 VM 또는 새 VM에서 업로드한 VHD를 연결합니다.
8. OS 디스크가 올바른지 확인하고 VM을 시작합니다.


이 단계에서 장애 복구(Failback)가 완료됩니다.


## <a name="conclusion"></a>결론

이 문서에서는 Azure Stack VM을 Azure로 복제했습니다. 복제가 수행된 후에 재해 복구 훈련을 실행하여 Azure로의 장애 조치(Failover)가 예상대로 작동하는지 확인했습니다. 또한 이 문서에는 Azure로의 전체 장애 조치(Failover) 및 Azure Stack으로의 장애 복구(Failback)를 실행하기 위한 단계도 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

장애 복구(Failover) 후 VM을 다시 보호하고 Azure로의 복제를 다시 시작합니다. 이를 위해서는 이 문서의 단계를 반복합니다.

