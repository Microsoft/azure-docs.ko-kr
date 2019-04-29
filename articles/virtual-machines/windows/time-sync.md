---
title: Azure의 Windows VM에 대한 시간 동기화 | Microsoft Docs
description: Windows 가상 머신에 대한 시간 동기화.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 1a2e75dcffe32c6f1aeaba8646b96bbc1500ffdf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438213"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Azure의 Windows VM에 대한 시간 동기화

시간 동기화는 보안 및 이벤트 상관 관계에서 중요합니다. 경우에 따라 시간 동기화는 분산 트랜잭션 구현에 사용됩니다. 여러 컴퓨터 시스템 간에 시간 정확도는 동기화를 통해 이루어집니다. 동기화는 재부팅 및 시간 원본 서버와 시간을 페칭하는 컴퓨터 간의 네트워크 트래픽을 비롯해 여러 작업에서 영향을 받을 수 있습니다. 

Azure는 이제 Windows Server 2016을 실행하는 인프라의 지원을 받습니다. Windows Server 2016은 시간을 수정하고 로컬 시계에 영향을 미치는 데 사용된 알고리즘을 개선하여 UTC와 동기화했습니다.  또한 Windows Server 2016은 정확한 시간을 위해 VM이 호스트와 동기화하는 방법을 제어하는 VMICTimeSync 서비스도 개선했습니다. 이러한 개선은 VM 시작 또는 VM 복원 시 시작 시간의 정확도 향상을 포함하며, Windows Time(W32time)에 제공되는 샘플에 대한 대기 시간 수정을 중단합니다. 


