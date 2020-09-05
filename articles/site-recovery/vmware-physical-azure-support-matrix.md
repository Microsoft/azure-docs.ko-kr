---
title: Azure Site Recovery의 VMware/물리적 재해 복구를 위한 지원 매트릭스
description: Azure Site Recovery를 사용 하 여 VMware Vm 및 물리적 서버에서 Azure로의 재해 복구에 대 한 지원을 요약 합니다.
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 288cebc4d4097ff40b618e2f1976039359458ecf
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719022"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM 또는 물리적 서버와 Azure 간 재해 복구를 위한 지원 매트릭스

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 VMware vm 및 물리적 서버를 Azure로 재해 복구 하는 데 지원 되는 구성 요소 및 설정을 요약 합니다.

- VMware v m/물리적 서버 재해 복구 아키텍처에 [대해 자세히 알아보세요](vmware-azure-architecture.md) .
- 재해 복구를 시도 하려면 [자습서](tutorial-prepare-azure.md) 를 따르세요.

> [!NOTE]
> Site Recovery은 원본 컴퓨터에 대 한 재해 복구를 설정 하는 대상 지역에서 고객 데이터를 이동 하거나 저장 하지 않습니다. 고객이 선택 하는 경우 다른 지역에서 Recovery Services 자격 증명 모음을 선택할 수 있습니다. Recovery Services 자격 증명 모음은 메타 데이터를 포함 하지만 실제 고객 데이터는 포함 하지 않습니다.

## <a name="deployment-scenarios"></a>배포 시나리오

**시나리오** | **세부 정보**
--- | ---
VMware Vm의 재해 복구 | 온-프레미스 VMware VM을 Azure로 복제. Azure Portal 또는 [PowerShell](vmware-azure-disaster-recovery-powershell.md)을 사용 하 여이 시나리오를 배포할 수 있습니다.
물리적 서버의 재해 복구 | 온-프레미스 Windows/Linux 실제 서버를 Azure로 복제 Azure Portal에서 이 시나리오를 배포할 수 있습니다.

## <a name="on-premises-virtualization-servers"></a>온-프레미스 가상화 서버

