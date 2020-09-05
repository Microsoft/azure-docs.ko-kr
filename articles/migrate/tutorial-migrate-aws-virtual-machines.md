---
title: AWS(Amazon Web Services) EC2 VM 검색, 평가 및 Azure로 마이그레이션
description: 이 문서에서는 Azure Migrate를 사용하여 AWS VM을 Azure로 마이그레이션하는 방법을 설명합니다.
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 0ef9adfe7ee88141b67bb9e8c9586c5cc6e5df6f
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762422"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>AWS(Amazon Web Services) VM 검색, 평가 및 Azure로 마이그레이션

이 자습서에서는 Azure Migrate: Server Assessment 및 Azure Migrate: Server Migration 도구를 사용하여 AWS(Amazon Web Services) VM(가상 머신)을 Azure VM으로 마이그레이션하는 방법을 보여줍니다.

> [!NOTE]
> AWS VM을 물리적 서버로 취급하여 Azure로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 배웁니다.
> [!div class="checklist"]
> * 마이그레이션을 위한 필수 조건을 확인합니다.
> * Azure Migrate: 서버 마이그레이션을 사용하여 Azure 리소스를 준비합니다. Azure Migrate에서 사용할 Azure 계정 및 리소스에 대한 권한을 설정합니다.
> * 마이그레이션을 위해 AWS EC2 인스턴스를 준비합니다.
> * Azure Migrate 허브에서 Azure Migrate: 서버 마이그레이션 도구를 추가합니다.
> * 복제 어플라이언스를 설정하고 구성 서버를 배포합니다.
> * Mobility Service를 마이그레이션하려는 AWS VM에 설치합니다.
> * VM에 대해 복제를 활성화합니다.
> * 복제 상태를 추적하고 모니터링합니다. 
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인합니다.
> * 전체 마이그레이션을 Azure로 실행합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="discover-and-assess"></a>검색 및 평가

Azure로 마이그레이션하기 전에 VM 검색 및 마이그레이션 평가를 수행하는 것이 좋습니다. 이 평가는 Azure로 마이그레이션하기 위해 AWS VM의 크기를 적절하게 조정하고 잠재적인 Azure 실행 비용을 예측하는 데 도움이 됩니다.

다음과 같이 평가를 설정합니다.

1. 이 [자습서](./tutorial-prepare-physical.md)에 따라 Azure를 설정하고 평가를 위해 AWS VM을 준비합니다. 다음 사항에 유의하세요.

    - Azure Migrate는 AWS 인스턴스를 검색할 때 암호 인증을 사용합니다. AWS 인스턴스는 기본적으로 암호 인증을 지원하지 않습니다. 인스턴스를 검색하려면 먼저 암호 인증을 사용하도록 설정해야 합니다.
        - Windows 컴퓨터의 경우 WinRM 포트 5986(HTTPS) 및 5985(HTTP)를 허용합니다. 이렇게 하면 원격 WMI 호출을 수행할 수 있습니다. 다음을 설정하는 경우 
        - Linux 컴퓨터의 경우:
            1. 각 Linux 머신에 로그인합니다.
            2. sshd_config file : vi /etc/ssh/sshd_config 열기
            3. 파일에서 **PasswordAuthentication** 줄을 찾아 값을 **예**로 변경합니다.
            4. 파일을 저장하고 닫습니다. ssh 서비스를 다시 시작합니다.

2. 그런 다음, 이 [자습서](./tutorial-assess-physical.md)에 따라 Azure Migrate 프로젝트 및 어플라이언스를 설정하여 AWS VM을 검색하고 평가합니다.

평가를 수행하는 것이 좋지만, 이는 VM을 마이그레이션하기 위한 필수 단계가 아닙니다.



## <a name="prerequisites"></a>사전 요구 사항 

