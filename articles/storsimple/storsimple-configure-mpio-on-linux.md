---
title: StorSimple Linux 호스트에서 MPIO 구성| Microsoft Docs
description: CentOS 6.6를 실행하는 Linux 호스트에 연결된 StorSimple에서 MPIO 구성
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: b968cc29a7139a4a6db5d2dea8dd6f8f4e1c7ccd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630787"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>CentOS를 실행하는 StorSimple 호스트에서 MPIO 구성
이 문서에서는 Centos 6.6 호스트 서버에서 다중 경로 IO(MPIO)를 구성하는 데 필요한 단계를 설명합니다. 호스트 서버는 iSCSI 초기자를 통해 고가용성용 Microsoft Azure StorSimple 디바이스에 연결됩니다. StorSimple 볼륨에 대한 다중 경로 디바이스 및 특정 설치의 자동 검색을 자세히 설명합니다.

이 절차는 StorSimple 8000 시리즈 디바이스의 모든 모델에 적용할 수 있습니다.

> [!NOTE]
> StorSimple Cloud Appliance에 이 절차를 사용할 수 없습니다. 자세한 내용은 클라우드 어플라이언스에 호스트 서버를 구성하는 방법을 참조하세요.


## <a name="about-multipathing"></a>다중 경로에 대해
다중 경로 기능을 사용하면 호스트 서버와 저장 디바이스 간의 여러 I/O 경로를 구성할 수 있습니다. 이러한 I/O 경로는 별도 케이블, 스위치, 네트워크 인터페이스 및 컨트롤러를 포함할 수 있는 물리적 SAN 연결입니다. 다중 경로는 I/O 경로를 집계하여 집계된 모든 경로와 연관된 새 디바이스를 구성합니다.

다중 경로의 목적은 두 가지입니다.

* **고가용성**: I/O 경로 (예: 케이블, 스위치, 네트워크 인터페이스 또는 컨트롤러)의 모든 요소에 실패 하면 대체 경로 제공 합니다.
* **부하 분산**: 저장소 장치의 구성에 따라 I/O 경로에서 부하를 감지 하 고 동적으로 이러한 부하를 리 밸 러 싱 하 여 성능을 향상 있습니다.

### <a name="about-multipathing-components"></a>다중 경로 구성 요소에 대해
Linux에서 다중 경로는 아래에 정리된 커널 구성 요소 및 사용자 공간 구성 요소로 구성되어 있습니다.

* **커널**: 기본 구성 요소를 *장치 매퍼* I/O의 경로 조정 하 고 경로 및 경로 그룹에 대 한 장애 조치를 지원 합니다.

* **사용자 공간**: 이들은 *다중 경로 도구* 장치 매퍼 다중 경로 모듈이 수행할 작업을 지시 하 여 다중 경로인 장치를 관리 하는 합니다. 도구는 다음으로 구성됩니다.
   
   * **다중 경로**: 다중 경로인 디바이스를 나열하고 구성합니다.
   * **Multipathd**: 다중 경로를 실행하고 경로를 모니터링하는 데몬입니다.
   * **Devmap-name**: devmaps에 대한 udev에 의미 있는 디바이스 이름을 제공합니다.
   * **Kpartx**: 선형 devmaps 디바이스 파티션에 매핑하여 분할 가능한 다중 경로 맵을 만듭니다.
   * **Multipath.conf**: 기본 제공 구성 테이블을 덮어쓰는 데 사용되는 다중 경로 데몬에 대한 구성 파일입니다.

### <a name="about-the-multipathconf-configuration-file"></a>Multipath.conf 구성 파일에 대해
구성 파일 `/etc/multipath.conf` 은 사용자를 구성할 수 있는 다양한 다중 경로 기능을 만듭니다. `multipath` 명령 및 커널 데몬 `multipathd`은 이 파일에 있는 정보를 사용합니다. 다중 경로 디바이스를 구성하는 동안에만 파일을 참고합니다. `multipath` 명령을 실행하기 전에 모든 변경 내용이 적용되는지 확인합니다. 나중에 파일을 수정하면 적용하려는 변경 내용에 다중 경로를 중지하고 다시 시작해야 합니다.

multipath.conf에는 다섯 가지 섹션이 있습니다.

