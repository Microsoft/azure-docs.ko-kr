---
title: Azure Site Recovery를 사용하여 VMware VM 재해 복구 준비
description: Azure Site Recovery 서비스를 사용하여 Azure에 재해 복구하기 위해 온-프레미스 VMware 서버를 준비하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b8fd34c8f1e3a32a8252074941a49d61aa540207
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86134905"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Azure에 재해 복구하기 위해 온-프레미스 VMware 서버 준비

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure에 재해 복구하기 위해 온-프레미스 VMware 서버를 준비하는 방법을 설명합니다. 

이는 온-프레미스 VMware VM에 대한 재해 복구를 Azure에 설정하는 방법을 보여 주는 자습서 시리즈 중 두 번째 자습서입니다. [첫 번째 자습서](tutorial-prepare-azure.md)에서는 VMware 재해 복구에 필요한 Azure 구성 요소를 설정했습니다.


이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * VM 검색을 자동화하기 위해 vCenter 서버 또는 vSphere ESXi 호스트에서 계정을 준비합니다.
> * VMware VM에 모바일 서비스 자동 설치를 위한 계정을 준비합니다.
> * VMware 서버 및 VM 요구 사항 및 지원을 검토합니다.
> * 장애 조치(Failover) 후 Azure VM에 연결하도록 준비합니다.

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 자세한 내용은 Site Recovery 목차의 방법 섹션에 있는 문서를 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

[이 시리즈의 첫 번째 자습서](tutorial-prepare-azure.md)에 설명된 대로 Azure가 준비되었는지 확인합니다.

## <a name="prepare-an-account-for-automatic-discovery"></a>자동 검색용 계정 준비

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- VM을 자동으로 검색합니다. 최소한 읽기 전용 계정이 필요합니다.
- 복제, 장애 조치 및 장애 복구를 오케스트레이션합니다. 디스크 만들기 및 제거, VM 전원 켜기와 같은 작업을 실행할 수 있는 계정이 필요합니다.

계정을 만들려면 다음을 수행합니다.

1. 전용 계정을 사용하려면 vCenter 수준에서 역할을 만듭니다. 역할에 **Azure_Site_Recovery**와 같은 이름을 지정합니다.
2. 아래 표에 요약된 권한을 역할에 할당합니다.
3. vCenter 서버 또는 vSphere 호스트에서 사용자를 만듭니다. 사용자에게 역할을 할당합니다.

### <a name="vmware-account-permissions"></a>VMware 계정 권한

**Task** | **역할/권한** | **세부 정보**
--- | --- | ---
**VM 검색** | 읽기 전용 사용자(최소)<br/><br/> 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.
**전체 복제, 장애 조치, 장애 복구** |  필요한 권한이 있는 역할(Azure_Site_Recovery)을 만든 다음 VMware 사용자 또는 그룹에 이 역할을 할당합니다.<br/><br/> 데이터 센터 개체 –> 자식 개체에 전파, role=Azure_Site_Recovery<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 머신 -&gt; 프로비전 -&gt; 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅샷 -&gt; 스냅샷 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

## <a name="prepare-an-account-for-mobility-service-installation"></a>모바일 서비스 설치를 위한 계정 준비

복제하려는 컴퓨터에 모바일 서비스가 설치되어야 합니다. 사용자가 컴퓨터에 복제를 사용하도록 설정할 경우 Site Recovery가 이 서비스의 강제 설치를 수행할 수 있으며, 그렇지 않을 경우 사용자가 수동으로 설치하거나 설치 도구를 사용하여 설치할 수 있습니다.

- 이 자습서에서는 강제 설치 방식으로 Mobility 서비스를 설치하겠습니다.
- 강제 설치를 사용하려는 경우 Site Recovery가 VM에 액세스하는 데 사용할 수 있는 계정을 준비해야 합니다. Azure 콘솔에서 재해 복구를 설정할 때 이 계정을 지정합니다.

다음과 같이 계정을 준비합니다.

VM에 설치할 수 있는 권한이 있는 도메인 또는 로컬 계정을 준비합니다.

- **Windows VM**: Windows VM에 설치하려면 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정합니다. 이렇게 하려면 레지스트리의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 값이 1인 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가합니다.
- **Linux VM**: Linux VM에 설치하려면 Linux 원본 서버에서 루트 계정을 준비합니다.


