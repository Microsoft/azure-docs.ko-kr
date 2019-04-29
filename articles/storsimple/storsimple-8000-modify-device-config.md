---
title: StorSimple 8000 시리즈 디바이스 구성 수정 | Microsoft Docs
description: 이미 배포된 StorSimple 디바이스를 다시 구성하기 위해 StorSimple 디바이스 관리자 서비스를 사용하는 방법에 대해 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632237"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스 구성 수정

## <a name="overview"></a>개요

**설정** 블레이드의 Azure Portal **디바이스 설정** 섹션에는 StorSimple 디바이스 관리자 서비스에서 관리하는 StorSimple 디바이스에서 다시 구성할 수 있는 모든 디바이스 매개 변수가 포함되어 있습니다. 이 자습서에서는 **설정** 블레이드를 사용하여 다음 디바이스 수준의 작업을 수행하는 방법을 설명합니다.

* 디바이스 이름 수정
* 디바이스 시간 설정 수정
* 보조 DNS 할당
* 네트워크 인터페이스 수정
* IP 교체 또는 재할당

## <a name="modify-device-friendly-name"></a>디바이스 이름 수정

Azure Portal을 사용하여 디바이스 이름을 변경하거나 사용자가 원하는 고유한 이름을 할당할 수 있습니다. 디바이스의 **일반 설정** 블레이드를 사용하여 디바이스 이름을 수정합니다. 이름은 모든 문자를 사용할 수 있으며 최대 64자 길이로 만들 수 있습니다.

> [!NOTE] 
> 디바이스 설정이 완료되기 전에 Azure Portal에서 디바이스 이름을 수정할 수 있습니다. 최소 디바이스 설정이 완료되면 디바이스 이름을 변경할 수 없습니다.

