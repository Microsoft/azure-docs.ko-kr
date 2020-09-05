---
title: Azure Migrate 어플라이언스
description: 서버 평가 및 마이그레이션에 사용되는 Azure Migrate 어플라이언스에 대해 간략히 설명합니다.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a313d7b964e6ea849acb5b034ed55975b8c5b524
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927516"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

이 문서에서는 Azure Migrate 어플라이언스의 필수 구성 요소 및 지원 요구 사항을 요약하고 있습니다. 

## <a name="deployment-scenarios"></a>배포 시나리오

Azure Migrate 어플라이언스가 사용되는 시나리오는 다음과 같습니다.

**시나리오** | **도구** | **용도** 
--- | --- | ---
**VMware VM 평가** | Azure Migrate: 서버 평가 | VMware VM을 검색합니다.<br/><br/> 컴퓨터 앱 및 종속성을 검색합니다.<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.
**VMware VM 에이전트 없는 마이그레이션** | Azure Migrate: 서버 마이그레이션 | VMware VM을 검색합니다. <br/><br/> 에이전트 없는 마이그레이션을 사용하여 VMware VM을 복제합니다.
**Hyper-V VM 평가** | Azure Migrate: 서버 평가 | Hyper-V VM을 검색합니다.<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.
**물리적 컴퓨터 평가** |  Azure Migrate: 서버 평가 |  물리적 서버(또는 물리적 서버로 처리하는 VM)를 검색합니다.<br/><br/> 평가를 위해 컴퓨터 메타데이터 및 성능 메타데이터를 수집합니다.

## <a name="deployment-methods"></a>배포 방법

어플라이언스는 다음과 같은 몇 가지 방법을 사용하여 배포할 수 있습니다.

- VMware VM 및 Hyper-V VM용 템플릿(VMware용 OVA 템플릿 또는 Hyper-V용 VHD 템플릿)을 사용하여 어플라이언스를 배포할 수 있습니다.
- 템플릿을 사용하지 않으려면 PowerShell 스크립트를 사용하여 VMware 또는 Hyper-V용 어플라이언스를 배포할 수 있습니다.
- Azure Government에서는 스크립트를 사용하여 어플라이언스를 배포해야 합니다.
- 물리적 서버의 경우 항상 스크립트를 사용하여 어플라이언스를 배포합니다.
- 다운로드 링크는 아래 표에 나와 있습니다.


## <a name="appliance---vmware"></a>어플라이언스 - VMware 

다음 표에는 VMware에 대한 Azure Migrate 어플라이언스 요구 사항이 요약되어 있습니다.