- 마이그레이션하려는 AWS VM에서 지원되는 OS 버전을 실행하고 있는지 확인합니다. AWS VM은 마이그레이션을 위해 물리적 컴퓨터로 처리됩니다. 물리적 서버 마이그레이션 워크플로에 [지원되는 운영 체제](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)를 검토합니다. 실제 마이그레이션을 진행하기 전에 테스트 마이그레이션(테스트 장애 조치)을 수행하여 VM이 예상대로 작동하는지 확인하는 것이 좋습니다.
- AWS VM에서 Azure로 마이그레이션하는 데 [지원되는 구성](./migrate-support-matrix-physical-migration.md#physical-server-requirements)을 준수하는지 확인합니다.
- Azure에 복제하는 AWS VM에서 [Azure VM 요구 사항](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)을 준수하는지 확인합니다.
- VM을 Azure로 마이그레이션하기 전에 VM에서 몇 가지 사항을 변경해야 합니다.
    - 일부 운영 체제의 경우 이러한 변경은 Azure Migrate에서 자동으로 수행합니다.
    - 이러한 변경은 마이그레이션을 시작하기 전에 수행해야 합니다. 변경하기 전에 VM을 마이그레이션하면 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.
필요한 [Windows](prepare-for-migration.md#windows-machines) 및 [Linux](prepare-for-migration.md#linux-machines) 변경 사항을 검토해야 합니다.

### <a name="prepare-azure-resources-for-migration"></a>마이그레이션을 위한 Azure 리소스 준비

마이그레이션을 위해 Azure Migrate: 서버 마이그레이션 도구를 사용하여 Azure 리소스를 준비합니다.

**Task** | **세부 정보**
--- | ---
**Azure Migrate 프로젝트 만들기** | Azure 계정에는 프로젝트를 만들 수 있는 기여자 또는 소유자 권한이 있어야 합니다.
**Azure 계정에 대한 사용 권한 확인** | Azure 계정에는 VM을 만들고 Azure 관리 디스크에 쓸 수 있는 권한이 필요합니다.

### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있어야 합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.

### <a name="assign-azure-account-permissions"></a>Azure 계정 권한 할당

Azure 계정에 Virtual Machine 기여자 역할을 할당합니다. 다음 항목에 대한 사용 권한을 제공합니다.

- 선택한 리소스 그룹에 VM 만들기
- 선택한 가상 네트워크에 VM 만들기
- Azure 관리 디스크에 씁니다. 

### <a name="create-an-azure-network"></a>Azure 네트워크 만들기

Azure VNet(Virtual Network)을 [설정](../virtual-network/manage-virtual-network.md#create-a-virtual-network)합니다. Azure에 복제하는 경우 만들어지는 Azure VM이 마이그레이션을 설정할 때 지정한 Azure VNet에 조인됩니다.

## <a name="prepare-aws-instances-for-migration"></a>마이그레이션을 위한 AWS 인스턴스 준비

AWS에서 Azure로의 마이그레이션을 준비하려면 마이그레이션을 위해 복제 어플라이언스를 준비하고 배포해야 합니다.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>복제 어플라이언스용 머신 준비

Azure Migrate: 서버 마이그레이션에서 복제 어플라이언스를 사용하여 컴퓨터를 Azure에 복제합니다. 복제 어플라이언스에서 실행하는 구성 요소는 다음과 같습니다.

- **구성 서버**: 구성 서버는 AWS 환경과 Azure 간의 통신을 조정하고 데이터 복제를 관리합니다.
- **프로세스 서버**: 프로세스 서버는 복제 게이트웨이의 역할을 합니다. 복제 데이터를 받고, 캐싱, 압축 및 암호화를 사용하여 최적화하고, Azure의 캐시 스토리지 계정으로 보냅니다.

다음과 같이 어플라이언스 배포를 준비합니다.

- 복제 어플라이언스를 호스팅하는 별도의 EC2 VM을 설정합니다. 이 인스턴스는 Windows Server 2012 R2 또는 Windows Server 2016을 실행해야 합니다. 어플라이언스에 대한 하드웨어, 소프트웨어 및 네트워킹 요구 사항을 [검토](./migrate-replication-appliance.md#appliance-requirements)합니다.
- 복제하려는 원본 VM 또는 이전에 설치한 Azure Migrate 검색 및 평가 어플라이언스에 어플라이언스를 설치하면 안 됩니다. 이는 다른 VM에 배포해야 합니다.
- 마이그레이션할 원본 AWS VM에는 복제 어플라이언스에 대한 네트워크 가시선이 있어야 합니다. 이를 사용하도록 설정하는 데 필요한 보안 그룹 규칙을 구성합니다. 복제 어플라이언스는 마이그레이션할 원본 VM과 동일한 VPC에 배포하는 것이 좋습니다. 복제 어플라이언스가 다른 VPC에 있어야 하는 경우 VPC 피어링을 통해 VPC를 연결해야 합니다.
- 원본 AWS VM은 복제 관리 및 복제 데이터 전송을 위해 443 HTTPS(제어 채널 오케스트레이션) 및 9443 TCP(데이터 전송) 인바운드 포트에서 복제 어플라이언스와 통신합니다. 이에 따라 복제 어플라이언스는 복제 데이터를 오케스트레이션하고 443 HTTPS 아웃바운드 포트를 통해 Azure로 보냅니다. 이러한 규칙을 구성하려면 적절한 포트 및 원본 IP 정보를 사용하여 보안 그룹 인바운드/아웃바운드 규칙을 편집합니다.

   ![AWS 보안 그룹 ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![보안 설정 편집 ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- 복제 어플라이언스는 MySQL을 사용합니다. MySQL을 어플라이언스에 설치하는 [옵션](migrate-replication-appliance.md#mysql-installation)을 검토합니다.
- 복제 어플라이언스가 [퍼블릭](migrate-replication-appliance.md#url-access) 및 [정부](migrate-replication-appliance.md#azure-government-url-access) 클라우드에 액세스하는 데 필요한 Azure URL을 검토합니다.

## <a name="add-the-server-migration-tool"></a>Server Migration 도구 추가

Azure Migrate 프로젝트를 설정한 다음, Server Migration 도구를 해당 프로젝트에 추가합니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.
3. **개요**에서 **서버 평가 및 마이그레이션**을 클릭합니다.
4. **서버 검색, 평가 및 마이그레이션** 아래에서 **서버 평가 및 마이그레이션**을 클릭합니다.

    ![서버 검색 및 평가](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. **서버 검색, 평가 및 마이그레이션**에서 **도구 추가**를 클릭합니다.
6. **프로젝트 마이그레이션**에서 Azure 구독을 선택하고, 아직 없는 경우 리소스 그룹을 만듭니다.
7. **프로젝트 세부 정보**에서 프로젝트 이름과 프로젝트를 만들려는 지역을 지정하고, **다음**을 클릭합니다. [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.
    - 지리적 프로젝트 위치는 AWS 컴퓨터에서 수집되는 메타데이터를 저장하는 데만 사용됩니다.
    - 대상 지역은 마이그레이션을 실행할 때 선택할 수 있습니다.

    ![Azure Migrate 프로젝트 만들기](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. **평가 도구 선택**에서 **평가 도구 추가 건너뛰기** > **다음**을 차례로 선택합니다.
9. **마이그레이션 도구 선택**에서 **Azure Migrate: 서버 마이그레이션** > **다음**을 차례로 선택합니다.
10. **검토 + 도구 추가**에서 설정을 검토하고, **도구 추가**를 클릭합니다.
11. 도구가 추가되면 Azure Migrate 프로젝트 > **서버** > **마이그레이션 도구**에 표시됩니다.

## <a name="set-up-the-replication-appliance"></a>복제 어플라이언스 설정

마이그레이션의 첫 번째 단계는 복제 어플라이언스를 설정하는 것입니다. AWS VM 마이그레이션을 위한 어플라이언스를 설정하려면 어플라이언스에 대한 설치 관리자 파일을 다운로드한 다음, [준비한 VM](#prepare-a-machine-for-the-replication-appliance)에서 실행해야 합니다.

### <a name="download-the-replication-appliance-installer"></a>복제 어플라이언스 설치 관리자 다운로드

1. Azure Migrate 프로젝트 > **서버**, **Azure Migrate: 서버 마이그레이션**에서 **검색**을 클릭합니다.

    ![VM 검색](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **가상화되지 않음/기타**를 클릭합니다.
3. **대상 지역**에서 머신을 마이그레이션하려는 Azure 지역을 선택합니다.
4. **마이그레이션의 대상 지역이 <region-name>인지 확인합니다.** 를 선택합니다.
5. **리소스 만들기**를 클릭합니다. 그러면 Azure Site Recovery 자격 증명 모음이 백그라운드에서 만들어집니다.
    - 이미 Azure Migrate 서버 마이그레이션을 사용하여 마이그레이션을 설정한 경우 이전에 리소스가 설정되었으므로 대상 옵션을 구성할 수 없습니다.
    - 이 단추를 클릭한 후에는 이 프로젝트의 대상 지역을 변경할 수 없습니다.
    - VM을 다른 지역으로 마이그레이션하려면 새롭거나 다른 Azure Migrate 프로젝트를 만들어야 합니다.

6. **새 복제 어플라이언스를 설치하거나 기존 설치를 확장하시겠습니까?** 에서 **복제 어플라이언스 설치**를 선택합니다.
7. **복제 어플라이언스 소프트웨어를 다운로드하고 설치합니다.** 에서 어플라이언스 설치 관리자 및 등록 키를 다운로드합니다. 어플라이언스를 등록하려면 키가 필요합니다. 키는 다운로드한 후 5일 동안 유효합니다.

    ![공급자 다운로드](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. 어플라이언스 설정 파일 및 키 파일을 복제 어플라이언스용으로 만든 Windows Server 2016 또는 Windows Server 2012 AWS VM에 복사합니다.
9. 다음 절차에서 설명한 대로 복제 어플라이언스 설치 파일을 실행합니다.  
    9.1. **시작하기 전에**에서 **구성 서버 및 프로세스 서버 설치**를 선택한 후 **다음**을 선택합니다.   
    9.2 **타사 소프트웨어 라이선스**에서 **타사 사용권 계약에 동의함**을 선택하고, **다음**을 선택합니다.   
    9.3 **등록**에서 **찾아보기**를 선택한 다음, 자격 증명 모음 등록 키 파일을 저장한 위치로 이동합니다. **다음**을 선택합니다.  
    9.4 **인터넷 설정**에서 **프록시 서버 없이 Azure Site Recovery에 직접 연결**을 선택하고, **다음**을 선택합니다.  
    9.5 **필수 구성 요소 확인** 페이지에서 여러 항목에 대한 검사를 실행합니다. 완료되면 **다음**을 선택합니다.  
    9.6 **MySQL 구성**에서 MySQL DB에 대한 암호를 제공하고, **다음**을 선택합니다.  
    9.7 **환경 세부 정보**에서 **아니요**를 선택합니다. VM을 보호할 필요가 없습니다. 그다음에 **다음**을 선택합니다.  
    9.8 **설치 위치**에서 **다음**을 선택하여 기본값을 적용합니다.  
    9.9 **네트워크 선택**에서 **다음**을 선택하여 기본값을 적용합니다.  
    9.10 **요약**에서 **설치**를 선택합니다.   
    9.11 설치 프로세스에 대한 정보가 **설치 진행률**에 표시됩니다. 완료되면 **다음**을 선택합니다. 다시 부팅하는 방법에 대한 메시지가 창에 표시됩니다. **확인**을 선택합니다.   
    9.12 그러면 구성 서버 연결 암호에 대한 메시지가 창에 표시됩니다. 암호를 클립보드에 복사하여 원본 VM의 임시 텍스트 파일에 저장합니다. 이 암호는 나중에 Mobility Service 설치 프로세스 중에 필요합니다.
10. 설치가 완료되면 어플라이언스 구성 마법사가 자동으로 시작됩니다(어플라이언스의 바탕 화면에 만들어진 cspsconfigtool 바로 가기를 사용하여 마법사를 수동으로 시작할 수도 있음). 마법사의 계정 관리 탭을 사용하여 모바일 서비스의 푸시 설치에 사용할 계정 세부 정보를 추가합니다. 이 자습서에서는 Mobility Service를 복제할 원본 VM에 수동으로 설치하므로 이 단계에서 더미 계정을 만들어 계속 진행합니다. 더미 계정 만들 때 식별 이름으로 "guest", 사용자 이름으로 "username", 계정 암호로 "password"를 입력할 수 있습니다. 이 더미 계정은 복제 사용 단계에서 사용됩니다. 
11. 설치 후에 어플라이언스가 다시 시작되면 **머신 검색**의 **구성 서버 선택**에서 새 어플라이언스를 선택하고 **등록 완료**를 클릭합니다. 등록 완료에서는 복제 어플라이언스를 준비하기 위한 몇 가지 최종 작업이 수행됩니다.

    ![등록 완료](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>모바일 서비스 설치

Mobility Service 에이전트가 마이그레이션할 원본 AWS VM에 설치되어 있어야 합니다. 에이전트 설치 관리자는 복제 어플라이언스에서 사용할 수 있습니다. 올바른 설치 관리자를 찾고, 마이그레이션하려는 각 머신에 에이전트를 설치합니다. 다음과 같이 수행합니다.

1. 복제 어플라이언스에 로그인합니다.
2. **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**로 이동합니다.
3. 원본 AWS VM의 운영 체제 및 버전에 맞는 설치 관리자를 찾습니다. [지원되는 운영 체제](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)를 검토하세요.
4. 설치 관리자 파일을 마이그레이션하려는 원본 AWS VM에 복사합니다.
5. 복제 어플라이언스를 설치할 때 만들어지고 저장된 암호 텍스트 파일이 있는지 확인합니다.
    - 암호를 저장하지 않은 경우 이 단계를 사용하여 복제 어플라이언스에서 암호를 확인할 수 있습니다. 명령줄에서 **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v**를 실행하여 현재 암호를 확인합니다.
    - 이제 이 암호를 클립보드에 복사하여 원본 VM의 임시 텍스트 파일에 저장합니다.

### <a name="installation-guide-for-windows-aws-vms"></a>Windows AWS VM 설치 지침

1. 설치 관리자 파일의 콘텐츠를 다음과 같이 AWS VM의 로컬 폴더(예: C:\Temp)에 추출합니다.

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Mobility Service 설치 관리자를 실행합니다.
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. 에이전트를 복제 어플라이언스에 등록합니다.
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Linux AWS VM 설치 지침

1. tarball 설치 관리자의 콘텐츠를 다음과 같이 AWS VM의 로컬 폴더(예: /tmp/MobSvcInstaller)에 추출합니다.
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. 설치 관리자 스크립트를 실행합니다.
    ```
    sudo ./install -r MS -q
    ```  

3. 에이전트를 복제 어플라이언스에 등록합니다.
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-aws-vms"></a>AWS VM에 복제 사용

> [!NOTE]
> 포털을 통해 복제할 VM을 한 번에 최대 10개까지 추가할 수 있습니다. 더 많은 VM을 동시에 복제하려면 VM을 10개씩 일괄 처리로 추가할 수 있습니다.

1. Azure Migrate 프로젝트 > **서버**, **Azure Migrate: 서버 마이그레이션**에서 **복제**를 클릭합니다.

    ![VM 복제](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. **복제** > **원본 설정** > **머신이 가상화되어 있나요?** 에서 **가상화되지 않음/기타**를 선택합니다.
3. **온-프레미스 어플라이언스**에서 설정한 Azure Migrate 어플라이언스의 이름을 선택합니다.
4. **프로세스 서버**에서 복제 어플라이언스의 이름을 선택합니다. 
5. **게스트 자격 증명**에서, [복제 설치 관리자 설치](#download-the-replication-appliance-installer) 중에 만든 더미 계정을 선택하여 모바일 서비스를 수동으로 설치합니다(푸시 설치는 지원되지 않음). 그런 다음, **다음: 가상 머신**을 클릭합니다.   
 
    ![VM 복제](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. **Virtual Machines**의 **평가에서 마이그레이션 설정을 가져오시겠습니까?** 에서 기본 설정인 **아니요, 수동으로 마이그레이션 설정 지정**을 그대로 유지합니다.
7. 마이그레이션하려는 각 VM을 선택합니다. 그런 다음, **다음: 대상 설정**을 클릭합니다.

    ![VM 선택](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. **대상 설정**에서 마이그레이션할 구독 및 대상 지역을 선택하고, 마이그레이션 후 Azure VM이 상주할 리소스 그룹을 지정합니다.
9. **Virtual Network**에서 마이그레이션 후 Azure VM이 조인될 Azure VNet/서브넷을 선택합니다.
10. **Azure 하이브리드 혜택**에서
    - Azure 하이브리드 혜택을 적용하지 않으려면 **아니요**를 선택합니다. 그런 후 **Next** 를 클릭합니다.
    - 활성 Software Assurance 또는 Windows Server 구독이 적용되는 Windows Server 머신이 있고 마이그레이션할 머신에 이 혜택을 적용하려면 **예**를 선택합니다. 그런 후 **Next** 를 클릭합니다.

    ![대상 설정](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

11. **컴퓨팅**에서 VM 이름, 크기, OS 디스크 유형 및 가용성 집합을 검토합니다. VM은 [Azure 요구 사항](migrate-support-matrix-physical-migration.md#azure-vm-requirements)을 준수해야 합니다.

    - **VM 크기**: 기본적으로 Azure Migrate 서버 마이그레이션은 Azure 구독에서 가장 일치하는 항목을 기준으로 크기를 선택합니다. 또는 **Azure VM 크기**에서 수동 크기를 선택합니다.
    - **OS 디스크**: VM에 맞는 OS(부팅) 디스크를 지정합니다. OS 디스크는 운영 체제 부팅 로더 및 설치 관리자가 있는 디스크입니다. 
    - **가용성 집합**: 마이그레이션 후에 VM이 Azure 가용성 집합에 있어야 하는 경우 해당 집합을 지정합니다. 이 집합은 마이그레이션을 위해 지정한 대상 리소스 그룹에 있어야 합니다.

    ![컴퓨팅 설정](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

12. **디스크**에서 VM 디스크를 Azure에 복제해야 하는지 여부를 지정하고, Azure에서 디스크 유형(표준 SSD/HDD 또는 프리미엄 관리 디스크)을 선택합니다. 그런 후 **Next** 를 클릭합니다.
    - 디스크를 복제에서 제외할 수 있습니다.
    - 디스크를 제외하는 경우 마이그레이션 후 Azure VM에 표시되지 않습니다. 

    ![디스크 설정](./media/tutorial-migrate-physical-virtual-machines/disks.png)

13. **검토 및 복제 시작**에서 설정을 검토하고, **복제**를 클릭하여 서버에 대한 초기 복제를 시작합니다.

> [!NOTE]
> 복제가 시작되기 전에 언제든지 **관리** > **머신 복제 중**에서 복제 설정을 업데이트할 수 있습니다. 복제가 시작된 후에는 설정을 변경할 수 없습니다.

## <a name="track-and-monitor-replication-status"></a>복제 상태 추적 및 모니터링

- **복제**를 클릭하면 복제 시작 작업이 시작됩니다.
- [복제 시작] 작업이 성공적으로 완료되면 VM에서 Azure로의 초기 복제를 시작합니다.
- 초기 복제가 완료되면 델타 복제가 시작됩니다. AWS VM 디스크에 대한 증분 변경 내용이 Azure의 복제본 디스크에 주기적으로 복제됩니다.

작업 상태는 포털 알림에서 추적할 수 있습니다.

**서버 복제 중**을 **Azure Migrate: 서버 마이그레이션**에서 클릭하여 복제 상태를 모니터링할 수 있습니다.  

![복제 모니터링](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>테스트 마이그레이션 실행

델타 복제가 시작되면 Azure로 전체 마이그레이션을 실행하기 전에 VM에 대한 테스트 마이그레이션을 실행할 수 있습니다. 실제 마이그레이션을 진행하기 전에 잠재적인 문제를 검색하고 해결할 수 있는 기회를 제공하므로 테스트 마이그레이션이 적극적으로 추천됩니다. 마이그레이션하기 전에 이 작업을 각 VM에 대해 한 번 이상 수행하는 것이 좋습니다.

- 테스트 마이그레이션이 실행되면 AWS VM에 영향을 주지 않고 마이그레이션이 예상대로 작동하는지 확인합니다. 이 경우 이 AWS VM은 작동 상태를 유지하고 복제를 계속합니다.
- 테스트 마이그레이션은 복제된 데이터를 사용하여 Azure VM을 만들어 마이그레이션을 시뮬레이션합니다(일반적으로 Azure 구독에서 비프로덕션 VNet으로 마이그레이션).
- 복제된 테스트 Azure VM을 사용하여 마이그레이션의 유효성을 검사하고, 애플리케이션 테스트를 수행하며, 전체 마이그레이션을 수행하기 전에 문제를 해결할 수 있습니다.

다음과 같이 테스트 마이그레이션을 수행합니다.

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 마이그레이션**에서 **마이그레이션된 서버 테스트**를 클릭합니다.

     ![마이그레이션된 서버 테스트](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. 마우스 오른쪽 단추로 테스트할 VM을 클릭하고, **테스트 마이그레이션**을 클릭합니다.

    ![테스트 마이그레이션](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. **테스트 마이그레이션**에서 마이그레이션 후 Azure VM이 위치할 Azure VNet을 선택합니다. 비프로덕션 VNet을 사용하는 것이 좋습니다.
4. **테스트 마이그레이션** 작업이 시작됩니다. 포털 알림에서 작업을 모니터링합니다.
5. 마이그레이션이 완료되면 Azure Portal의 **Virtual Machines**에서 마이그레이션된 Azure VM을 확인합니다. 머신 이름에는 **-Test** 접미사가 있습니다.
6. 테스트가 완료되면 **머신 복제 중**에서 마우스 오른쪽 단추로 Azure VM을 클릭하고, **테스트 마이그레이션 정리**를 클릭합니다.

    ![마이그레이션 정리](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-aws-vms"></a>AWS VM 마이그레이션

테스트 마이그레이션이 예상대로 작동하는지 확인한 후에는 AWS VM을 마이그레이션할 수 있습니다.

1. Azure Migrate 프로젝트 > **서버** > **Azure Migrate: 서버 마이그레이션**에서 **서버 복제 중**을 클릭합니다.

    ![서버 복제 중](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. **머신 복제 중**에서 마우스 오른쪽 단추로 VM > **마이그레이션**을 차례로 클릭합니다.
3. **마이그레이션** > **가상 머신을 종료하고 데이터 손실 없이 계획된 마이그레이션을 수행하시겠습니까?** 에서 **예** > **확인**을 차례로 선택합니다.
    - VM을 종료하지 않으려면 **아니요**를 선택합니다.
4. VM에 대한 마이그레이션 작업이 시작됩니다. 작업 상태는 포털 페이지의 오른쪽 위에서 알림 벨 아이콘을 클릭하거나 서버 마이그레이션 도구의 작업 페이지로 이동하여(도구 타일에서 [개요] 클릭 > 왼쪽 메뉴에서 [작업] 선택) 확인할 수 있습니다.
5. 작업이 완료되면 Virtual Machines 페이지에서 VM을 보고 관리할 수 있습니다.

### <a name="complete-the-migration"></a>마이그레이션 완료

1. 마이그레이션이 완료되면 마우스 오른쪽 단추로 VM > **마이그레이션 중지**를 차례로 클릭합니다. 다음을 수행합니다.
    - AWS VM 복제를 중지합니다.
    - Azure Migrate: 서버 마이그레이션의 **복제 중 서버** 수에서 AWS VM을 제거합니다.
    - VM에 대한 복제 상태 정보를 정리합니다.
2. [Linux](../virtual-machines/extensions/agent-linux.md) 에이전트를 마이그레이션된 컴퓨터에 설치합니다. Azure VM Windows 에이전트는 마이그레이션 프로세스 중에 미리 설치됩니다.
3. 데이터베이스 연결 문자열 업데이트, 웹 서버 구성 등의 마이그레이션 후 앱 조정을 수정합니다.
4. 이제 Azure에서 실행 중인 마이그레이션된 애플리케이션에서 최종 애플리케이션 및 마이그레이션 수용 테스트를 수행합니다.
5. 트래픽을 마이그레이션된 Azure VM 인스턴스로 전환합니다.
6. 내부 문서를 업데이트하여 Azure VM의 새 위치 및 IP 주소를 표시합니다. 




## <a name="post-migration-best-practices"></a>마이그레이션 후 작업 모범 사례

- 복원력 개선:
    - Azure Backup 서비스를 통해 Azure VM을 백업하여 데이터 보안을 유지합니다. [자세히 알아보기](../backup/quick-backup-vm-portal.md).
    - Site Recovery를 통해 Azure VM을 보조 지역에 복제하면 워크로드를 계속 실행하고 지속적으로 사용할 수 있습니다. [자세히 알아보기](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- 보안 강화:
    - [Azure Security Center - Just-In-Time 관리](../security-center/security-center-just-in-time.md)를 사용하여 인바운드 트래픽 액세스를 잠그고 제한합니다.
    - [네트워크 보안 그룹](../virtual-network/security-overview.md)을 사용하여 관리 엔드포인트에 대한 네트워크 트래픽을 제한합니다.
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)을 배포하여 디스크를 보호하고 데이터를 도난 및 무단 액세스로부터 안전하게 유지합니다.
    - [IaaS 리소스 보호](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)에 대해 자세히 알아보고 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 방문하세요.
- 모니터링 및 관리 앱:
    - 리소스 사용량과 비용을 모니터링하려면 [Azure Cost Management](../cost-management-billing/cloudyn/overview.md)를 배포하는 것이 좋습니다.



## <a name="troubleshooting--tips"></a>문제 해결/팁

**질문:** 마이그레이션을 위해 검색된 서버 목록에서 내 AWS VM이 표시되지 않습니다.   
**답변:** 복제 어플라이언스에서 요구 사항을 충족하는지 확인합니다. 모바일 에이전트가 마이그레이션할 원본 VM에 설치되어 있고 구성 서버에 등록되어 있는지 확인합니다. 네트워크 설정 및 방화벽 규칙을 확인하여 복제 어플라이언스와 원본 AWS VM 간의 네트워크 경로를 사용하도록 설정합니다.  

**질문:** 내 VM이 성공적으로 마이그레이션되었는지 확인하려면 어떻게 할까요?   
**답변:** 마이그레이션 후에 Virtual Machines 페이지에서 VM을 보고 관리할 수 있습니다. 마이그레이션된 VM에 연결하여 유효성을 검사합니다.  

**질문:** 이전에 만든 서버 평가 결과에서 마이그레이션할 VM을 가져올 수 없습니다.   
**답변:** 현재 이 워크플로에 대한 평가 가져오기를 지원하지 않습니다. 이 문제를 해결하려면 평가를 내보낸 다음, [복제 사용] 단계에서 VM 추천 사항을 수동으로 선택할 수 있습니다.
  
**질문:** AWS VM을 검색하는 동안 "BIOS GUID를 가져오지 못했습니다."라는 오류가 발생합니다.   
**답변:** 인증에는 항상 루트 로그인을 사용하고 의사 사용자는 사용하지 않습니다. 또한 AWS VM에 지원되는 운영 체제를 검토합니다.  

**질문:** 내 복제 상태가 진행되고 있지 않습니다.    
**답변:** 복제 어플라이언스에서 요구 사항을 충족하는지 확인합니다. 데이터 전송을 위해 복제 어플라이언스 9443 TCP 포트 및 443 HTTPS 포트에서 필요한 포트를 사용하도록 설정했는지 확인합니다. 동일한 프로젝트에 연결되는 복제 어플라이언스의 버전이 오래되고 중복되지 않는지 확인합니다.   

**질문:** 원격 Windows 관리 서비스에서 504 HTTP 상태 코드로 인해 Azure Migrate를 사용하여 AWS 인스턴스를 검색할 수 없습니다.    
**답변:** Azure Migrate 어플라이언스 요구 사항 및 URL 액세스 요구 사항을 검토해야 합니다. 어플라이언스 등록을 차단하는 프록시 설정이 없는지 확인합니다.   

## <a name="next-steps"></a>다음 단계

Azure 클라우드 채택 프레임워크에서 [클라우드 마이그레이션 과정](/azure/architecture/cloud-adoption/getting-started/migrate)을 조사합니다.