![일반 설정의 디바이스 이름](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

StorSimple 디바이스 관리자 서비스에 연결된 StorSimple 디바이스에는 기본 이름이 할당됩니다. 기본 이름에는 일반적으로 디바이스의 일련 번호가 반영됩니다. 예를 들어, 기본 디바이스 이름은 8600-SHX0991003G44HT와 같이 다음을 나타내는 15자 길이의 문자입니다.

* **8600** – 디바이스 모델을 나타냅니다.
* **SHX** – 제조 사이트를 나타냅니다.
* **0991003** - 특정 제품을 나타냅니다.
* **G44HT**- 마지막 5자리 숫자는 고유한 일련 번호를 만들도록 증가됩니다. 순차적인 집합이 아닐 수 있습니다.

## <a name="modify-device-description"></a>디바이스 설명 수정

디바이스의 **일반 설정** 블레이드를 사용하여 디바이스 설명을 수정합니다.

![일반 설정의 디바이스 설명](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

일반적으로 디바이스 설명은 디바이스의 소유자 및 물리적 위치를 식별하는 데 도움이 됩니다. 설명 필드에는 256자 미만의 문자가 포함되어야 합니다.

## <a name="modify-time-settings"></a>시간 설정 수정

디바이스는 클라우드 저장소 서비스 공급자를 사용하여 인증하기 위해 시간을 동기화해야 합니다. 디바이스의 **일반 설정** 블레이드를 사용하여 디바이스 시간 설정을 수정합니다.

![일반 설정의 디바이스 설명](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 드롭다운 목록에서 표준 시간대를 선택합니다. 최대 두 개의 NTP(Network Time Protocol) 서버를 지정할 수 있습니다.

 - **기본 NTP 서버** - 이 구성은 필수 항목이며 디바이스를 구성하기 위해 StorSimple용 Windows PowerShell을 사용할 때 지정됩니다. 기본 Windows 서버 **time.windows.com** 을 NTP 서버로 지정할 수 있습니다. Azure Portal을 통해 기본 NTP 서버 구성을 볼 수 있지만, 변경하려면 Windows PowerShell 인터페이스를 사용해야 합니다. `Set-HcsNTPClientServerAddress` cmdlet을 사용하여 디바이스의 기본 NTP 서버를 수정합니다. 자세한 내용을 보려면 [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet의 구문으로 이동하세요.

- **보조 NTP 서버** - 이 구성은 선택 사항입니다. 포털을 사용하여 보조 NTP 서버를 구성할 수 있습니다.

NTP 서버 구성 시 네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다. 공용 NTP 서버를 지정하는 경우, NTP 트래픽이 외부 네트워크 간에 여행할 수 있도록 네트워크 방화벽 및 기타 보안 디바이스를 구성해야 합니다. 양방향 NTP 트래픽이 허용되지 않는 경우 내부 NTP 서버(Windows 도메인 컨트롤러가 이 기능을 제공)를 사용해야 합니다. 디바이스가 시간을 동기화할 수 없는 경우 클라우드 저장소 공급자와 통신하지 못할 수 있습니다.

공용 NTP 서버의 목록을 보려면 [NTP 서버 웹](https://support.ntp.org/bin/view/Servers/WebHome)으로 이동하세요.

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>디바이스를 다른 표준 시간대에 배포하는 경우 어떻게 되나요?

디바이스를 다른 표준 시간대에 배포하는 경우 디바이스 표준 시간대가 변경됩니다. 디바이스 표준 시간대를 사용하는 모든 백업 정책이 있는 백업 정책이 새 표준 시간대에 따라 자동으로 조정됩니다. 사용자 개입이 필요 없습니다.

## <a name="modify-dns-settings"></a>DNS 설정 수정

DNS 서버는 디바이스가 클라우드 저장소 서비스 공급자와 통신하려고 할 때 사용됩니다. 디바이스의 **네트워크 설정** 블레이드를 사용하여 구성된 DNS 설정을 보고 수정합니다. 

![네트워크 설정에서 DNS 설정](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

고가용성을 위해 초기 디바이스를 배포하는 동안 기본 및 보조 DNS 서버를 모두 구성해야 합니다.

**기본 DNS 서버** - 먼저 StorSimple용 Windows PowerShell을 사용하여 초기 설정 중에 기본 DNS 서버를 지정합니다. Windows PowerShell 인터페이스를 통해서만 기본 DNS 서버를 다시 구성할 수 있습니다. `Set-HcsDNSClientServerAddress` cmdlet을 사용하여 디바이스의 기본 DNS 서버를 수정합니다. 자세한 내용을 보려면 [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet의 구문으로 이동하세요.

**보조 DNS 서버** - 보조 DNS 서버를 수정하려면 디바이스의 Windows PowerShell 인터페이스에서 `Set-HcsDNSClientServerAddress` cmdlet 또는 Azure Portal에서 StorSimple 디바이스의 **네트워크 설정** 블레이드를 사용합니다.

Azure Portal에서 보조 DNS 서버를 수정하려면 다음 단계를 수행합니다.

1. StorSimple 디바이스 관리자 서비스로 이동합니다. 디바이스 목록에서 디바이스를 선택하고 클릭합니다.

2. **설정** 블레이드에서 **디바이스 설정 &gt; 네트워크**로 이동합니다. 그러면 **네트워크 설정** 블레이드를 엽니다. **DNS 설정** 타일을 클릭합니다. 보조 DNS 서버 IP 주소를 수정합니다.

    ![보조 DNS 서버 IP 주소 수정](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. 명령 모음에서 **저장**을 클릭하고 확인하라는 메시지가 표시되면 **확인**을 클릭합니다.

    ![변경 내용 저장 및 확인](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>네트워크 인터페이스 수정

디바이스에는 6개의 디바이스 네트워크 인터페이스가 있으며, 그 중 4개는 1GbE, 2개는 10GbE입니다. 이러한 인터페이스는 DATA 0부터 DATA 5로 레이블이 지정됩니다. DATA 0, DATA 1, DATA 4 및 DATA 5는 1GbE인 반면, DATA 2 및 DATA 3은 10GbE 네트워크 인터페이스입니다.

**네트워크 설정** 블레이드를 사용하여 사용할 각 인터페이스를 구성합니다.

![네트워크 설정을 통해 네트워크 인터페이스 구성](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

고가용성을 위해 디바이스에 둘 이상의 iSCSI 인터페이스 및 두 개의 클라우드 지원 인터페이스가 있는 것이 좋습니다. 권장 사항이지만 사용하지 않는 인터페이스를 사용하지 않도록 설정할 필요는 없습니다.

각 네트워크 인터페이스의 경우 다음 매개 변수가 표시됩니다.

* **속도** – 사용자가 구성할 수 있는 매개 변수가 아닙니다. DATA 0, DATA 1, DATA 4 및 DATA 5는 1GbE인 반면, DATA 2 및 DATA 3은 10GbE 인터페이스입니다.
  
  > [!NOTE]
  > 속도 및 이중 교환 패턴은 항상 자동으로 협상합니다. Jumbo 프레임이 지원되지 않습니다.
  
* **인터페이스 상태** – 인터페이스를 설정하거나 해제할 수 있습니다. 설정 경우 디바이스가 인터페이스를 사용하려고 합니다. 네트워크에 연결 및 사용되는 해당 인터페이스만 설정하는 것이 좋습니다. 사용하지 않는 모든 인터페이스는 해제합니다.
* **인터페이스 유형** – 이 매개 변수를 사용하면 클라우드 저장소 트래픽에서 iSCSI 트래픽을 격리할 수 있습니다. 이 매개 변수는 다음 중 하나일 수 있습니다.
  
  * **클라우드 사용** – 사용하도록 설정하면 디바이스가 클라우드와 통신하는 데 이 인터페이스를 사용합니다.
  * **iSCSI 사용** – 사용하도록 설정하면 디바이스가 iSCSI 호스트와 통신하는 데 이 인터페이스를 사용합니다.
    
    클라우드 저장소 트래픽에서 iSCSI 트래픽을 격리하는 것이 좋습니다. 또한 호스트가 디바이스와 동일한 서브넷 내에 있는 경우, 게이트웨이를 할당할 필요가 없습니다. 하지만 호스트가 디바이스와 다른 서브넷에 있는 경우, 게이트웨이를 할당해야 합니다.
* **IP 주소** – 네트워크 인터페이스를 구성할 때 VIP(가상 IP)를 구성해야 합니다. IPv4나 IPv6 또는 둘 다가 될 수 있습니다. IPv4 및 IPv6 주소 모음이 모두 디바이스 네트워크 인터페이스를 지원합니다. IPv4를 사용하는 경우 십진수 표기법으로 32비트 IP 주소(*xxx.xxx.xxx.xxx*)를 지정합니다. IPv6를 사용하는 경우 4자리 접두사를 제공하면 해당 접두사를 기반으로 사용자의 디바이스 네트워크 인터페이스에 대해 128비트 주소가 자동으로 생성됩니다.
* **서브넷** – 서브넷 마스크를 참조하고 Widnows PowerShell 인터페이스를 통해 구성됩니다.
* **게이트웨이** – 동일한 IP 주소 공간(서브넷) 내에 있지 않은 노드와 통신하려고 할 때 이 인터페이스가 사용하는 기본 게이트웨이입니다. 기본 게이트웨이는 서브넷 마스크를 통해 결정된 대로 인터페이스 IP 주소와 동일한 주소 공간(서브넷)에 있어야 합니다.
* **고정 IP 주소** - 이 필드는 DATA 0 인터페이스를 구성하는 동안에만 사용할 수 있습니다. 업데이트 또는 디바이스 문제 해결과 같은 작업의 경우 디바이스 컨트롤러에 직접 연결해야 할 수 있습니다. 고정 IP 주소는 디바이스에서 활성 및 수동 컨트롤러에 모두 액세스하는 데 사용할 수 있습니다.

> [!NOTE]
> * 작업을 제대로 수행하려면 각 디바이스 인터페이스가 연결된 각 스위치에서 인터페이스 속도 및 이중을 확인합니다. 스위치 인터페이스는 기가비트 이더넷(1000Mbps)과 협상하거나 이에 대해 구성되며 전이중입니다. 더 느린 속도 또는 반이중에서의 인터페이스 운영은 성능 문제를 야기합니다.
> * 중단 및 가동 중지를 최소화하려면 디바이스의 iSCSI 네트워크 인터페이스가 연결될 각각의 스위치 포트에서 포트패스트를 설정하는 것이 좋습니다. 이렇게 하면 장애 조치 시 네트워크 연결을 신속하게 설정할 수 있습니다.

### <a name="configure-data-0"></a>DATA 0 구성

DATA 0은 기본적으로 클라우드가 지원됩니다. DATA 0을 구성할 때 고정된 두 IP 주소를 각 컨트롤러마다 하나씩 구성해야 합니다. 이러한 고정 IP 주소는 디바이스 컨트롤러에 직접 액세스하는 데 사용할 수 있으며 디바이스에서 업데이트를 설치하거나, 가비지 수집이 제대로 작동하게 하거나, 문제 해결을 목적으로 컨트롤러에 액세스할 때 유용합니다.

DATA 0 설정 블레이드를 통해 고정된 IP 컨트롤러를 다시 구성할 수 있습니다.

![네트워크 인터페이스 구성 - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> 컨트롤러의 고정 IP 주소는 디바이스에 대한 업데이트를 서비스하고 공간 확보 알고리즘(가비지 수집)이 제대로 작동하게 하기 위해 사용됩니다. 따라서 고정 IP는 라우팅 가능하고 인터넷에 연결할 수 있어야 합니다.

### <a name="configure-data-1---data-5"></a>DATA 1~DATA 5 구성

DATA 1~DATA 5 네트워크 인터페이스의 경우 다음 스크린샷에 표시된 대로 모든 네트워크 설정을 구성할 수 있습니다.

![네트워크 인터페이스 구성 DATA 1~DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>IP 교체 또는 재할당

현재, 컨트롤러의 모든 네트워크 인터페이스에 사용 중인 VIP가 할당된 경우(해당 네트워크 내의 동일한 디바이스 또는 다른 디바이스를 통해) 해당 컨트롤러는 장애 조치됩니다. 디바이스 네트워크 인터페이스의 VIP를 교환하거나 다시 할당하는 경우 중복 IP 상황을 만들 수 있는 만큼 적절한 절차를 따라야 합니다.

다음 단계를 수행하여 네트워크 인터페이스에 대한 VIP를 교체하거나 재할당합니다.

#### <a name="to-reassign-ips"></a>IP 재할당

1. 두 인터페이스에 대한 IP 주소 선택을 취소합니다.
2. IP 주소 선택을 취소한 후 해당 인터페이스에 새 IP 주소를 할당합니다.

## <a name="next-steps"></a>다음 단계

* [StorSimple 디바이스에 대해 MPIO를 구성](storsimple-8000-configure-mpio-windows-server.md)하는 방법을 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 알아봅니다.

