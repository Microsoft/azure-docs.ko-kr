---
title: Azure Migrate를 사용하여 마이그레이션을 위한 머신 준비
description: Azure Migrate를 사용하여 마이그레이션하기 위한 온-프레미스 머신을 준비하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: MVC
ms.openlocfilehash: 8acbb867d98a547787e207c410d4e1a852aa68f3
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606827"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Azure로 마이그레이션하기 위한 온-프레미스 머신 준비

이 문서에서는 [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용하여 온-프레미스 머신을 Azure로 마이그레이션하려면 먼저 어떤 준비 과정을 거쳐야 하는지 설명합니다.

이 문서에서는 다음 작업을 수행합니다.
> [!div class="checklist"]
> * 마이그레이션 제한 사항을 검토합니다.
> * VMware VM을 마이그레이션하기 위한 방법을 선택합니다.
> * 마이그레이션하려는 머신의 하이퍼바이저 및 운영 체제 요구 사항을 확인합니다.
> * 마이그레이션하려는 머신에 대한 URL 및 포트 액세스 검토를 사용하여 온-프레미스 머신을 준비하는 방법을 설명합니다.
> * 마이그레이션을 시작하기 전에 수행해야 할 변경을 검토합니다.
> * 마이그레이션된 머신의 Azure VM 요구 사항을 확인합니다.
> * 마이그레이션 후 Azure VM에 연결할 수 있도록 머신을 준비합니다.



## <a name="verify-migration-limitations"></a>마이그레이션 제한 사항 확인

다음 표에는 Azure Migrate의 검색, 평가 및 마이그레이션 제한 사항이 요약되어 있습니다. 마이그레이션하기 전에 머신을 평가하는 것이 좋지만, 반드시 평가해야 하는 것은 아닙니다.

**시나리오** | **프로젝트** | **검색/평가** | **마이그레이션**
--- | --- | --- | ---
**VMware VM** | 단일 Azure Migrate 프로젝트에서 최대 35,000개의 VM을 검색하고 평가할 수 있습니다. | VMware용 [Azure Migrate 어플라이언스](common-questions-appliance.md) 하나로 VMware VM을 10,000개까지 검색할 수 있습니다. | **에이전트 없는 마이그레이션**: 최대 300개 VM을 동시에 복제할 수 있습니다. 최상의 성능을 내려면 VM 수가 50개를 초과하는 경우 VM 일괄 처리를 여러 개 만드는 것이 좋습니다.<br/><br/> **에이전트 기반 마이그레이션**: [복제 어플라이언스](migrate-replication-appliance.md)를 [스케일 아웃](./agent-based-migration-architecture.md#performance-and-scaling)하여 VM을 대량으로 복제할 수 있습니다.<br/><br/> 포털에서 복제할 머신을 한 번에 10대까지 선택할 수 있습니다. 더 많은 머신을 복제하려면 10대 일괄 처리를 추가합니다.
**Hyper-V VM** | 단일 Azure Migrate 프로젝트에서 최대 35,000개의 VM을 검색하고 평가할 수 있습니다. | Azure Migrate 어플라이언스 하나로 Hyper-V VM을 5,000개까지 검색할 수 있습니다. | 어플라이언스는 Hyper-V 마이그레이션에 사용되지 않습니다. 대신 Hyper-V 복제 공급자는 각 Hyper-V 호스트에서 실행됩니다.<br/><br/> 복제 용량은 VM 변동, 복제 데이터의 업로드 대역폭 등의 성능 요인에 의해 영향을 받습니다.<br/><br/> 포털에서 복제할 머신을 한 번에 10대까지 선택할 수 있습니다. 더 많은 머신을 복제하려면 10대 일괄 처리를 추가합니다.
**물리적 컴퓨터** | 단일 Azure Migrate 프로젝트에서 최대 35,000개의 머신을 검색하고 평가할 수 있습니다. | 물리적 서버용 Azure Migrate 어플라이언스 하나로 최대 250대의 물리적 서버를 검색할 수 있습니다. | [복제 어플라이언스](migrate-replication-appliance.md)를 [스케일 아웃](./agent-based-migration-architecture.md#performance-and-scaling)하여 서버를 대량으로 복제할 수 있습니다.<br/><br/> 포털에서 복제할 머신을 한 번에 10대까지 선택할 수 있습니다. 더 많은 머신을 복제하려면 10대 일괄 처리를 추가합니다.

## <a name="select-a-vmware-migration-method"></a>VMware 마이그레이션 방법 선택

VMware VM을 Azure로 마이그레이션하는 경우 에이전트 없는 마이그레이션과 에이전트 기반 마이그레이션 방법을 [비교](server-migrate-overview.md#compare-migration-methods)하여 어떤 방법이 적합한지 결정합니다.

## <a name="verify-hypervisor-requirements"></a>하이퍼바이저 요구 사항 확인

- [VMware 에이전트 없는 마이그레이션](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) 또는 [VMware 에이전트 기반 마이그레이션](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) 요구 사항을 확인합니다.
- [Hyper-V 호스트](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) 요구 사항을 확인합니다.


## <a name="verify-operating-system-requirements"></a>운영 체제 요구 사항 확인

마이그레이션을 지원하는 운영 체제를 확인합니다.

- VMware VM 또는 Hyper-V VM을 마이그레이션하는 경우 [에이전트 없는](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) 마이그레이션 및 [에이전트 기반](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) 마이그레이션의 VMware VM 요구 사항과 [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) 요구 사항을 확인합니다.
- Azure에서 [Windows 운영 체제](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)가 지원되는지 확인합니다.
- Azure에서 [Linux 배포판](../virtual-machines/linux/endorsed-distros.md)이 지원되는지 확인합니다.

## <a name="review-url-and-port-access"></a>URL 및 포트 액세스 검토

마이그레이션 중에 액세스되는 URL과 포트를 검토합니다.

**시나리오** | **세부 정보** |  **URL** | **Ports**
--- | --- | --- | ---
**VMware 에이전트 없는 마이그레이션** | [Azure Migrate 어플라이언스](migrate-appliance-architecture.md)를 마이그레이션에 사용합니다. VMware VM에 아무것도 설치되지 않습니다. | 어플라이언스로 검색, 평가 및 마이그레이션하는 데 필요한 퍼블릭 클라우드 및 정부 [URL](migrate-appliance.md#url-access)을 검토합니다. | 에이전트 없는 마이그레이션의 포트 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)합니다.
**VMware 에이전트 기반 마이그레이션** | [복제 어플라이언스](migrate-replication-appliance.md)를 마이그레이션에 사용합니다. VM에 Mobility Service 에이전트가 설치됩니다. | 복제 어플라이언스에서 액세스해야 하는 [퍼블릭 클라우드](migrate-replication-appliance.md#url-access) 및 [Azure Government](migrate-replication-appliance.md#azure-government-url-access) URL을 검토합니다. | 에이전트 기반 마이그레이션에 사용되는 포트를 [검토](migrate-replication-appliance.md#port-access)합니다.
**Hyper-V 마이그레이션** | Hyper-V 호스트에 설치된 공급자를 마이그레이션에 사용합니다. Hyper-V VM에 아무것도 설치되지 않습니다. | 호스트에서 실행되는 복제 공급자가 액세스해야 하는 [퍼블릭 클라우드](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) 및 [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) URL을 검토합니다. | Hyper-V 호스트의 복제 공급자는 HTTPS 포트 443에서 아웃바운드 연결을 사용하여 VM 복제 데이터를 보냅니다.
**물리적 컴퓨터** | [복제 어플라이언스](migrate-replication-appliance.md)를 마이그레이션에 사용합니다. 물리적 머신에 Mobility Service 에이전트가 설치됩니다. | 복제 어플라이언스에서 액세스해야 하는 [퍼블릭 클라우드](migrate-replication-appliance.md#url-access) 및 [Azure Government](migrate-replication-appliance.md#azure-government-url-access) URL을 검토합니다. | 물리적 마이그레이션에 사용되는 포트를 [검토](migrate-replication-appliance.md#port-access)합니다.

## <a name="verify-required-changes-before-migrating"></a>마이그레이션 전 필요한 변경 확인

VM을 Azure로 마이그레이션하기 전에 VM에서 몇 가지 사항을 변경해야 합니다.

- 일부 운영 체제에서는 Azure Migrate가 복제/마이그레이션 프로세스 중에 자동으로 변경됩니다.
- 그 외의 운영 체제에서는 설정을 수동으로 구성해야 합니다.
- 마이그레이션을 시작하기 전에 설정을 수동으로 구성해야 합니다. 변경하기 전에 VM을 마이그레이션하면 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.

표를 검토하여 변경해야 하는 내용을 확인합니다.

### <a name="windows-machines"></a>Windows 컴퓨터

다음 표에는 변경할 내용이 요약되어 있습니다.

**동작** | **VMware(에이전트 없는 마이그레이션)** | **VMware(에이전트 기반)/물리적 머신** | **Hyper-V의 Windows** 
--- | --- | --- | ---
**SAN 정책을 [모두 온라인]으로 구성**<br/><br/> 이렇게 하면 Azure VM의 Windows 볼륨에 온-프레미스 VM과 동일한 드라이브 문자가 할당됩니다. | Windows Server 2008 R2 이상을 실행하는 머신은 자동으로 설정됩니다.<br/><br/> 이전 운영 체제는 수동으로 구성해야 합니다. | 대부분의 경우 자동으로 설정됩니다. | 수동으로 구성합니다.
**Hyper-V 게스트 통합 설치** | Windows Server 2003을 실행하는 머신에 [수동으로 설치](prepare-windows-server-2003-migration.md#install-on-vmware-vms)합니다. | Windows Server 2003을 실행하는 머신에 [수동으로 설치](prepare-windows-server-2003-migration.md#install-on-vmware-vms)합니다. | Windows Server 2003을 실행하는 머신에 [수동으로 설치](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms)합니다.
**Azure 직렬 콘솔을 사용하도록 설정합니다**.<br/><br/>문제 해결에 도움이 되도록 Azure VM에서 [콘솔을 사용하도록 설정](../virtual-machines/troubleshooting/serial-console-windows.md)합니다. VM을 다시 부팅할 필요가 없습니다. Azure VM은 디스크 이미지를 사용하여 부팅됩니다. 디스크 이미지 부팅은 새 VM을 다시 부팅하는 것과 같습니다. | 수동으로 사용하도록 설정 | 수동으로 사용하도록 설정 | 수동으로 사용하도록 설정
**마이그레이션 후 연결**<br/><br/> 마이그레이션 후 연결하려면 마이그레이션하기 전에 여러 단계를 수행해야 합니다. | 수동으로 [설정](#prepare-to-connect-to-azure-windows-vms)합니다. | 수동으로 [설정](#prepare-to-connect-to-azure-windows-vms)합니다. | 수동으로 [설정](#prepare-to-connect-to-azure-windows-vms)합니다.


#### <a name="configure-san-policy"></a>SAN 정책 구성

Azure VM에는 기본적으로 임시 스토리지로 사용할 D 드라이브가 할당됩니다.

- 이 드라이브 할당으로 인해 다른 모든 스토리지 드라이브 할당이 한 문자씩 증가합니다.
- 예를 들어 온-프레미스 설치에서 애플리케이션 설치를 위해 D 드라이브에 할당된 데이터 디스크를 사용하는 경우 VM을 Azure로 마이그레이션하면 이 드라이브에 대한 할당이 E 드라이브로 증가합니다. 
- 이 자동 할당을 방지하고 Azure에서 사용 가능한 다음 드라이브 문자를 임시 볼륨에 할당하도록 하려면 SAN(저장 영역 네트워크) 정책을 **OnlineAll**로 설정합니다.

이 설정을 다음과 같이 수동으로 구성합니다.

1. 온-프레미스 머신(호스트 서버가 아님)에서 관리자 권한 명령 프롬프트를 엽니다.
2. **diskpart**를 입력합니다.
3. **SAN**을 입력합니다. 게스트 운영 체제의 드라이브 문자가 유지되지 않으면 **모두 오프라인** 또는 **공유 디스크만 오프라인**이 반환됩니다.
4. **DISKPART** 프롬프트에서 **SAN Policy=OnlineAll**을 입력합니다. 이 설정을 사용하면 디스크가 온라인 상태가 되고 두 디스크를 모두 읽고 쓸 수 있습니다.
5. 테스트 마이그레이션 중에 드라이브 문자가 유지되는지 확인할 수 있습니다.


### <a name="linux-machines"></a>Linux 컴퓨터

다음 버전은 Azure Migrate에서 이러한 작업을 자동으로 완료합니다.

- Red Hat Enterprise Linux 7.0 이상, 6.5 이상
- CentOS 7.0 이상, 6.5 이상
- SUSE Linux Enterprise Server 12 SP1 이상
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

그 외의 버전은 표에 요약된 대로 머신을 준비합니다.  


**동작** | **세부 정보** | **Linux 버전**
--- | --- | ---
**Hyper-V Linux Integration Services 설치** | 필요한 Hyper-V 드라이버가 포함되도록 Linux 초기화 이미지를 다시 빌드합니다. 초기화 이미지가 다시 빌드하면 VM이 Azure에서 부팅됩니다. | 이 기능은 대부분의 새 Linux 배포판 버전에 기본적으로 포함되어 있습니다.<br/><br/> 포함되지 않은 경우 위에서 설명한 버전을 제외한 모든 버전에서는 수동으로 설치합니다.
**Azure 직렬 콘솔 로깅을 사용하도록 설정** | 콘솔 로깅을 사용하도록 설정하면 문제를 해결할 수 있습니다. VM을 다시 부팅할 필요가 없습니다. Azure VM은 디스크 이미지를 사용하여 부팅됩니다. 디스크 이미지 부팅은 새 VM을 다시 부팅하는 것과 같습니다.<br/><br/> [이 지침](../virtual-machines/troubleshooting/serial-console-linux.md)에 따라 사용하도록 설정합니다.
**디바이스 맵 파일 업데이트** | 영구 디바이스 식별자를 사용하도록 디바이스 맵 파일을 디바이스 이름-볼륨 연결로 업데이트합니다. | 위에서 설명한 버전을 제외한 모든 버전에서는 수동으로 설치합니다. (에이전트 기반 VMware 시나리오에만 해당)
**fstab 항목 업데이트** |  영구 볼륨 식별자를 사용하도록 항목을 업데이트합니다.    | 위에서 설명한 버전을 제외한 모든 버전에서는 수동으로 업데이트합니다.
**udev 규칙 제거** | mac 주소 등에 기반한 인터페이스 이름을 예약하는 모든 udev 규칙을 제거합니다. | 위에서 설명한 버전을 제외한 모든 버전에서는 수동으로 제거합니다.
**네트워크 인터페이스 업데이트** | DHCP.nst 기반의 IP 주소를 받도록 네트워크 인터페이스를 업데이트합니다. | 위에서 설명한 버전을 제외한 모든 버전에서는 수동으로 업데이트합니다.
**ssh 사용** | ssh를 사용하도록 설정되고 다시 부팅 시 sshd 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.<br/><br/> 들어오는 ssh 연결 요청이 OS 방화벽 또는 스크립팅 가능한 규칙으로 차단되지 않도록 합니다.| 위에서 설명한 버전을 제외한 모든 버전에서는 수동으로 설정합니다.

다음 표에는 위에 나열된 운영 체제에 대해 자동으로 수행되는 단계가 요약되어 있습니다.


| 작업                                      | 에이전트\-기반 VMware 마이그레이션 | 에이전트 없는 VMware 마이그레이션 | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Hyper\-V Linux Integration Services 설치 | yes                           | 예                        | 필요하지 않음 |
| Azure 직렬 콘솔 로깅 사용         | yes                           | 예                        | 예         |
| 디바이스 맵 파일 업데이트                      | 예                           | 아니요                         | 아니요         |
| fstab 항목 업데이트                        | yes                           | 예                        | 예         |
| udev 규칙 제거                            | yes                           | 예                        | 예         |
| 네트워크 인터페이스 업데이트                   | yes                           | 예                        | 예         |
| ssh 사용                                  | 아니요                            | 아니요                         | 아니요         |

[Azure에서 Linux VM 실행](../virtual-machines/linux/create-upload-generic.md) 단계에 대해 자세히 알아보고, 많이 사용되는 Linux 배포판에 대한 지침을 확인합니다.


## <a name="check-azure-vm-requirements"></a>Azure VM 요구 사항 확인

Azure에 복제하는 온-프레미스 머신은 운영 체제와 아키텍처, 디스크, 네트워크 설정 및 VM 명명에 대한 Azure VM 요구 사항을 준수해야 합니다.

마이그레이션하기 전에 [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) 및 [물리적 서버](migrate-support-matrix-physical-migration.md#azure-vm-requirements) 마이그레이션을 위한 Azure VM 요구 사항을 검토합니다.



## <a name="prepare-to-connect-after-migration"></a>마이그레이션 후 연결 준비

Azure VM은 Azure로 마이그레이션하는 동안 만들어집니다. 마이그레이션 후에는 새 Azure VM에 연결할 수 있어야 합니다. 성공적으로 연결하려면 여러 단계를 수행해야 합니다.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Azure Windows VM에 대한 연결 준비

온-프레미스 Windows 머신의 경우:

1. Windows 설정을 구성합니다. 설정에는 정적 영구 경로 또는 WinHTTP 프록시를 제거하는 작업이 포함됩니다.
2. [필요한 서비스](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)가 실행되고 있는지 확인합니다.
3. 원격 데스크톱(RDP)을 사용하도록 설정하여 온-프레미스 머신에 대한 원격 연결을 허용합니다. [PowerShell을 통해 RDP를 사용하도록 설정](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)하는 방법에 대해 알아보세요.
4. 마이그레이션 후 인터넷을 통해 Azure VM에 액세스하려면 온-프레미스 머신의 Windows Firewall에서 퍼블릭 프로필의 TCP 및 UDP를 허용하고 RDP를 모든 프로필에 대해 허용되는 앱으로 설정합니다.
5. 마이그레이션 후 사이트 간 VPN을 통해 Azure VM에 액세스하려면 온-프레미스 머신의 Windows Firewall에서 도메인 및 프라이빗 프로필에 대해 RDP를 허용합니다. [RDP 트래픽을 허용](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)하는 방법에 대해 알아보세요. 
6. 마이그레이션할 때 온-프레미스 VM에서 보류 중인 Windows 업데이트가 없는지 확인합니다. 업데이트가 있는 경우 마이그레이션 후에 해당 업데이트를 Azure VM에 설치할 수 있으며, 업데이트가 완료될 때까지 VM에 로그인할 수 없습니다.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Linux Azure VM에 대한 연결 준비

온-프레미스 Linux 머신의 경우:

1. 시스템 부팅 시 Secure Shell 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.
2. 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.

### <a name="configure-azure-vms-after-migration"></a>마이그레이션 후 Azure VM 구성

마이그레이션 후 만들어진 Azure VM에서 다음 단계를 완료합니다.

1. 인터넷을 통해 VM에 연결하려면 공용 IP 주소를 VM에 할당합니다. 온-프레미스 머신에 사용한 것과 다른 공용 IP 주소를 Azure VM에 사용해야 합니다. [자세히 알아보기](../virtual-network/virtual-network-public-ip-address.md).
2. VM의 NSG(네트워크 보안 그룹) 규칙에서 RDP 또는 SSH 포트로 들어오는 연결을 허용하는지 확인합니다.
3. [부트 진단](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine)을 확인하여 VM을 살펴봅니다.


## <a name="next-steps"></a>다음 단계

[VMware VM을 Azure로 마이그레이션](server-migrate-overview.md)하는 데 사용하려는 방법을 결정하거나, [Hyper-V VM](tutorial-migrate-hyper-v.md), [물리적 서버, 가상화된 VM 또는 클라우드 VM](tutorial-migrate-physical-virtual-machines.md)의 마이그레이션을 시작합니다.

## <a name="see-whats-supported"></a>지원되는 기능 보기

VMware VM의 경우 서버 마이그레이션은 [에이전트 없는 또는 에이전트 기반 마이그레이션](server-migrate-overview.md)을 지원합니다.

- **VMware VM**: VMware VM에 대한 [마이그레이션 요구 사항 및 지원](migrate-support-matrix-vmware-migration.md)을 확인합니다.
- **Hyper-V VM**: Hyper-V VM에 대한 [마이그레이션 요구 사항 및 지원](migrate-support-matrix-hyper-v-migration.md)을 확인합니다.
- **물리적 머신**: 온-프레미스 물리적 머신 및 다른 가상화된 서버에 대한 [마이그레이션 요구 사항 및 지원](migrate-support-matrix-physical-migration.md)을 확인합니다. 
