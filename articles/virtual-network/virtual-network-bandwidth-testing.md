---
title: Azure VM 네트워크 처리량 테스트
titlesuffix: Azure Virtual Network
description: Azure Virtual Machine 네트워크 처리량을 테스트하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743083"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>대역폭/처리량 테스트(NTTTCP)

Azure에서 네트워크 처리량 성능을 테스트하는 경우 테스트할 네트워크를 대상으로 지정하고 성능에 영향을 줄 수 있는 기타 리소스의 사용을 최소화하는 도구를 사용하는 것이 가장 좋습니다. NTTTCP가 권장됩니다.

이 도구를 같은 크기의 두 Azure VM에 복사합니다. 한 VM은 송신기 역할을 하고 다른 VM은 수신기 역할을 합니다.

#### <a name="deploying-vms-for-testing"></a>테스트를 위해 VM 배포
이 테스트의 목적에 맞게, 이러한 두 VM은 동일한 클라우드 서비스 또는 동일한 가용성 집합에 있어야 합니다. 그래야 내부 IP를 사용하고 테스트에서 부하 분산 장치를 제외할 수 있습니다. VIP로 테스트할 수 있지만 이러한 종류의 테스트는 이 문서에서 다루지 않습니다.

수신기의 IP 주소를 기록해 둡니다. 해당 IP를 "a.b.c.r"로 지칭하겠습니다.

VM의 코어 수를 기록해 둡니다. 이것을 "\#num\_cores"로 지칭하겠습니다.

송신기 VM과 수신기 VM에서 300초(또는 5분) 동안 NTTTCP 테스트를 실행합니다.

팁: 이 테스트를 처음 설정할 때는 더 빠른 피드백을 얻기 위해 테스트 기간을 더 짧게 유지할 수 있습니다. 이 도구가 예상대로 작동하면 가장 정확한 결과를 얻기 위해 테스트 기간을 300초로 연장합니다.

> [!NOTE]
> 송신기 **및** 수신기는 **동일한** 테스트 기간 매개 변수(-t)를 지정해야 합니다.

10초 동안 단일 TCP 스트림을 테스트하려면

수신기 매개 변수: ntttcp -r -t 10 -P 1

송신기 매개 변수: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> 이전 샘플은 구성을 확인하는 데만 사용해야 합니다. 올바른 테스트 예제는 이 문서 뒷부분에 나옵니다.

## <a name="testing-vms-running-windows"></a>Windows가 실행되는 VM 테스트:

#### <a name="get-ntttcp-onto-the-vms"></a>VM에 NTTTCP를 가져옵니다.

최신 버전을 다운로드합니다(<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>).

또는 이동된 경우 검색합니다(<https://www.bing.com/search?q=ntttcp+download>\<). 첫 번째로 검색되는 항목입니다.

NTTTCP를 c:\\tools와 같은 별도 폴더에 추가하는 것을 고려합니다.

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>NTTTCP가 Windows 방화벽을 통과하도록 허용
수신기에서 Windows 방화벽에 NTTTCP 트래픽이 도착하도록 허용 규칙을 만듭니다. 특정 TCP 포트에서 인바운드를 허용하는 것보다 이름으로 전체 NTTTCP 프로그램을 허용하는 것이 가장 쉽습니다.

다음과 같이 ntttcp가 Windows 방화벽을 통과하도록 허용합니다.

netsh advfirewall firewall add rule program=\<PATH\>\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

예를 들어 ntttcp.exe를 "c:\\tools" 폴더에 복사한 경우 명령은 다음과 같습니다. 

netsh advfirewall firewall add rule program=c:\\tools\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>NTTTCP 테스트 실행

수신기에서 NTTTCP를 시작합니다(PowerShell이 아닌 **CMD에서 실행**).

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

VM에 4개의 코어가 있고 IP 주소가 10.0.0.4이면 다음과 같이 표시됩니다.

ntttcp -r –m 8,\*,10.0.0.4 -t 300


수신기에서 NTTTCP를 시작합니다(PowerShell이 아닌 **CMD에서 실행**).

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

결과를 기다립니다.


## <a name="testing-vms-running-linux"></a>LINUX가 실행되는 VM 테스트:

nttcp-for-linux를 사용합니다. <https://github.com/Microsoft/ntttcp-for-linux>에서 사용할 수 있습니다.

Linux VM(송신기 및 수신기 둘 다)에서 다음 명령을 실행하여 VM에서 ntttcp-for-linux를 준비합니다.

CentOS - Git 설치:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Git 설치:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
둘 다에서 만들고 설치합니다.
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Windows 예제와 같이 Linux 수신기의 IP를 10.0.0.4로 가정합니다.

수신기에서 NTTTCP-for-Linux를 시작합니다.

``` bash
ntttcp -r -t 300
```

그런 후 송신기에서 다음을 실행합니다.

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
시간 매개 변수를 지정하지 않을 경우 기본 테스트 시간은 60초입니다.

## <a name="testing-between-vms-running-windows-and-linux"></a>Windows 및 LINUX가 실행되는 VM 간의 테스트:

이 시나리오에서는 테스트를 실행할 수 있도록 비동기화 모드를 사용하도록 설정합니다. Linux의 경우 **-N 플래그**, Windows의 경우 **-ns 플래그**를 사용하여 설정합니다.

#### <a name="from-linux-to-windows"></a>Linux에서 Windows로:

Receiver \<Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

보낸 사람 \<Linux >:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Windows에서 Linux로:

Receiver \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Sender \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>클라우드 서비스 인스턴스 테스트:
다음 섹션을 ServiceDefinition.csdef에 추가해야 합니다.
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>다음 단계
* 결과에 따라 시나리오에 대해 [네트워크 처리량 컴퓨터를 최적화](virtual-network-optimize-network-bandwidth.md)할 수 있는 시간적 여유가 있을 수 있습니다.
* [가상 머신에 대역폭이 할당되는 방법](virtual-machine-network-throughput.md)을 알아봅니다.
* [Azure Virtual Network FAQ(질문과 대답)](virtual-networks-faq.md)에 대해 자세히 알아보기