## <a name="check-vmware-requirements"></a>VMware 요구 사항 확인

VMware 서버 및 Vm이 요구 사항을 준수하는지 확인 합니다.

1. VMware 서버 요구 사항을 [확인](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)합니다.
2. Linux VM의 경우 파일 시스템 및 스토리지 요구 사항을 [확인](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)합니다. 
3. 온-프레미스 [네트워크](vmware-physical-azure-support-matrix.md#network) 및 [스토리지](vmware-physical-azure-support-matrix.md#storage) 지원을 확인합니다. 
4. 장애 조치(failover) 후 [Azure 네트워킹](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [스토리지](vmware-physical-azure-support-matrix.md#azure-storage) 및 [컴퓨팅](vmware-physical-azure-support-matrix.md#azure-compute)에 대해 지원되는 기능을 확인합니다.
5. Azure에 복제하려는 온-프레미스 VM은 [Azure VM 요구 사항](vmware-physical-azure-support-matrix.md#azure-vm-requirements)을 준수해야 합니다.
6. Linux Virtual Machines에서 디바이스 이름과 탑재 지점 이름은 고유해야 합니다. 이름이 같은 두 개의 디바이스/탑재 지점 이름이 없어야 합니다. 이름은 대/소문자를 구분하지 않습니다. 예를 들어 동일한 VM의 두 디바이스에 _device1_과 _Device1_로 이름을 지정하는 것이 허용되지 않습니다.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

장애 조치(failover) 후 온-프레미스 네트워크의 Azure VM에에 연결할 수 있습니다.

장애 조치 후 RDP를 사용하여 Windows VM에 연결하려면 다음을 수행합니다.

- **인터넷 액세스**. 장애 조치(failover) 전에 온-프레미스 VM에서 RDP를 활성화합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.
- **사이트 간 VPN 액세스**:
    - 장애 조치(failover) 전에 온-프레미스 컴퓨터에서 RDP를 활성화합니다.
    - RDP가 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 **도메인 또는 프라이빗** 네트워크에 대해 허용되어야 합니다.
    - 운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아봅니다](https://support.microsoft.com/kb/3031135).
- 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없어야 합니다. 있는 경우 업데이트가 완료될 때까지 가상 머신에 로그인할 수 없습니다.
- 장애 조치 후 Microsoft Azure VM에서 **부트 진단**을 확인하여 VM의 스크린샷을 검토합니다. 연결할 수 없는 경우 VM이 실행 중인지 확인하고 해당 [문제 해결 팁](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)(영문)을 검토합니다.

장애 조치 후 SSH를 사용하여 Linux VM에 연결하려면 다음을 수행합니다.

- 장애 조치 전에 온-프레미스 컴퓨터에서 시스템 부팅 시 SSH(Secure Shell) 서비스가 자동으로 시작되도록 설정되어 있는지 확인합니다.
- 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.
- 장애 조치 후 Azure VM에서 장애 조치된 VM의 네트워크 보안 그룹 규칙 및 연결되어 있는 Azure 서브넷에 대한 SSH 포트로 들어오는 연결을 허용합니다.
- VM에 대한 [공용 IP 주소를 추가](./site-recovery-monitor-and-troubleshoot.md)합니다.
- **부트 진단**을 확인하여 VM에 대한 스크린샷을 볼 수 있습니다.


## <a name="failback-requirements"></a>장애 복구 요구 사항
온-프레미스 사이트로 장애 복구(failback)를 수행하려는 경우 [장애 복구(failback)에 대한 필수 구성 요소](vmware-azure-reprotect.md#before-you-begin)가 몇 가지 있습니다. 지금 준비할 수 있지만 꼭 해야 하는 것은 아닙니다. Azure로 장애 조치(failover)한 후에 준비할 수 있습니다.



## <a name="next-steps"></a>다음 단계

재해 복구를 설정합니다. 여러 VM을 복제하는 경우에는 용량을 계획합니다.
> [!div class="nextstepaction"]
> [Azure에 VMware VM 재해 복구 설정](vmware-azure-tutorial.md)
> [용량 계획 수행](site-recovery-deployment-planner.md).
