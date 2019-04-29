---
title: STONITH를 사용하여 Azure(대규모 인스턴스)의 SAP HANA에 대한 고가용성 설정 | Microsoft Docs
description: STONITH를 사용하여 SUSE에서 Azure(대규머 인스턴스)의 SAP HANA에 대한 고가용성 설정
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ef1656a7e8a66092de3050a8f14c5b38e0e2e6c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123572"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>STONITH를 사용하여 SUSE에서 고가용성 설정
이 문서는 STONITH 디바이스를 사용하여 SUSE 운영 체제에서 고가용성을 설정하는 자세한 단계별 지침을 제공합니다.

**고지 사항:** *이 가이드는 성공적으로 작동하는 Microsoft HANA 대규모 인스턴스 환경에서 설정을 테스트하여 작성하였습니다. Microsoft의 HANA 대규모 인스턴스 서비스 관리 팀은 운영 체제를 지원하지 않으므로 운영 체제 계층에 관한 추가 문제 해결 또는 설명은 SUSE에 문의하십시오. Microsoft 서비스 관리 팀은 STONITH 디바이스를 설정하고 완전히 도움을 드릴 것이며 STONITH 디바이스 문제에 대한 문제 해결에 참여할 수 있습니다.*
## <a name="overview"></a>개요
SUSE 클러스터링을 사용하여 고가용성을 설정하려면 다음 필수 구성 요소를 충족해야 합니다.
### <a name="pre-requisites"></a>필수 조건
- HANA 대규모 인스턴스를 프로비전
- 운영 체제 등록
- 패치/패키지를 가져오기 위해 HANA 대규모 인스턴스 서버를 SMT 서버에 연결
- 운영 체제에 최신 패치 설치
- NTP(시간 서버) 설정
- HA 설정에 관한 SUSE 설명서의 최신 버전을 읽고 이해

### <a name="setup-details"></a>설정 정보
이 가이드에서 사용하는 설정은 다음과 같습니다.
- 운영 체제: SAP용 SLES 12 SP1
- HANA 대규모 인스턴스: 2xS192(4 소켓, 2TB)
- HANA 버전: HANA 2.0 SP1
- 서버 이름: sapprdhdb95(노드 1) 및 sapprdhdb96(노드 2)
- STONITH 디바이스: iSCSI 기반 STONITH 디바이스
- HANA 대규모 인스턴스 노드 중 하나에서 NTP 설정

HSR을 사용하여 HANA 대규모 인스턴스를 설정하는 경우 Microsoft 서비스 관리 팀에 STONITH 설정을 요청해야 합니다. 이미 HANA 대규모 인스턴스를 프로비전한 기존 고객으로서 기존 블레이드에 대한 STONITH 디바이스 설정이 필요한 경우 서비스 요청 양식(SRF)에 다음 정보를 작성하여 Microsoft 서비스 관리 팀에 제공해야 합니다. 기술 계정 관리자 또는 Microsoft의 HANA 대규모 인스턴스 온보딩 담당자를 통해 SRF 양식을 요청할 수 있습니다. 새 고객은 프로비전 시 STONITH 디바이스를 요청할 수 있습니다. 입력은 프로비전 요청 양식에서 사용할 수 있습니다.

- 서버 이름 및 서버 IP 주소(예: myhanaserver1, 10.35.0.1)
- 위치(예: 미국 동부)
- 고객 이름(예: Microsoft)
- SID - HANA 시스템 식별자(예: H11)

STONITH 디바이스가 구성되면 Microsoft 서비스 관리 팀에서 STONITH 설정을 구성하는 데 사용할 수 있는 iSCSI 저장소의 SBD 디바이스 이름과 IP 주소를 제공합니다. 

STONITH를 사용하여 종단 간 HA를 설정하려면 다음 단계를 따라야 합니다.

1.  SBD 디바이스 식별
2.  SBD 디바이스 초기화
3.  클러스터 구성
4.  Softdog Watchdog 설정
5.  클러스터에 노드 조인
6.  클러스터 유효성 검사
7.  클러스터에 대한 리소스 구성
8.  장애 조치(failover) 프로세스 테스트

## <a name="1---identify-the-sbd-device"></a>1.   SBD 디바이스 식별
이 섹션에서는 Microsoft 서비스 관리 팀이 STONITH를 구성한 후 설정에 맞는 SBD 디바이스를 결정하는 방법을 설명합니다. **이 섹션은 기존 고객에게만 적용됩니다**. 새 고객의 경우 Microsoft 서비스 관리 팀이 SBD 디바이스 이름을 제공하며 따라서 이 섹션을 건너뛸 수 있습니다.

1.1 */etc/iscsi/initiatorname.isci*를 다음으로 수정 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft 서비스 관리에서 이 문자열을 제공합니다. 노드 **둘 다**에서 이 파일을 수정하지만 노드 번호는 노드마다 다릅니다.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 */etc/iscsi/iscsid.conf* 수정: *node.session.timeo.replacement_timeout=5* 및 *node.startup = automatic*을 설정합니다. 노드 **둘 다**에서 파일을 수정합니다.

1.3 검색 명령을 실행하여 4개 세션을 표시합니다. 이 작업은 두 노드에서 모두 실행합니다.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 iSCSI 디바이스에 로그인하는 명령을 실행하고 4개 세션을 표시합니다. 이 작업은 **두** 노드에서 모두 실행합니다.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 다시 검사 스크립트 실행: *rescan-scsi-bus.sh*.  이 스크립트는 사용자를 위해 생성된 새 디스크를 표시합니다.  이 작업은 두 노드에서 모두 실행합니다. 0보다 더 큰 LUN 번호가 표시됩니다. (예: 1, 2 등)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 디바이스 이름을 가져오려면 *fdisk –l* 명령을 실행합니다. 이 작업은 두 노드에서 모두 실행합니다. **178MiB** 크기를 가진 디바이스를 선택합니다.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   SBD 디바이스 초기화

2.1 두 노드에서 **모두** SBD 디바이스를 초기화합니다.

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 디바이스에 기록된 내용을 확인합니다. 이 작업은 두 노드에서 **모두** 실행합니다.

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   클러스터 구성
이 섹션에서는 SUSE HA 클러스터를 설정하는 단계를 설명합니다.
### <a name="31-package-installation"></a>3.1 패키지 설치
3.1.1 ha_sles 및 SAPHanaSR-doc 패턴이 설치되었는지 확인하십시오. 설치되지 않은 경우 설치합니다. 이 패키지는 두 노드에서 **모두** 실행합니다.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 클러스터 설치
3.2.1   *ha-cluster-init* 명령을 사용하거나 yast2 마법사를 사용하여 클러스터를 설치할 수 있습니다. 여기서는 yast2 마법사를 사용합니다. **주 노드에 대해서만** 이 단계를 수행합니다.

yast2> 고가용성 > 클러스터 ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)를 수행합니다.

halk2 패키지가 이미 설치되었으므로 **취소**를 클릭합니다.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

**계속**을 클릭합니다.

예상 값=배포한 노드 수(이 경우 2) ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) **다음**
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)를 클릭합니다. 노드 이름을 추가한 다음 “권장 파일 추가”를 클릭합니다.

“csync2 켜기”를 클릭합니다.

“미리 공유한 키”를 클릭하여 아래 팝업을 표시합니다.

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

**확인**

IP 주소 및 Csync2의 미리 공유한 키를 사용하여 인증을 수행합니다. csync2 -k /etc/csync2/key_hagroup을 사용하여 키 파일을 생성합니다. key_hagroup 파일을 생성한 후 클러스터의 모든 멤버에 수동으로 복사해야 합니다. **반드시 노드 1에서 노드 2로 파일을 복사해야 합니다**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

**다음**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)을 클릭합니다.

기본 옵션(부팅 꺼짐)에서 부팅할 때 Pacemaker가 시작되도록 “켜기”로 변경해야 합니다. 설정 요구 사항에 따라 선택할 수 있습니다.
**다음**을 클릭하면 클러스터 구성이 완료됩니다.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Softdog Watchdog 설정
이 섹션에서는 Watchdog(softdog) 구성을 설명합니다.

4.1 두 노드에서 **모두** 다음 줄을 */etc/init.d/boot.local*에 추가합니다.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 아래와 같이 두 노드에서 **모두** */etc/sysconfig/sbd*를 업데이트합니다.
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 두 노드에서 **모두** 다음 명령을 실행하여 커널 모듈을 로드합니다.
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 아래와 같이 두 노드에서 **모두** softdog이 실행 중인지 확인합니다.
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 두 노드에서 **모두** SBD 디바이스를 시작합니다.
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 두 노드에서 **모두** SBD 디먼을 시작합니다. 두 노드에서 **모두** 두 항목을 구성한 후 해당 항목이 표시됩니다.
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 노드 중 **한 개**에 테스트 메시지를 보냅니다.
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 **두 번째** 노드(노드 2)에서 메시지 상태를 확인할 수 있습니다.
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 sbd config를 채택하려면 다음과 같이 */etc/sysconfig/sbd* 파일을 업데이트합니다. 노드 **둘 다**에서 파일을 업데이트합니다.
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10    **주 노드**(노드 1)에서 Pacemaker 서비스를 시작합니다.
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pacemaker 서비스가 *실패*한 경우 *시나리오 5: Pacemaker 서비스 실패*를 참조하세요.

## <a name="5---joining-the-cluster"></a>5.   클러스터 조인
이 섹션에서는 노드를 클러스터에 조인하는 방법을 설명합니다.

### <a name="51-add-the-node"></a>5.1 노드 추가
다음 명령을 **노드 2**에서 실행하여 노드 2를 클러스터에 조인합니다.
```
ha-cluster-join
```
클러스터 조인 중에 *오류*가 표시되는 경우 *시나리오 6: 노드 2가 클러스터를 조인할 수 없는 경우*를 참조하세요.

## <a name="6---validating-the-cluster"></a>6.   클러스터 유효성 검사

### <a name="61-start-the-cluster-service"></a>6.1 클러스터 서비스 시작
두 노드에서 **모두** 클러스터를 확인 및 처음 시작합니다(옵션).
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 상태 모니터링
두 노드에서 **모두** *crm_mon* 명령을 실행하여 노드가 온라인인지 확인합니다. 이 작업은 클러스터의 **임의 노드**에서 실행할 수 있습니다.
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) hawk 클러스터 상태 확인에 로그인에 로그인 할 수 있습니다 *https://\<노드 IP >: 7630*합니다. 기본 사용자는 hacluster이며 암호는 linux입니다. 필요한 경우 *passwd* 명령을 사용하여 암호를 변경할 수 있습니다.

## <a name="7-configure-cluster-properties-and-resources"></a>7. 클러스터 속성 및 리소스 구성 
이 섹션에서는 클러스터 리소스를 구성하는 단계를 설명합니다.
이 예제에서는 다음 리소스를 설정하였으며, 나머지는 SUSE HA 가이드를 참조하여 구성할 수 있습니다(필요한 경우). **노드 중 한 개**에서만 이 구성을 수행합니다. 주 노드에서 수행합니다.

- 클러스터 부트스트랩
- STONITH 디바이스
- 가상 IP 주소


### <a name="71-cluster-bootstrap-and-more"></a>7.1 클러스터 부트스트랩 및 그 이상
클러스터 부트스트랩을 추가합니다. 파일을 만들고 다음 텍스트를 추가합니다.
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
클러스터에 구성을 추가합니다.
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH 디바이스
리소스 STONITH를 추가합니다. 파일을 만들고 다음 텍스트를 추가합니다.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
클러스터에 구성을 추가합니다.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 가상 IP 주소
리소스 가상 IP를 추가합니다. 파일을 만들고 아래 텍스트를 추가합니다.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
클러스터에 구성을 추가합니다.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 리소스 유효성 검사

*crm_mon* 명령을 실행하면 다음 두 리소스를 확인할 수 있습니다.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

또한에서 상태를 확인할 수 있습니다 *https://\<노드 IP 주소 >: 7630/cib/라이브/상태*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. 장애 조치(failover) 프로세스 테스트
장애 조치 프로세스를 테스트하려면 노드 1에서 Pacemaker 서비스를 중단하고 리소스를 노드 2에 장애 조치합니다.
```
Service pacemaker stop
```
이제 **노드 2**에서 Pacemaker 서비스를 중단하고 리소스를 **노드 1**에 대해 장애 조치합니다.

