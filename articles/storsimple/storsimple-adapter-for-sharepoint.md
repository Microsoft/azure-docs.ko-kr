---
title: SharePoint용 StorSimple 어댑터 설치 | Microsoft Docs
description: SharePoint 서버 팜에서 SharePoint용 StorSimple 어댑터를 설치 및 구성하거나 제거하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: a2f8e75578e396085e7d80f43c1180e158967061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633421"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>SharePoint용 StorSimple 어댑터 설치 및 구성
## <a name="overview"></a>개요
SharePoint용 StorSimple 어댑터는 Microsoft Azure StorSimple 유연한 저장소 및 데이터 보호를 SharePoint 서버 팜에 제공할 수 있도록 하는 구성 요소입니다. 어댑터를 사용하여 SQL Server 콘텐츠 데이터베이스에서 Microsoft Azure StorSimple 하이브리드 클라우드 저장소 디바이스에 BLOB(Binary Large Object) 콘텐츠를 이동할 수 있습니다.

SharePoint용 StorSimple 어댑터는 RBS(Remote BLOB Storage) 공급자로서 기능하며 SQL Server 원격 BLOB 스토리지 기능을 사용하여 StorSimple 장치에서 지원하는 파일 서버에 구조화되지 않은 SharePoint 콘텐츠(Blob 형식)를 저장합니다.

> [!NOTE]
> SharePoint용 StorSimple 어댑터는 SharePoint Server 2010 RBS(Remote BLOB Storage)를 지원합니다. SharePoint Server 2010 EBS(External BLOB Storage)를 지원하지 않습니다.


* SharePoint용 StorSimple 어댑터를 다운로드하려면 Microsoft 다운로드 센터에서 [SharePoint용 StorSimple 어댑터][1]로 이동합니다.
* RBS 및 RBS 제한을 위한 계획에 대한 내용을 보려면 [SharePoint 2013에서 RBS 사용 결정][2] 또는 [RBS(SharePoint Server 2010)을 위한 계획][3]으로 이동합니다.

