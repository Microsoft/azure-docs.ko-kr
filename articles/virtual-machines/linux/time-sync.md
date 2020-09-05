---
title: Azure에서 Linux VM의 시간 동기화
description: Linux 가상 머신의 시간 동기화입니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 8a122a36b14bd3c5f4912387dc98585cb89ab53b
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705643"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure에서 Linux VM의 시간 동기화

시간 동기화는 보안 및 이벤트 상관 관계에서 중요합니다. 경우에 따라 시간 동기화는 분산 트랜잭션 구현에 사용됩니다. 여러 컴퓨터 시스템 간에 시간 정확도는 동기화를 통해 이루어집니다. 동기화는 재부팅 및 시간 원본 서버와 시간을 페치하는 컴퓨터 간의 네트워크 트래픽을 비롯해 여러 작업에서 영향을 받을 수 있습니다. 

Azure는 Windows Server 2016을 실행하는 인프라의 지원을 받습니다. Windows Server 2016은 시간을 수정하고 로컬 시계에 영향을 미치는 데 사용된 알고리즘을 개선하여 UTC와 동기화했습니다.  Windows Server 2016의 정확한 시간 기능은 정확한 시간을 위해 VMICTimeSync 서비스가 호스트를 통해 VM을 제어하는 방법을 크게 개선했습니다. 이러한 개선은 VM 시작 또는 VM 복원 시 시작 시간의 정확도 향상을 포함하며 대기 시간 수정을 중단합니다. 

