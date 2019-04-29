---
title: StorSimple 8000 디바이스 문제 해결을 위한 진단 도구 | Microsoft Docs
description: StorSimple 디바이스 모드 및 StorSimple용 Windows PowerShell을 사용하여 디바이스 모드를 변경하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5cce4337e3ef95c6407d46d9b8b6401fe4f6600b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576189"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>StorSimple 진단 도구를 사용하여 8000 시리즈 디바이스 문제 해결

## <a name="overview"></a>개요

StorSimple 진단 도구는 StorSimple 디바이스에 대한 시스템, 성능, 네트워크 및 하드웨어 구성 요소 상태와 관련된 문제를 진단합니다. 진단 도구는 다양한 시나리오에서 사용할 수 있습니다. 이러한 시나리오에는 워크로드 계획, StorSimple 디바이스 배포, 네트워크 환경 평가 및 작동 가능 디바이스의 성능을 결정하는 작업이 포함됩니다. 이 문서에서는 진단 도구에 대한 개요와 도구를 StorSimple 디바이스와 함께 사용할 수 있는 방법을 설명합니다.

진단 도구는 주로 StorSimple 8000 시리즈 온-프레미스 디바이스(8100 및 8600)에 사용하도록 고안되었습니다.

## <a name="run-diagnostics-tool"></a>진단 도구 실행

이 도구는 사용자의 StorSimple 디바이스의 Windows PowerShell 인터페이스를 통해 실행할 수 있습니다. 사용자 디바이스의 로컬 인터페이스에 액세스하는 방법에는 두 가지가 있습니다.