**Server** | **요구 사항** | **설명**
--- | --- | ---
vCenter Server | 버전 7.0, 6.7, 6.5, 6.0 또는 5.5 | 재해 복구 배포에 vCenter 서버를 사용 하는 것이 좋습니다.
vSphere 호스트 | 버전 7.0, 6.7, 6.5, 6.0 또는 5.5 | vSphere 호스트와 vCenter 서버가 프로세스 서버와 동일한 네트워크에 있는 것이 좋습니다. 기본적으로 프로세스 서버는 구성 서버에서 실행됩니다. [자세히 알아보기](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery 구성 서버

구성 서버는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함하여 Site Recovery 구성 요소를 실행하는 온-프레미스 컴퓨터입니다.

- VMware Vm의 경우에는 파일을 다운로드 하 여 VMware VM을 만드는 구성 서버를 설정 합니다.
- 물리적 서버의 경우 구성 서버 컴퓨터를 수동으로 설정 합니다.

**구성 요소** | **요구 사항**
--- |---
CPU 코어 | 8
RAM | 16GB
디스크 수 | 3개의 디스크<br/><br/> 디스크에는 OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용)가 포함됩니다.
사용 가능한 디스크 공간 | 프로세스 서버 캐시에 600 GB의 공간이 있습니다.
사용 가능한 디스크 공간 | 보존 드라이브용 600 GB 공간.
운영 체제  | Windows Server 2012 R2 또는 데스크톱 경험을 사용 하는 Windows Server 2016 <br/><br> 장애 복구 (failback)를 위해이 어플라이언스의 기본 제공 마스터 대상을 사용할 계획인 경우 OS 버전이 복제 된 항목과 같거나 그 이상 인지 확인 합니다.|
운영 체제 로케일 | 미국 영어(en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | 구성 서버 버전 [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 이상에서는 필요 하지 않습니다.
Windows Server 역할 | Active Directory Domain Services 사용 안 함 인터넷 정보 서비스 (IIS) 또는 Hyper-v.
그룹 정책| - 명령 프롬프트에 대한 액세스 방지 <br/> - 레지스트리 편집 도구에 대한 액세스 방지 <br/> - 파일 첨부를 위한 트러스트 논리 <br/> - 스크립트 실행 켜기 <br/> - [자세한 정보](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | 다음을 확인합니다.<br/><br/> -기존의 기본 웹 사이트 없음 <br/> - [익명 인증](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) 사용 <br/> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 설정 사용  <br/> - 포트 443에서 수신 대기하는 기존의 웹 사이트/앱 없음<br/>
NIC 유형 | VMXNET3(VMware VM으로 배포될 경우)
IP 주소 유형 | 정적
포트 | 443 컨트롤 채널 오케스트레이션에 사용 됨<br/>데이터 전송의 경우 9443

## <a name="replicated-machines"></a>복제된 컴퓨터

Site Recovery는 지원되는 컴퓨터에서 실행되는 모든 워크로드의 복제를 지원합니다.

**구성 요소** | **설명**
--- | ---
컴퓨터 설정 | Azure로 복제하는 컴퓨터는 [Azure 요구 사항](#azure-vm-requirements)을 충족해야 합니다.
머신 워크로드 | Site Recovery는 지원되는 컴퓨터에서 실행되는 모든 워크로드의 복제를 지원합니다. [자세히 알아보기](https://aka.ms/asr_workload).
머신 이름 | 컴퓨터의 표시 이름이 [Azure 예약 리소스 이름](../azure-resource-manager/templates/error-reserved-resource-name.md) 에 포함 되지 않는지 확인 합니다.<br/><br/> 논리적 볼륨 이름은 대/소문자를 구분 하지 않습니다. 장치의 두 볼륨 이름이 동일 하지 않은지 확인 합니다. Ex: 이름이 "voLUME1", "voLUME1" 인 볼륨은 Azure Site Recovery를 통해 보호할 수 없습니다.

### <a name="for-windows"></a>Windows의 경우

**운영 체제** | **세부 정보**
--- | ---
Windows Server 2019 | [업데이트 롤업 34](https://support.microsoft.com/help/4490016) (모바일 서비스 버전 9.22)부터 지원 됩니다.
Windows Server 2016 64 비트 | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2012 R2/Windows Server 2012 | 지원됨.
Windows Server 2008 R2 SP1 이상 | 지원됨.<br/><br/> 모바일 서비스 에이전트의 버전 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 에서 Windows 2008 R2 SP1 이상을 실행 하는 컴퓨터에 설치 된 [SSU (서비스 스택 업데이트](https://support.microsoft.com/help/4490628) ) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419) 를 설치 해야 합니다. SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.
Windows Server 2008 SP2 이상 (64 비트/32 비트) |  마이그레이션에만 지원 됩니다. [자세히 알아보기](migrate-tutorial-windows-server-2008.md).<br/><br/> 모바일 서비스 에이전트 버전 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 에서는 WINDOWS 2008 SP2 컴퓨터에 설치 된 [SSU (서비스 스택 업데이트](https://support.microsoft.com/help/4493730) ) 및 [SHA-2 업데이트가](https://support.microsoft.com/help/4474419) 설치 되어 있어야 합니다. ISHA-1은 9 월 2019에서 지원 되지 않으며, SHA-2 코드 서명을 사용 하도록 설정 하지 않으면 에이전트 확장이 예상 대로 설치/업그레이드 되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)에 대해 자세히 알아보세요.
Windows 10, Windows 8.1, Windows 8 | 지원됨.
Windows 7 SP1 64 비트 | [업데이트 롤업 36](https://support.microsoft.com/help/4503156) (모바일 서비스 버전 9.22)부터 지원 됩니다. </br></br> 모바일 서비스 에이전트의 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 부터 WINDOWS 7 SP1 컴퓨터에 설치 된 [SSU (서비스 스택 업데이트](https://support.microsoft.com/help/4490628) ) 및 [s h a-2 업데이트가](https://support.microsoft.com/help/4474419) 설치 되어 있어야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)에 대해 자세히 알아보세요.

### <a name="for-linux"></a>Linux의 경우

**운영 체제** | **세부 정보**
--- | ---
Linux | 64 비트 시스템만 지원 됩니다. 32 비트 시스템은 지원 되지 않습니다.<br/><br/>모든 Linux 서버에는 [LIS (linux Integration Services) 구성 요소가](https://www.microsoft.com/download/details.aspx?id=55106) 설치 되어 있어야 합니다. 테스트 장애 조치/장애 조치 (failover) 후 Azure에서 서버를 부팅 해야 합니다. 빌드된 LIS 구성 요소가 없는 경우 Azure에서 부팅 하도록 컴퓨터에 대해 복제를 사용 하도록 설정 하기 전에 [구성 요소](https://www.microsoft.com/download/details.aspx?id=55106) 를 설치 해야 합니다. <br/><br/> Site Recovery는 Azure에서 Linux 서버를 실행하도록 장애 조치(failover)를 오케스트레이션합니다. 그러나 Linux 공급 업체 지원 수명 종료에 해당하지 않는 배포 버전만으로 제한될 수 있습니다.<br/><br/> Linux 배포에서는 배포의 부 버전 릴리스/업데이트에 포함된 스톡 커널만 지원됩니다.<br/><br/> 주요 Linux 배포 버전에서 보호된 시스템을 업그레이드하는 것은 지원되지 않습니다. 업그레이드하려면 복제를 사용하지 않도록 설정하고, 운영 체제를 업그레이드한 다음, 복제를 다시 사용하도록 설정합니다.<br/><br/> Azure에서 Linux 및 오픈 소스 기술에 대 한 지원에 대해 [자세히 알아보세요](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) .
Linux Red Hat Enterprise | 5.2 ~ 5.11</b><br/> 6.1 ~ 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, [7.5, 7.6](https://support.microsoft.com/help/4578241/) , [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), 7.9 </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609) <br/> Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10을 실행 하는 서버에 대 한 이전 커널에는 미리 설치 된 [LIS (Linux Integration Services) 구성 요소가](https://www.microsoft.com/download/details.aspx?id=55106) 없습니다. 빌드된 LIS 구성 요소가 없는 경우 Azure에서 부팅 하도록 컴퓨터에 대해 복제를 사용 하도록 설정 하기 전에 [구성 요소](https://www.microsoft.com/download/details.aspx?id=55106) 를 설치 해야 합니다.
Linux: CentOS | 5.2 ~ 5.11</b><br/> 6.1 ~ 6.10</b><br/> </br> 7.0, 7.1, 7.2, 7.3, 7.4, [7.5, 7.6](https://support.microsoft.com/help/4578241/) , [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), 7.9 </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609) <br/><br/> CentOS 5.2-5.11 &를 실행 하는 서버의 이전 커널 중 일부에는 미리 설치 된  [LIS (Linux Integration Services) 구성 요소가](https://www.microsoft.com/download/details.aspx?id=55106) 없습니다. 빌드된 LIS 구성 요소가 없는 경우 Azure에서 부팅 하도록 컴퓨터에 대해 복제를 사용 하도록 설정 하기 전에 [구성 요소](https://www.microsoft.com/download/details.aspx?id=55106) 를 설치 해야 합니다.
Ubuntu | Ubuntu 14.04 LTS 서버 [(지원 되는 커널 버전 검토)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 서버 [(지원 되는 커널 버전 검토)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS 서버 [(지원 되는 커널 버전 검토)](#ubuntu-kernel-versions) Ubuntu 18.4.03 (커널 v 5.4)는 [9.36](https://support.microsoft.com/help/4578241/) 에서 지원 됩니다. </br> Ubuntu 20.04 LTS 서버 [(지원 되는 커널 버전 검토)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 (모든 7에 대 한 지원이 포함 되어 있습니다. *x*, 8. *x* 버전) [(지원 되는 커널 버전 검토)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(지원 되는 커널 버전 검토)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(지원 되는 커널 버전 검토)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3. [구성 서버에서 최신 모바일 에이전트 설치 관리자를 다운로드 해야](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-server)합니다. </br> SUSE Linux Enterprise Server 11 SP4 </br> **참고**: SUSE LINUX ENTERPRISE SERVER 11 SP3에서 s p 4로 복제 된 컴퓨터 업그레이드는 지원 되지 않습니다. 업그레이드 하려면 복제를 사용 하지 않도록 설정 하 고 업그레이드 후 다시 사용 하도록 설정 합니다. <br/>|
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, [7.3, 7.4](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) [, 7.5](https://support.microsoft.com/help/4573888/) [, 7.6](https://support.microsoft.com/help/4573888/), 7.7, 7.8, [8.0, 8.2](https://support.microsoft.com/help/4573888/)  <br/> Red Hat 호환 커널 또는 UEK(Unbreakable Enterprise Kernel Release) 3, 4 및 5를 실행<br/><br/>8.1<br/>모든 UEK 커널 및 RedHat 커널 <= 3.10.0-1062. *에서의 실행은 [9.36](https://support.microsoft.com/help/4578241/) [9.35](https://support.microsoft.com/help/4573888/) 에서 지원 됩니다.

> [!Note]
> 각 Windows 버전에 대해 Azure Site Recovery는 [LTSC (장기 서비스 채널)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 빌드만 지원 합니다.  지금은 [반기 채널](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 릴리스가 지원 되지 않습니다.

### <a name="ubuntu-kernel-versions"></a>Ubuntu 커널 버전

**지원되는 릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR], [9.33](https://support.microsoft.com/help/4564347/), [9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/) | 3.13.0-24-generic에서 3.13.0-170-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-148-generic<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
|||
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic에서 4.4.0로<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-제네릭-4.15.0-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-4.15.0-1092-azure |
16.04 LTS | [9.35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-generic to 4.4.0-184-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-4.15.0-106-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-4.15.0-1089-azure |
16.04 LTS | [9.34](https://support.microsoft.com/help/4570609) | 4.4.0-21-4.4.0-178-제네릭<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-4.15.0-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-4.15.0-1083-azure |
16.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.4.0-21-4.4.0-178-제네릭<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-4.15.0-99-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-4.15.0-1082-azure|
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-generic에서 4.4.0-171-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-74-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1066-azure|
|||
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic to 4.15.0-112-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-5.0.0-58-generic </br> 5.3.0-19-5.3.0-64-generic </br> 5.4.0-37-5.4.0-42-제네릭</br> 4.15.0-1009-4.15.0-1092-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0로 </br> 5.4.0-1020-azure에서 5.4.0로|
18.04 LTS | [9.35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-4.15.0-108-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-제네릭-5.0.0-제네릭 </br> 5.3.0-19-generic ~ 5.3.0-61-generic </br> 4.15.0-1009-4.15.0-1089-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0로|
18.04 LTS | [9.34](https://support.microsoft.com/help/4570609) | 4.15.0-20-4.15.0-101-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-5.0.0-48-generic </br> 5.3.0-19-generic to 5.3.0-53-generic </br> 4.15.0-1009-4.15.0-1083-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0로|
18.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.15.0-20-generic to 4.15.0-99-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-47-generic </br> 5.3.0-19-generic to 5.3.0-51-generic </br> 4.15.0-1009-4.15.0-1082-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-5.0.0-1036-azure </br> 5.3.0-1007-5.3.0-1020-azure|
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-generic에서 4.15.0-74-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-37-generic </br> 5.3.0-19-generic에서 5.3.0-24-generic </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1028-azure </br> 5.3.0-1007-azure에서 5.3.0-1009-azure|
|||
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-5.4.0-42 </br> -generic 5.4.0-5.4.0-azure

### <a name="debian-kernel-versions"></a>Debian 커널 버전


**지원되는 릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | [9.32][9.32 UR], [9.33](https://support.microsoft.com/help/4564347/), [9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/)| 3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/) | 3.16.0-amd64 to 3.16.0-11-amd64, 4.9.0 64,-amd64 to 4.9.0 64,. bpo |
Debian 8 | [9.32][9.32 UR], [9.33](https://support.microsoft.com/help/4564347/), [9.34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64에서 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.11-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 지원되는 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.36](https://support.microsoft.com/help/4578241/) | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure-4.12.14-6.43 </br> 4.12.14-16.7-4.12.14-16.22-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.35](https://support.microsoft.com/help/4573888/) | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure-4.12.14-6.43 </br> 4.12.14-16.7-4.12.14-16.19-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.34](https://support.microsoft.com/help/4570609) | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure-4.12.14-6.43 </br> 4.12.14-16.7-4.12.14-16.13-azure  |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4) | 9.32, [9.33](https://support.microsoft.com/help/4564347/) | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.34-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>지원 되는 커널 버전 SUSE Linux Enterprise Server 15 개

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 및 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/)  | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-4.12.14-5.47-azure </br></br> 4.12.14-4.12.14-8.38-azure
SUSE Linux Enterprise Server 15 및 15 SP1 | [9.35](https://support.microsoft.com/help/4573888/)  | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-4.12.14-5.47-azure </br></br> 4.12.14-4.12.14-8.33-azure 
SUSE Linux Enterprise Server 15 및 15 SP1 | [9.34](https://support.microsoft.com/help/4570609)  | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-4.12.14-5.47-azure </br></br> 4.12.14-4.12.14-8.30-azure 
SUSE Linux Enterprise Server 15 및 15 SP1 | [9.33](https://support.microsoft.com/help/4564347/) | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-4.12.14-5.47-azure </br></br> 4.12.14-4.12.14-8.30-azure |
SUSE Linux Enterprise Server 15 및 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다. </br></br> 4.12.14-5.5-azure에서 4.12.14-8.22-azure

## <a name="linux-file-systemsguest-storage"></a>Linux 파일 시스템/게스트 스토리지

**구성 요소** | **지원됨**
--- | ---
파일 시스템 | ext3, ext4, XFS, BTRFS (이 테이블에 따라 적용 가능한 조건)
LVM (논리 볼륨 관리) 프로 비전| 굵고 프로 비전-예 <br></br> 씬 프로 비전-아니요
볼륨 관리자 | -LVM이 지원 됩니다.<br/> -LVM의/boot는 [업데이트 롤업 31](https://support.microsoft.com/help/4478871/) (모바일 서비스 버전 9.20)부터 지원 됩니다. 이전 모바일 서비스 버전에서는 지원 되지 않습니다.<br/> -여러 OS 디스크가 지원 되지 않습니다.
반가상화 스토리지 디바이스 | 반가상화 드라이버에서 내보낸 디바이스는 지원되지 않습니다.
다중 큐 블록 IO 디바이스 | 지원되지 않습니다.
HP CCISS 스토리지 컨트롤러가 있는 물리적 서버 | 지원되지 않습니다.
디바이스/탑재 지점 명명 규칙 | 디바이스 이름과 탑재 지점 이름은 고유해야 합니다.<br/> 이름에 대/소문자를 구분 하는 두 개의 장치/탑재 지점이 없는지 확인 합니다. 예를 들어 *장치 1* 및 *장치 1* 와 동일한 VM에 대 한 장치 이름을 지정 하는 것은 지원 되지 않습니다.
디렉터리 | 버전 9.20 이전 버전의 모바일 서비스를 실행 하는 경우 ( [업데이트 롤업 31](https://support.microsoft.com/help/4478871/)에서 출시) 다음 제한이 적용 됩니다.<br/><br/> -이러한 디렉터리 (별도의 파티션/파일 시스템으로 설정 된 경우)는 원본 서버의 동일한 OS 디스크에 있어야 합니다./(root),/boot,/usr,/usr/local,/var,/etc</br> -/Boot 디렉터리는 디스크 파티션에 있어야 하며 LVM 볼륨이 아니어야 합니다.<br/><br/> 버전 9.20 부터는 이러한 제한이 적용 되지 않습니다. 
루트 디렉터리 | -부팅 디스크는 GPT 파티션 형식 되어서는 안됩니다. 이것은 Azure 아키텍처의 제한 사항입니다. GPT 디스크는 데이터 디스크로 지원 됩니다.<br/><br/> VM의 여러 부팅 디스크가 지원 되지 않음<br/><br/> -두 개 이상의 디스크에서 LVM 볼륨의/boot 지원 되지 않습니다.<br/> -부팅 디스크가 없는 컴퓨터는 복제할 수 없습니다.
사용 가능한 공간 요구 사항| /root 파티션: 2GB <br/><br/> 설치 폴더: 250MB
XFSv5 | 메타 데이터 체크섬과 같은 XFS 파일 시스템의 XFSv5 기능이 지원 됩니다 (모바일 서비스 버전 9.10 이상).<br/> xfs_info 유틸리티를 사용하여 파티션에 대한 XFS 수퍼 블록을 확인합니다. 을 `ftype` 1로 설정 하면 XFSv5 기능이 사용 됩니다.
BTRFS | BTRFS는 [업데이트 롤업 34](https://support.microsoft.com/help/4490016) (모바일 서비스 버전 9.22)부터 지원 됩니다. 다음의 경우 BTRFS가 지원 되지 않습니다.<br/><br/> -보호를 사용 하도록 설정한 후 BTRFS 파일 시스템 하위 볼륨이 변경 됩니다.</br> -BTRFS 파일 시스템이 여러 디스크에 분산 되어 있습니다.</br> -BTRFS 파일 시스템은 RAID를 지원 합니다.

## <a name="vmdisk-management"></a>VM/디스크 관리

**동작** | **세부 정보**
--- | ---
복제된 VM에서 디스크 크기 조정 | 장애 조치 (failover) 전에 원본 VM에서 VM 속성에 직접 지원 됩니다. 복제를 해제/다시 설정할 필요가 없습니다.<br/><br/> 장애 조치 (failover) 후 원본 VM을 변경 하면 변경 내용이 캡처 되지 않습니다.<br/><br/> 장애 조치 (failover) 후 Azure VM에서 디스크 크기를 변경 하는 경우 장애 복구 (failback)를 수행 하면 업데이트를 사용 하 여 새 VM을 만들 Site Recovery.
복제된 VM에 디스크 추가 | 지원되지 않습니다.<br/> VM에 대해 복제를 사용 하지 않도록 설정 하 고, 디스크를 추가한 다음, 복제를 다시 사용 하도록 설정 합니다.

> [!NOTE]
> 디스크 id를 변경 하는 것은 지원 되지 않습니다. 예를 들어 디스크 분할이 GPT에서 MBR로 또는 그 반대로 변경 된 경우 디스크 id가 변경 됩니다. 이러한 시나리오에서는 복제가 중단 되 고 새로 설치가 필요 합니다. 

## <a name="network"></a>Network (네트워크)

**구성 요소** | **지원됨**
--- | ---
호스트 네트워크 NIC 팀 | VMware VM에서 지원됩니다. <br/><br/>물리적 컴퓨터 복제에 지원되지 않습니다.
호스트 네트워크 VLAN | 예.
호스트 네트워크 IPv4 | 예.
호스트 네트워크 IPv6 | 아니요.
게스트/서버 네트워크 NIC 팀 | 아니요.
게스트/서버 네트워크 IPv4 | 예.
게스트/서버 네트워크 IPv6 | 아니요.
게스트/서버 네트워크 정적 IP(Windows) | 예.
게스트/서버 네트워크 정적 IP(Linux) | 예. <br/><br/>VM이 장애 복구(Failback) 시 DHCP를 사용하도록 구성되어 있습니다.
게스트/서버 네트워크 다중 NIC | 예.
Site Recovery 서비스에 대 한 개인 링크 액세스 | 예. [자세히 알아보기](hybrid-how-to-enable-replication-private-endpoints.md).


## <a name="azure-vm-network-after-failover"></a>Azure VM 네트워크(장애 조치(failover) 후)

**구성 요소** | **지원됨**
--- | ---
Azure ExpressRoute | 예
ILB | 예
ELB | 예
Azure Traffic Manager | 예
다중 NIC | 예
예약된 IP 주소 | 예
IPv4 | 예
원본 IP 주소 유지 | 예
Azure 가상 네트워크 서비스 엔드포인트<br/> | 예
가속된 네트워킹 | 예

## <a name="storage"></a>스토리지
**구성 요소** | **지원됨**
--- | ---
동적 디스크 | OS 디스크는 기본 디스크 여야 합니다. <br/><br/>데이터 디스크는 동적 디스크일 수 있습니다.
Docker 디스크 구성 | 아니요
호스트 NFS | VMware의 경우 예<br/><br/> 물리적 서버의 경우 아니요
호스트 SAN(iSCSI/FC) | 예
호스트 vSAN | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음
호스트 다중 경로(MPIO) | 예. 테스트 제품: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
호스트 가상 볼륨(VVol) | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음
게스트/서버 VMDK | 예
게스트/서버 공유 클러스터 디스크 | 아니요
게스트/서버 암호화된 디스크 | 아니요
게스트/서버 NFS | 아니요
게스트/서버 iSCSI | 마이그레이션-예<br/>재해 복구의 경우 iSCSI는 VM에 연결 된 디스크로 장애 복구 (failback) 합니다.
게스트/서버 SMB 3.0 | 아니요
게스트/서버 RDM | 예<br/><br/> 물리적 서버의 경우 해당 없음
게스트/서버 디스크 > 1 TB | 예, 디스크가 1024 MB 보다 커야 합니다.<br/><br/>관리 디스크에 복제 하는 경우 최대 8192 GB (9.26 버전 이상)<br></br> 저장소 계정에 복제 하는 경우 최대 4095 GB
4K 논리적 및 4k 물리적 섹터 크기 포함 게스트/서버 디스크 | 아니요
4K 논리 및 512 바이트의 실제 섹터 크기를 포함 하는 게스트/서버 디스크 | 아니요
스트라이프 디스크 포함 게스트/서버 볼륨 4TB 이상 | 예
논리 볼륨 관리(LVM)| 굵고 프로 비전-예 <br></br> 씬 프로비저닝-아니요
게스트/서버 - 스토리지 공간 | 아니요
게스트/서버 디스크 핫 추가/제거 | 아니요
게스트/서버 - 디스크 제외 | 예
게스트/서버 다중 경로(MPIO) | 아니요
게스트/서버 GPT 파티션 | 5 개의 파티션은 [업데이트 롤업 37](https://support.microsoft.com/help/4508614/) (모바일 서비스 버전 9.25)부터 지원 됩니다. 이전 4 개가 지원 되었습니다.
ReFS | 복원 파일 시스템은 모바일 서비스 버전 9.23 이상에서 지원 됩니다.
게스트/서버 EFI/UEFI 부팅 | -Site Recovery 모바일 에이전트 버전 9.30부터 모든 [Azure MARKETPLACE UEFI os](../virtual-machines/windows/generation-2.md#generation-2-vm-images-in-azure-marketplace) 에 대해 지원 됩니다. <br/> -보안 UEFI 부팅 유형이 지원 되지 않습니다. [자세한 정보](../virtual-machines/windows/generation-2.md#on-premises-vs-azure-generation-2-vms)

## <a name="replication-channels"></a>복제 채널

|**복제 유형**   |**지원됨**  |
|---------|---------|
|ODX (오프 로드 된 데이터 전송)    |       아니요  |
|오프 라인 시드        |   아니요      |
| Azure Data Box | 아니요

## <a name="azure-storage"></a>Azure Storage

**구성 요소** | **지원됨**
--- | ---
로컬 중복 스토리지 | 예
지역 중복 스토리지 | 예
읽기 액세스 지역 중복 스토리지 | 예
쿨 스토리지 | 아니요
핫 스토리지| 아니요
블록 Blob | 아니요
미사용 암호화 (SSE)| 예
미사용 암호화 (CMK)| 예 (PowerShell Az 3.3.0 모듈을 통해)
휴지 상태의 이중 암호화 | 예 (PowerShell Az 3.3.0 모듈을 통해). [Windows](../virtual-machines/windows/disk-encryption.md) 및 [Linux](../virtual-machines/linux/disk-encryption.md)에 대해 지원 되는 지역에 대해 자세히 알아보세요.
Premium Storage | 예
보안 전송 옵션 | 예
Import/Export 서비스 | 아니요
Vnet에 대 한 Azure Storage 방화벽 | 예.<br/> 복제 데이터를 저장 하는 데 사용 되는 대상 저장소/캐시 저장소 계정에 구성 됩니다.
범용 v2 저장소 계정 (핫 및 쿨 계층) | 예 (v 2에 비해 트랜잭션 비용이 V1에 비해 크게 높음)

## <a name="azure-compute"></a>Azure Compute

**기능** | **지원됨**
--- | ---
가용성 집합 | 예
가용성 영역 | 아니요
HUB | 예
관리 디스크 | 예

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제 된 온-프레미스 Vm은이 표에 요약 된 Azure VM 요구 사항을 충족 해야 합니다. Site Recovery에서 복제에 대 한 필수 구성 요소 확인을 실행 하면 일부 요구 사항이 충족 되지 않은 경우 검사가 실패 합니다.

**구성 요소** | **요구 사항** | **설명**
--- | --- | ---
게스트 운영 체제 | 복제된 컴퓨터에 대해 [지원되는 운영 체제](#replicated-machines)를 확인합니다. | 지원되지 않는 경우 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 64개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 관리 디스크 (9.26 버전 이상)에 복제 하는 경우 최대 8192 GB<br></br>저장소 계정으로 복제 하는 경우 최대 4095 GB| 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다. |
VM 이름 | 1~63자 사이입니다.<br/><br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.

## <a name="resource-group-limits"></a>리소스 그룹 제한

단일 리소스 그룹에서 보호할 수 있는 가상 머신의 수를 이해 하려면 [구독 제한 및 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)에 대 한 문서를 참조 하세요.

## <a name="churn-limits"></a>변동 제한

다음 테이블에는 Azure Site Recovery 제한이 제공됩니다.
- 이러한 제한은 테스트를 기반으로 하지만 가능한 모든 앱 i/o 조합을 포함 하지는 않습니다.
- 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.
- 최상의 결과를 얻으려면 [Deployment Planner 도구](site-recovery-deployment-planner.md)를 실행 하 고 테스트 장애 조치 (failover)를 사용 하 여 광범위 한 응용 프로그램 테스트를 수행 하 여 앱에 대 한 진정한 성능 그림을 확인 하는 것이 좋습니다.

**복제 대상** | **평균 원본 디스크 I/O 크기** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20MB/s | 디스크당 1,684GB


**원본 데이터 변동률** | **최대 제한**
---|---
VM의 모든 디스크에 대한 최고 데이터 변동률 | 54MB/초
프로세스 서버에서 지원하는 1일 최대 데이터 변동률 | 2TB

- 여기서는 I/O가 30% 겹치고 있다고 가정하는 평균 숫자입니다.
- Site Recovery는 중첩 비율, 더 큰 쓰기 크기 및 실제 워크로드 I/O 동작에 따라 더 높은 처리량을 다룰 수 있습니다.
- 이러한 숫자는 약 5 분의 일반적인 백로그를 가정 합니다. 즉, 데이터를 업로드한 후에 처리되며 5분 내에 복구 지점이 생성됩니다.

## <a name="vault-tasks"></a>자격 증명 모음 작업

**작업** | **지원됨**
--- | ---
리소스 그룹 간 자격 증명 모음 이동 | 아니요
구독 내 및 구독 간에 자격 증명 모음 이동 | 아니요
스토리지 그룹 간 스토리지, 네트워크, Azure VM 이동 | 아니요
저장소, 네트워크, Azure Vm을 구독 내 및 구독 간에 이동 합니다. | 아니요


## <a name="obtain-latest-components"></a>최신 구성 요소 가져오기

**이름** | **설명** | **설명**
--- | --- | ---
구성 서버 | 온-프레미스에 설치 됩니다.<br/> 온-프레미스 VMware 서버 또는 물리적 컴퓨터와 Azure 간의 통신을 조정 합니다. | - 구성 서버 [에 대해 알아봅니다](vmware-physical-azure-config-process-server-overview.md) .<br/> - 최신 버전으로 업그레이드 하는 방법 [에 대해 알아봅니다](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) .<br/> - 구성 서버를 설정 하는 [방법에 대해 알아봅니다](vmware-azure-deploy-configuration-server.md) .
프로세스 서버 | 기본적으로 구성 서버에 설치합니다.<br/> 복제 데이터를 수신 하 고, 캐싱, 압축 및 암호화를 사용 하 여 최적화 하 고, Azure로 전송 합니다.<br/> 배포가 커지면 추가 프로세스 서버를 추가 하 여 더 많은 볼륨의 복제 트래픽을 처리할 수 있습니다. | - 프로세스 서버 [에 대해 알아봅니다](vmware-physical-azure-config-process-server-overview.md) .<br/> - 최신 버전으로 업그레이드 하는 방법 [에 대해 알아봅니다](vmware-azure-manage-process-server.md#upgrade-a-process-server) .<br/> - 스케일 아웃 프로세스 서버를 설정 하는 [방법에 대해 알아봅니다](vmware-physical-large-deployment.md#set-up-a-process-server) .
Mobility Service | 복제 하려는 VMware VM 또는 물리적 서버에 설치 되어 있어야 합니다.<br/> 온-프레미스 VMware 서버/물리적 서버와 Azure 간 복제를 조정 합니다.| - 모바일 서비스 [에 대해 알아봅니다](vmware-physical-mobility-service-overview.md) .<br/> - 최신 버전으로 업그레이드 하는 방법 [에 대해 알아봅니다](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) .<br/>



## <a name="next-steps"></a>다음 단계
VMware VM의 재해 복구용으로 Azure를 준비하는 방법을 [알아봅니다](tutorial-prepare-azure.md).

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery