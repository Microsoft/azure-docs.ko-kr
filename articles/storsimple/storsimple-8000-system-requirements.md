---
title: StorSimple 8000 시리즈 시스템 요구 사항 | Microsoft Docs
description: Microsoft Azure StorSimple 솔루션에 대한 모범 사례와 소프트웨어, 네트워킹, 고가용성 요구 사항을 설명합니다.
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
ms.openlocfilehash: f05e3e85d36ffc23a193a6771a0271c71b2f8544
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631909"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000 시리즈 소프트웨어, 높은 가용성 및 네트워킹 요구 사항

## <a name="overview"></a>개요

Microsoft Azure StorSimple 시작을 환영합니다. 이 문서에서는 중요한 시스템 요구 사항 및 StorSimple 디바이스와 해당 디바이스에 액세스하는 저장소 클라이언트에 대한 모범 사례를 설명합니다. StorSimple 시스템을 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **저장소 클라이언트에 대한 소프트웨어 요구 사항** -지원되는 운영 체제 및 이러한 운영 체제에 대한 추가 요구 사항을 설명합니다.
* **StorSimple 디바이스에 대한 네트워킹 요구 사항** - iSCSI, 클라우드 또는 관리 트래픽을 허용하도록 방화벽에서 열려야 하는 포트에 대한 정보를 제공합니다.
* **StorSimple에 대한 고가용성 요구 사항** - StorSimple 디바이스 및 호스트 컴퓨터에 대한 고가용성 요구 사항 및 모범 사례를 설명합니다.

## <a name="software-requirements-for-storage-clients"></a>저장소 클라이언트에 대한 소프트웨어 요구 사항

다음 소프트웨어 요구 사항은 StorSimple 디바이스에 액세스하는 저장소 클라이언트에 대한 것입니다.