* [PuTTY를 사용하여 디바이스 직렬 콘솔에 연결합니다](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [StorSimple용 Windows PowerShell을 통해 도구에 원격으로 액세스합니다](storsimple-8000-remote-connect.md).

이 문서에서는 PuTTY를 통해 디바이스 직렬 콘솔에 연결한 것으로 가정합니다.

#### <a name="to-run-the-diagnostics-tool"></a>진단 도구를 실행하려면 다음을 수행합니다.

디바이스의 Windows PowerShell 인터페이스에 연결한 후 다음 단계를 수행하여 cmdlet을 실행합니다.
1. [디바이스 직렬 콘솔 연결에 PuTTY 사용](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)단계를 수행하여 디바이스 직렬 콘솔에 로그온합니다.

2. 다음 명령을 입력합니다.

    `Invoke-HcsDiagnostics`

    범위 매개 변수를 지정하지 않으면 cmdlet은 모든 진단 테스트를 실행합니다. 이러한 테스트에는 시스템, 하드웨어 구성 요소 상태, 네트워크 및 성능이 포함됩니다. 
    
    특정 테스트만 실행하려면 범위 매개 변수를 지정합니다. 예를 들어,네트워크 테스트만 실행하려면 다음을 입력합니다.

    `Invoke-HcsDiagnostics -Scope Network`

3. PuTTY 창에서 출력을 선택하여 추가 분석을 위해 텍스트 파일로 복사합니다.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>진단 도구를 사용하기 위한 시나리오

진단 도구를 사용하여 시스템의 네트워크, 성능, 시스템 및 하드웨어 상태 문제를 해결합니다. 몇 가지 가능한 시나리오는 다음과 같습니다.

* **디바이스 오프라인** - 사용자의 StorSimple 8000 시리즈 디바이스가 오프라인 상태입니다. 단, Windows PowerShell 인터페이스에서 두 컨트롤러가 모두 켜져 있고 실행 중인 것 같습니다.
    * 이 도구를 사용하여 네트워크 상태를 결정할 수 있습니다.
         
         > [!NOTE]
         > 등록(또는 설치 마법사를 통해 구성)하기 전에는 이 도구를 사용하여 디바이스의 성능 및 네트워크 설정을 평가하지 마십시오. 설치 마법사 및 등록하는 과정에서 유효한 IP가 디바이스에 할당됩니다. 등록되지 않은 디바이스에서 하드웨어 상태 및 시스템에 대해 이 cmdlet을 실행할 수 있습니다. 다음과 같이 범위 매개 변수를 사용합니다.
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **영구 디바이스 문제** - 영구적으로 보이는 디바이스 문제가 발생했습니다. 예를 들어 등록에 실패합니다. 디바이스를 성공적으로 등록하고 잠시 동안 작동한 후에 문제가 발생할 수 있습니다.
    * 이 경우 Microsoft 지원에 서비스 요청을 로그하기 전에 임시 문제 해결에 대한 이 도구를 사용합니다. 이 도구를 실행하고 이 도구의 출력을 캡처하는 것이 좋습니다. 그런 다음, 문제 해결을 더 신속하게 처리하기 위해 이 출력을 지원에 제공할 수 있습니다.
    * 하드웨어 구성 요소 또는 클러스터 오류가 발생하는 경우 지원 요청에 로그인해야 합니다.

* **낮은 디바이스 성능** - 사용자의 StorSimple 디바이스 속도가 느립니다.
    * 이 경우 성능으로 설정된 범위 매개 변수로 이 cmdlet을 실행합니다. 출력을 분석합니다. 클라우드 읽기-쓰기 대기 시간을 가져옵니다. 보고된 대기 시간을 최대 달성 가능한 대상으로 사용하고, 내부 데이터 처리에 일부 오버헤드를 고려한 다음, 시스템에 워크로드를 배포합니다. 자세한 정보는 [네트워크 테스트를 사용하여 디바이스 성능 문제 해결](#network-test)을 참조하세요.


## <a name="diagnostics-test-and-sample-outputs"></a>진단 테스트 및 샘플 출력

### <a name="hardware-test"></a>하드웨어 테스트

이 테스트는 하드웨어 구성 요소, USM 펌웨어 및 시스템에서 실행되는 디스크 펌웨어의 상태를 결정합니다.

* 보고된 하드웨어 구성 요소는 테스트에 실패하였거나 시스템에 존재하지 않는 구성 요소입니다.
* USM 펌웨어 및 디스크 펌웨어 버전은 사용자의 시스템에서 Controller 0, Controller 1 및 공유 구성 요소에 대해 보고됩니다. 하드웨어 구성 요소의 전체 목록은 다음으로 이동합니다.

    * [기본 인클로저의 구성 요소](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [EBOD 인클로저의 구성 요소](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> 하드웨어 테스트에서 실패한 구성 요소를 보고하는 경우 [Microsoft 지원으로 서비스 요청에 로그인](storsimple-8000-contact-microsoft-support.md)합니다.

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>8100 디바이스에서 실행되는 하드웨어 테스트의 샘플 출력

다음은 StorSimple 8100 디바이스의 샘플 출력입니다. 8100 모델 디바이스에 EBOD 인클로저는 존재하지 않습니다. 따라서 EBOD 컨트롤러 구성 요소는 보고되지 않습니다.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>시스템 테스트

이 테스트는 사용자 디바이스에 대한 시스템 정보, 사용 가능한 업데이트, 클러스터 정보 및 서비스 정보를 보고합니다.

* 시스템 정보에는 시스템에서 실행되는 모델, 디바이스 일련 번호, 표준 시간대, 컨트롤러 상태 및 자세한 소프트웨어 버전이 포함됩니다. 출력으로 보고된 다양한 시스템 매개 변수를 이해하려면 [시스템 정보 해석](#appendix-interpreting-system-information)으로 이동하세요.

* 업데이트 가용성은 일반 및 유지 관리 모드를 사용할 수 있는지 여부와 연결된 패키지 이름을 보고합니다. `RegularUpdates` 및 `MaintenanceModeUpdates`가 `false`인 경우 이는 업데이트를 사용할 수 없음을 나타냅니다. 사용자 디바이스가 최신 상태입니다.
* 클러스터 정보에는 모든 HCS 클러스터 그룹 및 해당 상태의 여러 논리 구성 요소에 대한 정보가 포함됩니다. 보고서의 이 섹션에 오프라인 클러스터 그룹이 있는 경우 [Microsoft 지원으로 문의하세요](storsimple-8000-contact-microsoft-support.md).
* 서비스 정보에는 사용자 디바이스에서 실행 중인 모든 HCS 및 CiS 서비스의 이름 및 상태가 포함됩니다. 이 정보는 디바이스 문제 해결 시 Microsoft 지원에 유용합니다.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>8100 디바이스에서 실행되는 시스템 테스트의 샘플 출력

다음은 8100 디바이스에서 실행되는 시스템 테스트의 샘플 출력입니다.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>네트워크 테스트

이 테스트는 사용자 StorSimple 디바이스의 네트워크 인터페이스, 포트, DNS 및 NTP 서버 연결, SSL 인증서, 스토리지 계정 자격 증명, 업데이트 서버에 대한 연결 및 웹 프록시 연결 상태를 확인합니다.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>DATA0만 사용하는 경우 네트워크 테스트의 샘플 출력

다음은 8100 디바이스의 샘플 출력입니다. 출력에서 다음을 확인할 수 있습니다.
* DATA 0 및 DATA 1 네트워크 인터페이스만 사용하도록 설정 및 구성되어 있습니다.
* DATA 2 - 5는 포털에서 사용하도록 설정되어 있지 않습니다.
* DNS 서버 구성은 유효하며 디바이스는 DNS 서버를 통해 연결할 수 있습니다.
* NTP 서버 연결도 가능합니다.
* 포트 80 및 443이 열려 있습니다. 그러나 포트 9354는 차단되어 있습니다. [시스템 네트워크 요구 사항](storsimple-system-requirements.md)에 따라 Service Bus 통신에 대한 이 포트를 열어야 합니다.
* SSL 인증이 유효합니다.
* 장치를 스토리지 계정인 _myss8000storageacct_에 연결할 수 있습니다.
* 업데이트 서버에 대한 연결이 유효합니다.
* 이 디바이스에 대한 웹 프록시가 구성되어 있지 않습니다.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>DATA0 및 DATA1을 사용하는 경우 네트워크 테스트의 샘플 출력

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>성능 테스트

이 테스트는 사용자 디바이스에 대한 클라우드 읽기-쓰기 대기 시간을 통해 클라우드 성능을 보고합니다. 이 도구는 StorSimple과 함께 얻을 수 있는 클라우드 성능 기준을 설정하는 데 사용할 수 있습니다. 도구는 사용자의 연결에서 얻을 수 있는 최대 성능(읽기-쓰기 대기 시간의 모범 사례 시나리오)을 보고합니다.

도구에서 달성 가능한 최대 성능을 보고하므로 작업 부하를 배포할 때 보고된 읽기-쓰기 대기 시간을 대상으로 사용할 수 있습니다.

테스트는 디바이스의 다양한 볼륨 형식과 연결된 Blob 크기를 시뮬레이션합니다. 일반 계층화되고 로컬로 고정된 일반 볼륨의 백업은 64KB Blob 크기를 사용합니다. 선택된 보관 옵션을 사용하는 계층화된 볼륨은 512KB Blob 데이터 크기를 사용합니다. 사용자의 디바이스에 계층화되고 로컬로 고정된 볼륨이 구성되어 있는 경우 64KB Blob 데이터 크기에 해당하는 테스트만 실행됩니다.

이 도구를 사용하려면 다음 단계를 수행합니다.

1.  첫째, 계층화된 볼륨과 선택된 보관 옵션을 사용하는 계층화된 볼륨의 조합을 만듭니다. 이 작업은 도구에서 64KB와 512KB Blob 크기 모두에 대한 테스트를 실행하는지 확인합니다.

2. 볼륨을 만들고 구성한 후 cmdlet을 실행합니다. 형식:

    `Invoke-HcsDiagnostics -Scope Performance`

3. 도구에서 보고된 읽기-쓰기 대기 시간을 기록해 둡니다. 이 테스트는 결과를 보고하기 전에 실행하는 데 몇 분 정도 걸릴 수 있습니다.

4. 연결 대기 시간이 모두 예상된 범위 내인 경우 작업 부하를 배포할 때 도구에서 보고된 대기 시간을 달성 가능한 최대 대상으로 사용할 수 있습니다. 내부 데이터 처리를 위해 약간의 오버헤드를 고려해야 합니다.

    진단 도구에서 보고된 읽기-쓰기 대기 시간이 너무 높은 경우:

    1. Azure Storage 계정에 대한 대기 시간을 이해하기 위해 Blob services에 대한 스토리지 분석을 구성하고 출력을 분석합니다. 자세한 지침은 [저장소 분석 설정 및 구성](../storage/common/storage-enable-and-view-metrics.md)으로 이동합니다. 그러한 대기 시간이 높고 StorSimple 진단 도구에서 수신한 숫자와 비교 가능한 경우 Azure Storage로 서비스 요청을 로그해야 합니다.

    2. 저장소 계정 대기 시간이 너무 낮은 경우 사용자 네트워크의 대기 시간 문제 조사를 위해 네트워크 관리자에게 문의합니다.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>8100 디바이스에서 실행되는 성능 테스트의 샘플 출력

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>부록: 시스템 정보 해석

다음은 매핑할 시스템 정보의 다양한 Windows PowerShell 매개 변수를 설명하는 테이블입니다. 

| PowerShell 매개 변수    | 설명  |
|-------------------------|------------------|
| 인스턴스 ID             | 모든 컨트롤러에는 고유 식별자 또는 연결된 GUID입니다.|
| 이름                    | 디바이스를 배포하는 동안 Azure Portal을 통해 구성된 디바이스의 친숙한 이름입니다. 기본 친숙한 이름은 디바이스 일련 번호입니다. |
| 모델                   | 사용자 StorSimple 8000 시리즈 디바이스의 모델입니다. 모델은 8100 또는 8600일 수 있습니다.|
| SerialNumber            | 디바이스 일련 번호는 공장에서 할당되고 길이가 15자입니다. 예를 들어, 8600-SHX0991003G44HT는 다음을 나타냅니다.<br> 8600 – 디바이스 모델을 나타냅니다.<br>SHX – 제조 사이트를 나타냅니다.<br> 0991003 - 특정 제품을 나타냅니다. <br> G44HT - 마지막 5자리 숫자는 고유한 일련 번호를 만들기 위해 증가됩니다. 순차적인 집합이 아닐 수 있습니다.|
| TimeZone                | 디바이스를 배포하는 동안 Azure Portal에서 구성된 디바이스 표준 시간대입니다.|
| CurrentController       | 사용자 StorSimple 디바이스의 Windows PowerShell 인터페이스를 통해 연결한 컨트롤러입니다.|
| ActiveController        | 사용자 디바이스에서 활성 상태이며 모든 네트워크 및 디스크 작업을 제어하는 컨트롤러입니다. 이는 컨트롤러 0 또는 컨트롤러 1일 수 있습니다.  |
| Controller0Status       | 사용자 디바이스에서 컨트롤러 0의 상태입니다. 컨트롤러 상태는 일반, 복구 모드 또는 연결할 수 없는 상태일 수 있습니다.|
| Controller1Status       | 사용자 디바이스에서 컨트롤러 1의 상태입니다.  컨트롤러 상태는 일반, 복구 모드 또는 연결할 수 없는 상태일 수 있습니다.|
| SystemMode              | StorSimple 디바이스의 전반적인 상태입니다. 디바이스 상태는 일반, 유지 관리 또는 서비스 해제됨(Azure Portal에서 비활성화에 해당) 상태일 수 있습니다.|
| FriendlySoftwareVersion | 디바이스 소프트웨어 버전에 해당하는 친숙한 문자열입니다. 업데이트 4를 실행하는 시스템의 경우 친숙한 소프트웨어 버전은 StorSimple 8000 시리즈 업데이트 4.0입니다.|
| HcsSoftwareVersion      | 사용자 디바이스에서 실행되는 HCS 소프트웨어 버전입니다. 예를 들어, StorSimple 8000 시리즈 업데이트 4.0에 해당하는 HCS 소프트웨어 버전은 6.3.9600.17820입니다. |
| ApiVersion              | HCS 디바이스의 Windows PowerShell API의 소프트웨어 버전입니다.|
| VhdVersion              | 디바이스와 함께 제공된 출하 시 이미지의 소프트웨어 버전입니다. 사용자 디바이스를 공장 기본값으로 다시 설정한 경우 이 소프트웨어 버전을 실행합니다.|
| OSVersion               | 디바이스에서 실행 중인 Windows Server 운영 체제의 소프트웨어 버전입니다. StorSimple 디바이스는 6.3.9600에 해당하는 Windows Server 2012 R2를 기반으로 합니다.|
| CisAgentVersion         | 사용자의 StorSimple 디바이스에서 실행되는 사용자의 Cis 에이전트에 대한 버전입니다. 이 에이전트는 Azure에서 실행되는 StorSimple Manager 서비스와 통신하는 데 도움이 됩니다.|
| MdsAgentVersion         | 사용자의 StorSimple 디바이스에서 실행되는 사용자의 Mds 에이전트에 해당하는 버전입니다. 이 에이전트는 MDS(모니터링 및 진단 서비스)로 데이터를 이동시킵니다.|
| Lsisas2Version          | 사용자의 StorSimple 디바이스에서 LSI 드라이버에 해당하는 버전입니다.|
| 용량                | 디바이스의 총 용량(바이트)입니다.|
| RemoteManagementMode    | Windows PowerShell 인터페이스를 통해 디바이스를 원격으로 관리할 수 있는지 여부를 나타냅니다. |
| FipsMode                | 사용자의 디바이스에서 미국 FIPS(Federal Information Processing Standard) 모드를 사용할 수 있는지 여부를 나타냅니다. FIPS 140 표준은 중요한 데이터의 보호를 위해 미국 연방 정부 컴퓨터 시스템에서 사용할 수 있도록 승인된 암호화 알고리즘을 정의합니다. 업데이트 4 이상을 실행하는 디바이스의 경우 FIPS 모드는 기본적으로 활성화되어 있습니다. |

## <a name="next-steps"></a>다음 단계

* [Invoke-HcsDiagnostics cmdlet의 구문](https://technet.microsoft.com/library/mt795371.aspx)에 대해 알아보세요.

* 사용자의 StorSimple 디바이스에서 [배포 문제 해결](storsimple-troubleshoot-deployment.md) 방법에 대한 자세한 정보를 알아보세요.