- **시스템 수준 기본값** *(기본값)*: 시스템 수준 기본값을 재정의할 수 있습니다.
- **장치 블랙 리스트에 올랐습니다** *(블랙 리스트)*: 장치 매퍼에서 제어 하지 말아야 하는 장치 목록을 지정할 수 있습니다.
- **블랙 리스트 예외** *(blacklist_exceptions)*: 블랙 리스트에 나열 된 경우에 다중 경로 장치로 취급할 수를 특정 장치를 식별할 수 있습니다.
- **저장소 컨트롤러 특정 설정** *(장치)*: 공급 업체 및 제품 정보를 포함 하는 장치에 적용 될 구성 설정을 지정할 수 있습니다.
- **장치 특정 설정** *(다중 경로)*: 이 섹션에서는 개별 Lun에 대 한 구성 설정을 미세 조정 하기 위해 사용할 수 있습니다.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Linux 호스트에 연결된 StorSimple에서 다중 경로 구성
고가용성 및 부하 분산을 위해 Linux 호스트에 연결된 StorSimple 디바이스를 구성할 수 있습니다. 예를 들어 Linux 호스트에 SAN에 연결된 두 개의 인터페이스가 있고 디바이스에 SAN에 연결된 두 개의 인터페이스가 있으면 이러한 인터페이스는 동일한 서브넷에 있고 사용 가능한 4개의 경로가 있습니다. 그러나 디바이스의 각 데이터 인터페이스 및 호스트 인터페이스가 다른 IP 서브넷에 있으면(또 라우팅할 수 없음) 2개의 경로만 사용할 수 있습니다. 다중 경로를 구성하여 자동으로 사용 가능한 모든 경로를 검색하고 해당 경로에 대한 부하를 분산하는 알고리즘을 선택하며 StorSimple 전용 볼륨에 특정 구성 설정을 적용한 다음 다중 경로를 설정 및 확인할 수 있습니다.

다음 절차는 두 네트워크 인터페이스가 있는 StorSimple 디바이스가 두 네트워크 인터페이스가 있는 호스트에 연결된 경우 다중 경로를 구성하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 섹션은 CentOS 서버 및 StorSimple 디바이스에 대한 필수 구성 요소를 자세히 설명합니다.

### <a name="on-centos-host"></a>CentOS 호스트에서
1. CentOS 호스트에 사용 가능한 2개의 네트워크 인터페이스가 있는지 확인합니다. 형식:
   
    `ifconfig`
   
    다음 예에서는 두 가지 네트워크 인터페이스(`eth0` 및 `eth1`)가 호스트에 있는 경우 출력을 보여줍니다.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
1. CentOS 서버에 *iSCSI-initiator-utils* 를 설치합니다. 다음 단계를 수행하여 *iSCSI-initiator-utils*를 설치합니다.
   
   1. CentOS 호스트에 `root` 로 로그온합니다.
   1. *iSCSI-initiator-utils*를 설치합니다. 형식:
      
       `yum install iscsi-initiator-utils`
   1. *iSCSI-Initiator-utils* 를 성공적으로 설치한 후에 iSCSI 서비스를 시작합니다. 형식:
      
       `service iscsid start`
      
       경우에 따라 `iscsid`이 실제로 시작되지 않을 수 있고 `--force` 옵션이 필요할 수 있습니다.
   1. 부팅 시간 동안 iSCSI 초기자를 사용하도록 설정하려면 `chkconfig` 명령을 사용하여 서비스를 사용할 수 있습니다.
      
       `chkconfig iscsi on`
   1. 제대로 설치되었는지 확인하려면 다음 명령을 실행합니다.
      
       `chkconfig --list | grep iscsi`
      
       샘플 출력은 다음과 같습니다.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       위의 예제에서 iSCSI 환경이 실행 수준 2, 3, 4 및 5에서 부팅 시간에 실행된 것을 확인할 수 있습니다.
1. *device-mapper-multipath*를 설치합니다. 형식:
   
    `yum install device-mapper-multipath`
   
    설치가 시작됩니다. 확인하라는 메시지가 표시되면 **Y** 를 입력합니다.

### <a name="on-storsimple-device"></a>StorSimple 디바이스에서
StorSimple 디바이스에는 다음이 있어야 합니다.