| 지원되는 운영 체제 | 필요한 버전 | 추가 요구 사항/메모 |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2, 2016 |StorSimple iSCSI 볼륨은 다음과 같은 Windows 디스크 유형에 사용하는 경우에만 지원됩니다.<ul><li>기본 디스크의 단순 볼륨</li><li>동적 디스크의 단순 및 미러 볼륨</li></ul>운영 체제에 기본적으로 있는 소프트웨어 iSCSI 초기자만 지원됩니다. 하드웨어 iSCSI 초기자는 지원되지 않습니다.<br></br>Windows Server 2012 및 2016 씬 프로비전 및 ODX 기능은 StorSimple iSCSI 볼륨을 사용하는 경우에 지원됩니다.<br><br>StorSimple은 씬 프로비전된 볼륨과 완전히 프로비전된 볼륨을 만들 수 있습니다. 부분적으로 프로비전된 볼륨은 만들 수 없습니다.<br><br>씬 프로비전된 볼륨을 다시 포맷하는 데에는 시간이 오래 걸릴 수 있습니다. 다시 포맷하는 대신 볼륨을 삭제했다가 새 볼륨을 만드는 것이 좋습니다. 그래도 볼륨을 다시 포맷하려면,<ul><li>공간 재사용에 따른 지연을 방지하려면 다시 포맷하기 전에 다음 명령을 실행합니다. <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>서식 지정이 완료된 후에 다음 명령을 사용하여 공간 재사용을 다시 활성화합니다.<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>[KB 2878635](https://support.microsoft.com/kb/2870270)에 설명된 대로 Windows Server 컴퓨터에 Windows Server 2012 핫픽스를 적용합니다.</li></ul></li></ul></ul> StorSimple 스냅숏 관리자 또는 SharePoint용 StorSimple 어댑터를 구성하려면 [선택적 구성 요소에 대한 소프트웨어 요구 사항](#software-requirements-for-optional-components)으로 이동하세요. |
| VMware ESX |5.5 및 6.0 |iSCSI 클라이언트로 VMware vSphere와 함께 지원됩니다. VAAI 블록 기능은 StorSimple 디바이스에서 VMware vSphere와 함께 지원됩니다. |
| Linux RHEL/CentOS |5, 6 및 7 |Open iSCSI 초기자 버전 5, 6 및 7과 함께 Linux iSCSI 클라이언트를 지원합니다. |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> IBM AIX는 현재 StorSimple에 지원되지 않습니다.


## <a name="software-requirements-for-optional-components"></a>선택적 구성 요소에 대한 소프트웨어 요구 사항

다음 소프트웨어 요구 사항은 선택적 StorSimple 구성 요소(StorSimple 스냅숏 관리자 및 SharePoint용 StorSimple 어댑터)에 대한 것입니다.

| 구성 요소 | 호스트 플랫폼 | 추가 요구 사항/메모 |
| --- | --- | --- |
| StorSimple 스냅숏 관리자 |Windows Server 2008 R2 SP1, 2012, 2012 R2 |Windows Server에서 StorSimple Snapshot Manager 사용은 미러링한 동적 디스크의 백업/복원에 필요하며 애플리케이션에 일관된 백업에 필요합니다.<br> StorSimple Snapshot Manager는 Windows Server 2008 R2 SP1(64비트), Windows Server 2012 R2 및 Windows Server 2012에서만 지원됩니다.<ul><li>Window Server 2012를 사용하는 경우 StorSimple Snapshot Manager를 설치하기 전에 .NET 3.5–4.5를 설치해야 합니다.</li><li>Windows Server 2008 R2 SP1을 사용하는 경우 StorSimple Snapshot Manager를 설치하기 전에 Windows Management Framework 3.0을 설치해야 합니다.</li></ul> |
| SharePoint용 StorSimple 어댑터 |Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>SharePoint용 StorSimple 어댑터는 SharePoint 2010 및 SharePoint 2013에서만 지원됩니다.</li><li>RBS는 SQL Server Enterprise Edition 2008 R2 또는 2012 버전이 필요합니다.</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>StorSimple 디바이스에 대한 네트워킹 요구 사항

StorSimple 디바이스는 잠긴 디바이스입니다. 하지만 iSCSI, 클라우드 및 관리 트래픽에 허용하도록 포트가 방화벽에서 열려야 합니다. 다음 표에서 방화벽에서 열려야 하는 포트를 나열합니다. 이 테이블에서 *인* 또는 *인바운드*는 디바이스에 대한 들어오는 클라이언트 요청 액세스에서 방향을 참조합니다. *아웃* 또는 *아웃바운드*는 배포 후 데이터를 외부로 보내는 StorSimple 디바이스에서 방향을 참조합니다.

| 포트 번호 <sup>1, 2</sup> | 인 또는 아웃 | 포트 범위 | 필수 | 메모 |
| --- | --- | --- | --- | --- |
| TCP 80(HTTP)<sup>3</sup> |아웃 |WAN |아니요 |<ul><li>업데이트 복구를 위한 인터넷 액세스에는 아웃바운드 포트가 사용됩니다.</li><li>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다.</li><li>시스템 업데이트를 허용하려면 컨트롤러 고정 IP에 대해 이 포트도 오픈되어야 합니다.</li></ul> |
| TCP 443(HTTPS)<sup>3</sup> |아웃 |WAN |yes |<ul><li>아웃바운드 포트는 클라우드의 데이터에 액세스하는 데 사용됩니다.</li><li>아웃바운드 웹 프록시는 사용자가 구성할 수 있습니다.</li><li>시스템 업데이트를 허용하려면 컨트롤러 고정 IP에 대해 이 포트도 오픈되어야 합니다.</li><li>이 포트도 가비지 수집을 위한 두 컨트롤러에 대해 사용됩니다.</li></ul> |
| UDP 53(DNS) |아웃 |WAN |일부 경우에는 메모를 참조하십시오. |이 포트는 인터넷 기반 DNS 서버로 사용하는 경우에만 필요합니다. |
| UDP 123(NTP) |아웃 |WAN |일부 경우에는 메모를 참조하십시오. |이 포트는 인터넷 기반 NTP 서버로 사용하는 경우에만 필요합니다. |
| TCP 9354 |아웃 |WAN |예 |아웃바운드 포트는 StorSimple 디바이스에서 StorSimple 디바이스 관리자 서비스와 통신하는 데 사용됩니다. |
| 3260(iSCSI) |그런 다음 |LAN |아닙니다. |이 포트는 iSCSI를 통해 데이터에 액세스하는 데 사용됩니다. |
| 5985 |그런 다음 |LAN |아닙니다. |인바운드 포트는 StorSimple 디바이스와의 통신을 위해 StorSimple 스냅숏 관리자에 사용됩니다.<br>이 포트는 HTTP를 통해 StorSimple용 Windows PowerShell에 원격으로 연결할 때에도 사용됩니다. |
| 5986 |그런 다음 |LAN |아닙니다. |이 포트는 HTTPS를 통해 StorSimple에 대해 Windows PowerShell에 원격으로 연결할 때 사용됩니다. |

<sup>1</sup> 인바운드 포트는 공용 인터넷에서 열릴 필요가 없습니다.

<sup>2</sup> 여러 포트가 게이트웨이 구성을 수행하는 경우 아웃바운드 라우팅된 트래픽 순서는 아래 [포트 라우팅](#routing-metric)에 설명된 포트 라우팅 순서에 따라 결정됩니다.

<sup>3</sup> StorSimple 디바이스에서 IP가 고정된 컨트롤러는 라우팅할 수 있어야 하며 인터넷에 직접 또는 구성된 웹 프록시를 통해 연결할 수 있어야 합니다. 고정 IP 주소는 가비지 수집 및 디바이스에 대한 업데이트를 제공하는 데 사용됩니다. 디바이스 컨트롤러가 고정 IP를 통해 인터넷에 연결되는 경우 StorSimple 디바이스를 업데이트할 수 없으며 가비지 수집이 제대로 작동하지 않습니다.

> [!IMPORTANT]
> 방화벽이 StorSimple 디바이스 및 Azure 사이의 모든 SSL 트래픽을 수정하거나 암호를 해독하지 않도록 해야 합니다.


### <a name="url-patterns-for-firewall-rules"></a>방화벽 규칙에 대한 URL 패턴

네트워크 관리자는 URL 패턴을 기준으로 하는 고급 방화벽 규칙이 인바운드 및 아웃바운드 트래픽을 필터링하도록 구성할 수 있습니다. StorSimple 디바이스 및 StorSimple 디바이스 관리자 서비스는 Azure Service Bus, Azure Active Directory Access Control, 저장소 계정 및 Microsoft 업데이트 서버 등의 다른 Microsoft 애플리케이션에 의존합니다. 이러한 애플리케이션과 연결된 URL 패턴을 사용하여 방화벽 규칙을 구성할 수 있습니다. 이러한 애플리케이션과 연결된 URL 패턴은 달라질 수 있습니다. 따라서 네트워크 관리자는 StorSimple에 대한 방화벽 규칙을 모니터링하고 필요에 따라 업데이트해야 합니다.

StorSimple 고정 IP 주소에 따라 대부분의 경우에서 자유롭게 아웃바운드 트래픽에 대한 방화벽 규칙을 설정하는 것이 좋습니다. 그러나 보안 환경을 만드는 데 필요한 고급 방화벽 규칙을 설정하려면 아래 정보를 사용할 수 있습니다.

> [!NOTE]
> 디바이스(원본) IP는 항상 사용하도록 설정된 네트워크 인터페이스로 설정되어야 합니다. 대상 IP는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)로 설정해야 합니다.


#### <a name="url-patterns-for-azure-portal"></a>Azure 포털의 URL 패턴

| URL 패턴 | 구성 요소/기능 | 디바이스 IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple 디바이스 관리자 서비스<br>Access Control Service<br>Azure Service Bus<br>인증 서비스 |클라우드 사용 네트워크 인터페이스 |
| `https://*.backup.windowsazure.com` |디바이스 등록 |데이터 0만 해당 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |인증서 해지 |클라우드 사용 네트워크 인터페이스 |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure 저장소 계정 및 모니터링 |클라우드 사용 네트워크 인터페이스 |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft 업데이트 서버<br> |컨트롤러 고정 IP만 |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |컨트롤러 고정 IP만 |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |지원 패키지 |클라우드 사용 네트워크 인터페이스 |

#### <a name="url-patterns-for-azure-government-portal"></a>Azure Government 포털의 URL 패턴

| URL 패턴 | 구성 요소/기능 | 디바이스 IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple 디바이스 관리자 서비스<br>Access Control Service<br>Azure Service Bus<br>인증 서비스 |클라우드 사용 네트워크 인터페이스 |
| `https://*.backup.windowsazure.us` |디바이스 등록 |데이터 0만 해당 |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |인증서 해지 |클라우드 사용 네트워크 인터페이스 |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure 저장소 계정 및 모니터링 |클라우드 사용 네트워크 인터페이스 |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft 업데이트 서버<br> |컨트롤러 고정 IP만 |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |컨트롤러 고정 IP만 |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |지원 패키지 |클라우드 사용 네트워크 인터페이스 |

### <a name="routing-metric"></a>라우팅 메트릭

라우팅 메트릭은 데이터를 특정 네트워크로 라우팅하는 게이트웨이 및 인터페이스와 연결됩니다. 라우팅 메트릭은 주어진 대상에 대해 다수의 경로가 존재한다는 것이 파악되면, 라우팅 프로토콜이 해당 대상에 대한 최상의 경로를 계산하는 데 사용됩니다. 라우팅 메트릭이 낮을수록 기본 설정이 높습니다.

StorSimple에 대해 다수의 네트워크 인터페이스와 게이트웨이가 트래픽을 전달하기 위해 구성되면 라우팅 메트릭이 인터페이스가 사용될 상대적인 순서를 결정하는 데 작용하게 됩니다. 라우팅 메트릭은 사용자에 의해 변경될 수 없습니다. 하지만 `Get-HcsRoutingTable` cmdlet을 사용하여 StorSimple 디바이스에 라우팅 테이블(및 메트릭)을 출력할 수 있습니다. Get-HcsRoutingTable cmdlet에 대한 자세한 내용은 [StorSimple 배포 문제 해결](storsimple-troubleshoot-deployment.md)을 참조하세요.

업데이트 2 이상 버전에 사용되는 라우팅 메트릭 알고리즘은 다음과 같이 설명할 수 있습니다.

* 미리 결정된 값 집합이 네트워크 인터페이스에 할당되었습니다.
* 클라우드를 사용하거나 클라우드를 사용하지 않지만 게이트웨이가 구성되어 있는 다양한 네트워크 인터페이스에 대해 값이 할당된 아래의 예제 테이블을 고려해 보세요. 여기에 할당된 값은 예제입니다.

    | Linux | 클라우드 사용 | 클라우드 미사용(게이트웨이 구성됨) |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* 네트워크 인터페이스를 통해 클라우드 트래픽이 라우팅되는 순서는 다음과 같습니다.
  
    *Data 0 > Data 1 > Date 2 > Data 3 > Data 4 > Data 5*
  
    이 내용은 다음 예제를 통해 설명됩니다.
  
    클라우드 사용 네트워크 인터페이스가 두 개(Data 0과 Data 5)인 StorSimple 디바이스가 있다고 가정합니다. Data 1에서 Data 4까지는 클라우드를 사용하지 않지만 구성된 게이트웨이가 있습니다. 이 디바이스에 대해 트래픽이 라우팅되는 순서는 다음과 같습니다.
  
    *Data 0(1) > Data 5(6) > Data 1(20) > Data 2(30) > Data 3(40) > Data 4(50)*
  
    *괄호 안의 숫자는 각각의 라우팅 메트릭을 나타냅니다.*
  
    Data 0에 실패하면 클라우드 트래픽은 Data 5를 통해 라우팅됩니다. 다른 모든 네트워크에 게이트웨이가 구성되어 있기 때문에 Data 0 및 Data 5에 실패하면 클라우드 트래픽은 Data 1을 거쳐갑니다.
* 클라우드 사용 네트워크 인터페이스가 실패하면, 인터페이스 연결을 30초 지연 후에 3회 재시도합니다. 재시도가 모두 실패하면 트래픽은 라우팅 테이블에 의해 결정되는 사용 가능한 다음 번 클라우드 사용 인터페이스로 라우팅됩니다. 클라우드 사용 네트워크 인터페이스가 모두 실패하면, 디바이스는 다른 컨트롤러로 장애 조치됩니다(이 경우 재부팅 없이).
* iSCSI 사용 네트워크 인터페이스에 VIP 오류가 있으면, 2초 지연 후에 재시도가 3회 실행됩니다. 이 동작은 이전 릴리스와 동일하게 유지됩니다. iSCSI 네트워크 인터페이스가 모두 실패하면, 컨트롤러 장애 조치(failover)가 발생합니다(재부팅 동반).
* VIP 오류가 있으면 StorSimple 디바이스에 경고가 생성됩니다. 자세한 내용은 [경고 빠른 참조](storsimple-8000-manage-alerts.md)를 참조하세요.
* 재시도에 있어서 iSCSI는 클라우드에 우선합니다.
  
    다음 예제를 살펴보세요. StorSimple 장치에 사용 하도록 설정 하는 두 개의 네트워크 인터페이스, Data 0 및 Data 1입니다. Data 0은 클라우드를 사용하지만 Data 1은 클라우드와 iSCSI 모두를 사용합니다. 이 디바이스에서 클라우드 또는 iSCSI에 사용하도록 설정된 다른 네트워크 인터페이스는 없습니다.
  
    Data 1에 실패하면, 이것이 마지막 iSCSI 네트워크 인터페이스라는 전제 하에, 다른 컨트롤러의 Data 1로 컨트롤러 장애 조치(failover)가 발생하게 됩니다.

### <a name="networking-best-practices"></a>네트워킹 모범 사례

StorSimple 솔루션의 최적의 성능을 위해 위의 네트워킹 요구 사항 외에도 다음 모범 사례를 준수합니다.

* StorSimple 디바이스에서 항상 전용 40Mbps 대역폭(이상)을 사용할 수 있어야 합니다. 이 대역폭을 다른 애플리케이션과 공유되지 않아야 합니다(또는 QoS 정책을 사용하여 할당을 보장해야 함).
* 항상 인터넷에 네트워크 연결이 되어야 합니다. 인터넷 연결이 전혀 안되는 것을 비롯하여 산발적이거나 안정적이지 않은 인터넷 연결은 지원되지 않은 구성을 야기합니다.
* iSCSI 및 클라우드 액세스를 위해 디바이스에서 전용 네트워크 인터페이스를 갖추어 iSCSI 및 클라우드 트래픽을 분리합니다. 자세한 내용은 StorSimple 디바이스에서 [네트워크 인터페이스를 수정](storsimple-8000-modify-device-config.md#modify-network-interfaces) 하는 방법을 참조하세요.
* 네트워크 인터페이스에 대한 링크 집합 제어 프로토콜(LACP) 구성을 사용하지 마십시오. 지원되지 않는 구성입니다.

## <a name="high-availability-requirements-for-storsimple"></a>StorSimple에 대한 고가용성 요구 사항

StorSimple 솔루션에 포함된 하드웨어 플랫폼은 데이터센터에서 가용성이 높고, 내결함성을 갖춘 저장소 인프라에 대한 기반을 제공하는 가용성 및 안정성 기능이 있습니다. 하지만 StorSimple 솔루션의 가용성을 위해 준수해야 하는 요구 사항 및 모범 사례가 있습니다. StorSimple을 배포하기 전에 StorSimple 디바이스 및 연결된 호스트 컴퓨터에 대한 다음 요구 사항 및 모범 사례를 신중하게 검토해야 합니다.

StorSimple 디바이스의 하드웨어 구성 요소 모니터링 및 유지 관리에 대한 자세한 내용을 보려면 [StorSimple 디바이스 관리자 서비스를 사용하여 하드웨어 구성 요소 및 상태 모니터링](storsimple-8000-monitor-hardware-status.md) 및 [StorSimple 하드웨어 구성 요소 교체](storsimple-8000-hardware-component-replacement.md)로 이동하세요.

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>StorSimple 디바이스에 대한 고가용성 요구 사항 및 절차

StorSimple 디바이스의 고가용성을 위해 다음 정보를 신중하게 검토해야 합니다.

#### <a name="pcms"></a>PCM

StorSimple 디바이스에는 운영 중 스왑 가능한 중복 전원 및 냉각 모듈(PCM)이 포함됩니다. 각 PCM에는 전체 섀시에 서비스를 제공하기에 충분한 용량이 있습니다. 고가용성을 위해서는 두 PCM이 모두 설치되어야 합니다.

* 전원에 문제가 있는 경우 가용성을 제공하기 위해 다른 전원에 PCM을 연결합니다.
* PCM에 문제가 있으면 즉시 교체를 요청합니다.
* 교체가 있고 설치 준비가 되었을 때에만 문제가 있는 PCM을 제거합니다.
* 두 PCM을 동시에 제거하지 마십시오. PCM 모듈에는 백업 배터리 모듈이 포함됩니다. PCM 두 개를 모두 제거하면 배터리 보호 없이 전원이 꺼지고 디바이스 상태가 저장되지 않습니다. 배터리에 대한 자세한 내용을 보려면 [백업 배터리 모듈 유지 관리](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module)로 이동하세요.

#### <a name="controller-modules"></a>컨트롤러 모듈

StorSimple 디바이스에는 운영 중 스왑 가능한 중복 컨트롤러 모듈이 포함됩니다. 컨트롤러 모듈은 활성/수동 방식으로 작동합니다. 지정된 시간에 다른 컨트롤러 모듈이 수동인 동안 하나의 컨트롤러 모듈은 활성 상태이며 서비스를 제공합니다. 수동 컨트롤러 모듈이 켜져 있고 활성 컨트롤러 모듈에 오류가 발생하거나 제거된 경우 작동하게 됩니다. 각 컨트롤러 모듈에는 전체 섀시에 서비스를 제공하기에 충분한 용량이 있습니다. 높은 가용성을 위해 컨트롤러 모듈을 모두 설치해야 합니다.

* 항상 두 컨트롤러 모듈이 모두 설치되었는지 확인합니다.
* 컨트롤러 모듈에 문제가 있으면 즉시 교체를 요청합니다.
* 교체가 있고 설치 준비가 되었을 때에만 문제가 있는 컨트롤러 모듈을 제거합니다. 오랫동안 모듈을 제거하면 기류에 영향을 미치므로 시스템 냉각에도 영향을 미칩니다.
* 두 컨트롤러 모듈에 대한 네트워크 연결이 동일해야 하며 연결된 네트워크 인터페이스에 동일한 네트워크 구성이 있어야 합니다.
* 한 컨트롤러 모듈에 장애가 발생하거나 교체가 필요한 경우, 장애가 발생한 컨트롤러 모듈을 교체하기 전에 다른 컨트롤러 모듈이 활성 상태인지 확인해야 합니다. 컨트롤러가 활성인지 확인하려면 [디바이스의 활성 컨트롤러 식별](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)로 이동하세요.
* 두 컨트롤러 모듈을 동시에 제거하지 마십시오. 컨트롤러 장애 조치가 진행 중이면 대기 컨트롤러 모듈을 종료하거나 섀시에서 제거하지 마십시오.
* 컨트롤러 장애 조치 후 컨트롤러 모듈을 제거하기 전에 5분 이상 기다립니다.

#### <a name="network-interfaces"></a>네트워크 인터페이스

StorSimple 디바이스 컨트롤러 모듈마다 1기가비트 4개 및 10기가비트 2개의 이더넷 네트워크 인터페이스가 있습니다.

* 두 컨트롤러 모듈에 대한 네트워크 연결이 동일해야 하며 컨트롤러 모듈 인터페이스가 연결된 네트워크 인터페이스에 동일한 네트워크 구성이 있어야 합니다.
* 가능하면 네트워크 디바이스에 오류가 발생하는 경우 서비스 가용성을 보장하기 위해 다른 스위치 간에 네트워크 연결을 배포합니다.
* 유일한 또는 마지막 남은 iSCSI 사용 인터페이스(할당된 IP 포함)를 분리할 때 먼저 해당 인터페이스를 사용하지 않도록 설정하고 케이블을 분리합니다. 인터페이스를 먼저 분리하는 경우 활성 컨트롤러가 수동 컨트롤러로 장애 조치됩니다. 또한 수동 컨트롤러에 해당 인터페이스가 분리되어 있는 경우 한 컨트롤러를 정하기 전에 두 컨트롤러가 여러 번 재부팅됩니다.
* 각 컨트롤러 모듈에서 네트워크에 둘 이상의 데이터 인터페이스를 연결합니다.
* 2개의 10GbE 인터페이스를 사용하도록 설정한 경우 다른 스위치 간에 배포합니다.
* 가능한 경우 서버가 링크, 네트워크 또는 인터페이스 오류를 허용할 수 있도록 서버에서 MPIO를 사용합니다.

고가용성 및 성능을 위한 디바이스 네트워킹 관련 정보를 보려면 [StorSimple 8100 디바이스 설치](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) 또는 [StorSimple 8600 디바이스 설치](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)로 이동하세요.

#### <a name="ssds-and-hdds"></a>SSD 및 HDD

StorSimple 디바이스에는 미러링된 공간을 사용하여 보호되는 SSD(Solid State Disk) 및 HDD(Hard Disk Drive)가 포함됩니다. 미러링된 공간을 사용하면 해당 디바이스가 하나 이상의 SSD 또는 HDD의 장애를 허용할 수 있습니다.

* 모든 SSD 및 HDD 모듈이 설치되었는지 확인합니다.
* SSD 또는 HDD 문제가 있으면 즉시 교체를 요청합니다.
* SSD 또는 HDD에 문제가 있거나 교체가 필요한 경우, 교체가 필요한 SSD 또는 HDD만 제거해야 합니다.
* 언제든지 시스템에서 하나 이상의 SSD 또는 HDD를 제거하지 마십시오.
  특정 유형의 2개 이상의 디스크(HDD, SDD) 오류 또는 단시간 프레임 내의 연속된 오류는 시스템 오작동 및 잠재적 데이터 손실을 야기할 수 있습니다. 이 경우 지원을 위해 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md) 합니다.
* 교체하는 동안 SSD 및 HDD에서 드라이브의 **하드웨어 상태** 블레이드에서 **공유 구성 요소**를 모니터링합니다. 녹색 확인 상태는 디스크가 정상 또는 확인 상태인지 나타내며, 빨간색 느낌표는 오류가 있는 SDD 또는 HDD를 나타냅니다.
* 시스템 오류가 있는 경우 보호가 필요한 모든 볼륨에 대한 클라우드 스냅숏을 구성하는 것이 좋습니다.

#### <a name="ebod-enclosure"></a>EBOD 인클로저

StorSimple 디바이스 모델 8600에는 기본 인클로저 외에도 확장 EBOD(Extended Bunch of Disks) 인클로저가 포함됩니다. EBOD에는 미러링된 공간을 사용하여 보호되는 EBOD 컨트롤러 및 HDD(하드 디스크 드라이브)가 포함됩니다. 미러링된 공간을 사용하면 해당 디바이스가 하나 이상의 HDD의 장애를 허용할 수 있습니다. EBOD 인클로저는 중복 SAS 케이블을 통해 기본 인클로저에 연결됩니다.

* 두 EBOD 인클로저 컨트롤러 모듈, 두 SAS 케이블 및 모든 하드 디스크 드라이브가 설치되어 있어야 합니다.
* EBOD 인클로저 컨트롤러 모듈에 오류가 있는 경우 즉시 교체를 요청합니다.
* EBOD 인클로저 컨트롤러 모듈에 오류가 있는 경우 오류가 있는 모듈을 교체하기 전에 다른 컨트롤러 모듈이 활성 상태인지 확인합니다. 컨트롤러가 활성인지 확인하려면 [디바이스의 활성 컨트롤러 식별](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)로 이동하세요.
* EBOD 컨트롤러 모듈을 교체하는 동안 **모니터** > **하드웨어 상태**에 액세스하여 StorSimple 디바이스 관리자 서비스의 구성 요소 상태를 연속해서 모니터링합니다.
* SAS 케이블에 오류가 있거나 교체가 필요한 경우(확인을 위해 Microsoft 지원이 포함되어야 함) 교체가 필요한 SAS 케이블만 제거해야 합니다.
* 언제든지 시스템에서 두 SAS 케이블을 동시에 제거하지 마십시오.

### <a name="high-availability-recommendations-for-your-host-computers"></a>호스트 컴퓨터에 대한 고가용성 권장 사항

StorSimple 디바이스에 연결된 호스트의 고가용성을 위해 이러한 모범 사례를 신중하게 검토해야 합니다.

* [2-노드 파일 서버 클러스터 구성][1]으로 StorSimple을 구성합니다. 호스트 쪽의 중복에서 오류 및 구축의 단일 지점을 제거하여 전체 솔루션 가용성이 높아집니다.
* 저장소 컨트롤러를 장애 조치하는 동안 고가용성을 위해 Windows Server 2012(SMB 3.0)와 함께 사용할 수 있는 CA(Continuously Available) 공유를 사용합니다. Windows Server 2012를 사용하여 파일 서버 클러스터 및 CA 공유 구성을 위한 추가 정보는 이 [비디오 데모](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [StorSimple 시스템 제한에 대해 자세히 알아봅니다](storsimple-8000-limits.md).
* [StorSimple 솔루션 배포 방법을 알아봅니다](storsimple-8000-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