>[!NOTE]
>Windows Time 서비스에 대한 빠른 개요는 이 [고급 개요 비디오](https://aka.ms/WS2016TimeVideo)를 참조하세요.
>
> 자세한 내용은 [Windows Server 2016의 정확한 시간](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)을 참조하세요. 

## <a name="overview"></a>개요

컴퓨터 시계의 정확도는 컴퓨터 시계가 UTC(협정 세계시) 표준 시간에 근접한 정도로 측정됩니다. UTC는 300년에 1초밖에 차이가 나지 않는 여러 국가의 정밀한 원자 시계 샘플로 정의됩니다. 그러나 UTC를 직접 읽으려면 특수화된 하드웨어가 필요합니다. 대신 시간 서버가 UTC로 동기화되고 다른 컴퓨터에서 액세스되어 확장성 및 안정성을 제공합니다. 모든 컴퓨터에는 사용할 시간 서버를 알고 있으며 주기적으로 컴퓨터 시계를 수정해야 하는지 여부를 확인하고 필요한 경우 시간을 조정하는 시간 동기화 서비스가 실행되고 있습니다. 

Azure 호스트는 GPS 안테나가 있는 Microsoft 소유의 Stratum 1 디바이스에서 시간을 사용하는 내부 Microsoft 시간 서버에 동기화됩니다. Azure의 가상 머신은 정확한 시간(*호스트 시간*)을 VM에 전달하기 위해 호스트에 의존할 수 있거나, VM이 직접 시간 서버 또는 둘의 조합에서 시간을 가져올 수 있습니다. 

호스트와 가상 머신의 상호 작용은 시계에도 영향을 줄 수 있습니다. [메모리 보존 유지 관리](maintenance-and-updates.md#maintenance-not-requiring-a-reboot) 중에는 VM이 최대 30초 동안 일시 중지됩니다. 예를 들어 유지 관리를 시작하면 먼저 VM 시계는 오전 10:00:00시를 표시한 후, 28초간 지속됩니다. VM이 다시 시작되면 VM 시계는 여전히 오전 10:00:00시를 표시한 다음, 28초가 해제됩니다. 이를 수정하려면 VMICTimeSync 서비스가 호스트에서 발생하는 상황을 모니터링하고 VM에서 발생되는 변경을 보완하도록 요구합니다.

VMICTimeSync 서비스는 샘플 또는 동기화 모드에서 작동하며 미래의 시계에만 영향을 줍니다. W32time이 실행되어야 하는 샘플 모드에서는 VMICTimeSync 서비스가 5초마다 호스트를 폴링하고 W32time에 시간 샘플을 제공합니다. W32time 서비스는 약 30초마다 최신 시간 샘플을 받아서 해당 샘플로 게스트 시계에 영향을 줍니다. 동기화 모드는 게스트가 다시 시작되었거나 게스트 시계가 호스트 시계보다 5초 넘게 늦는 경우 활성화됩니다. W32time 서비스가 제대로 실행되는 경우에는 후자의 상황이 절대 발생하지 않아야 합니다.

시간 동기화가 작동하지 않으면 VM 시계는 오류를 누적하게 됩니다. 단 하나의 VM이 있는 경우 워크로드가 상당히 정확한 시간 기록을 요구하지 않는 한 영향은 크지 않을 수 있습니다. 하지만 대부분의 경우에 트랜잭션을 추적하는 데 시간을 사용하고 전체 배포 과정에 걸쳐 시간이 일관되어야 하는 상호 연결된 VM이 여러 개 있습니다. VM 간의 시간이 다른 경우 다음과 같은 영향이 표시됩니다.

- 인증이 실패합니다. Kerberos 또는 인증서 종속 기술 같은 보안 프로토콜은 시스템에서 일관성이 유지되는 시간에 의존합니다. 
- 로그(또는 기타 데이터)가 시간마다 다를 경우 시스템에서 발생한 상황을 파악하기는 매우 어렵습니다. 동일한 이벤트가 서로 다른 시간대에 발생되는 것 같아 상관 관계를 어렵게 만듭니다.
- 시계가 꺼지면 청구가 부정확하게 계산될 수 있습니다.

최근 개선된 시간 동기화를 사용할 수 있는 Windows Server 2016을 게스트 운영 체제로 사용하여 Windows 배포에 대한 최상의 결과를 얻을 수 있습니다.

## <a name="configuration-options"></a>구성 옵션

Azure에 호스팅된 Windows VM에 대한 시간 동기화를 구성할 수 있는 세 가지 옵션이 있습니다.

- 호스트 시간 및 time.windows.com. 이 구성은 Azure Marketplace 이미지에 사용된 기본 구성입니다.
- 호스트 전용.
- 호스트 시간을 사용하거나 사용하지 않고 다른 외부 시간 서버를 사용합니다.


### <a name="use-the-default"></a>기본값 사용

기본적으로 Windows OS VM 이미지는 두 개의 원본에서 동기화되는 w32time에 대해 구성됩니다. 

- time.windows.com에서 정보를 가져오는 NtpClient 공급자.
- VM이 유지 관리를 위해 일시 중지된 후 VM에 호스트 시간을 통신하고 수정하는 데 사용되는 VMICTimeSync 서비스. Azure 호스트는 정확한 시간을 유지하기 위해 Microsoft 소유의 Stratum 1 디바이스를 사용합니다.

w32time은 계층 수준, 루트 지연, 루트 분산, 시간 오프셋의 우선 순위로 시간 공급자를 사용합니다. 대부분의 경우에 w32time은 time.windows.com이 하위 계층을 보고하기 때문에 호스트보다time.windows.com을 우선합니다. 

도메인에 연결된 머신의 경우 도메인 자체가 시간 동기화 계층 구조를 설정하지만 포리스트 루트는 임의 위치에서 여전히 시간이 걸리며 다음 고려 사항은 여전히 유효합니다.


### <a name="host-only"></a>호스트 전용 

time.windows.com은 공용 NTP 서버이므로 시간과 동기화는 인터넷을 통해 트래픽을 전송해야 하며, 다양한 패킷 지연 시간이 시간 동기화의 품질을 저하시킬 수 있습니다. 호스트 전용 동기화로 전환하여 time.windows.com을 제거하면 경우에 따라 시간 동기화 결과를 향상시킬 수 있습니다.

기본 구성을 사용하는 시간 동기화 문제를 겪는 경우 호스트 전용 시간 동기화로 전환하는 것이 합리적입니다. 이 방법이 VM에서 시간 동기화를 향상시키는지 확인하려면 호스트 전용 동기화를 사용해 보세요. 

### <a name="external-time-server"></a>외부 시간 서버

특정 시간 동기화 요구 사항이 있는 경우 외부 시간 서버를 사용할 수도 있습니다. 외부 시간 서버는 테스트 시나리오에 유용할 수 있는 특정 시간을 제공하면서 타사 데이터센터에 호스팅된 머신을 사용하여 시간 일관성을 보장하거나, 특별한 방식으로 윤초를 처리할 수 있습니다.

외부 서버를 VMICTimeSync 서비스 및 VMICTimeProvider와 결합하여 기본 구성과 유사한 결과를 제공할 수 있습니다. 

## <a name="check-your-configuration"></a>구성 확인


NtpClient 시간 공급자가 명시적 NTP 서버(NTP) 또는 도메인 시간 동기화(NT5DS)를 사용하도록 구성되는지 확인합니다.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

VM이 NTP를 사용하는 경우 다음과 같은 출력이 표시됩니다.

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

다음과 같은 관리자 권한 명령 프롬프트 형식에서 NtpClient 시간 공급자가 사용하는 시간 서버를 확인하려면

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

VM이 기본값을 사용하는 경우 출력은 다음과 같이 표시됩니다.

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


현재 사용되고 있는 시간 공급자를 확인하려면

```
w32tm /query /source
```


확인할 수 있는 출력 및 해당 의미는 다음과 같습니다.
    
- **time.windows.com** - w32time은 기본 구성의 time.windows.com에서 시간을 가져옵니다. 시간 동기화 품질은 인터넷 연결에 따라 달라지며 패킷 지연의 영향을 받습니다. 이 출력은 기본 설정에서 생성된 일반 출력입니다.
- **VM IC 시간 동기화 공급자** - VM은 호스트에서 시간을 동기화합니다. 이는 호스트 전용 시간 동기화를 옵트인하거나 NtpServer를 현재 사용할 수 없는 경우의 일반적인 결과입니다. 
- *도메인 서버* - 현재 머신은 도메인에 있으며 해당 도메인은 시간 동기화 계층 구조를 정의합니다.
- *기타 일부 서버* - w32time은 해당 다른 서버에서 시간을 가져오도록 명시적으로 구성되었습니다. 시간 동기화 품질은 이 시간 서버 품질에 따라 달라집니다.
- **로컬 CMOS 시계** - 시계가 동기화되지 않았습니다. w32time이 다시 부팅 뒤 시작할 충분한 시간이 없는 경우 또는 모든 구성된 시간 원본을 사용할 수 없는 경우 이 출력을 얻을 수 있습니다.


## <a name="opt-in-for-host-only-time-sync"></a>호스트 전용 시간 동기화에 대한 옵트인

Azure에서는 호스트의 시간 동기화를 개선하기 위해 지속적으로 작업하고 있으며, 모든 시간 동기화 인프라가 Microsoft 소유의 데이터센터에 배치돼 있음을 보장할 수 있습니다. time.windows.com을 기본 시간 원본으로 사용하려는 기본 설정이 포함된 시간 동기화 문제가 있는 경우 다음 명령을 사용하여 호스트 전용 시간 동기화를 옵트인할 수 있습니다.

VMIC 공급자를 사용으로 표시합니다. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

NTPClient 공급자를 사용 안 함으로 표시합니다.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

w32time 서비스를 다시 시작합니다.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 및 R2 VM 

Windows Server 2012 및 Windows Server 2012 R2에는 시간 동기화에 대한 서로 다른 기본 설정이 있습니다. 기본적으로 w32time은 정확한 시간보다 서비스의 낮은 오버헤드를 선호하는 방식으로 구성됩니다. 

정확한 시간을 선호하는 최신 기본값을 사용하기 위해 Windows Server 2012 및 2012 R2 배포로 이동하려는 경우 다음 설정을 적용할 수 있습니다.

w32time 폴링을 업데이트하고 Windows Server 2016 설정과 일치하도록 간격을 업데이트합니다.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

w32time이 새 폴링 간격을 사용할 수 있으려면 NtpServers가 이를 사용하는 것으로 표시되어야 합니다. 서버에 0x1 Bitflag 마스크로 주석이 표시되는 경우에는 이 메커니즘을 재정의하고 w32time은 대신 SpecialPollInterval을 사용합니다. 지정된 NTP 서버는 0x8 플래그를 사용하거나 전혀 플래그를 사용하지 않아야 합니다.

사용된 NTP 서버에 사용되고 있는 플래그를 확인합니다.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>다음 단계

시간 동기화에 대한 자세한 세부 정보에 대한 링크는 다음과 같습니다.

- [Windows 시간 서비스 도구 및 설정](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Windows Server 2016 개선 사항](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Windows Server 2016의 정확한 시간](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [정확도 높은 환경에 대한 Windows 시간 서비스를 구성하도록 경계 지원](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