* iSCSI에 사용 가능한 두 개의 최소 인터페이스입니다. 두 인터페이스가 StorSimple 디바이스에서 iSCSI를 사용할 수 있는지를 확인하려면 StorSimple 디바이스에 대한 Azure 클래식 포털에서 다음 단계를 수행합니다.
  
  1. StorSimple 디바이스에 대한 클래식 포털에 로그인합니다.
  1. StorSimple Manager 서비스를 선택하고 **디바이스**를 클릭하고, 특정 StorSimple 디바이스를 선택합니다. **구성** 을 클릭하고 네트워크 인터페이스 설정을 검사합니다. 두 가지 iSCSI를 사용하는 네트워크 인터페이스가 있는 스크린샷은 아래와 같습니다. 여기서 데이터 2와 데이터 3은 모두 iSCSI에 10GbE 인터페이스를 사용할 수 있습니다.
     
      ![MPIO StorsSimple 데이터 2 구성](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple 데이터 3 구성](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      **구성** 페이지에서
     
     1. 네트워크 인터페이스가 둘 모두  iSCSI를 사용할 수 있는지 확인합니다. **iSCSI를 사용 가능한** 필드를 **예**로 설정해야 합니다.
     1. 네트워크 인터페이스의 속도가 동일한지 확인합니다. 둘 모두 1GbE 또는 10GbE여야 합니다.
     1. iSCSI를 사용 가능한 인터페이스의 IPv4 주소를 확인하고 나중에 사용하기 위해 호스트에 저장합니다.
* CentOS 서버에서 StorSimple 디바이스의 iSCSI 인터페이스를 연결할 수 있어야 합니다.
      이를 확인하려면 호스트 서버에서 StorSimple iSCSI를 사용 가능한 네트워크 인터페이스의 IP 주소를 제공해야 합니다. DATA2(10.126.162.25) 및 DATA3 (10.126.162.26)로 사용된 명령 및 해당 출력은 아래와 같습니다.
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>하드웨어 구성
중복성을 위해 별도 경로에 두 개의 iSCSI 네트워크 인터페이스를 연결하는 것이 좋습니다. 아래 그림에서는 고가용성을 위한 추천 하드웨어 구성과 CentOS 서버 및 StorSimple 디바이스를 위한 다중 경로 부하 분산을 보여 줍니다.

![Linux 호스트에 StorSimple을 위한 MPIO 하드웨어 구성](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

이전 그림에서 표시된 것처럼

* StorSimple 디바이스가 두 개의 컨트롤러를 사용하여 활성-수동 구성 중입니다.
* 두 개의 SAN 스위치가 디바이스 컨트롤러에 연결됩니다.
* 두 개의 iSCSI 초기자를 StorSimple 디바이스에서 사용 가능합니다.
* 두 개의 네트워크 인터페이스를 CentOS 호스트에서 사용 가능합니다.

위의 구성은 호스트와 데이터 인터페이스가 라우팅될 수 있는 경우 디바이스와 호스트 사이에 4개의 개별 경로를 생성합니다.

> [!IMPORTANT]
> * 다중 경로에 1GbE 및 10GbE 네트워크 인터페이스를 혼용하지 않는 것이 좋습니다. 두 네트워크 인터페이스를 사용하면 두 인터페이스 모두 동일한 형식이어야 합니다.
> * StorSimple 디바이스에서 DATA0, DATA1, DATA4 및 DATA5는 1GbE 인터페이스인 반면 DATA2 및 DATA3은 10GbE 네트워크 인터페이스입니다. |
> 
> 

## <a name="configuration-steps"></a>구성 단계
다중 경로를 위한 구성 단계는 자동 검색에 사용 가능한 경로 구성, 사용할 부하 분산 알고리즘 지정, 다중 경로를 사용하도록 설정 및 마지막으로 구성 확인을 포함합니다. 이러한 각 단계는 다음 섹션에서 자세히 설명합니다.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>1단계: 자동 검색에 대 한 다중 경로 구성
다중 경로를 지원하는 디바이스를 자동으로 검색 및 구성할 수 있습니다.

1. `/etc/multipath.conf` 파일을 초기화합니다. 형식:
   
     `mpathconf --enable`
   
    위의 명령은 `sample/etc/multipath.conf` 파일을 만듭니다.
1. 다중 경로 서비스를 시작합니다. 형식:
   
    `service multipathd start`
   
    다음 출력이 표시됩니다.
   
    `Starting multipathd daemon:`
1. 다중 경로의 자동 검색을 사용하도록 설정합니다. 형식:
   
    `mpathconf --find_multipaths y`
   
    아래와 같이 `multipath.conf` 의 기본값 섹션을 수정합니다.
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>2단계: StorSimple 볼륨에 대 한 다중 경로 구성
기본적으로 모든 디바이스는 multipath.conf 파일에서 금지 목록에 오르고 무시됩니다. 금지 목록 예외를 만들어서 StorSimple 디바이스에서 볼륨에 다중 경로를 허용해야 합니다.

1. `/etc/mulitpath.conf` 파일을 편집합니다. 형식:
   
    `vi /etc/multipath.conf`
1. multipath.conf 파일에서 blacklist_exceptions 섹션을 찾습니다. StorSimple 디바이스는 이 섹션에서 금지 목록 예외로 나열되어야 합니다. 이 파일에서 관련된 줄의 주석 처리를 제거하여 아래 그림과 같이 수정할 수 있습니다.(사용하는 디바이스의 특정 모델에만 사용)
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>3단계: 라운드 로빈 다중 경로 구성
이 부하 분산 알고리즘은 분산된 라운드 로빈 방식으로 활성 컨트롤러에 사용 가능한 모든 다중 경로를 사용합니다.

1. `/etc/multipath.conf` 파일을 편집합니다. 형식:
   
    `vi /etc/multipath.conf`
1. `defaults` 섹션에서 `path_grouping_policy`를 `multibus`으로 설정합니다. `path_grouping_policy` 는 기본 경로 그룹화 정책을 지정하여 지정되지 않은 다중 경로에 적용합니다. 기본값 섹션은 아래와 같이 표시됩니다.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> `path_grouping_policy`의 가장 일반적인 값은 다음을 포함합니다.
> 
> * 장애 조치 = 우선 순위 그룹 당 1개의 경로
> * multibus = 1개의 우선 순위 그룹에서 모든 유효한 경로
> 
> 

### <a name="step-4-enable-multipathing"></a>4단계: 다중 경로 사용 하도록 설정
1. `multipathd` 데몬을 다시 시작합니다. 형식:
   
    `service multipathd restart`
1. 출력은 아래와 같습니다.
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>5단계: 다중 경로 확인 합니다.
1. 먼저 iSCSI 연결이 StorSimple 디바이스에 다음과 같이 설정되어 있는지 확인합니다.
   
   a. StorSimple 디바이스를 검색합니다. 형식:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    DATA0의 IP 주소가 10.126.162.25이고 3260 포트가 아웃바운드 iSCSI 트래픽에 대한 StorSimple 디바이스에 열린 경우 출력은 아래와 같습니다.
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    이전 출력에서 StorSimple 디바이스 `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`의 IQN을 복사합니다.

   b. 대상 IQN을 사용하여 디바이스에 연결합니다. StorSimple 디바이스는 여기서 iSCSI 대상입니다. 형식:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    다음 예제에서는 `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`의 대상 IQN를 사용하여 출력을 표시합니다. 이 출력은 디바이스에서 두 개의 iSCSI를 사용하는 네트워크 인터페이스에 성공적으로 연결할 수 있음을 나타냅니다.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    하나의 호스트 인터페이스 및 두 개의 경로가 표시되면 iSCSI용 호스트에 두 개의 인터페이스를 모두 사용하도록 설정해야 합니다. [Linux 설명서의 자세한 지침](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)을 따르세요.

1. 볼륨은 StorSimple 디바이스에서 CentOS 서버에 노출됩니다. 자세한 내용은 참조 하세요. [6 단계: 볼륨 만들기](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) StorSimple 장치에서 Azure portal을 통해.

1. 사용 가능한 경로를 확인합니다. 형식:

      ```
      multipath –l
      ```

      다음 예제에서는 두 개의 사용 가능한 경로를 사용하여 단일 호스트 네트워크 인터페이스에 연결된 StorSimple 디바이스에 두 네트워크 인터페이스에 대한 출력을 보여 줍니다.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>다중 경로 문제 해결
이 섹션에서는 다중 경로를 구성하는 동안 문제가 발생하는 경우 유용한 팁을 제공합니다.

Q. `multipath.conf` 파일에서 적용된 변경 내용을 확인할 수 없습니다.

a. `multipath.conf` 파일을 변경한 경우 경로 지정 서비스를 다시 시작해야 합니다. 다음 명령을 입력합니다.

    service multipathd restart

Q. StorSimple 디바이스에 두 개의 네트워크 인터페이스, 호스트에 두 개의 네트워크 인터페이스를 사용하도록 설정했습니다. 사용 가능한 경로를 나열하는 경우 두 개의 경로만 표시됩니다. 네 개의 사용 가능한 경로가 확인되어야 합니다.

a. 두 개의 경로를 라우팅할 수 있으며 동일한 서브넷에 있는지를 확인합니다. 네트워크 인터페이스가 다른 vLAN에 있고 라우팅할 수 없으면 두 개의 경로만 표시됩니다. 이를 확인하는 방법은 StorSimple 디바이스의 네트워크 인터페이스에서 호스트 인터페이스 모두를 연결할 수 있는지 확인하는 것입니다. 이 방법은 지원 세션을 통해 수행할 수 있으므로 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md) 해야 합니다.

Q. 사용 가능한 경로를 나열하는 경우 어떤 출력도 나타나지 않습니다.

a. 일반적으로 다중 경로인 경로가 표시 되지 않으면 다중 경로인 데몬과 문제가 있을 수 있고 `multipath.conf` 파일에 어떤 문제가 있을 가능성이 높습니다.

또한 다중 경로 목록으로부터 응답이 없으면 디스크가 없다는 것을 의미할 수 있으므로 대상에 연결한 후에 일부 디스크를 실제로 볼 수 있는지 확인하는 것이 좋습니다.

* 다음 명령을 사용하여 SCSI 버스를 다시 스캔합니다.
  
    `$ rescan-scsi-bus.sh` (sg3_utils 패키지의 일부)
* 다음 명령을 입력합니다.
  
    `$ dmesg | grep sd*`
     
     또는
  
    `$ fdisk –l`
  
    최근에 추가된 디스크의 세부 정보를 반환합니다.
* StorSimple 디스크인지를 확인하려면 다음 명령을 사용합니다.
  
    `cat /sys/block/<DISK>/device/model`
  
    StorSimple 디스크인지를 확인하는 문자열을 반환합니다.

또한 가능성이 적지만 가능한 원인은 iscsid pid일 수 있습니다. 다음 명령을 사용하여 iSCSI 세션에서 로그오프합니다.

    iscsiadm -m node --logout -p <Target_IP>

StorSimple 디바이스인 iSCSI 대상에서 연결된 모든 네트워크 인터페이스에 이 명령을 반복합니다. 모든 iSCSI 세션에서 로그오프하면 iSCSI 대상 IQN을 사용하여 iSCSI 세션을 다시 설정합니다. 다음 명령을 입력합니다.

    iscsiadm -m node --login -T <TARGET_IQN>


Q. 디바이스를 허용 목록에 추가되었는지 잘 모릅니다.

a. 디바이스를 허용 목록에 추가되었는지를 확인하려면 다음 문제 해결 대화형 명령을 사용합니다.

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


자세한 내용은 [다중 경로에 문제 해결 대화형 명령 사용](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html)으로 이동하세요.

## <a name="list-of-useful-commands"></a>유용한 명령 목록
| 확인하라는 메시지가 표시되면  | 명령 | 설명 |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |iSCSI 서비스 시작 |
| &nbsp; |`service iscsid stop` |iSCSI 서비스 중지 |
| &nbsp; |`service iscsid restart` |iSCSI 서비스 다시 시작 |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |지정된 주소에서 사용할 수 있는 대상 검색 |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |iSCSI 대상에 로그인 |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |iSCSI 대상에서 로그아웃 |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |iSCSI 초기자 이름 인쇄 |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |iSCSI 세션 및 호스트에서 검색된 볼륨의 상태 확인 |
| &nbsp; |`iscsi –m session` |호스트와 StorSimple 디바이스 간에 설정된 모든 iSCSI 세션 표시 |
|  | | |
| **다중 경로 지정** |`service multipathd start` |다중 경로 디먼 시작 |
| &nbsp; |`service multipathd stop` |다중 경로 디먼 중지 |
| &nbsp; |`service multipathd restart` |다중 경로 디먼 다시 시작 |
| &nbsp; |`chkconfig multipathd on` </br> 또는 </br> `mpathconf –with_chkconfig y` |부팅 시 시작되도록 다중 경로 디먼 설정 |
| &nbsp; |`multipathd –k` |문제 해결을 위한 대화형 콘솔 시작 |
| &nbsp; |`multipath –l` |다중 경로 연결 및 디바이스 나열 |
| &nbsp; |`mpathconf --enable` | `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>다음 단계
또한 Linux 호스트에서 MPIO를 구성했기 때문에 다음 CentoS 6.6 문서를 참조해야 할 수 있습니다.

* [CentOS에 MPIO 설정](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Linux 교육 가이드](http://linux-training.be/linuxsys.pdf)