이 개요의 나머지 부분은 어댑터를 설치하고 구성하기 전에 알고 있어야 하는 SharePoint용 StorSimple 어댑터 및 SharePoint 용량 및 성능 제한의 역할을 간략하게 설명합니다. 이 정보를 검토한 후에 [SharePoint 설치용 StorSimple 어댑터](#storsimple-adapter-for-sharepoint-installation)로 이동하여 어댑터를 설정하기 시작합니다.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>SharePoint용 StorSimple 어댑터의 이점
SharePoint 사이트에서 콘텐츠는 하나 이상의 콘텐츠 데이터베이스에 구조화되지 않은 BLOB 데이터로 저장됩니다. 기본적으로 이러한 데이터베이스는 SQL Server를 실행하고 SharePoint 서버 팜에 있는 컴퓨터에서 호스팅됩니다. BLOB는 신속하게 크기가 증가할 수 있으며 이는 많은 온-프레미스 저장소를 소비합니다. 이러한 이유로 다른 더 저렴한 저장소 솔루션을 찾으려 할 수 있습니다. SQL Server는 SQL Server 데이터베이스 외부의 파일 시스템에 BLOB 콘텐츠를 저장할 수 있는 RBS(Remote BLOB Storage)라고 하는 기술을 제공합니다. RBS를 사용하면 BLOB를 SQL Server를 실행하는 컴퓨터의 파일 시스템에 두거나 다른 서버 컴퓨터의 파일 시스템에 저장할 수 있습니다.

RBS는 SharePoint용 StorSimple 어댑터와 같은 RBS 공급자를 사용하여 SharePoint에서 RBS를 활성화해야 합니다. SharePoint용 StorSimple 어댑터는 RBS를 사용하여 작동하며 이는 Microsoft Azure StorSimple 시스템에서 백업 서버로 BLOB를 이동시킵니다. Microsoft Azure StorSimple은 사용량에 따라 BLOB 데이터를 로컬 또는 클라우드에 저장합니다. 매우 활발한(일반적으로 계층1 또는 핫 데이터라고 함) BLOB은 로컬에 상주합니다. 덜 활성화된 데이터 및 아카이브 데이터는 클라우드에 상주합니다. 콘텐츠 데이터베이스에서 RBS를 사용하도록 설정한 후에 SharePoint에서 만든 새 BLOB 콘텐츠를 콘텐츠 데이터베이스가 아닌 StorSimple 디바이스에 저장합니다.

RBS의 Microsoft Azure StorSimple 구현은 다음과 같은 이점을 제공합니다.

* 별도 서버로 BLOB 콘텐츠를 이동하여 SQL Server에 쿼리 로드를 줄일 수 있고 이는 SQL Server 응답성을 향상시킬 수 있습니다. 
* Azure StorSimple는 중복 제거 및 압축을 사용하여 데이터 크기를 줄입니다.
* Azure StorSimple는 로컬 및 클라우드 스냅숏 형식으로 데이터 보호를 제공합니다. 또한 StorSimple 디바이스에 데이터베이스 자체를 배치하는 경우 충돌 일관성이 있는 방식으로 콘텐츠 데이터베이스 및 BLOB를 함께 백업할 수 있습니다. (콘텐츠 데이터베이스를 디바이스로 이동하면 StorSimple 8000 시리즈 디바이스만을 지원합니다. 이 기능은 5000 또는 7000 시리즈를 지원하지 않습니다.)
* Azure StorSimple는 장애 조치, 파일 및 볼륨 복구(테스트 복구 포함)를 포함하는 재해 복구 기능과 신속한 데이터 복원을 포함합니다.
* BLOB 데이터의 StorSimple 스냅숏으로 Kroll Ontrack PowerControls와 같은 데이터 복구 소프트웨어를 사용하여 SharePoint 콘텐츠의 항목 수준 복구를 수행합니다. (이 데이터 복구 소프트웨어는 별도 구매입니다.)
* SharePoint 용 StorSimple 어댑터를 SharePoint 중앙 관리 포털에 연결하면 전체 SharePoint 솔루션을 중앙 위치에서 관리할 수 있습니다.

파일 시스템에 BLOB 콘텐츠를 이동하면 다른 비용 절감 및 혜택을 제공할 수 있습니다. 예를 들어 RBS를 사용하면 비용이 많이 드는 계층1 저장소에 대한 필요성을 줄일 수 있고 콘텐츠 데이터베이스를 축소시키기 때문에 RBS가 SharePoint 서버 팜에서 필요한 데이터베이스의 수를 줄일 수 있습니다. 그러나 데이터베이스 크기 제한과 같은 다른 요인 및 비-RBS는 콘텐츠 양은 저장소 요구 사항에 영향을 줄 수 있습니다. 비용 및 RBS 사용의 이점에 대한 자세한 내용은 [RBS(SharePoint Foundation 2010)을 위한 계획][4] 및 [SharePoint 2013에서 RBS 사용 결정][5]을 참조하세요.

### <a name="capacity-and-performance-limits"></a>용량 및 성능 제한
SharePoint 솔루션에서 RBS를 사용을 고려하기 전에 SharePoint Server 2010 및 SharePoint Server 2013의 테스트된 성능 및 용량 제한과 이러한 제한이 어떻게 적절한 성능과 연관되는지 인식해야 합니다. 자세한 내용은 [소프트웨어 경계 및 SharePoint 2013에 대한 한계](https://technet.microsoft.com/library/cc262787.aspx)를 참조하세요.

RBS를 구성하기 전에 다음을 검토합니다.

* 콘텐츠의 총 크기가(콘텐츠 데이터베이스 크기와 연결되고 구체화된 BLOB의 크기를 더한 크기) SharePoint에서 지원하는 RBS 크기 제한을 초과하지 않도록 합니다. 이 제한은 200GB입니다. 
  
    **콘텐츠 데이터베이스 및 BLOB 크기를 측정하려면**
  
  1. 중앙 관리 WFE에서 이 쿼리를 실행합니다. SharePoint 관리 셸을 시작하고 다음 Windows PowerShell 명령을 입력하여 콘텐츠 데이터베이스의 크기를 가져옵니다.
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      이 단계는 디스크에서 콘텐츠 데이터베이스의 크기를 가져옵니다.
  2. 각 콘텐츠 데이터베이스의 SQL server 상자에 있는 SQL Management Studio에서 다음 SQL 쿼리 중 하나를 실행하고 단계1에서 확인된 숫자에 결과를 추가합니다.
     
     SharePoint 2013 콘텐츠 데이터베이스에서 다음을 입력합니다.
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     SharePoint 2010 콘텐츠 데이터베이스에서 다음을 입력합니다.
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     이 단계는 구체화된 BLOB의 크기를 가져옵니다.
* StorSimple 디바이스에 로컬로 모든 BLOB 및 데이터베이스 콘텐츠를 저장하는 것이 좋습니다. StorSimple 디바이스는 고가용성을 위한 두 개의 노드 클러스터입니다. StorSimple 디바이스에서 콘텐츠 데이터베이스 및 Blob을 배치하면 고가용성을 제공합니다.
  
    기존의 SQL Server 마이그레이션 모범 사례를 사용하여 콘텐츠 데이터베이스를 StorSimple 디바이스로 이동합니다. 모든 BLOB 콘텐츠를 RBS를 통해 데이터베이스에서 파일 공유로 이동한 후에만 데이터베이스를 이동합니다. 콘텐츠 데이터베이스를 StorSimple 디바이스로 이동하기로 선택한 경우, 기본 볼륨인 디바이스에서 콘텐츠 데이터베이스 저장소를 구성하는 것이 좋습니다.
* Microsoft Azure StorSimple에서 계층화된 볼륨을 사용하는 경우 StorSimple 디바이스에 로컬로 저장된 해당 콘텐츠가 Microsoft Azure 클라우드 저장소에 계층화되지 않았음을 보장할 방법은 없습니다. 따라서 SharePoint RBS와 함께 로컬로 고정된 StorSimple 볼륨을 사용하는 것이 좋습니다. 이렇게 하면 모든 Blob 콘텐츠가 StorSimple 디바이스에 로컬로 유지되고 Microsoft Azure로 이동되지 않습니다.
* StorSimple 디바이스에 콘텐츠 데이터베이스를 저장하지 않는 경우 RBS를 지원하는 기존의 SQL Server 고가용성 모범 사례를 사용합니다. SQL Server 미러링이 지원하지 않는 반면 SQL Server 클러스터링은 RBS를 지원합니다. 

> [!WARNING]
> RBS를 사용하도록 설정하지 않은 경우 콘텐츠 데이터베이스를 StorSimple 디바이스로 이동하지 않는 것이 좋습니다. 테스트되지 않은 구성입니다.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>SharePoint 설치용 StorSimple 어댑터
SharePoint용 StorSimple 어댑터를 설치하기 전에 StorSimple 디바이스를 구성해야 하고 SharePoint 서버 팜 및 SQL Server 인스턴스화가 필수 구성 요소를 충족하는지 확인합니다. 이 자습서에서는 SharePoint용 StorSimple 어댑터 설치 및 업그레이드에 대한 절차와 더불어 구성 요구 사항도 설명합니다.

## <a name="configure-prerequisites"></a>필수 조건 구성
SharePoint용 StorSimple 어댑터를 설치하기 전에 StorSimple 디바이스, SharePoint 서버 팜 및 SQL Server 인스턴스화가 다음 필수 구성 요소를 충족하는지 확인합니다.

### <a name="system-requirements"></a>시스템 요구 사항
SharePoint용 StorSimple 어댑터는 다음 하드웨어 및 소프트웨어와 함께 작동합니다.

* 지원되는 운영 체제 – Windows Server 2008 R2 SP1, Windows Server 2012 또는 Windows Server 2012 R2
* 지원되는 SharePoint 버전 – SharePoint Server 2010 또는 SharePoint Server 2013
* 지원되는 SQL Server 버전 – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition 또는 SQL Server 2012 Enterprise Edition
* 지원되는 StorSimple 디바이스 – StorSimple 8000 시리즈, StorSimple 7000 시리즈 또는 StorSimple 5000 시리즈입니다.

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple 디바이스 필수 구성 요소
StorSimple 디바이스는 블록 디바이스이며 따라서 데이터를 호스팅할 수 있는 파일 서버가 필요합니다. SharePoint 팜의 기존 서버보다 별도 서버를 사용하는 것이 좋습니다. 이 파일 서버는 콘텐츠 데이터베이스를 호스팅하는 SQL Server 컴퓨터와 동일한 LAN(로컬 영역 네트워크)에 있어야 합니다.

> [!TIP]
> * 또한 고가용성을 위해 SharePoint 팜을 구성하는 경우 고가용성을 위해 파일 서버를 배포해야 합니다.
> * StorSimple 디바이스에 콘텐츠 데이터베이스를 저장하지 않는 경우 RBS를 지원하는 기존의 고가용성 모범 사례를 사용합니다. SQL Server 미러링이 지원하지 않는 반면 SQL Server 클러스터링은 RBS를 지원합니다. 


StorSimple 디바이스를 제대로 구성하고 SharePoint 배포를 지원하기 위해 적절한 볼륨이 SQL Server 컴퓨터에서 구성되고 액세스할 수 있는지 확인합니다. StorSimple 디바이스를 배포 및 구성하려면 [온-프레미스 StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)로 이동합니다. StorSimple 디바이스의 IP 주소는 SharePoint용 StorSimple 어댑터를 설치하는 동안 필요합니다.

또한 BLOB 표면화에 사용할 볼륨이 다음 요구 사항을 충족하는지 확인합니다.

* 볼륨은 64KB 할당 단위 크기로 포맷되어야 합니다.
* WFE(웹 프런트 엔드) 및 애플리케이션 서버는 UNC(범용 명명 규칙) 경로를 통해 이 볼륨에 액세스할 수 있어야 합니다.
* SharePoint 서버 팜을 구성하여 볼륨에 써야 합니다.

> [!NOTE]
> 어댑터를 설치하고 구성한 후에 모든 BLOB 표면화는 StorSimple 디바이스를 통해 이동해야 합니다.(디바이스는 볼륨을 SQL Server에 표시하고 저장소 계층을 관리합니다.) BLOB 표면화에 다른 대상을 사용할 수 없습니다.


StorSimple Snapshot Manager를 사용하여 BLOB 및 데이터베이스 데이터의 스냅숏을 만드는 경우 데이터베이스 서버에 StorSimple Snapshot Manager를 설치하므로 SQL 기록기 서비스를 사용하여 Windows VSS(볼륨 섀도 복사본 서비스)를 구현할 수 있습니다.

> [!IMPORTANT]
> StorSimple Snapshot Manager는 SharePoint VSS 기록기를 지원하지 않으며 애플리케이션이 일관된 SharePoint 데이터의 스냅숏을 사용할 수 없습니다. SharePoint 시나리오에서 StorSimple Snapshot Manager는 크래시 일관성이 있는 백업만 제공합니다.


## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint 팜 필수 구성 요소
SharePoint 서버 팜이 올바르게 구성되어있는지 다음과 같이 확인합니다.

* SharePoint 서버 팜이 정상 상태인지 확인하고 다음을 확인합니다.
* 팜에 등록된 모든 SharePoint WFE와 애플리케이션 서버는 실행 중이며 SharePoint용 StorSimple 어댑터를 설치하는 서버에서 ping을 실행할 수 있습니다.
* SharePoint 타이머 서비스(SPTimerV3 또는 SPTimerV4)는 각 WFE 서버 및 애플리케이션 서버에서 실행됩니다.
* SharePoint 중앙 관리 사이트가 실행 중인 SharePoint 타이머 서비스 및 IIS 애플리케이션 풀은 관리 권한을 가집니다.
* Internet Explorer 보안 강화 컨텍스트(IE ESC)가 사용되지 않는지 확인합니다. 다음 단계를 수행하여 IE ESC를 사용하지 않도록 설정합니다.
  
  1. Internet Explorer의 모든 인스턴스를 닫습니다.
  2. 서버 관리자를 시작합니다.
  3. 왼쪽 창에서 **로컬 서버**를 클릭합니다.
  4. 오른쪽 창에서 **IE 보안 강화 구성** 옆의 **켜기**를 클릭합니다.
  5. **관리자**에서 **끄기**를 클릭합니다.
  6. **확인**을 클릭합니다.

## <a name="remote-blob-storage-rbs-prerequisites"></a>RBS(Remote BLOB Storage) 필수 구성 요소
SQL Server 중 지원되는 버전을 사용하고 있는지 확인합니다. 다음 버전만 지원되며 RBS를 사용할 수 있습니다.

* SQL Server 2008 Enterprise 버전
* SQL Server 2008 R2 Enterprise 버전
* SQL Server 2012 Enterprise 버전

BLOB는 SQL Server에 StorSimple 디바이스를 나타내는 볼륨에서만 표면화할 수 있습니다. BLOB 표면화를 위해 다른 대상은 지원되지 않습니다.

모든 필수 구성 요소 구성 단계를 완료한 경우 [SharePoint용 StorSimple 어댑터 설치](#install-the-storsimple-adapter-for-sharepoint)로 이동합니다.

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>SharePoint용 StorSimple 어댑터 설치
SharePoint용 StorSimple 어댑터를 설치하려면 다음 단계를 따르세요. 소프트웨어를 다시 설치하는 경우 [SharePoint용 StorSimple 어댑터 업그레이드 또는 다시 설치](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)를 참조하세요. 설치에 필요한 시간은 SharePoint 서버 팜에서 SharePoint 데이터베이스의 총 수에 따라 달라집니다.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>RBS 구성
SharePoint용 StorSimple 어댑터를 설치한 후에 다음 절차에서 설명한 대로 RBS를 구성합니다.

> [!TIP]
> SharePoint 중앙 관리 페이지에 SharePoint용 StorSimple 어댑터를 연결하면 SharePoint 팜의 각 콘텐츠 데이터베이스에서 RBS를 사용하거나 사용하지 못하도록 합니다. 그러나 콘텐츠 데이터베이스에서 RBS를 활성화 또는 비활성화하면 IIS가 재설정되어 팜 구성에 따라 일시적으로 SharePoint WFE(웹 프런트 엔드)의 가용성을 중단될 수 있습니다. (프런트 엔드 부하 분산 장치의 사용, 현재 서버 워크로드 등의 요인으로 이 중단을 제거하거나 제한할 수 있습니다.) 중단에서 사용자를 보호하려면 계획된 유지 관리 기간 동안 RBS를 사용하거나 사용하지 않도록 설정하는 것이 좋습니다.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>가비지 수집 구성
개체가 SharePoint 사이트에서 삭제되면 RBS 저장소 볼륨에서 자동으로 삭제되지 않습니다. 대신 비동기 백그라운드 유지 관리 프로그램이 삭제 저장소에서 분리된 BLOB를 삭제합니다. 시스템 관리자는 이 프로세스가 주기적으로 실행되도록 예약하거나 필요할 때마다 프로세스를 시작할 수 있습니다.

RBS를 사용하도록 설정하면 이 유지 관리 프로그램(Microsoft.Data.SqlRemoteBlobs.Maintainer.exe)은 모든 SharePoint WFE 서버 및 애플리케이션 서버에 자동으로 설치됩니다. 프로그램은 다음 위치에 설치됩니다. *부팅 드라이브*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

유지 관리 프로그램을 구성 및 사용하는 방법에 대한 자세한 내용은 [SharePoint Server 2013에서 RBS 유지 관리][8]를 참조하세요.

> [!IMPORTANT]
> RBS 유지 관리 프로그램은 많은 리소스를 사용합니다. SharePoint 팜에서 활동이 적은 기간 동안 실행되도록 예약해야 합니다.


### <a name="delete-orphaned-blobs-immediately"></a>분리된 BLOB 즉시 삭제
분리된 BLOB를 즉시 삭제해야 하는 경우 다음 지침을 사용할 수 있습니다. 이러한 지침은 다음 구성 요소가 있는 SharePoint 2013 환경에서 이를 수행하는 방법의 예시입니다.

* 콘텐츠 데이터베이스 이름은 WSS_Content입니다.
* SQL Server 이름은 SHRPT13 SQL12\SHRPT13입니다.
* 웹 애플리케이션 이름은 SharePoint-80입니다.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>SharePoint용 StorSimple 어댑터 업그레이드 또는 다시 설치
다음 절차를 사용하여 SharePoint 서버를 업그레이드한 다음 SharePoint용 StorSimple 어댑터를 다시 설치하거나 기존 SharePoint 서버 팜에서 어댑터를 업그레이드하거나 다시 설치합니다.

> [!IMPORTANT]
> SharePoint 소프트웨어를 업그레이드 및/또는 SharePoint용 StorSimple 어댑터를 업그레이드하거나 다시 설치하기 전에 다음 정보를 검토합니다.
> 
> * 다시 설치가 완료되고 RBS 기능을 다시 사용하도록 설정할 때까지 이전에 RBS를 통해 외부 저장소에 이동된 모든 파일은 사용할 수 없습니다. 사용자 영향을 제한하려면 계획된 유지 관리 기간 동안 업그레이드 또는 다시 설치를 수행합니다.
> * 업그레이드/다시 설치에 필요한 시간은 SharePoint 서버 팜에서 SharePoint 데이터베이스의 총 수에 따라 달라집니다.
> * 업그레이드/다시 설치가 완료되면 콘텐츠 데이터베이스에 RBS를 사용하도록 설정해야 합니다. 자세한 내용은 [RBS 구성](#configure-rbs)을 참조하세요.
> * 매우 많은(200개 초과) 데이터베이스가 있는 SharePoint 팜에 RBS를 구성하는 경우 **SharePoint 중앙 관리** 페이지가 시간을 초과할 수 있습니다. 발생하는 경우 페이지를 새로 고칩니다. 구성 프로세스에는 영향을 주지 않습니다.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>SharePoint용 StorSimple 어댑터 제거
다음 절차에서는 Blob을 SQL Server 콘텐츠 데이터베이스로 다시 이동한 다음 SharePoint용 StorSimple 어댑터를 제거하는 방법을 설명합니다. 

> [!IMPORTANT]
> 어댑터 소프트웨어를 제거하기 전에 Blob을 콘텐츠 데이터베이스로 다시 이동해야 합니다.


### <a name="before-you-begin"></a>시작하기 전에
데이터를 SQL Server 콘텐츠 데이터베이스로 다시 이동하고 어댑터 제거 프로세스를 시작하기 전에 다음 정보를 수집합니다.

* RBS가 사용되는 모든 데이터베이스의 이름
* 구성된 Blob 저장소의 UNC 경로

### <a name="move-the-blobs-back-to-the-content-databases"></a>Blob을 콘텐츠 데이터베이스로 다시 이동
SharePoint용 StorSimple 어댑터 소프트웨어를 제거하기 전에 표면화된 모든 Blob을 SQL Server 콘텐츠 데이터베이스로 다시 마이그레이션해야 합니다. 모든 Blob을 콘텐츠 데이터베이스로 다시 이동하기 전에 SharePoint용 StorSimple 어댑터를 제거하려고 하면 다음과 같은 경고 메시지가 표시됩니다.

![경고 메시지](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Blob을 콘텐츠 데이터베이스로 다시 이동하려면
1. 각 표면화된 개체를 다운로드합니다.
2. **SharePoint 중앙 관리** 페이지를 열고 **시스템 설정**으로 이동합니다.
3. **Azure StorSimple**에서 **StorSimple 어댑터 구성**을 클릭합니다.
4. **StorSimple 어댑터 구성** 페이지에서 외부 Blob Storage에서 제거하려는 각 콘텐츠 데이터베이스 아래의 **사용 안 함** 단추를 클릭합니다. 
5. SharePoint에서 개체를 삭제한 다음 다시 업로드합니다.

Microsoft를 사용할 수 있습니다 또는 `RBS Migrate()` SharePoint에 포함 된 PowerShell cmdlet. 자세한 내용은 [RBS에서 콘텐츠 마이그레이션](https://technet.microsoft.com/library/ff628255.aspx)을 참조하세요.

Blob을 콘텐츠 데이터베이스로 다시 이동한 후 다음 단계로 이동 합니다. [어댑터 제거](#uninstall-the-adapter)합니다.

### <a name="uninstall-the-adapter"></a>어댑터 제거
Blob을 SQL Server 콘텐츠 데이터베이스로 다시 이동한 후 다음 옵션 중 하나를 사용하여 SharePoint용 StorSimple 어댑터를 제거합니다.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>설치 프로그램을 사용하여 어댑터를 제거하려면
1. 관리자 권한이 있는 계정을 사용하여 WFE(웹 프런트 엔드) 서버에 로그온합니다.
2. SharePoint용 StorSimple 어댑터 설치 관리자를 두 번 클릭합니다. 설치 마법사가 시작됩니다.
   
    ![설치 마법사](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. **다음**을 클릭합니다. 다음 페이지가 나타납니다.
   
    ![설치 마법사 제거 페이지](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. **제거** 를 클릭하여 제거 프로세스를 선택합니다. 다음 페이지가 나타납니다.
   
    ![설치 마법사 확인 페이지](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. **제거**를 클릭하여 제거를 확인합니다. 다음 진행률 페이지가 나타납니다.
   
    ![설치 마법사 진행률 페이지](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. 제거가 완료되면 마침 페이지가 나타납니다. **마침**을 클릭하여 설치 마법사를 닫습니다.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>제어판을 사용하여 어댑터를 제거하려면
1. 제어판을 열고 **프로그램 및 기능**을 클릭합니다.
2. **SharePoint용 StorSimple 어댑터**를 선택하고 **제거**를 클릭합니다.

## <a name="next-steps"></a>다음 단계
[StorSimple에 대해 자세히 알아봅니다](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