**요구 사항** | **VMware** 
--- | ---
**권한** | 어플라이언스 웹앱에 로컬 또는 원격으로 액세스하려면 어플라이언스 컴퓨터의 도메인 관리자 또는 로컬 관리자여야 합니다.
**어플라이언스 구성 요소** | 어플라이언스의 구성 요소는 다음과 같습니다.<br/><br/> - **관리 앱**: 어플라이언스를 배포하는 동안 사용자 입력을 위한 웹앱입니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/> - **검색 에이전트**: 에이전트가 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/>- **수집기 에이전트**: 에이전트는 성능 데이터를 수집 합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).<br/>- **DRA 에이전트**: VM 복제를 오케스트레이션하고, 복제된 컴퓨터와 Azure 간의 통신을 조정합니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure에 복제할 때만 사용됩니다.<br/>- **게이트웨이**: 복제된 데이터를 Azure에 보냅니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure에 복제할 때만 사용됩니다.
**지원되는 배포** | OVA 템플릿을 사용하여 VMware VM으로 배포합니다.<br/><br/> PowerShell 설치 스크립트를 사용하여 VMware VM 또는 물리적 컴퓨터로 배포합니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 vCenter Server에서 최대 10,000개의 VMware VM을 검색할 수 있습니다.<br/> 어플라이언스는 단일 vCenter Server에 연결할 수 있습니다.
**OVA 템플릿** | 포털에서 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140333) 에서 다운로드<br/><br/> 다운로드 크기는 11.6 GB입니다.<br/><br/> 다운로드한 어플라이언스 템플릿에는 180일 동안 유효한 Windows Server 2016 평가 라이선스가 제공됩니다. 평가 기간이 곧 만료되는 경우 새 어플라이언스를 다운로드하여 배포하거나 어플라이언스 VM의 운영 체제 라이선스에 대한 정품 인증을 수행하는 것이 좋습니다.
**PowerShell 스크립트** | 이 [문서](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)를 참조 하세요.<br/><br/> 
**소프트웨어/하드웨어** |  어플라이언스는 Windows Server 2016, 32GB RAM, 8개의 vCPU, 약 80GB의 디스크 스토리지 및 외부 가상 스위치가 있는 컴퓨터에서 실행해야 합니다.<br/> 어플라이언스는 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> VMware VM에서 어플라이언스를 실행하는 경우 요구 사항을 충족하는 VM을 할당하는 데 충분한 리소스가 vCenter Server에 있어야 합니다.<br/><br/> 물리적 컴퓨터에서 어플라이언스를 실행하는 경우 어플라이언스에서 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다.
**VMware 요구 사항** | 어플라이언스를 VMware VM으로 배포하는 경우 버전 5.5 이상을 실행하는 ESXi 호스트에 배포해야 합니다.<br/><br/> vCenter Server에서 5.5, 6.0, 6.5 또는 6.7을 실행합니다.
**VDDK(에이전트 없는 마이그레이션)** | 어플라이언스를 VMware VM으로 배포하고 에이전트 없는 마이그레이션을 실행하는 경우 VMware vSphere VDDK를 어플라이언스 VM에 설치해야 합니다.
**해시 값 - OVA** | OVA 템플릿 해시 값을 [확인](tutorial-assess-vmware.md#verify-security)합니다.
**해시 값 - PowerShell 스크립트** | PowerShell 스크립트 해시 값을 [확인](deploy-appliance-script.md#verify-file-security)합니다.




## <a name="appliance---hyper-v"></a>어플라이언스 - Hyper-V

**요구 사항** | **Hyper-V** 
--- | ---
**권한** | 어플라이언스 웹앱에 로컬 또는 원격으로 액세스하려면 어플라이언스 컴퓨터의 도메인 관리자 또는 로컬 관리자여야 합니다.
**어플라이언스 구성 요소** | 어플라이언스의 구성 요소는 다음과 같습니다.<br/><br/>- **관리 앱**: 어플라이언스를 배포하는 동안 사용자 입력을 위한 웹앱입니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/> - **검색 에이전트**: 에이전트가 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/>- **수집기 에이전트**: 에이전트는 성능 데이터를 수집 합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).
**지원되는 배포** | VHD 템플릿을 사용하여 Hyper-V VM으로 배포합니다.<br/><br/> PowerShell 설치 스크립트를 사용하여 Hyper-V VM 또는 물리적 컴퓨터로 배포합니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 최대 5,000개의 Hyper-V VM을 검색할 수 있습니다.<br/> 어플라이언스는 최대 300개의 Hyper-V 호스트에 연결할 수 있습니다.
**VHD 템플릿** | VHD를 포함하여 압축된 폴더입니다. 포털에서 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140422)에서 다운로드 합니다.<br/><br/> 다운로드 크기는 10.4 GB입니다.<br/><br/> 다운로드한 어플라이언스 템플릿에는 180일 동안 유효한 Windows Server 2016 평가 라이선스가 제공됩니다. 평가 기간이 곧 만료되는 경우 새 어플라이언스를 다운로드하여 배포하거나 어플라이언스 VM의 운영 체제 라이선스에 대한 정품 인증을 수행하는 것이 좋습니다.
**PowerShell 스크립트** | 이 [문서](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)를 참조 하세요.<br/><br/> 
**소프트웨어/하드웨어***   |  어플라이언스는 Windows Server 2016, 16gb RAM, 8 개의 vCPUs, 80 GB의 디스크 저장소 및 외부 가상 스위치를 사용 하 여 컴퓨터에서 실행 해야 합니다.<br/> 어플라이언스는 고정 또는 동적 IP 주소가 필요하며, 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> 어플라이언스를 Hyper-V VM으로 실행하는 경우 16GB RAM, 8개의 vCPU, 약 80GB의 스토리지 공간 및 어플라이언스 VM에 대한 외부 스위치를 할당하는 데 충분한 리소스가 Hyper-V 호스트에 있어야 합니다.<br/><br/> 물리적 컴퓨터에서 어플라이언스를 실행하는 경우 어플라이언스에서 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다. 
**Hyper-V 요구 사항** | VHD 템플릿을 사용하여 어플라이언스를 배포하는 경우 Azure Migrate에서 제공하는 어플라이언스 VM은 Hyper-V VM 버전 5.0입니다.<br/><br/> Hyper-V 호스트에서 Windows Server 2012 R2 이상을 실행해야 합니다. 
**해시 값 - VHD** | VHD 템플릿 해시 값을 [확인](tutorial-assess-hyper-v.md#verify-security)합니다.
**해시 값 - PowerShell 스크립트** | PowerShell 스크립트 해시 값을 [확인](deploy-appliance-script.md#verify-file-security)합니다.


## <a name="appliance---physical"></a>어플라이언스 - 물리적 서버

**요구 사항** | **물리적 서버** 
--- | ---
**권한** | 어플라이언스 웹앱에 로컬 또는 원격으로 액세스하려면 어플라이언스 컴퓨터의 도메인 관리자 또는 로컬 관리자여야 합니다.
**어플라이언스 구성 요소** | 어플라이언스의 구성 요소는 다음과 같습니다. <br/><br/> - **관리 앱**: 어플라이언스를 배포하는 동안 사용자 입력을 위한 웹앱입니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/> - **검색 에이전트**: 에이전트가 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/>- **수집기 에이전트**: 에이전트는 성능 데이터를 수집 합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.<br/>- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).
**지원되는 배포** | PowerShell 설치 스크립트를 사용하여 전용 물리적 컴퓨터 또는 VM으로 배포합니다. 스크립트는 포털에서 다운로드할 수 있습니다.
**프로젝트 지원** |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다. <br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 
**검색 제한** | 어플라이언스는 최대 1000 대의 물리적 서버를 검색할 수 있습니다.
**PowerShell 스크립트** | 포털에서 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140334)에서 zip 폴더의 스크립트 (AzureMigrateInstaller.ps1)를 다운로드 합니다. [자세히 알아보기](tutorial-assess-physical.md#set-up-the-azure-migrate-appliance).<br/><br/> 다운로드 크기는 85 MB입니다.
**소프트웨어/하드웨어** |  어플라이언스는 Windows Server 2016, 16gb RAM, 8 개의 vCPUs, 80 GB의 디스크 저장소 및 외부 가상 스위치를 사용 하 여 컴퓨터에서 실행 해야 합니다.<br/> 어플라이언스는 고정 또는 동적 IP 주소가 필요하며, 직접 또는 프록시를 통해 인터넷에 액세스해야 합니다.<br/><br/> 물리적 컴퓨터에서 어플라이언스를 실행하는 경우 어플라이언스에서 Windows Server 2016을 실행하고 하드웨어 요구 사항을 충족하는지 확인합니다.<br/> Windows Server 2019를 사용하는 컴퓨터에서 어플라이언스를 실행하는 것은 지원되지 않습니다.
**해시 값** | PowerShell 스크립트 해시 값을 [확인](deploy-appliance-script.md#verify-file-security)합니다.

## <a name="url-access"></a>URL 액세스

Azure Migrate 어플라이언스는 인터넷에 연결해야 합니다.

- 어플라이언스를 배포하는 경우 Azure Migrate에서 필요한 URL에 대한 연결을 확인합니다.
- 목록의 모든 URL에 대한 액세스를 허용해야 합니다. 평가만 수행하는 경우 VMware 에이전트 없는 마이그레이션에만 필요한 것으로 표시된 URL을 건너뛸 수 있습니다.
-  URL 기반 프록시를 사용하여 인터넷에 연결하는 경우 프록시에서 URL을 조회하는 동안 받은 모든 CNAME 레코드를 확인하는지 확인합니다.

### <a name="public-cloud-urls"></a>퍼블릭 클라우드 URL

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.com  | Azure Portal로 이동합니다.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 어플라이언스에서 Azure Migrate와 통신할 수 있도록 Azure AD(Active Directory) 앱을 만듭니다.
management.azure.com | 어플라이언스에서 Azure Migrate 서비스와 통신할 수 있도록 Azure AD 앱을 만듭니다.
*.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
*.vault.azure.net | Azure Key Vault에서 비밀을 관리합니다. 참고: 복제할 컴퓨터에서 이 URL에 액세스할 수 있는지 확인합니다.
aka.ms/* | aka 링크에 대한 액세스를 허용합니다. Azure Migrate 어플라이언스 업데이트에 사용됩니다.
download.microsoft.com/download | Microsoft 다운로드에서 다운로드할 수 있습니다.
\*.servicebus.windows.net | 어플라이언스와 Azure Migrate 서비스 간의 통신입니다.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure Migrate 서비스 URL에 연결합니다.
\*.hypervrecoverymanager.windowsazure.com | **VMware 에이전트 없는 마이그레이션에 사용**됩니다.<br/><br/> Azure Migrate 서비스 URL에 연결합니다.
\*.blob.core.windows.net |  **VMware 에이전트 없는 마이그레이션에 사용**됩니다.<br/><br/>마이그레이션을 위해 데이터를 스토리지에 업로드합니다.

### <a name="government-cloud-urls"></a>Government 클라우드 URL

**URL** | **세부 정보**  
--- | --- |
*.portal.azure.us  | Azure Portal로 이동합니다.
graph.windows.net | Azure 구독에 로그인합니다.
login.microsoftonline.us  | 어플라이언스에서 Azure Migrate와 통신할 수 있도록 Azure AD(Active Directory) 앱을 만듭니다.
management.usgovcloudapi.net | 어플라이언스에서 Azure Migrate 서비스와 통신할 수 있도록 Azure AD 앱을 만듭니다.
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
*.vault.usgovcloudapi.net | Azure Key Vault에서 비밀을 관리합니다.
aka.ms/* | aka 링크에 대한 액세스를 허용합니다. Azure Migrate 어플라이언스 업데이트에 사용됩니다.
download.microsoft.com/download | Microsoft 다운로드에서 다운로드할 수 있습니다.
*.servicebus.usgovcloudapi.net  | 어플라이언스와 Azure Migrate 서비스 간의 통신입니다.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Azure Migrate 서비스 URL에 연결합니다.
*. hypervrecoverymanager.windowsazure.us | **VMware 에이전트 없는 마이그레이션에 사용**됩니다.<br/><br/> Azure Migrate 서비스 URL에 연결합니다.
*.blob.core.usgovcloudapi.net  |  **VMware 에이전트 없는 마이그레이션에 사용**됩니다.<br/><br/>마이그레이션을 위해 데이터를 스토리지에 업로드합니다.
*.applicationinsights.us | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.





## <a name="collected-data---vmware"></a>수집되는 데이터 - VMware

어플라이언스는 메타데이터, 성능 데이터 및 종속성 분석 데이터를 수집합니다(에이전트 없는 [종속성 분석](concepts-dependency-visualization.md)이 사용되는 경우).

### <a name="metadata"></a>메타데이터

Azure Migrate 어플라이언스에서 검색된 메타데이터를 사용하면 컴퓨터와 앱을 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 컴퓨터와 앱으로 마이그레이션할 준비가 되었는지, 비용 계획을 파악하고 애플리케이션 종속성을 분석할 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

어플라이언스에서 수집하여 Azure에 보내는 VMware VM 메타데이터의 전체 목록은 다음과 같습니다.

**데이터** | **카운터**
--- | --- 
**컴퓨터 세부 정보** | 
VM ID | vm.Config.InstanceUuid 
VM 이름 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
VM 설명 | vm.Summary.Config.Annotation
라이선스 제품 이름 | vm.Client.ServiceContent.About.LicenseProductName
운영 체제 유형 | vm.SummaryConfig.GuestFullName
부팅 유형 | vm.Config.Firmware
코어 수 | vm.Config.Hardware.NumCPU
메모리(MB) | vm.Config.Hardware.MemoryMB
디스크 수 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
디스크 크기 목록 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
네트워크 어댑터 목록 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 사용률 | cpu.usage.average
메모리 사용률 |mem.usage.average
**디스크당 세부 정보** | 
디스크 키 값 | disk.Key
Dikunit 번호 | disk.UnitNumber
디스크 컨트롤러 키 값 | disk.ControllerKey.Value
프로비전된 기가바이트 | virtualDisk.DeviceInfo.Summary
디스크 이름 | disk.UnitNumber, disk.Key, disk.ControllerKey.VAlue를 사용하여 생성된 값
초당 읽기 작업 | virtualDisk.numberReadAveraged.average
초당 쓰기 작업 | virtualDisk.numberWriteAveraged.average
읽기 처리량(MB/초) | virtualDisk.read.average
쓰기 처리량(MB/초) | virtualDisk.write.average
**NIC당 세부 정보** | 
네트워크 어댑터 이름 | nic.Key
MAC 주소 | ((VirtualEthernetCard)nic).MacAddress
IPv4 주소 | vm.Guest.Net
IPv6 주소 | vm.Guest.Net
읽기 처리량(MB/초) | net.received.average
쓰기 처리량(MB/초) | net.transmitted.average
**인벤토리 경로 세부 정보** | 
속성 | container.GetType().Name
자식 개체 유형 | container.ChildType
참조 세부 정보 | container.MoRef
부모 세부 정보 | Container.Parent
VM당 폴더 세부 정보 | ((Folder)container).ChildEntity.Type
VM당 데이터 센터 정보 | ((Datacenter)container).VmFolder
호스트 폴더당 데이터 센터 정보 | ((Datacenter)container).HostFolder
호스트당 클러스터 세부 정보 | ((ClusterComputeResource)container).Host
VM당 호스트 세부 정보 | ((HostSystem)container).VM

### <a name="performance-data"></a>성능 데이터


어플라이언스에서 수집하여 Azure에 보내는 VMware VM 성능 데이터는 다음과 같습니다.

**Data** | **카운터** | **평가 영향**
--- | --- | ---
CPU 사용률 | cpu.usage.average | 추천 VM 크기/비용
메모리 사용률 | mem.usage.average | 추천 VM 크기/비용
디스크 읽기 처리량(MB/초) | virtualDisk.read.average | 디스크 크기, 스토리지 비용, VM 크기 계산
디스크 쓰기 처리량(MB/초) | virtualDisk.write.average | 디스크 크기, 스토리지 비용, VM 크기 계산
디스크 읽기 작업 수/초 | virtualDisk.numberReadAveraged.average | 디스크 크기, 스토리지 비용, VM 크기 계산
디스크 쓰기 작업 수/초 | virtualDisk.numberWriteAveraged.average  | 디스크 크기, 스토리지 비용, VM 크기 계산
NIC 읽기 처리량(MB/초) | net.received.average | VM 크기 계산
NIC 쓰기 처리량(MB/초) | net.transmitted.average  |VM 크기 계산


### <a name="installed-apps-metadata"></a>설치 된 앱 메타 데이터

응용 프로그램 검색은 설치 된 응용 프로그램 및 운영 체제 데이터를 수집 합니다.

#### <a name="windows-vm-apps-data"></a>Windows VM 앱 데이터

다음은 응용 프로그램 검색에 대해 사용 하도록 설정 된 각 VM에서 기기가 수집 하는 설치 된 응용 프로그램 데이터입니다. 이 데이터는 Azure에 보냅니다.

**Data** | **레지스트리 위치** | **Key**
--- | --- | ---
애플리케이션 이름  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
버전  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
공급자  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Windows VM 기능 데이터

다음은 응용 프로그램 검색에 대해 사용 하도록 설정 된 각 VM에서 기기가 수집 하는 기능 데이터입니다. 이 데이터는 Azure에 보냅니다.

**Data**  | **PowerShell cmdlet** | **속성**
--- | --- | ---
속성  | Get Add-windowsfeature  | 속성
기능 유형 | Get Add-windowsfeature  | FeatureType
Parent  | Get Add-windowsfeature  | Parent

#### <a name="windows-vm-sql-server-metadata"></a>Windows VM SQL Server 메타 데이터

응용 프로그램 검색에 사용 하도록 설정 된 Microsoft SQL server를 실행 하는 Vm에서 기기가 수집 하는 SQL server 메타 데이터는 다음과 같습니다. 이 데이터는 Azure에 보냅니다.

**Data**  | **레지스트리 위치**  | **Key**
--- | --- | ---
속성  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL 서버 \ 인스턴스 Names\SQL  | installedInstance
버전  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | 버전 
서비스 팩  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | SP
버전  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | 버전 

#### <a name="windows-vm-operating-system-data"></a>Windows VM 운영 체제 데이터

다음은 기기가 응용 프로그램 검색에 대해 사용 하도록 설정 된 각 VM을 수집 하는 운영 체제 데이터입니다. 이 데이터는 Azure에 보냅니다.

데이터  | WMI 클래스(WMI class)  | WMI 클래스 속성
--- | --- | ---
속성  | Win32_operatingsystem  | 캡션
버전  | Win32_operatingsystem  | 버전
아키텍처  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Linux VM 앱 데이터

다음은 응용 프로그램 검색에 대해 사용 하도록 설정 된 각 VM에서 기기가 수집 하는 설치 된 응용 프로그램 데이터입니다. VM의 운영 체제에 따라 하나 이상의 명령이 실행 됩니다. 이 데이터는 Azure에 보냅니다.

데이터  | 명령
--- | --- 
속성 | rpm, dpkg, snap
버전 | rpm, dpkg, snap
공급자 | rpm, dpkg, snap

#### <a name="linux-vm-operating-system-data"></a>Linux VM 운영 체제 데이터

다음은 기기가 응용 프로그램 검색에 대해 사용 하도록 설정 된 각 VM을 수집 하는 운영 체제 데이터입니다. 이 데이터는 Azure에 보냅니다.

**Data**  | **명령** 
--- | --- | ---
속성 <br/> 버전 | 다음 파일 중 하나 이상에서 수집 됩니다.<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
아키텍처 | uname


### <a name="app-dependencies-metadata"></a>앱 종속성 메타데이터

에이전트 없는 종속성 분석은 연결 및 프로세스 데이터를 수집합니다.

#### <a name="windows-vm-app-dependencies-data"></a>Windows VM 응용 프로그램 종속성 데이터

어플라이언스에서 에이전트 없는 종속성 분석에 사용하도록 설정된 각 VM으로부터 수집하는 연결 데이터는 다음과 같습니다. 이 데이터는 Azure에 보냅니다.

**Data** | **사용되는 명령** 
--- | --- 
로컬 포트 | netstat
로컬 IP 주소 | netstat
원격 포트 | netstat
원격 IP 주소 | netstat
TCP 연결 상태 | netstat
프로세스 ID | netstat
활성 연결 수 | netstat


어플라이언스에서 에이전트 없는 종속성 분석에 사용하도록 설정된 각 VM으로부터 수집하는 프로세스 데이터는 다음과 같습니다. 이 데이터는 Azure에 보냅니다.

**Data** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
프로세스 이름 | Win32_Process | ExecutablePath
프로세스 인수 | Win32_Process | 명령줄
애플리케이션 이름 | Win32_Process | ExecutablePath 속성의 VersionInfo.ProductName 매개 변수

#### <a name="linux-vm-app-dependencies-data"></a>Linux VM 앱 종속성 데이터

어플라이언스에서 에이전트 없는 종속성 분석에 사용하도록 설정된 각 Linux VM으로부터 수집하는 연결 및 프로세스 데이터는 다음과 같습니다. 이 데이터는 Azure에 보냅니다.

**Data** | **사용되는 명령** 
--- | ---
로컬 포트 | netstat 
로컬 IP 주소 | netstat 
원격 포트 | netstat 
원격 IP 주소 | netstat 
TCP 연결 상태 | netstat 
활성 연결 수 | netstat
프로세스 ID  | netstat 
프로세스 이름 | ps
프로세스 인수 | ps
애플리케이션 이름 | dpkg 또는 rpm



## <a name="collected-data---hyper-v"></a>수집되는 데이터 - Hyper-V

어플라이언스는 메타데이터, 성능 데이터 및 종속성 분석 데이터를 수집합니다(에이전트 없는 [종속성 분석](concepts-dependency-visualization.md)이 사용되는 경우).

### <a name="metadata"></a>메타데이터
Azure Migrate 어플라이언스에서 검색된 메타데이터를 사용하면 컴퓨터와 앱을 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 컴퓨터와 앱으로 마이그레이션할 준비가 되었는지, 비용 계획을 파악하고 애플리케이션 종속성을 분석할 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

어플라이언스에서 수집하여 Azure에 보내는 Hyper-V VM 메타데이터의 전체 목록은 다음과 같습니다.

**데이터** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
**컴퓨터 세부 정보** | 
BIOS _ Msvm_BIOSElement의 일련 번호 | BIOSSerialNumber
VM 유형(Gen 1 또는 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 표시 이름 | Msvm_VirtualSystemSettingData | ElementName
VM 버전 | Msvm_ProcessorSettingData | VirtualQuantity
메모리(바이트) | Msvm_MemorySettingData | VirtualQuantity
VM에서 사용할 수 있는 최대 메모리 | Msvm_MemorySettingData | 제한
동적 메모리 사용 | Msvm_MemorySettingData | DynamicMemoryEnabled
운영 체제 이름/버전/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 이름 데이터
VM 전원 상태 | Msvm_ComputerSystem | EnabledState
**디스크당 세부 정보** | 
디스크 식별자 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
가상 하드 디스크 유형 | Msvm_VirtualHardDiskSettingData | Type
가상 하드 디스크 크기 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
가상 하드 디스크 부모 | Msvm_VirtualHardDiskSettingData | ParentPath
**NIC당 세부 정보** | 
IP 주소(가상 NIC) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 사용(가상 NIC) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID(가상 NIC) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 주소(가상 NIC) | Msvm_SyntheticEthernetPortSettingData | 주소
NIC ID(레거시 NIC) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC MAC ID(레거시 NIC) | MsvmEmulatedEthernetPortSetting Data | 주소

### <a name="performance-data"></a>성능 데이터

어플라이언스에서 수집하여 Azure에 보내는 Hyper-V VM 성능 데이터는 다음과 같습니다.

**성능 카운터 클래스** | **카운터** | **평가 영향**
--- | --- | ---
Hyper-V 하이퍼바이저 가상 프로세서 | % 게스트 실행 시간 | 추천 VM 크기/비용
Hyper-V 동적 메모리 VM | 현재 압력(%)<br/> 게스트 가시 실제 메모리(MB) | 추천 VM 크기/비용
Hyper-V 가상 스토리지 디바이스 | 읽기 바이트 수/초 | 디스크 크기, 스토리지 비용, VM 크기 계산
Hyper-V 가상 스토리지 디바이스 | 쓰기 바이트 수/초 | 디스크 크기, 스토리지 비용, VM 크기 계산
Hyper-V 가상 네트워크 어댑터 | 받은 바이트 수/초 | VM 크기 계산
Hyper-V 가상 네트워크 어댑터 | 보낸 바이트 수/초 | VM 크기 계산

- CPU 사용률은 VM에 연결된 모든 가상 프로세서에 대한 모든 사용량의 합계입니다.
- 메모리 사용률은 (현재 압력 * 게스트 가시 실제 메모리) / 100입니다.
- 디스크 및 네트워크 사용률 값은 나열된 Hyper-V 성능 카운터에서 수집됩니다.


## <a name="collected-data---physical"></a>수집되는 데이터 - 물리적 서버

어플라이언스는 메타데이터, 성능 데이터 및 종속성 분석 데이터를 수집합니다(에이전트 없는 [종속성 분석](concepts-dependency-visualization.md)이 사용되는 경우).

### <a name="windows-metadata"></a>Windows 메타데이터

Azure Migrate 어플라이언스에서 검색된 메타데이터를 사용하면 컴퓨터와 앱을 Azure로 마이그레이션할 준비가 되었는지, 적절한 크기의 컴퓨터와 앱으로 마이그레이션할 준비가 되었는지, 비용 계획을 파악하고 애플리케이션 종속성을 분석할 수 있습니다. Microsoft는 라이선스 규정 준수 감사에서 이 데이터를 사용하지 않습니다.

어플라이언스에서 수집하여 Azure에 보내는 Windows 서버 메타데이터의 전체 목록은 다음과 같습니다.

**데이터** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain, Name, PartOfDomain
프로세서 코어 수 | Win32_PRocessor | NumberOfCores
할당된 메모리 | Win32_ComputerSystem | TotalPhysicalMemory
BIOS 일련 번호 | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS GUID | Win32_ComputerSystemProduct | UUID
부팅 유형 | Win32_DiskPartition | 파티션에서 EFI/BIOS에 대해 Type = **GPT:System**인지 확인
OS 이름 | Win32_OperatingSystem | 캡션
OS 버전 |Win32_OperatingSystem | 버전
OS 아키텍처 | Win32_OperatingSystem | OSArchitecture
디스크 수 | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
디스크 크기 | Win32_DiskDrive | 크기
NIC 목록 | Win32_NetworkAdapterConfiguration | Description, Index
NIC IP 주소 | Win32_NetworkAdapterConfiguration | IPAddress
NIC MAC 주소 | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux 메타데이터

어플라이언스에서 수집하여 Azure에 보내는 Linux 서버 메타데이터의 전체 목록은 다음과 같습니다.

**데이터** | **Linux** 
--- | --- 
FQDN | cat /proc/sys/kernel/hostname, hostname -f
프로세서 코어 수 |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
할당된 메모리 | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
BIOS 일련 번호 | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
부팅 유형 | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
OS 이름/버전 | OS 버전 및 이름에 대해 다음 파일에 액세스합니다.<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
OS 아키텍처 | Uname -m
디스크 수 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
부팅 디스크 | df /boot \| sed -n 2p \| awk '{print $1}'
디스크 크기 | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC 목록 | ip -o -4 addr show \| awk '{print $2}'
NIC IP 주소 | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC MAC 주소 | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows 성능 데이터

어플라이언스에서 수집하여 Azure에 보내는 Windows 서버 성능 데이터는 다음과 같습니다.

**Data** | **WMI 클래스** | **WMI 클래스 속성**
--- | --- | ---
CPU 사용량 | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
메모리 사용량 | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC 수 | Win32_PerfFormattedData_Tcpip_NetworkInterface | 네트워크 디바이스 수를 가져옵니다.
NIC당 받은 데이터 | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
NIC당 전송된 데이터 | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
디스크 수 | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | 디스크 수
디스크 세부 정보 | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec

### <a name="linux-performance-data"></a>Linux 성능 도구

어플라이언스에서 수집하여 Azure에 보내는 Linux 서버 성능 데이터는 다음과 같습니다.

**Data** | **Linux** 
--- | --- 
CPU 사용량 | cat /proc/stat/| grep 'cpu' /proc/stat
메모리 사용량 | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
NIC 수 | lshw -class network \| grep eth[0-60] \| wc -l
NIC당 받은 데이터 | cat /sys/class/net/eth$nic/statistics/rx_bytes
NIC당 전송된 데이터 | cat /sys/class/net/eth$nic/statistics/tx_bytes
디스크 수 | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
디스크 세부 정보 | cat /proc/diskstats


## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스에서 실행되는 Azure Migrate 에이전트가 업데이트되면 어플라이언스가 업그레이드됩니다. 이는 어플라이언스에서 기본적으로 자동 업데이트를 사용하도록 설정되어 있으므로 자동으로 수행됩니다. 이 기본 설정을 변경하여 어플라이언스 서비스를 수동으로 업데이트할 수 있습니다.

### <a name="turn-off-auto-update"></a>자동 업데이트 해제

1. 어플라이언스를 실행하는 컴퓨터에서 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**로 이동합니다.
3. 자동 업데이트를 해제하려면 DWORD 값이 0인 **AutoUpdate** 레지스트리 키를 만듭니다.

    ![레지스트리 키 설정](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>자동 업데이트 설정

다음 방법 중 하나를 사용하여 자동 업데이트를 설정할 수 있습니다.

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance에서 AutoUpdate 레지스트리 키를 삭제합니다.
- 자동 업데이트를 설정 하려면 **필수 구성 요소** 패널의 최신 업데이트 검사에서 **어플라이언스 서비스 보기** 를 클릭 합니다.

레지스트리 키를 삭제하려면 다음을 수행합니다.

1. 어플라이언스를 실행하는 컴퓨터에서 레지스트리 편집기를 엽니다.
2. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**로 이동합니다.
3. 자동 업데이트를 해제하기 위해 이전에 만든 **AutoUpdate** 레지스트리 키를 삭제합니다.

검색이 완료된 후 Appliance Configuration Manager에서 설정하려면 다음을 수행합니다.

1. 어플라이언스 구성 관리자에서 **필수 구성 요소 패널 설정** 으로 이동 합니다.
2. 최신 업데이트 확인에서 **어플라이언스 서비스 보기** 를 클릭 하 고 링크를 클릭 하 여 자동 업데이트를 설정 합니다.

    ![자동 업데이트 설정](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>어플라이언스 서비스 버전 확인

다음 방법 중 하나를 사용하여 어플라이언스 서비스 버전을 확인할 수 있습니다.

- 어플라이언스 구성 관리자에서 **필수 구성 요소 패널 설정** 으로 이동 합니다.
- 어플라이언스 컴퓨터의 **제어판** > **프로그램 및 기능**에서

어플라이언스 구성 관리자를 체크 인하려면:

1. 어플라이언스 구성 관리자에서 **필수 구성 요소 패널 설정** 으로 이동 합니다.
2. 최신 업데이트 확인에서 **어플라이언스 서비스 보기**를 클릭 합니다.

    ![버전 확인](./media/migrate-appliance/versions.png)

제어판에서 확인하려면 다음을 수행합니다.

1. 어플라이언스에서 **시작** > **제어판** > **프로그램 및 기능**을 차례로 클릭합니다.
2. 목록에서 어플라이언스 서비스 버전을 확인합니다.

    ![제어판에서 버전 확인](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>이전 버전을 수동으로 업데이트

구성 요소 중 하나에 대해 이전 버전을 실행하고 있는 경우 서비스를 제거하고 최신 버전으로 수동으로 업데이트해야 합니다.

1. 최신 어플라이언스 서비스 버전을 확인하려면 LatestComponents.json 파일을 [다운로드](https://aka.ms/latestapplianceservices)합니다.
2.    다운로드되면 메모장에서 LatestComponents.json 파일을 엽니다.
3. 파일에서 최신 서비스 버전 및 해당 다운로드 링크를 찾습니다. 다음은 그 예입니다.

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    파일의 다운로드 링크를 사용하여 오래된 서비스의 최신 버전을 다운로드합니다.
5. 다운로드되면 관리자 명령 창에서 다음 명령을 실행하여 다운로드한 MSI의 무결성을 확인합니다.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` 예:  C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. 명령 출력이 파일의 서비스에 대한 해시 값 항목(예: 위의 MD5 해시 값)과 일치하는지 확인합니다.
6. 이제 MSI를 실행하여 서비스를 설치합니다. 자동 설치이며, 완료되면 설치 창이 닫힙니다.
7. 설치가 완료되면 **제어판** > **프로그램 및 기능**에서 서비스 버전을 확인합니다. 이제 서비스 버전을 json 파일에 표시된 최신 버전으로 업그레이드해야 합니다.



## <a name="next-steps"></a>다음 단계

- [VMware용 어플라이언스를 설정하는 방법](how-to-set-up-appliance-vmware.md)에 대해 알아봅니다.
- [Hyper-V용 어플라이언스를 설정하는 방법](how-to-set-up-appliance-hyper-v.md)에 대해 알아봅니다.
- [물리적 서버용 어플라이언스를 설정하는 방법](how-to-set-up-appliance-physical.md)에 대해 알아봅니다.