**장애 조치 전**
![Before-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**장애 조치 후**
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. 문제 해결
이 섹션에서는 설치 중에 발생할 수 있는 몇 가지 실패 시나리오를 설명합니다. 이 문제가 발생하지 않을 수도 있습니다.

### <a name="scenario-1-cluster-node-not-online"></a>시나리오 1: 클러스터 노드가 온라인이 아닌 경우
노드 중 하나 이상이 클러스터 관리자에 온라인으로 표시되지 않는 경우 다음을 실행하여 온라인으로 만들어 볼 수 있습니다.

iSCSI 서비스 시작
```
service iscsid start
```

그리고 이제는 해당 iSCSI 노드에 로그인할 수 있을 것입니다.
```
iscsiadm -m node -l
```
예상 출력은 다음과 같이 표시됩니다.
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>시나리오 2: yast2가 그래픽 보기에 표시되지 않는 경우
yast2 그래픽 화면이 이 문서의 고가용성 클러스터를 설정하는 데 사용됩니다. yast2가 그림과 같은 그래픽 창과 함께 열리지 않고 Qt 오류를 throw하면 다음과 같이 단계를 수행합니다. 그래픽 창과 함께 열리면 이 단계를 건너뛸 수 있습니다.

**오류**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**예상 출력**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

yast2가 그래픽 창과 함께 열리지 않는 경우 다음 단계를 수행합니다.

필요한 패키지를 설치합니다. “루트” 사용자로 로그인해야 하며 SMT 설치 프로그램을 다운로드하거나/패키지를 설치해야 합니다.

패키지를 설치하려면 yast>소프트웨어>소프트웨어 관리>종속성> 옵션 “권장 패키지 설치...”를 사용합니다. 다음 스크린 샷은 예상 화면을 보여 줍니다.
>[!NOTE]
>두 노드에서 모두 yast2 그래픽 보기에 액세스할 수 있도록 두 노드에서 모두 이 단계를 수행해야 합니다.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

종속성 아래에서 "권장 패키지 설치" ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)를 선택합니다.

변경 내용을 검토하고 확인을 누릅니다.

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

패키지 설치에서 ![yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)를 계속 진행합니다.

다음을 클릭합니다.

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

마침을 클릭합니다.

libqt4 및 libyui-qt 패키지도 설치해야 합니다.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) yast2는 이제 그림과 같이 그래픽 보기를 열 수 있을 것입니다.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>시나리오 3: yast2에 고가용성 옵션이 표시되지 않는 경우
고가용성 옵션을 yast2 제어 센터에서 볼 수 있도록 하려면 추가 패키지를 설치해야 합니다.

Yast2>소프트웨어>소프트웨어 관리>를 사용하여 다음 패턴을 선택합니다.

- SAP HANA 서버 베이스
- C/C++ 컴파일러 및 도구
- 고가용성
- SAP 애플리케이션 서버 베이스

다음 화면은 패턴을 설치하는 단계를 나타냅니다.

yast2 > 소프트웨어 > 소프트웨어 관리 사용

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

패턴을 선택합니다.

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

**동의**를 클릭합니다.

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

**계속**을 클릭합니다.

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

설치가 완료되면 **다음**을 클릭합니다.

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>시나리오 4: HANA 설치가 실패하고 gcc 어셈블리 오류가 발생하는 경우
HANA 설치가 실패하고 다음 오류가 발생합니다.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

문제를 해결하려면 다음과 같이 라이브러리(libgcc_sl 및 libstdc++6)를 설치해야 합니다.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>시나리오 5: Pacemaker 서비스 실패

Pacemaker 서비스를 시작하는 동안 다음 문제가 발생했습니다.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

문제를 해결하려면 */usr/lib/systemd/system/fstrim.timer* 파일에서 다음 줄을 삭제합니다.

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>시나리오 6: 노드 2가 클러스터에 조인할 수 없는 경우

*ha-cluster-join* 명령을 사용하여 노드 2를 기존 클러스터에 조인할 때 다음 오류가 발생했습니다.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

해결하려면 두 노드에서 모두 다음을 실행합니다.

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

앞의 문제 해결 후 노드 2가 클러스터에 추가되었을 것입니다.

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. 일반 설명서
다음 문서에서 SUSE HA 설정에 관한 추가 정보를 찾을 수 있습니다. 

- [SAP HANA SR 성능 최적화된 시나리오](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [저장소 기반 울타리](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [블로그 - SAP HANA에 Pacemaker 클러스터 사용- 1부](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [블로그 - SAP HANA에 Pacemaker 클러스터 사용- 2부](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
