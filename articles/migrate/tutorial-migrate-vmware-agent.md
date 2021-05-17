---
title: 에이전트 기반 Azure Migrate Server Migration으로 VMware VM 마이그레이션
description: Azure Migrate를 사용하여 VMware VM의 에이전트 기반 마이그레이션을 실행하는 방법에 대해 알아봅니다.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 15bf8f4fde2128181664fa7b94f2479bac7ad5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881520"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware VM을 Azure로 마이그레이션(에이전트 기반)

이 문서에서는 에이전트 기반 마이그레이션을 통한 [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용하여 온-프레미스 VMware VM을 Azure로 마이그레이션하는 방법에 대해 설명합니다.  에이전트 없는 마이그레이션을 사용하여 VMware VM을 마이그레이션할 수도 있습니다. 메서드를 [비교](server-migrate-overview.md#compare-migration-methods)합니다.


 이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Migrate를 사용할 수 있게 Azure를 준비합니다.
> * 에이전트 기반 마이그레이션을 준비합니다. Azure Migrate에서 마이그레이션을 위해 머신을 검색할 수 있도록 VMware 계정을 설정합니다. Mobility Service 에이전트가 마이그레이션하려는 머신에 설치할 수 있도록 계정을 설정하고, 복제 어플라이언스로 작동할 머신을 준비합니다.
> * Azure Migrate:Server Migration 도구 추가
> * 복제 어플라이언스를 설정합니다.
> * VM을 복제합니다.
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인합니다.
> * 전체 마이그레이션을 Azure로 실행합니다.

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에 VMware 에이전트 기반 마이그레이션 아키텍처를 [검토](./agent-based-migration-architecture.md)합니다.

## <a name="prepare-azure"></a>Azure 준비

에이전트 기반 마이그레이션을 위해 Azure를 준비하려면 표의 태스크를 완료합니다.

**Task** | **세부 정보**
--- | ---
**Azure Migrate 프로젝트 만들기** | Azure 계정에는 프로젝트를 만들 수 있는 기여자 또는 소유자 권한이 있어야 합니다.
**Azure 계정 권한 확인** | Azure 계정에는 VM을 만들고 Azure 관리 디스크에 쓸 수 있는 권한이 필요합니다.
**Azure 네트워크 설정** | 마이그레이션 후 Azure VM이 가입할 네트워크를 설정합니다.

### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당
Azure Migrate 프로젝트가 없는 경우 새로 만들 수 있는 권한이 있는지 확인합니다.


1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인** 에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.
3. **Contributor** 또는 **Owner** 권한이 있는지 확인합니다.

    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.
    
### <a name="assign-azure-account-permissions"></a>Azure 계정 권한 할당

계정에 다음과 같은 권한을 갖도록 Virtual Machine Contributor 역할을 할당합니다.

- 선택한 리소스 그룹에 VM 만들기
- 선택한 가상 네트워크에 VM 만들기
- Azure 관리 디스크에 씁니다. 


### <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

[Azure 네트워크를 설정합니다](../virtual-network/manage-virtual-network.md#create-a-virtual-network). 온-프레미스 머신이 Azure 관리 디스크에 복제됩니다. 마이그레이션을 위해 Azure로 장애 조치하면 Azure VM이 이러한 관리 디스크에서 만들어지고, 설정한 Azure 네트워크에 조인됩니다.

## <a name="prepare-for-migration"></a>마이그레이션 준비

지원 요구 사항 및 사용 권한을 확인하고, 복제 어플라이언스 배포를 준비합니다. 

### <a name="prepare-an-account-to-discover-vms"></a>VM을 검색할 계정 준비

Azure Migrate Server Migration은 마이그레이션하려는 VM을 검색하기 위해 VMware 서버에 액세스해야 합니다. 계정을 만들려면 다음을 수행합니다.

1. 전용 계정을 사용하려면 vCenter 수준에서 역할을 만듭니다. 역할에 **Azure_Migrate** 와 같은 이름을 지정합니다.
2. 아래 표에 요약된 권한을 역할에 할당합니다.
3. vCenter 서버 또는 vSphere 호스트에서 사용자를 만듭니다. 사용자에게 역할을 할당합니다.

#### <a name="vmware-account-permissions"></a>VMware 계정 권한

**Task** | **역할/권한** | **세부 정보**
--- | --- | ---
**VM 검색** | 읽기 전용 사용자(최소)<br/><br/> 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려면 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.
**복제** |  필요한 권한이 있는 역할(Azure_Site_Recovery)을 만든 다음 VMware 사용자 또는 그룹에 이 역할을 할당합니다.<br/><br/> 데이터 센터 개체 –> 자식 개체에 전파, role=Azure_Site_Recovery<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 머신 -&gt; 프로비전 -&gt; 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅샷 -&gt; 스냅샷 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려면 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

### <a name="prepare-an-account-for-mobility-service-installation"></a>모바일 서비스 설치를 위한 계정 준비

복제하려는 컴퓨터에 모바일 서비스가 설치되어야 합니다.

- 머신에 복제를 사용하도록 설정하면 Azure Migrate 복제 어플라이언스에서 이 서비스의 강제 설치를 수행할 수 있습니다. 그렇지 않으면 수동으로 설치하거나 설치 도구를 사용하여 설치할 수 있습니다.
- 이 자습서에서는 강제 설치 방식으로 Mobility 서비스를 설치하겠습니다.
- 강제 설치의 경우 Azure Migrate 서버 마이그레이션에서 VM에 액세스하는 데 사용할 수 있는 계정을 준비해야 합니다. 이 계정은 Mobility Service를 수동으로 설치하지 않은 경우 푸시 설치에만 사용됩니다.

다음과 같이 계정을 준비합니다.

1. VM에 설치할 수 있는 권한이 있는 도메인 또는 로컬 계정을 준비합니다.
2. Windows VM의 경우 도메인 계정을 사용하지 않으면 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 에서 레지스트리의 값과 함께 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가하여 로컬 머신에서 원격 사용자 액세스 제어를 사용하지 않도록 설정합니다.
3. Linux VM의 경우 원본 Linux 서버에서 루트 계정을 준비합니다.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>복제 어플라이언스용 머신 준비

어플라이언스는 Azure에 머신을 복제하는 데 사용됩니다. 어플라이언스는 다음 구성 요소를 호스팅하는 단일 고가용성 온-프레미스 VMware VM입니다.

- **구성 서버**: 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
- **프로세스 서버**: 프로세스 서버는 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하여 캐싱, 압축 및 암호화를 사용하여 최적화한 후 Azure의 캐시 스토리지 계정으로 보냅니다. 또한 프로세스 서버는 복제하려는 VM에 모바일 서비스 에이전트를 설치하고, 온-프레미스 VMware VM을 자동으로 검색합니다.

다음과 같이 어플라이언스를 준비합니다.

- [어플라이언스 요구 사항을 검토](migrate-replication-appliance.md#appliance-requirements)합니다. 일반적으로 다운로드한 OVA 파일을 사용하여 복제 어플라이언스를 VMware VM으로 설정합니다. 템플릿은 모든 요구 사항을 준수하는 어플라이언스를 만듭니다.
- MySQL은 어플라이언스에 설치되어야 합니다. 설치 방법을 [검토](migrate-replication-appliance.md#mysql-installation)합니다.
- 어플라이언스 머신이 액세스해야 하는 [퍼블릭 클라우드 URL](migrate-replication-appliance.md#url-access) 및 [Azure Government URL](migrate-replication-appliance.md#azure-government-url-access)을 검토합니다.
- 복제 어플라이언스 머신에서 액세스해야 하는 [포트를 검토](migrate-replication-appliance.md#port-access)합니다.



### <a name="check-vmware-requirements"></a>VMware 요구 사항 확인

VMware 서버와 VM에서 Azure로 마이그레이션하기 위한 요구 사항을 준수하는지 확인합니다. 

1. VMware 서버 요구 사항을 [확인](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)합니다.
2. 마이그레이션에 대한 VM 요구 사항을 [확인](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)합니다.
3. Azure 설정을 확인합니다. Azure에 복제하는 온-프레미스 VM은 [Azure VM 요구 사항](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)을 준수해야 합니다.
4. VM을 Azure로 마이그레이션하기 전에 VM에서 몇 가지 사항을 변경해야 합니다.
    - 이러한 변경은 마이그레이션을 시작하기 전에 수행해야 합니다. 변경하기 전에 VM을 마이그레이션하면 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.
    - 필요한 [Windows](prepare-for-migration.md#windows-machines) 및 [Linux](prepare-for-migration.md#linux-machines) 변경 사항을 검토해야 합니다.

> [!NOTE]
> Azure Migrate 서버 마이그레이션을 사용하는 에이전트 기반 마이그레이션은 Azure Site Recover 서비스의 기능을 기반으로 합니다. 일부 요구 사항은 Site Recovery 설명서에 연결될 수 있습니다.

## <a name="set-up-the-replication-appliance"></a>복제 어플라이언스 설정

이 절차에서는 다운로드한 OVA(Open Virtualization Application) 템플릿을 사용하여 어플라이언스를 설정하는 방법을 설명합니다. 이 방법을 사용할 수 없는 경우 [스크립트를 사용하여](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) 어플라이언스를 설정할 수 있습니다. 

### <a name="download-the-replication-appliance-template"></a>복제 어플라이언스 템플릿 다운로드

다음과 같이 템플릿을 다운로드합니다.

1. Azure Migrate 프로젝트의 **마이그레이션 목표** 아래에서 **서버** 를 클릭합니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 마이그레이션** 에서 **검색** 을 클릭합니다.

    ![VM 검색](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. **머신 검색** > **머신이 가상화되어 있나요?** 에서 **예, VMware vSphere 하이퍼바이저 사용** 을 클릭합니다.
4. **원하는 마이그레이션 방법을 선택하세요.** 에서 **에이전트 기반 복제 사용** 을 선택합니다.
5. **대상 지역** 에서 머신을 마이그레이션하려는 Azure 지역을 선택합니다.
6. **마이그레이션의 대상 지역이 region-name인지 확인합니다.** 를 선택합니다.
7. **리소스 만들기** 를 클릭합니다. 그러면 Azure Site Recovery 자격 증명 모음이 백그라운드에서 만들어집니다. 이 단추를 클릭한 후에는 이 프로젝트의 대상 지역을 변경할 수 없으며, 이후의 모든 마이그레이션은 이 지역으로 수행됩니다.

    ![Recovery Services 자격 증명 모음 만들기](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. **새 복제 어플라이언스를 설치하거나 기존 설치를 확장하시겠습니까?** 에서 **복제 어플라이언스 설치** 를 선택합니다.
9. **다운로드** 를 클릭합니다. 이렇게 하면 OVF 템플릿을 다운로드합니다.
    ![OVA 다운로드](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. 리소스 그룹 및 Recovery Services 자격 증명 모음의 이름을 적어둡니다. 이러한 항목은 어플라이언스를 배포하는 동안 필요합니다.


### <a name="import-the-template-in-vmware"></a>VMware에서 템플릿 가져오기

OVF 템플릿이 다운로드되면 VMware로 가져와서 Windows Server 2016을 실행하는 VMware VM에 복제 애플리케이션을 만듭니다.

1. VMware vSphere 클라이언트를 사용하여 VMware vCenter 서버 또는 vSphere ESXi 호스트에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포** 를 선택하여 **OVF 템플릿 배포 마법사** 를 시작합니다. 
3. **원본 선택** 에서 다운로드한 OVF의 위치를 입력합니다.
4. **세부 정보 검토** 에서 **다음** 을 선택합니다.
5. **이름 및 폴더 선택** 과 **구성 선택** 에서 기본 설정을 그대로 적용합니다.
6. **스토리지 선택** > **가상 디스크 형식 선택** 에서 최상의 성능을 위해 **Thick Provision Eager Zeroed** 를 선택합니다.
7. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
8. **완료 준비** 에서 기본 설정을 사용하여 VM을 설정하려면 **배포 후 전원 켜기** > **마침** 을 선택합니다.

   > [!TIP]
   > 추가 NIC를 추가하려면 **배포 후 전원 켜기** > **마침** 의 선택을 취소합니다. 템플릿에는 기본적으로 단일 NIC가 포함됩니다. 배포 후 NIC를 추가할 수 있습니다.

### <a name="start-appliance-setup"></a>어플라이언스 설정 시작

1. VMware vSphere 클라이언트 콘솔에서 VM을 켭니다. VM이 Windows Server 2016 설치 환경으로 부팅됩니다.
2. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 관리자 암호를 사용하여 관리자 권한으로 VM에 로그인합니다. 처음 로그인하면 복제 어플라이언스 설정 도구(Azure Site Recovery 구성 도구)가 몇 초 내에 시작됩니다.
5. 어플라이언스를 Server Migration에 등록하는 데 사용할 이름을 입력합니다. 그런 후 **Next** 를 클릭합니다.
6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인** 을 선택하여 Azure 구독에 로그인합니다.
7. 도구에서 Azure AD 앱 등록을 마칠 때까지 기다린 후에 어플라이언스를 식별합니다. 어플라이언스가 다시 부팅됩니다.
1. 컴퓨터에 다시 로그인합니다. 몇 초 내로 구성 서버 관리 마법사가 자동으로 시작됩니다.

### <a name="register-the-replication-appliance"></a>복제 어플라이언스 등록

복제 어플라이언스 설정 및 등록을 완료합니다.

1. 어플라이언스 설정에서 **연결 설정** 을 선택합니다.
2. 복제 어플라이언스에서 VM 검색에 사용하는 NIC(기본적으로 하나의 NIC만 있음)를 선택하고, Mobility Service를 원본 머신에 강제 설치합니다.
3. 복제 어플라이언스에서 Azure에 연결하는 데 사용하는 NIC를 선택합니다. 그런 다음 **저장** 을 선택합니다. 구성된 후에는 이 설정을 변경할 수 없습니다.
4. 어플라이언스가 프록시 서버 뒤에 있는 경우 프록시 설정을 지정해야 합니다.
    - 프록시 이름을 **http://ip-address** 또는 **http://FQDN** 으로 지정합니다. HTTPS 프록시 서버는 지원되지 않습니다.
5. 구독, 리소스 그룹 및 자격 증명 모음 세부 정보를 묻는 메시지가 표시되면 어플라이언스 템플릿을 다운로드할 때 적어둔 세부 정보를 추가합니다.
6. **타사 소프트웨어 설치** 에서 사용권 계약에 동의합니다. **다운로드 및 설치** 를 선택하여 MySQL 서버를 설치합니다.
7. **VMware PowerCLI 설치** 를 선택합니다. 이렇게 하기 전에 모든 브라우저 창을 닫아야 합니다. 그런 다음, **계속** 을 선택합니다.
8. 계속하기 전에 **어플라이언스 구성 유효성 검사** 에서 필수 구성 요소가 확인됩니다.
9. **vCenter Server/vSphere ESXi 서버 구성** 에 복제하려는 VM이 있는 vCenter 서버 또는 vSphere 호스트의 FQDN 또는 IP 주소를 입력합니다. 서버가 수신 대기하는 포트를 입력합니다. 자격 증명 모음의 VMware 서버에 사용할 이름을 입력합니다.
10. VMware 검색을 위해 [만든](#prepare-an-account-to-discover-vms) 계정의 자격 증명을 입력합니다. **추가** > **계속** 을 선택합니다.
11. **가상 머신 자격 증명 구성** 에서 VM에 복제를 사용하도록 설정할 때 Mobility Service의 강제 설치를 위해 [만든](#prepare-an-account-for-mobility-service-installation) 자격 증명을 입력합니다.  
    - Windows 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다.
    - Linux의 경우 루트 계정에 대한 세부 정보를 제공합니다.
12. **구성 완료** 를 선택하여 등록을 완료합니다.


복제 어플라이언스가 등록되면 Azure Migrate 서버 평가에서 지정된 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다. 검색된 VM은 **관리** > **검색된 항목** 의 **기타** 탭에서 확인할 수 있습니다.



## <a name="replicate-vms"></a>VM 복제

마이그레이션할 VM을 선택합니다.

> [!NOTE]
> 포털에서 복제에 대해 한 번에 최대 10개의 머신을 선택할 수 있습니다. 추가로 복제해야 하는 경우 10개의 일괄 처리로 그룹화합니다.

1. Azure Migrate 프로젝트 > **서버**, **Azure Migrate: 서버 마이그레이션** 에서 **복제** 를 클릭합니다.

    ![Azure Migrate의 서버 화면 스크린샷. Azure Migrate에서 복제 단추를 선택합니다. 서버 마이그레이션에서 선택한 복제 단추가 표시됩니다.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. **복제** > **원본 설정** > **머신이 가상화되어 있습니까?** 에서 **예, VMware vSphere 사용** 을 선택합니다.
3. **온-프레미스 어플라이언스** 에서 설정한 Azure Migrate 어플라이언스의 이름을 선택합니다.
4. **vCenter 서버** 에서 VM을 관리하는 vCenter 서버 또는 VM이 호스팅되는 vSphere 서버의 이름을 지정합니다.
5. **프로세스 서버** 에서 복제 어플라이언스의 이름을 선택합니다.
6. **게스트 자격 증명** 에서 Mobility Service의 강제 설치에 사용할 VM 관리자 계정을 지정합니다. 그런 다음, **다음: 가상 머신** 을 클릭합니다.

    ![복제 화면에 있는 원본 설정 탭의 스크린샷. 게스트 자격 증명 필드가 강조 표시되고 값이 VM 관리자 계정으로 설정됩니다.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. **Virtual Machines** 에서 복제하려는 머신을 선택합니다.

    - VM 평가를 실행한 경우 평가 결과에서 VM 크기 조정 및 디스크 유형(프리미엄/표준) 추천 사항을 적용할 수 있습니다. 이렇게 하려면 **Azure Migrate 평가에서 마이그레이션 설정을 가져오시겠습니까?** 에서 **예** 옵션을 선택합니다.
    - 평가를 실행하지 않았거나 평가 설정을 사용하지 않으려면 **아니요** 옵션을 선택합니다.
    - 평가를 사용하도록 선택한 경우 VM 그룹 및 평가 이름을 선택합니다.
8. **가용성 옵션** 에서 다음을 선택합니다.
    -  마이그레이션된 머신을 지역의 특정 가용성 영역에 고정하는 가용성 영역. 이 옵션을 사용하여 가용성 영역에서 다중 노드 애플리케이션 계층을 구성하는 서버를 배포합니다. 이 옵션을 선택하는 경우 Compute 탭에서 선택한 각 머신에 사용할 가용성 영역을 지정해야 합니다. 이 옵션은 마이그레이션을 위해 선택한 대상 지역이 가용성 영역을 지원하는 경우에만 사용할 수 있습니다.
    -  마이그레이션된 머신을 가용성 집합에 배치하기 위한 가용성 집합입니다. 이 옵션을 사용하려면 선택한 대상 리소스 그룹에 하나 이상의 가용성 집합이 있어야 합니다.
    - 마이그레이션된 머신에 대해 이러한 가용성 구성이 필요하지 않은 경우에는 인프라 중복이 필요하지 않습니다.
9. 마이그레이션하려는 각 VM을 선택합니다. 그런 다음, **다음: 대상 설정** 을 클릭합니다.
10. **대상 설정** 에서 마이그레이션할 구독 및 대상 지역을 선택하고, 마이그레이션 후 Azure VM이 상주할 리소스 그룹을 지정합니다.
11. **Virtual Network** 에서 마이그레이션 후 Azure VM이 조인될 Azure VNet/서브넷을 선택합니다.
12. **가용성 옵션** 에서 다음을 선택합니다.
    -  마이그레이션된 머신을 지역의 특정 가용성 영역에 고정하는 가용성 영역. 이 옵션을 사용하여 가용성 영역에서 다중 노드 애플리케이션 계층을 구성하는 서버를 배포합니다. 이 옵션을 선택하는 경우 Compute 탭에서 선택한 각 머신에 사용할 가용성 영역을 지정해야 합니다. 이 옵션은 마이그레이션을 위해 선택한 대상 지역이 가용성 영역을 지원하는 경우에만 사용할 수 있습니다.
    -  마이그레이션된 머신을 가용성 집합에 배치하기 위한 가용성 집합입니다. 이 옵션을 사용하려면 선택한 대상 리소스 그룹에 하나 이상의 가용성 집합이 있어야 합니다.
    - 마이그레이션된 머신에 대해 이러한 가용성 구성이 필요하지 않은 경우에는 인프라 중복이 필요하지 않습니다.
13. **디스크 암호화 유형** 에서 다음을 선택합니다.
    - 플랫폼 관리형 키를 사용하여 저장 데이터 암호화
    - 고객 관리형 키를 사용하여 저장소 데이터 암호화
    - 플랫폼 관리형 키 및 고객 관리형 키를 사용한 이중 암호화

   > [!NOTE]
   > CMK를 사용하여 VM을 복제하려면 대상 리소스 그룹 아래에 [디스크 암호화 집합을 생성](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)해야 합니다. 디스크 암호화 집합 개체는 SSE에 사용할 CMK가 포함된 Key Vault에 Managed Disks를 매핑됩니다.
  
14. **Azure 하이브리드 혜택** 에서

    - Azure 하이브리드 혜택을 적용하지 않으려면 **아니요** 를 선택합니다. 그런 후 **Next** 를 클릭합니다.
    - 활성 Software Assurance 또는 Windows Server 구독이 적용되는 Windows Server 머신이 있고 마이그레이션할 머신에 이 혜택을 적용하려면 **예** 를 선택합니다. 그런 후 **Next** 를 클릭합니다.

    ![대상 설정](./media/tutorial-migrate-vmware/target-settings.png)

15. **Compute** 에서 VM 이름, 크기, OS 디스크 유형 및 가용성 구성을 검토합니다(이전 단계에서 선택한 경우). VM은 [Azure 요구 사항](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)을 준수해야 합니다.

   - **VM 크기**: 평가 권장 사항을 사용하는 경우 VM 크기 드롭다운에서 권장 크기를 표시합니다. 그렇지 않으면 Azure Migrate는 Azure 구독에서 가장 일치하는 항목을 기준으로 크기를 선택합니다. 또는 **Azure VM 크기** 에서 수동 크기를 선택합니다. 
    - **OS 디스크**: VM에 맞는 OS(부팅) 디스크를 지정합니다. OS 디스크는 운영 체제 부팅 로더 및 설치 관리자가 있는 디스크입니다. 
    - **가용성 영역**: 사용할 가용성 영역을 지정합니다.
    - **가용성 집합**: 사용할 가용성 집합을 지정합니다.

16. **디스크** 에서 VM 디스크를 Azure에 복제해야 하는지 여부를 지정하고, Azure에서 디스크 유형(표준 SSD/HDD 또는 프리미엄 관리 디스크)을 선택합니다. 그런 후 **Next** 를 클릭합니다.
    - 디스크를 복제에서 제외할 수 있습니다.
    - 디스크를 제외하는 경우 마이그레이션 후 Azure VM에 표시되지 않습니다. 

17. **검토 및 복제 시작** 에서 설정을 검토하고, **복제** 를 클릭하여 서버에 대한 초기 복제를 시작합니다.

> [!NOTE]
> 복제가 시작되기 전에 언제든지 **관리** > **머신 복제 중** 에서 복제 설정을 업데이트할 수 있습니다. 복제가 시작된 후에는 설정을 변경할 수 없습니다.


## <a name="track-and-monitor"></a>추적 및 모니터링

1. 포털 알림에서 작업 상태를 추적합니다. 

    ![작업 추적](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. 복제 상태를 모니터링하려면 **서버 복제 중** 을 **Azure Migrate: 서버 마이그레이션** 에서 클릭합니다.

    ![복제 모니터링](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

복제는 다음과 같이 수행됩니다.
- 복제 시작 작업이 완료되면 머신에서 Azure로의 초기 복제를 시작합니다.
- 초기 복제가 완료되면 델타 복제가 시작됩니다. 온-프레미스 디스크에 대한 증분 변경 내용은 Azure의 복제본 디스크에 주기적으로 복제됩니다.


## <a name="run-a-test-migration"></a>테스트 마이그레이션 실행


델타 복제가 시작되면 Azure로 전체 마이그레이션을 실행하기 전에 VM에 대한 테스트 마이그레이션을 실행할 수 있습니다. 이 작업은 마이그레이션하기 전에 각 머신에 대해 한 번 이상 수행하는 것이 좋습니다.

- 테스트 마이그레이션을 실행하면 작동 상태를 유지하고 복제를 계속하는 온-프레미스 머신에 영향을 주지 않고 마이그레이션이 예상대로 작동하는지 확인합니다. 
- 테스트 마이그레이션은 복제된 데이터를 사용하여 Azure VM을 만들어 마이그레이션을 시뮬레이션합니다(일반적으로 Azure 구독에서 비프로덕션 VNet으로 마이그레이션).
- 복제된 테스트 Azure VM을 사용하여 마이그레이션의 유효성을 검사하고, 애플리케이션 테스트를 수행하며, 전체 마이그레이션을 수행하기 전에 문제를 해결할 수 있습니다.

다음과 같이 테스트 마이그레이션을 수행합니다.


1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 마이그레이션** 에서 **마이그레이션된 서버 테스트** 를 클릭합니다.

     ![마이그레이션된 서버 테스트](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. 마우스 오른쪽 단추로 테스트할 VM을 클릭하고, **테스트 마이그레이션** 을 클릭합니다.

    ![테스트 마이그레이션](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. **테스트 마이그레이션** 에서 마이그레이션 후 Azure VM이 위치할 Azure VNet을 선택합니다. 비프로덕션 VNet을 사용하는 것이 좋습니다.
4. **테스트 마이그레이션** 작업이 시작됩니다. 포털 알림에서 작업을 모니터링합니다.
5. 마이그레이션이 완료되면 Azure Portal의 **Virtual Machines** 에서 마이그레이션된 Azure VM을 확인합니다. 머신 이름에는 **-Test** 접미사가 있습니다.
6. 테스트가 완료되면 **머신 복제 중** 에서 마우스 오른쪽 단추로 Azure VM을 클릭하고, **테스트 마이그레이션 정리** 를 클릭합니다.

    ![마이그레이션 정리](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>VM 마이그레이션

테스트 마이그레이션이 예상대로 작동하는지 확인한 후에는 온-프레미스 머신을 마이그레이션할 수 있습니다.

1. Azure Migrate 프로젝트 > **서버** > **Azure Migrate: 서버 마이그레이션** 에서 **서버 복제 중** 을 클릭합니다.

    ![서버 복제 중](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. **머신 복제 중** 에서 마우스 오른쪽 단추로 VM > **마이그레이션** 을 차례로 클릭합니다.
3. **마이그레이션** > **가상 머신을 종료하고 데이터 손실 없이 계획된 마이그레이션을 수행하시겠습니까?** 에서 **예** > **확인** 을 차례로 선택합니다.
    - 기본적으로 Azure Migrate는 온-프레미스 VM을 종료하여 데이터 손실을 최소화합니다. 
    - VM을 종료하지 않으려면 **아니요** 를 선택합니다.
4. VM에 대한 마이그레이션 작업이 시작됩니다. Azure 알림에서 작업을 추적합니다.
5. 작업이 완료되면 **Virtual Machines** 페이지에서 VM을 보고 관리할 수 있습니다.

## <a name="complete-the-migration"></a>마이그레이션 완료

1. 마이그레이션이 완료되면 마우스 오른쪽 단추로 VM > **마이그레이션 중지** 를 차례로 클릭합니다. 다음을 수행합니다.
    - 온-프레미스 머신에 대한 복제를 중지합니다.
    - Azure Migrate의 **서버 복제** 수에서 머신을 제거합니다. Server Migration에 연결합니다.
    - VM에 대한 복제 상태 정보를 정리합니다.
2. Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) 또는 [Linux](../virtual-machines/extensions/agent-linux.md) 에이전트를 마이그레이션된 머신에 설치합니다.
3. 데이터베이스 연결 문자열 업데이트, 웹 서버 구성 등의 마이그레이션 후 앱 조정을 수정합니다.
4. 이제 Azure에서 실행 중인 마이그레이션된 애플리케이션에서 최종 애플리케이션 및 마이그레이션 수용 테스트를 수행합니다.
5. 트래픽을 마이그레이션된 Azure VM 인스턴스로 전환합니다.
6. 로컬 VM 인벤토리에서 온-프레미스 VM을 제거합니다.
7. 로컬 백업 작업에서 온-프레미스 VM을 제거합니다.
8. 내부 문서를 업데이트하여 Azure VM의 새 위치 및 IP 주소를 표시합니다. 

## <a name="post-migration-best-practices"></a>마이그레이션 후 작업 모범 사례

- 온-프레미스
    - 마이그레이션된 Azure VM 인스턴스에서 실행 중인 앱으로 앱 트래픽을 이동합니다.
    - 로컬 VM 인벤토리에서 온-프레미스 VM을 제거합니다.
    - 로컬 백업 작업에서 온-프레미스 VM을 제거합니다.
    - 내부 문서를 업데이트하여 Azure VM의 새 위치 및 IP 주소를 표시합니다.
- 마이그레이션 후 Azure VM 설정을 조정합니다.
    - [Azure VM 에이전트](../virtual-machines/extensions/agent-windows.md)는 Azure Fabric Controller와 VM 간 상호 작용을 관리합니다. 이는 Azure Backup, Site Recovery, Azure Security 같은 일부 Azure 서비스에 필요합니다. 에이전트 기반 마이그레이션으로 VMare VM을 마이그레이션할 때 Mobility Service 설치 관리자는 Windows 머신에 Azure VM 에이전트를 설치합니다. Linux VM에서는 마이그레이션 후 에이전트를 설치하는 것이 좋습니다.
    - 마이그레이션 후 Azure VM에서 모바일 서비스를 수동으로 제거합니다.
    - 마이그레이션 후 VMware 도구를 수동으로 제거합니다.
- Azure에서:
    - 데이터베이스 연결 문자열 업데이트, 웹 서버 구성 등의 마이그레이션 후 앱 조정을 수정합니다.
    - 이제 Azure에서 실행 중인 마이그레이션된 애플리케이션에서 최종 애플리케이션 및 마이그레이션 수용 테스트를 수행합니다.
- 비즈니스 연속성/재해 복구
    - Azure Backup 서비스를 통해 Azure VM을 백업하여 데이터 보안을 유지합니다. [자세히 알아보기](../backup/quick-backup-vm-portal.md).
    - Site Recovery를 통해 Azure VM을 보조 지역에 복제하면 워크로드를 계속 실행하고 지속적으로 사용할 수 있습니다. [자세히 알아보기](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- 보안 강화:
    - [Azure Security Center - Just-In-Time 관리](../security-center/security-center-just-in-time.md)를 사용하여 인바운드 트래픽 액세스를 잠그고 제한합니다.
    - [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 사용하여 관리 엔드포인트에 대한 네트워크 트래픽을 제한합니다.
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)을 배포하여 디스크를 보호하고 데이터를 도난 및 무단 액세스로부터 안전하게 유지합니다.
    - [IaaS 리소스 보호](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)에 대해 자세히 알아보고 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 방문하세요.
- 모니터링 및 관리 앱:
    - 리소스 사용량과 비용을 모니터링하려면 [Azure Cost Management](../cost-management-billing/cloudyn/overview.md)를 배포하는 것이 좋습니다.




 ## <a name="next-steps"></a>다음 단계

Azure 클라우드 채택 프레임워크에서 [클라우드 마이그레이션 과정](/azure/architecture/cloud-adoption/getting-started/migrate)을 조사합니다.