> [!NOTE]
> Windows 시간 서비스에 대한 간략한 개요는 이 [고급 개요 비디오](https://aka.ms/WS2016TimeVideo)를 살펴보세요.
>
> 자세한 내용은 [Windows Server 2016의 정확한 시간](/windows-server/networking/windows-time-service/accurate-time)을 참조하세요. 

## <a name="overview"></a>개요

컴퓨터 시계의 정확도는 컴퓨터 시계가 UTC(협정 세계시) 표준 시간에 근접한 정도로 측정됩니다. UTC는 300년에 1초밖에 차이가 나지 않는 여러 국가의 정밀한 원자 시계 샘플로 정의됩니다. 그러나 UTC를 직접 읽으려면 특수화된 하드웨어가 필요합니다. 대신 시간 서버가 UTC로 동기화되고 다른 컴퓨터에서 액세스되어 확장성 및 안정성을 제공합니다. 모든 컴퓨터에는 사용할 시간 서버를 알고 있으며 주기적으로 컴퓨터 시계를 수정해야 하는지 여부를 확인하고 필요한 경우 시간을 조정하는 시간 동기화 서비스가 실행되고 있습니다. 

Azure 호스트는 GPS 안테나가 있는 Microsoft 소유의 Stratum 1 디바이스에서 시간을 사용하는 내부 Microsoft 시간 서버에 동기화됩니다. Azure의 가상 머신은 정확한 시간(*호스트 시간*)을 VM에 전달하기 위해 호스트에 의존할 수 있거나, VM이 직접 시간 서버 또는 둘의 조합에서 시간을 가져올 수 있습니다. 

독립 실행형 하드웨어에서 Linux OS는 부팅 시 호스트 하드웨어 시계만 읽습니다. 그 이후에는 Linux 커널의 인터럽트 타이머를 사용하여 시계를 유지 관리합니다. 이 구성에서 시계는 시간이 지나면서 변경됩니다. Azure의 최신 Linux 배포에서 VM은 LIS(Linux 통합 서비스)에 포함된 VMICTimeSync 공급자를 사용하여 호스트에서 시계 업데이트를 더 자주 쿼리할 수 있습니다.

호스트와 가상 머신의 상호 작용은 시계에도 영향을 줄 수 있습니다. [메모리 보존 유지 관리](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) 중에는 VM이 최대 30초 동안 일시 중지됩니다. 예를 들어 유지 관리를 시작하면 먼저 VM 시계는 오전 10:00:00시를 표시한 후, 28초간 지속됩니다. VM이 다시 시작되면 VM 시계는 여전히 오전 10:00:00시를 표시한 다음, 28초가 해제됩니다. 이를 수정하려면 VMICTimeSync 서비스가 호스트에서 발생하는 상황을 모니터링하고 VM에서 발생되는 변경을 보완하도록 요구합니다.

시간 동기화가 작동하지 않으면 VM 시계는 오류를 누적하게 됩니다. 단 하나의 VM이 있는 경우 워크로드가 상당히 정확한 시간 기록을 요구하지 않는 한 영향은 크지 않을 수 있습니다. 하지만 대부분의 경우에 트랜잭션을 추적하는 데 시간을 사용하고 전체 배포 과정에 걸쳐 시간이 일관되어야 하는 상호 연결된 VM이 여러 개 있습니다. VM 간의 시간이 다른 경우 다음과 같은 영향이 표시됩니다.

- 인증이 실패합니다. Kerberos 또는 인증서 종속 기술 같은 보안 프로토콜은 시스템에서 일관성이 유지되는 시간에 의존합니다.
- 로그(또는 기타 데이터)가 시간마다 다를 경우 시스템에서 발생한 상황을 파악하기는 매우 어렵습니다. 동일한 이벤트가 서로 다른 시간대에 발생되는 것 같아 상관 관계를 어렵게 만듭니다.
- 시계가 꺼지면 청구가 부정확하게 계산될 수 있습니다.



## <a name="configuration-options"></a>구성 옵션

일반적으로 Azure에 호스트된 Linux VM의 시간 동기화를 구성할 수 있는 세 가지 방법이 있습니다.

- Azure Marketplace 이미지의 기본 구성은 NTP 시간 및 VMICTimeSync 호스트 시간을 둘 다 사용합니다. 
- VMICTimeSync를 사용한 호스트 전용.
- VMICTimeSync 호스트 시간을 사용하거나 사용하지 않고 다른 외부 시간 서버를 사용합니다.


### <a name="use-the-default"></a>기본값 사용

기본적으로 Linux의 대부분 Azure Marketplace 이미지는 두 가지 원본에서 동기화하도록 구성됩니다. 

- NTP 서버에서 시간을 가져오는 1차로서 NTP. 예를 들어 Ubuntu 16.04 LTS Marketplace 이미지는 **ntp.ubuntu.com**을 사용합니다.
- VM이 유지 관리를 위해 일시 중지된 후 VM에 호스트 시간을 통신하고 수정하는 데 사용되는 2차로서 VMICTimeSync 서비스. Azure 호스트는 정확한 시간을 유지하기 위해 Microsoft 소유의 Stratum 1 디바이스를 사용합니다.

최신 Linux 배포판에서 VMICTimeSync 서비스는 PTP (Precision Time Protocol) 하드웨어 클록 원본을 제공 하지만 이전 배포는이 클록 원본을 제공 하지 않을 수 있으며 호스트에서 시간을 가져오기 위해 NTP로 대체 합니다.

NTP가 올바르게 동기화하고 있는지 확인하려면 `ntpq -p` 명령을 실행합니다.

### <a name="host-only"></a>호스트 전용 

time.windows.com 및 ntp.ubuntu.com과 같은 NTP 서버는 공용이므로 NTP 서버와 시간을 동기화하려면 인터넷을 통해 트래픽을 보내야 합니다. 패킷 지연의 변화는 시간 동기화의 품질에 부정적인 영향을 줄 수 있습니다. 호스트 전용 동기화로 전환 하 여 NTP를 제거 하면 시간 동기화 결과를 향상 시킬 수 있습니다.

기본 구성을 사용하는 시간 동기화 문제를 겪는 경우 호스트 전용 시간 동기화로 전환하는 것이 합리적입니다. 이 방법이 VM에서 시간 동기화를 향상시키는지 확인하려면 호스트 전용 동기화를 사용해 보세요. 

### <a name="external-time-server"></a>외부 시간 서버

특정 시간 동기화 요구 사항이 있는 경우 외부 시간 서버를 사용할 수도 있습니다. 외부 시간 서버는 테스트 시나리오에 유용할 수 있는 특정 시간을 제공하면서 타사 데이터 센터에 호스트된 머신을 사용하여 시간 일관성을 보장하거나, 특별한 방식으로 윤초를 처리할 수 있습니다.

외부 시간 서버를 VMICTimeSync 서비스와 결합하여 기본 구성과 유사한 결과를 제공할 수 있습니다. 외부 시간 서버를 VMICTimeSync와 결합하는 기능은 유지 관리를 위해 VM이 일시 중지될 때 발생할 수 있는 문제를 처리하는 가장 좋은 옵션입니다. 

## <a name="tools-and-resources"></a>도구 및 리소스

시간 동기화 구성을 검사하기 위한 몇 가지 기본 명령이 있습니다. Linux 배포 설명서는 해당 배포의 시간 동기화를 구성하는 최상의 방법에 대한 자세한 내용을 제공합니다.

### <a name="integration-services"></a>통합 서비스

통합 서비스(hv_utils)가 로드되었는지 확인합니다.

```bash
lsmod | grep hv_utils
```
다음과 유사한 내용이 표시되어야 합니다.

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Hyper-V 통합 서비스 디먼이 실행 중인지 확인합니다.

```bash
ps -ef | grep hv
```

다음과 유사한 내용이 표시되어야 합니다.

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>PTP 클록 소스 확인

최신 버전의 Linux, PTP(Precision Time Protocol) 시계 원본은 VMICTimeSync 공급자의 일부로 사용할 수 있습니다. 이전 버전의 Red Hat Enterprise Linux 또는 CentOS 7.x에서 [Linux 통합 서비스](https://github.com/LIS/lis-next)를 다운로드하여 업데이트된 드라이버를 설치하는 데 사용할 수 있습니다. PTP 클록 원본을 사용할 수 있는 경우 Linux 장치는/dev/ptp*x*형식이 됩니다. 

사용 가능한 PTP 시계 원본을 확인합니다.

```bash
ls /sys/class/ptp
```

이 예제에서 반환된 값은 *ptp0*이므로 이를 사용하여 시계 이름을 확인합니다. 디바이스를 확인하려면 시계 이름을 확인합니다.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

`hyperv`을 반환해야 합니다.

### <a name="chrony"></a>chrony

Ubuntu 19.10 이상 버전, Red Hat Enterprise Linux 및 CentOS .x에서 [chrony](https://chrony.tuxfamily.org/) 는 PTP 원본 클록을 사용 하도록 구성 됩니다. Chrony 대신 이전 버전의 Linux 릴리스에서는 PTP (Network Time Protocol daemon)를 사용 합니다 .이 디먼은 PTP 원본을 지원 하지 않습니다. 이러한 릴리스에서 PTP를 사용 하도록 설정 하려면 다음 코드를 사용 하 여 chrony를 수동으로 설치 하 고 구성 해야 합니다 (chrony).

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Ubuntu 및 NTP에 대 한 자세한 내용은 [시간 동기화](https://help.ubuntu.com/lts/serverguide/NTP.html)를 참조 하세요.

Red Hat 및 NTP에 대 한 자세한 내용은 [Ntp 구성](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP)을 참조 하세요. 

Chrony에 대 한 자세한 내용은 [Chrony 사용](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)을 참조 하세요.

Chrony 및 VMICTimeSync 원본을 동시에 사용 하도록 설정 하는 경우 다른 원본을 백업으로 설정 하는 것을 **선호**하는 것으로 표시할 수 있습니다. NTP 서비스는 오랜 기간이 지난 후에만 시계의 큰 불일치(skew)를 업데이트하므로 VMICTimeSync는 일시 중지된 VM 이벤트에서 NTP 기반 도구만 사용하는 경우보다 훨씬 더 빠르게 시계를 복구합니다.

기본적으로 chronyd는 시스템 클록을 가속화 하거나 느리게 하 여 시간 드리프트를 수정 합니다. 드리프트가 너무 커지면 chrony는 드리프트를 수정 하지 못합니다. 이를 해결 하기 위해 `makestep` **/etc/chrony.conf** 의 매개 변수를 변경 하 여 드리프트가 지정 된 임계값을 초과 하는 경우 시간 동기화를 강제로 수행할 수 있습니다.

 ```bash
makestep 1.0 -1
```

여기서 chrony는 드리프트가 1 초 보다 큰 경우 시간 업데이트를 강제로 수행 합니다. 변경 내용을 적용 하려면 chronyd 서비스를 다시 시작 합니다.

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

19.10 이전 SUSE 및 Ubuntu 릴리스에서는 [systemd](https://www.freedesktop.org/wiki/Software/systemd/)를 사용 하 여 시간 동기화를 구성 합니다. Ubuntu에 대 한 자세한 내용은 [시간 동기화](https://help.ubuntu.com/lts/serverguide/NTP.html)를 참조 하세요. SUSE에 대 한 자세한 내용은 [SUSE Linux Enterprise Server 12 SP3 릴리스](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)정보의 4.5.8 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Windows Server 2016의 정확한 시간](/windows-server/networking/windows-time-service/accurate-time)을 참조하세요.


