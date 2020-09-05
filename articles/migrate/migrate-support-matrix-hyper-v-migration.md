---
title: Azure Migrate의 Hyper-v 마이그레이션 지원
description: Azure Migrate를 사용 하 여 Hyper-v 마이그레이션 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 4f3609560fa59c08c4d92f4faa36c7fbbffb95d7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051154"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Hyper-v 마이그레이션을 위한 지원 매트릭스

이 문서에서는 [Azure Migrate: 서버 마이그레이션을](migrate-services-overview.md#azure-migrate-server-migration-tool) 사용 하 여 hyper-v vm을 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약 합니다. Azure로 마이그레이션할 Hyper-v Vm을 평가 하는 방법에 대 한 정보를 찾고 있는 경우 [평가 지원 매트릭스](migrate-support-matrix-hyper-v.md)를 검토 하세요.

## <a name="migration-limitations"></a>마이그레이션 제한 사항

복제를 위해 한 번에 최대 10 개의 Vm을 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려는 경우 10 그룹으로 복제 합니다.


## <a name="hyper-v-host-requirements"></a>Hyper-v 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **배포**       | Hyper-v 호스트는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. <br/>Hyper-v 호스트에 Azure Migrate 복제 소프트웨어 (Hyper-v 복제 공급자)가 설치 되어 있습니다.|
| **권한**           | Hyper-v 호스트에 대 한 관리자 권한이 필요 합니다. |
| **호스트 운영 체제** | Windows Server 2019, Windows Server 2016 또는 최신 업데이트가 포함 된 Windows Server 2012 R2 이러한 운영 체제의 Server core 설치도 지원 됩니다. |
| **기타 소프트웨어 요구 사항** | .NET Framework 4.7 이상 |
| **포트 액세스** |  VM 복제 데이터를 보내기 위한 HTTPS 포트 443의 아웃 바운드 연결
| **사용 가능한 디스크 공간 (캐시)** |  600GB |
| **사용 가능한 디스크 공간(보존 디스크)** |  600GB |


## <a name="hyper-v-vms"></a>Hyper-V VM

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | Azure에서 지 원하는 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](../virtual-machines/linux/endorsed-distros.md) 운영 체제입니다. |
**Windows Server 2003** | Windows Server 2003를 실행 하는 Vm의 경우 마이그레이션하기 전에 [hyper-v Integration Services를 설치](prepare-windows-server-2003-migration.md) 해야 합니다. | 
**Azure의 Linux Vm** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다.<br/><br/> Linux의 경우 이러한 운영 체제에 대 한 변경 내용이 자동으로 Azure Migrate.<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 이상<br/> -CentOS 6.5 이상, 7.0 이상</br> -SUSE Linux Enterprise Server 12 SP1 이상<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8. 다른 운영 체제의 경우 [필요한 변경](prepare-for-migration.md#linux-machines) 작업을 수동으로 수행 합니다.
| **Azure에 대 한 필수 변경 내용** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다. 마이그레이션하기 전에 수동으로 조정 합니다. 관련 문서에는이 작업을 수행 하는 방법에 대 한 지침이 포함 되어 있습니다. |
| **Linux 부팅**                 | /Boot는 전용 파티션에 있는 경우 OS 디스크에 상주해 야 하며 여러 디스크에 분산 되 면 안 됩니다.<br/> /Boot가 루트 (/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 걸쳐 있지 않아야 합니다. |
| **UEFI 부팅**                  | 지원됨. Azure 2 세대 VM에서 지 원하는 VM 크기를 선택 해야 합니다.  |
| **UEFI-보안 부팅**         | 마이그레이션에 지원 되지 않습니다.|
| **디스크 크기**                  | OS 디스크의 경우 2tb, 데이터 디스크의 경우 4tb|
| **디스크 번호** | VM 당 최대 16 개의 디스크|
| **암호화 된 디스크/볼륨**    | 마이그레이션에 지원 되지 않습니다.|
| **RDM/통과 디스크**      | 마이그레이션에 지원 되지 않습니다.|
| **공유 디스크** | 공유 디스크를 사용 하는 Vm은 마이그레이션을 지원 하지 않습니다.|
| **NFS**                        | Vm에 볼륨으로 탑재 된 NFS 볼륨이 복제 되지 않습니다.|
| **ISCSI**                      | ISCSI 대상을 사용 하는 Vm은 마이그레이션을 지원 하지 않습니다.
| **대상 디스크**                | 관리 디스크를 사용 하 여 Azure Vm으로 마이그레이션할 수 있습니다. |
| **IPv6** | 지원 안 됨|
| **NIC 팀** | 지원 안 됨|
| **Azure Site Recovery** | Azure Site Recovery를 사용 하 여 복제에 대해 VM을 사용 하도록 설정한 경우 Azure Migrate 서버 마이그레이션을 사용 하 여 복제할 수 없습니다.|
| **Ports** | VM 복제 데이터를 보내기 위한 HTTPS 포트 443의 아웃 바운드 연결|

### <a name="url-access-public-cloud"></a>URL 액세스 (공용 클라우드)

Hyper-v 호스트의 복제 공급자 소프트웨어는 이러한 Url에 액세스할 수 있어야 합니다.

**URL** | **세부 정보**
--- | ---
login.microsoftonline.com | Active Directory를 사용 하 여 액세스 제어 및 id 관리.
*.backup.windowsazure.com | 복제 데이터 전송 및 조정.
\*.hypervrecoverymanager.windowsazure.com | 복제 관리에 사용 됩니다.
\*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다. 
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
time.windows.com | 시스템 시간과 글로벌 시간 간의 시간 동기화를 확인 합니다.

### <a name="url-access-azure-government"></a>URL 액세스 (Azure Government)

Hyper-v 호스트의 복제 공급자 소프트웨어는 이러한 Url에 액세스할 수 있어야 합니다.

**URL** | **세부 정보**
--- | ---
login.microsoftonline.us | Active Directory를 사용 하 여 액세스 제어 및 id 관리.
backup.windowsazure.us | 복제 데이터 전송 및 조정.
*. hypervrecoverymanager.windowsazure.us | 복제 관리에 사용 됩니다.
*.blob.core.usgovcloudapi.net | 저장소 계정에 데이터를 업로드 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
time.nist.gov | 시스템 시간과 글로벌 시간 간의 시간 동기화를 확인 합니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제 된 모든 온-프레미스 Vm은이 표에 요약 된 Azure VM 요구 사항을 충족 해야 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 16 개이 하 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 최대 4,095GB | 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원 안 됨 | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원 안 됨 | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원 안 됨 | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행 하는 Azure Vm에 연결 하려면 다음을 수행 합니다.<br/><br/> -마이그레이션하기 전에 온-프레미스 VM에서 RDP를 사용 하도록 설정 합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.<br/><br/> -사이트 간 VPN 액세스의 경우 rdp를 사용 하도록 설정 하 고 **Windows Firewall**  ->  **도메인 및 개인** 네트워크의 Windows 방화벽**허용 되는 앱 및 기능** 에서 rdp를 허용 합니다. 또한 운영 체제의 SAN 정책이 **OnlineAll**으로 설정 되어 있는지 확인 합니다. [자세히 알아보기](prepare-for-migration.md). |
마이그레이션 후 연결-Linux | SSH를 사용 하 여 마이그레이션한 후 Azure Vm에 연결 하려면:<br/><br/> -마이그레이션 전에 온-프레미스 컴퓨터에서 Secure Shell 서비스가 시작으로 설정 되어 있고 방화벽 규칙에서 SSH 연결을 허용 하는지 확인 합니다.<br/><br/> -마이그레이션 후 Azure VM에서 장애 조치 (failover) 된 VM의 네트워크 보안 그룹 규칙에 대 한 SSH 포트 및 연결 된 Azure 서브넷에 대 한 들어오는 연결을 허용 합니다. 또한 VM에 대 한 공용 IP 주소를 추가 합니다. |  

## <a name="next-steps"></a>다음 단계

마이그레이션을 위해 [Hyper-v vm을 마이그레이션합니다](tutorial-migrate-hyper-v.md) .
