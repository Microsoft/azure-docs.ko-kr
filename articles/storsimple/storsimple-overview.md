---
title: StorSimple 8000 시리즈 솔루션 개요 | Microsoft Docs
description: StorSimple 계층화, 디바이스, 가상 디바이스, 서비스, 저장소 관리에 대해 설명하고 StorSimple에서 사용하는 주요 용어를 소개합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 63906e65acb8e8aa836e6e59714bddca24ea21eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630209"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 시리즈: 하이브리드 클라우드 저장소 솔루션
## <a name="overview"></a>개요
온-프레미스 디바이스 및 Microsoft Azure 클라우드 저장소 간의 저장소 작업을 관리하는 통합된 저장소 솔루션인 Microsoft Azure StorSimple을 시작합니다. StorSimple은 다양한 문제 및 엔터프라이즈 저장소 및 데이터 보호와 관련된 비용을 제거하는 효과적이고, 비용 효율적이며, 관리가 용이한 저장소 영역 네트워크(SAN) 솔루션입니다. 소유 StorSimple 8000 시리즈 디바이스를 사용하고, 클라우드 서비스와 통합되며, 클라우드 저장소를 비롯한 모든 엔터프라이즈 저장소의 원활한 뷰를 위한 관리 도구 집합을 제공합니다. (Microsoft Azure 웹 사이트에 게시된 StorSimple 배포 정보는 StorSimple 8000 시리즈 디바이스에만 적용됩니다. StorSimple 5000/7000 시리즈 디바이스를 사용하는 경우 [StorSimple 도움말](http://onlinehelp.storsimple.com/)로 이동합니다.)

StorSimple은 [저장소 계층화](#automatic-storage-tiering) 를 사용하여 다양한 저장소 미디어에 저장된 데이터를 관리합니다. 현재 작업 집합은 SSD(반도체 드라이브)에 온-프레미스로 저장되고, 자주 사용하지 않는 데이터는 HDD(하드 디스크 드라이브)에 저장되고, 보관 데이터는 클라우드에 푸시됩니다. 또한 StorSimple은 중복 제거 및 압축을 사용하여 데이터가 사용하는 저장소 크기를 줄입니다. 자세한 내용은 [중복 제거 및 압축](#deduplication-and-compression)으로 이동합니다. StorSimple 8000 시리즈 설명서에서 사용되는 다른 주요 용어의 정의 및 개념은 이 문서 끝에 있는 [StorSimple 용어](#storsimple-terminology) 로 이동합니다.

저장소 관리 외에도 StorSimple 데이터 보호 기능을 통해 주문형 백업 및 예약된 백업을 만든 다음 로컬로 또는 클라우드에 저장할 수 있습니다. Backup은 증분 스냅숏 형태로 생성되므로 빠르게 만들고 복원할 수 있습니다. 클라우드 스냅숏은 보조 저장소 시스템(예: 테이프 백업)을 대체하기 때문에 재해 복구 시나리오에서 매우 중요할 수 있으며, 필요한 경우 데이터 센터 또는 대체 사이트에 데이터를 복원할 수 있게 해줍니다.

![동영상 아이콘](./media/storsimple-overview/video_icon.png)  Microsoft Azure StorSimple의 간단한 소개에 대한 동영상을 봅니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>StorSimple을 사용하는 이유
다음 테이블은 Microsoft Azure StorSimple이 제공하는 주요 이점 중 일부를 설명합니다.

| 기능 | 혜택 |
| --- | --- |
| 투명한 통합 |iSCSI 프로토콜을 사용하여 데이터 저장소 시설을 보이지 않게 연결합니다. 이 클라우드, 데이터 센터 또는 원격 서버에 저장된 데이터가 단일 위치에 저장된 것처럼 보이게 합니다. |
| 저장소 비용 감소 |현재 요구에 맞게 충분한 로컬 또는 클라우드 저장소를 할당하고 필요한 경우 클라우드 저장소를 확장합니다. 동일한 데이터의 중복 버전을 제거하고(중복 제거) 압축을 사용하여 추가로 저장소 요구 사항 및 비용을 줄입니다. |
| 단순화된 저장소 관리 |온-프레미스, 원격 서버와 클라우드에 저장된 데이터를 구성 및 관리하는 시스템 관리 도구를 제공합니다. 또한 Microsoft Management Console(MMC) 스냅인에서 백업 및 복원 기능을 관리할 수 있습니다.|
| 향상된 재해 복구 및 규정 준수 |복구 시간을 확장할 필요가 없습니다. 대신, 필요할 때마다 데이터를 복원합니다. 즉, 정상 작업 중단을 최소화하면서 계속할 수 있습니다. 또한 백업 일정 및 데이터 보존 기간을 지정하는 정책을 구성할 수 있습니다. |
| 데이터 이동성 |복구 및 마이그레이션을 위해 다른 사이트에서 Microsoft Azure 클라우드 서비스에 업로드된 데이터에 액세스할 수 있습니다. 또한 Microsoft Azure에서 실행 중인 가상 머신(VM)에서 StorSimple Cloud Appliance를 구성하도록 StorSimple을 사용할 수 있습니다. VM을 가상 디바이스 사용하여 테스트 또는 복구 목적으로 저장된 데이터에 액세스할 수 있습니다. |
| 비즈니스 연속성 |StorSimple 5000~7000 시리즈 사용자가 StorSimple 8000 시리즈 디바이스에 해당 데이터를 마이그레이션할 수 있습니다. |
| Azure Government 포털의 가용성 |StorSimple은 Azure Government 포털에서 사용 가능합니다. 자세한 내용은 [정부 포털에서 온-프레미스 StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-gov-u2.md)를 참조하세요. |
| 데이터 보호 및 가용성 |StorSimple 8000 시리즈는 LRS(로컬 중복 저장소) 및 GRS(지역 중복 저장소) 외에도 ZRS(영역 중복 저장소)를 지원합니다. ZRS 세부 정보는 [Azure Storage 중복 옵션에 대한 기사](https://azure.microsoft.com/documentation/articles/storage-redundancy/) 를 참조하세요. |
| 중요한 애플리케이션에 대한 지원 |StorSimple을 사용하면 적절한 로컬 고정 볼륨을 식별할 수 있습니다. 따라서 중요한 애플리케이션에 필요한 데이터를 클라우드에 계층화하지 않아도 됩니다. 로컬로 고정된 볼륨은 클라우드 대기 시간 또는 연결 문제가 적용되지 않습니다. 로컬 고정 볼륨에 대한 자세한 내용은 [StorSimple 디바이스 관리자 서비스를 사용하여 볼륨 관리](storsimple-8000-manage-volumes-u2.md)를 참조하세요. |
| 짧은 대기 시간 및 고성능 |Azure Premium Storage의 고성능 및 낮은 대기 시간 기능을 활용하는 클라우드 어플라이언스를 만들 수 있습니다. StorSimple Premium Cloud Appliance에 대한 자세한 내용은 [Azure에서 StorSimple Cloud Appliance 배포 및 관리](storsimple-8000-cloud-appliance-u2.md)로 이동합니다. |


## <a name="storsimple-components"></a>StorSimple 구성 요소
Microsoft Azure StorSimple 솔루션에는 다음 구성 요소가 포함됩니다.

* **Microsoft Azure StorSimple 디바이스** – SSD 및 HDD가 포함된 온-프레미스 하이브리드 저장소 배열로, 중복 컨트롤러 및 자동 장애 조치 기능을 함께 제공합니다. 컨트롤러는 현재 사용되는(또는 핫) 데이터를 로컬 저장소(디바이스 또는 온-프레미스 서버)에 배치하고 덜 자주 사용되는 데이터는 클라우드로 이동하여 저장소 계층화를 관리합니다.
* **StorSimple Cloud Appliance** – StorSimple 가상 어플라이언스라고도 하며, 물리적 하이브리드 저장 디바이스의 아키텍처와 대부분의 기능을 복제하는 StorSimple 디바이스의 소프트웨어 버전입니다. StorSimple Cloud Appliance는 Azure 가상 컴퓨터의 단일 노드에서 실행됩니다. Azure Premium Storage를 활용하는 프리미엄 가상 장치는 업데이트 2 이상에서 사용할 수 있습니다.
* **StorSimple 디바이스 관리자 서비스** – 단일 웹 인터페이스에서 StorSimple 디바이스 또는 StorSimple Cloud Appliance를 관리할 수 있는 Azure Portal의 확장입니다. StorSimple 디바이스 관리자 서비스를 사용하여 서비스를 만들고 관리하고, 디바이스를 보고 관리하고, 경고를 보고, 볼륨을 관리하고 백업 정책 및 백업 카탈로그를 보고 관리할 수 있습니다.
* **StorSimple용 Windows PowerShell** – StorSimple 디바이스를 관리하는 데 사용할 수 있는 명령줄 인터페이스입니다. StorSimple용 Windows PowerShell에는 StorSimple 디바이스를 등록하고, 디바이스에서 네트워크 인터페이스를 구성하고 특정 형식의 업데이트를 설치하고, 지원 세션에 액세스하여 디바이스 문제를 해결하고 디바이스 상태를 변경할 수 있는 기능이 있습니다. 직렬 콘솔에 연결하거나 Windows PowerShell 원격 기능을 사용하여 StorSimple용 Windows PowerShell에 액세스할 수 있습니다.
* **Azure PowerShell StorSimple cmdlet** – 명령줄에서 서비스 수준 작업 및 마이그레이션 작업을 자동화할 수 있도록 하는 Windows PowerShell cmdlet 모음입니다. StorSimple용 Azure PowerShell cmdlet에 대한 자세한 내용은 [cmdlet 참조](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure)를 참조하세요.
* **StorSimple 스냅숏 관리자** – 볼륨 그룹 및 Windows 볼륨 섀도 복사본 서비스를 사용하여 애플리케이션에 일관된 백업을 생성하는 MMC 스냅인입니다. 또한 백업 일정 및 복제를 만들거나 볼륨을 복원하려면 StorSimple 스냅숏 관리자를 사용할 수 있습니다.
* **SharePoint용 StorSimple 어댑터** – SharePoint 중앙 관리 포털에서 StorSimple 저장소를 볼 수 있고 관리할 수 있도록 하면서 SharePoint 서버 팜으로 Microsoft Azure StorSimple 저장소 및 데이터 보호를 투명하게 확장하는 도구입니다.

아래 다이어그램은 Microsoft Azure StorSimple 아키텍처 및 구성 요소에 대한 상위 레벨 보기를 제공합니다.

![StorSimple 아키텍처](./media/storsimple-overview/overview-big-picture.png)

다음 섹션에서는 이러한 각 구성 요소를 자세히 설명하고 솔루션이 데이터를 정렬하고, 저장소를 할당하고 저장소 관리 및 데이터 보호를 용이하게 방법에 대해 설명합니다. 마지막 섹션에서는 StorSimple 구성 요소 및 해당 관리와 관련된 중요한 일부 용어 및 개념의 정의를 제공합니다.

## <a name="storsimple-device"></a>StorSimple 디바이스
Microsoft Azure StorSimple 디바이스는 기본 저장소 및 그 곳에 저장된 데이터에 대한 iSCSI 액세스를 제공하는 온-프레미스 하이브리드 저장소 배열입니다. 클라우드 저장소와의 통신을 관리하고 보안 및 Microsoft Azure StorSimple 솔루션에 저장된 모든 데이터의 기밀성을 보장하는데 도움이 됩니다.

StorSimple 디바이스에 클러스터링 및 자동 장애 조치에 대한 지원 및 SSD 및 하드 디스크 드라이브 HDD가 포함됩니다. 공유 프로세서, 공유 저장소 및 미러링된 컨트롤러 두 개를 포함합니다. 각 컨트롤러는 다음을 제공합니다.

* 호스트 컴퓨터에 연결
* 로컬 영역 네트워크(LAN)에 연결하는데 최대 6개의 네트워크 포트
* 하드웨어 모니터링
* 전원이 중단되는 경우 정보를 유지하는 비휘발성 임의 액세스 메모리(NVRAM)
* 클러스터 인식 업데이트는 서비스 가용성에 영향을 주지 않거나 최소한의 영향을 주도록 장애 조치 클러스터의 서버의 소프트웨어 업데이트를 관리합니다.
* 백엔드 클러스터와 같은 기능의 클러스터 서비스는 고가용성을 제공하며 HDD 또는 SSD가 고장나거나 오프라인으로 전환되는 경우 발생할 수 있는 부정적인 영향을 최소화합니다.

언제든지 컨트롤러 하나만 활성화됩니다. 활성 컨트롤러에 실패하면 두 번째 컨트롤러가 자동으로 활성화됩니다.

자세한 내용은 [StorSimple 하드웨어 구성 요소 및 상태](storsimple-8000-monitor-hardware-status.md)로 이동합니다.

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
StorSimple을 사용하여 물리적 하이브리드 저장소 디바이스의 아키텍처 및 기능을 복제하는 클라우드 어플라이언스를 만들 수 있습니다. StorSimple Cloud Appliance(StorSimple 가상 어플라이언스라고도 함)는 Azure 가상 머신의 단일 노드에서 실행됩니다. (클라우드 어플라이언스는 Azure 가상 머신에만 만들 수 있습니다. StorSimple 디바이스 또는 온-프레미스 서버에 만들 수 없습니다.)

클라우드 어플라이언스에는 다음과 같은 기능이 있습니다.

* 실제 기기처럼 동작하고 클라우드에서 가상 머신에 대한 iSCSI 인터페이스를 제공할 수 있습니다.
* 클라우드에서 개수에 제한 없이 클라우드 어플라이언스를 만들고 필요에 따라 설정 및 해제할 수 있습니다.
* 재해 복구, 개발 및 테스트 시나리오에서 온-프레미스 환경을 시뮬레이션할 수 있고 백업에서 항목 수준의 검색에 도움이 될 수 있습니다.

StorSimple Cloud Appliance는 8010 디바이스(이전의 1100 모델)와 8020 디바이스라는 두 가지 모델에서 사용할 수 있습니다. 8010 디바이스의 최대 용량은 30TB입니다. Azure Premium Storage를 활용하는 8020 장치의 최대 용량은 64TB입니다. (로컬 계층에서 Azure Premium Storage는 SSD에 데이터를 저장하는 반면 표준 스토리지는 HDD에 데이터를 저장합니다.) Premium Storage를 사용하려면 Azure Premium Storage 계정이 있어야 합니다.

StorSimple Cloud Appliance에 대한 자세한 내용은 [Azure에서 StorSimple Cloud Appliance 배포 및 관리](storsimple-8000-cloud-appliance-u2.md)로 이동합니다.

## <a name="storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스
Microsoft Azure StorSimple은 데이터 센터와 클라우드 저장소를 집중적으로 관리하기 위해 사용하는 웹 기반 사용자 인터페이스(StorSimple 디바이스 관리자 서비스)를 제공합니다. 다음 작업을 수행하는 데 StorSimple Device Manager 서비스를 사용할 수 있습니다.

* StorSimple 디바이스에 대한 시스템 설정을 구성합니다.
* StorSimple 디바이스에 대한 보안 설정을 구성하고 관리합니다.
* 클라우드 자격 증명 및 속성을 구성합니다.
* 서버에서 볼륨을 구성하고 관리합니다.
* 볼륨 그룹을 구성합니다.
* 데이터를 백업하고 복원합니다.
* 성능을 모니터링합니다.
* 시스템 설정을 검토하고 발생 가능한 문제를 식별합니다.

초기 설정 및 업데이트의 설치와 같은 시스템 작동 중단을 필요로 하는 작업을 제외한 모든 관리 작업을 수행하려면 StorSimple 디바이스 관리자 서비스를 사용할 수 있습니다.

자세한 내용은 [StorSimple Device Manager 서비스를 사용하여 StorSimple 디바이스 관리](storsimple-8000-manager-service-administration.md)로 이동합니다.

## <a name="windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell
StorSimple용 Windows PowerShell은 Microsoft Azure StorSimple 서비스 만들기, 관리, 설정 및 StorSimple 디바이스 설정 및 모니터링에 사용할 수 있는 명령줄 인터페이스를 제공합니다. StorSimple 디바이스를 관리하기 위한 전용 cmdlet을 포함하는 Windows PowerShell 기반의 명령줄 인터페이스입니다. StorSimple용 Windows PowerShell에서 다음을 수행할 수 있는 기능이 있습니다.

* 디바이스를 등록합니다.
* 디바이스에서 네트워크 인터페이스를 구성합니다.
* 특정 형식의 업데이트를 설치합니다.
* 지원 세션에 액세스하여 디바이스 문제를 해결합니다.
* 디바이스 상태를 변경합니다.

직렬 콘솔(디바이스에 직접 연결되는 호스트 컴퓨터)에서 또는 Windows PowerShell 원격 기능을 사용하여 원격으로 StorSimple용 Windows PowerShell에 액세스할 수 있습니다. 초기 디바이스 등록과 같이 StorSimple용 일부 Windows PowerShell은 직렬 콘솔에서만 수행할 수 있습니다.

자세한 내용은 [StorSimple용 Windows PowerShell을 사용하여 디바이스 관리](storsimple-8000-windows-powershell-administration.md)로 이동합니다.

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple cmdlet
Azure PowerShell StorSimple cmdlet은 명령줄에서 서비스 수준 작업 및 마이그레이션 작업을 자동화할 수 있도록 하는 Windows PowerShell cmdlet 모음입니다. StorSimple용 Azure PowerShell cmdlet에 대한 자세한 내용은 [cmdlet 참조](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0)를 참조하세요.

## <a name="storsimple-snapshot-manager"></a>StorSimple 스냅숏 관리자
StorSimple 스냅숏 관리자는 로컬 및 클라우드 데이터의 일관된 지정 시간 백업 복사본을 만드는 데 사용할 수 있는 Microsoft Management Console(MMC) 스냅인입니다. 스냅인은 Windows Server 기반 호스트에서 실행됩니다. StorSimple 스냅숏 관리자를 사용하여 다음을 수행할 수 있습니다.

* 볼륨을 구성, 백업 및 삭제합니다.
* 백업된 데이터가 애플리케이션과 일관되도록 볼륨 그룹을 구성합니다.
* 데이터를 미리 결정된 일정에 따라 백업하고 지정된 위치(로컬 또는 클라우드에)에 저장되도록 백업 정책을 관리합니다.
* 볼륨 및 개별 파일을 복원합니다.

Backup은 스냅숏으로 캡처되며, 마지막 스냅숏 백업 이후 변경 내용만 기록하고 전체 백업보다 훨씬 적은 저장소 공간을 필요로 합니다. 백업 일정을 만들 수도 있고 필요에 따라 즉시 백업할 수 있습니다. 또한 저장될 스냅숏의 크기를 제어하는 보존 정책을 설정하려면 StorSimple 스냅숏 관리자를 사용할 수 있습니다. 이후에 백업에서 데이터를 복원해야 하는 경우, StorSimple 스냅숏 관리자는 로컬 또는 클라우드 스냅숏의 카탈로그에서 선택하게 할 수 있습니다. 

재해가 발생한 경우 또는 다른 이유로 데이터를 복원해야 하는 경우, StorSimple 스냅숏 관리자는 필요할 때 증분 방식으로 복원합니다. 파일을 복원하거나, 장비를 교체 하거나 다른 사이트로 이동하는 동안 시스템 전체를 종료하지 않아도 됩니다.

자세한 내용은 [StorSimple 스냅숏 관리자란?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>SharePoint용 StorSimple 어댑터
Microsoft Azure StorSimple은 StorSimple 저장소 및 데이터 보호 기능을 SharePoint 서버 팜으로 투명하게 확장하는 선택적 구성 요소인, SharePoint용 StorSimple 어댑터를 포함합니다. 어댑터는 Blob를 Microsoft Azure StorSimple 시스템에서 백업한 서버로 이동할 수 있도록 원격 Blob Storage(RBS) 공급자 및 SQL Server RBS 기능과 함께 작동합니다. Microsoft Azure StorSimple은 사용량에 따라 BLOB 데이터를 로컬 또는 클라우드에 저장합니다.

SharePoint용 StorSimple 어댑터는 SharePoint 중앙 관리 포털 내에서 관리됩니다. 따라서 SharePoint 관리가 중앙 집중화된 상태로 유지되며 모든 저장소는 SharePoint 팜에 있는 것으로 나타납니다.

자세한 내용은 [SharePoint용 StorSimple 어댑터](storsimple-adapter-for-sharepoint.md)로 이동합니다. 

## <a name="storage-management-technologies"></a>저장소 관리 기술
전용 StorSimple 디바이스, 가상 디바이스 및 다른 구성 요소 외에, Microsoft Azure StorSimple은 다음 소프트웨어 기술을 사용하여 데이터에 대한 빠른 데이터 액세스를 제공하고 저장소 사용량을 줄입니다.

* [자동 저장소 계층화](#automatic-storage-tiering) 
* [씬 프로비저닝](#thin-provisioning) 
* [중복 제거 및 압축](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>자동 저장소 계층화
Microsoft Azure StorSimple은 현재 사용량, 수명 및 다른 데이터에 대한 관계에 따라 논리적 계층의 데이터를 자동으로 정렬합니다. 가장 활성화된 데이터는 로컬로 저장되지만, 덜 활성화된 데이터 및 덜 비활성된 데이터는 자동으로 클라우드로 마이그레이션됩니다. 다음 다이어그램에서는 이러한 저장소 사용 방식을 보여줍니다.

![StorSimple 저장소 계층](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

빠른 액세스를 설정하려면 StorSimple은 StorSimple 디바이스의 SSD에 사용량이 매우 많은 데이터(핫 데이터)를 저장합니다. 가끔씩 사용되는 데이터(웜 데이터)를 데이터 센터에 있는 서버 또는 디바이스의 HDD에 저장합니다. 비활성 데이터, 백업 데이터 및 보존 및 보관 또는 규정 준수용으로 보유한 데이터를 클라우드로 이동합니다. 

> [!NOTE]
> 업데이트 2 이상에서 볼륨을 로컬로 고정으로 지정할 수 있으며 이 경우 데이터는 로컬 디바이스에 남아 있고 클라우드로 계층화되지 않습니다. 


StorSimple는 데이터와 저장소 할당을 조정하여 사용량 패턴 변경으로 다시 지정합니다. 예를 들어 일부 정보는 시간이 지남에 따라 덜 활성화될 수 있습니다. 점진적으로 덜 활성화되면서 SSD에서 HDD로 마이그레이션된 다음 클라우드로 마이그레이션됩니다. 일부 데이터가 다시 활성화되면, 저장 디바이스에 다시 마이그레이션됩니다.

저장소 계층화 프로세스는 다음과 같이 수행됩니다.

1. 시스템 관리자가 Microsoft Azure 클라우드 저장소 계정을 설정합니다.
2. 관리자는 직렬 콘솔과 StorSimple 디바이스 관리자 서비스(Azure Portal에서 실행)를 사용하여 디바이스 및 파일 서버를 구성하고, 볼륨과 데이터 보호 정책을 만듭니다. 온-프레미스 컴퓨터(예: 파일 서버)가 iSCSI(Internet Small Computer System Interface)를 사용하여 StorSimple 디바이스에 액세스합니다.
3. 처음에 StorSimple은 디바이스의 빠른 SSD 계층에 데이터를 저장합니다.
4. SSD 계층이 용량에 가까워지면 StorSimple에서 가장 오래된 데이터 블록을 중복 제거 및 압축하고 HDD 계층으로 이동합니다.
5. HDD 계층이 용량에 가까워지면 StorSimple에서 가장 오래된 데이터 블록을 암호화하고 HTTPS를 통해 Microsoft Azure 저장소 계정으로 안전하게 보냅니다.
6. Microsoft Azure가 해당 데이터 센터 및 원격 데이터 센터에 데이터의 여러 복제본을 만들어 재해가 발생할 경우 데이터를 복구할 수 있도록 합니다.
7. 파일 서버가 클라우드에 저장된 데이터를 요청하는 경우 StorSimple에서 원활하게 데이터를 반환하고 StorSimple 디바이스의 SSD 계층에 복사본을 저장합니다.

#### <a name="how-storsimple-manages-cloud-data"></a>StorSimple에서 클라우드 데이터를 관리하는 방법

StorSimple에서는 모든 스냅숏 및 주 데이터(호스트가 기록한 데이터)에서 중복된 고객 데이터를 제거합니다. 중복 제거가 저장소 효율성 면에서는 유용하지만, "클라우드가 무엇인가"라는 질문을 복잡하게 만듭니다. 계층화된 주 데이터 및 스냅숏 데이터는 서로 겹칩니다. 클라우드에서 데이터의 단일 청크는 계층화된 주 데이터 계층으로 사용되거나 여러 스냅숏에 의해 참조될 수 있습니다. 모든 클라우드 스냅숏을 통해 해당 스냅숏을 삭제할 때까지 특정 시점 데이터의 모든 복사본을 클라우드에 잠글 수 있습니다.

해당 데이터에 대한 참조가 없는 경우에만 클라우드에서 데이터가 삭제됩니다. 예를 들어, StorSimple 디바이스에 있는 모든 데이터의 클라우드 스냅숏을 사용하고 일부 주 데이터를 삭제한다면 _주 데이터_가 즉시 삭제됩니다. 계층화된 데이터와 백업을 포함하는 _클라우드 데이터_는 동일하게 유지됩니다. 클라우드 데이터를 계속 참조하는 스냅숏이 아직 존재하기 때문입니다. 클라우드 스냅숏(및 동일한 데이터를 참조하는 다른 모든 스냅숏)이 삭제된 후에 클라우드 소비가 감소합니다. 클라우드 데이터를 제거하기 전에 스냅숏이 해당 데이터를 계속 참조하지 않는지 확인합니다. 이 프로세스는 _가비지 수집_이라고 하며 디바이스에서 실행되는 배경 서비스입니다. 데이터를 삭제하기 전에 가비지 수집 서비스가 해당 데이터에 대한 다른 참조가 있는지 확인하기 때문에 클라우드 데이터는 즉시 제거되지 않습니다. 가비지 수집의 속도는 스냅숏 및 전체 데이터의 총 수에 따라 다릅니다. 일반적으로 클라우드 데이터는 일주일 내에 정리됩니다.


### <a name="thin-provisioning"></a>씬 프로비저닝
씬 프로비저닝은 사용 가능한 저장소가 실제 리소스를 초과하는 것처럼 표시하는 가상화 기술입니다. 충분한 저장소를 사전에 예약하는 대신 StorSimple는 씬 프로비전을 사용하여 현재 요구 사항에 맞게 충분한 공간을 할당합니다. 클라우드 저장소의 탄력적인 특징으로 인해 StorSimple가 변화하는 요구에 맞게 클라우드 저장소를 늘리거나 줄일 수 있으므로 이 방법이 용이합니다.

> [!NOTE]
> 로컬로 고정된 볼륨은 씬 프로비전되지 않습니다. 볼륨을 만들 때 로컬 전용 볼륨에 할당된 저장소는 전체적으로 프로비전됩니다.


### <a name="deduplication-and-compression"></a>중복 제거 및 압축
Microsoft Azure StorSimple은 중복 제거 및 데이터 압축을 사용하여 저장소 요구 사항을 줄일 수 있습니다.

중복 제거는 저장된 데이터 집합에서 중복을 제거하여 저장된 데이터의 전체 크기를 줄일 수 있습니다. 정보가 변경되면 StorSimple은 변경되지 않은 데이터를 무시하고 변경 내용만 캡처합니다. 또한 StorSimple은 불필요한 정보를 식별하여 제거하여 저장된 데이터의 크기를 줄입니다. 

> [!NOTE]
> 로컬로 고정된 볼륨의 데이터는 중복 제거되거나 압축되지 않습니다. 그러나 로컬 고정된 볼륨의 백업은 중복 제거되며 압축됩니다.


## <a name="storsimple-workload-summary"></a>StorSimple 워크로드 요약
지원되는 StorSimple 워크로드가 아래 표에 요약되어 있습니다.

| 시나리오 | 워크로드 | 지원됨 | 제한 | Version |
| --- | --- | --- | --- | --- |
| 공동 작업 |파일 공유 |예 | |모든 버전 |
| 공동 작업 |분산 파일 공유 |예 | |모든 버전 |
| 공동 작업 |SharePoint |예* |로컬 고정 볼륨에 대해서만 지원됩니다. |업데이트 2 이상 |
| 보관 |단순 파일 보관 |예 | |모든 버전 |
| 가상화 |가상 머신 |예* |로컬 고정 볼륨에 대해서만 지원됩니다. |업데이트 2 이상 |
| 데이터베이스 |SQL |예* |로컬 고정 볼륨에 대해서만 지원됩니다. |업데이트 2 이상 |
| 비디오 감시 |비디오 감시 |예* |StorSimple 디바이스가 이 작업에만 전용으로 사용될 때 지원됩니다. |업데이트 2 이상 |
| Backup |기본 대상 백업 |예* |StorSimple 디바이스가 이 작업에만 전용으로 사용될 때 지원됩니다. |업데이트 3 이상 |
| Backup |보조 대상 백업 |예* |StorSimple 디바이스가 이 작업에만 전용으로 사용될 때 지원됩니다. |업데이트 3 이상 |

*예&#42; - 솔루션 지침 및 제한 사항이 적용됩니다.*

다음 워크로드는 StorSimple 8000 시리즈 디바이스에서 지원되지 않습니다. StorSimple에 배포하는 경우 이러한 워크로드로 인해 지원되지 않는 구성이 형성됩니다.

* 의료 이미지
* Exchange
* VDI
* Oracle
* SAP
* 빅데이터
* 콘텐츠 배포
* SCSI에서 부팅

다음은 StorSimple 지원 인프라 구성 요소의 목록입니다.

| 시나리오 | 워크로드 | 지원됨 | 제한 | Version |
| --- | --- | --- | --- | --- |
| 일반 |Express 경로 |예 | |모든 버전 |
| 일반 |DataCore FC |예* |DataCore SANsymphony 지원 |모든 버전 |
| 일반 |DFSR |예* |로컬 고정 볼륨에 대해서만 지원됩니다. |모든 버전 |
| 일반 |인덱싱 |예* |계층화된 볼륨의 경우 메타데이터만 인덱싱만 지원됩니다(데이터 없음).<br>로컬 고정 볼륨의 경우 전체 인덱싱이 지원됩니다. |모든 버전 |
| 일반 |바이러스 백신 |예* |계층화된 볼륨의 경우 열기 및 닫기 시 검색만 지원됩니다.<br>  로컬 고정 볼륨의 경우 전체 검색이 지원됩니다. |모든 버전 |

*예&#42; - 솔루션 지침 및 제한 사항이 적용됩니다.*

솔루션을 빌드하기 위해 StorSimple에서 사용되는 다른 소프트웨어의 목록은 다음과 같습니다.

| 워크로드 유형 | StorSimple에서 사용되는 소프트웨어 | 지원되는 버전|솔루션 가이드에 연결| 
| --- | --- | --- | --- |
| Backup 대상 |Veeam |Veeam v 9 이상 |[Veaam에서 백업 대상인 StorSimple](storsimple-configure-backup-target-veeam.md)|
| Backup 대상 |Veritas Backup Exec |Backup Exec 16 이상 |[Backup Exec에서 백업 대상으로 StorSimple 구성](storsimple-configure-backup-target-using-backup-exec.md)|
| Backup 대상 |Veritas NetBackup |NetBackup 7.7.x 이상  |[NetBackup에서 백업 대상인 StorSimple](storsimple-configure-backuptarget-netbackup.md)|
| 전역 파일 공유 <br></br> 공동 작업 |Talon  |[Talon을 사용하는 StorSimple](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple 용어
Microsoft Azure StorSimple 솔루션을 배포하기 전에 다음 용어와 정의를 검토하는 것이 좋습니다.

### <a name="key-terms-and-definitions"></a>주요 용어 및 정의
| 용어(머리글자어 또는 약어) | 설명 |
| --- | --- |
| 액세스 제어 레코드(ACR) |연결할 수 있는 호스트를 결정하는, Microsoft Azure StorSimple 디바이스의 볼륨과 연결된 레코드입니다. 이 결정은 StorSimple 디바이스에 연결하는 호스트의 IQN(iSCSI 정규화된 이름)(ACR에 포함됨)을 기반으로 합니다. |
| AES-256 |클라우드에 출입할 때 데이터를 암호화하기 위한 256비트 AES(Advanced Encryption Standard) 알고리즘입니다. |
| 할당 단위 크기(AUS) |Windows 파일 시스템에 파일을 저장하기 위해 할당될 수 있는 가장 작은 크기의 디스크 공간입니다. 파일 크기가 클러스터 크기의 짝수 배수가 아닌 경우 파일을 저장하기 위해 클러스터 크기의 다음 배수까지 추가 공간을 사용해야 하므로 공간 손실 및 하드 디스크 조각화가 발생합니다. <br>Azure StorSimple 볼륨에 대한 권장 AUS는 64KB로, 중복 제거 알고리즘에서 효과적으로 작동하기 때문입니다. |
| 자동화된 저장소 계층화 |사용 빈도가 낮은 데이터를 SSD에서 HDD로 이동한 후 다시 클라우드의 한 계층으로 자동으로 이동하고 중앙 사용자 인터페이스에서 모든 저장소를 관리할 수 있도록 합니다. |
| 백업 카탈로그 |일반적으로 사용된 애플리케이션 유형과 관련된 백업 컬렉션입니다. 이 컬렉션은 StorSimple 디바이스 관리자 서비스 UI의 Backup 카탈로그 블레이드에 표시됩니다. |
| 백업 카탈로그 파일 |StorSimple 스냅숏 관리자의 백업 데이터베이스에 현재 저장되어 있는 사용 가능한 스냅숏 목록을 포함하는 파일입니다. |
| 백업 정책 |미리 정의된 일정에 따라 백업을 만들 수 있는 선택한 볼륨, 백업 유형 및 시간표입니다. |
| Binary Large Object(BLOB) |데이터베이스 관리 시스템에서 단일 엔터티로 저장되는 이진 데이터 컬렉션입니다. 이진 실행 코드가 BLOB으로 저장되는 경우도 있지만 BLOB은 일반적으로 이미지, 오디오 또는 기타 멀티미디어 개체입니다. |
| Challenge Handshake 인증 프로토콜(CHAP) |암호를 공유하는 피어를 기준으로 연결의 피어를 인증하는 데 사용되는 프로토콜입니다. CHAP는 단방향 또는 상호일 수 있습니다. 단방향 CHAP에서는 대상이 초기자를 인증합니다. 상호 CHAP에서는 대상이 초기자를 인증하고 초기자가 대상을 인증해야 합니다. |
| 복제 |볼륨의 중복 복사본입니다. |
| Cloud as a Tier(CaaT) |모든 저장소가 단일 엔터프라이즈 저장소 네트워크의 일부로 표시되도록 저장소 아키텍처 내에 계층으로 통합된 클라우드 저장소입니다. |
| 클라우드 서비스 공급자(CSP) |클라우드 컴퓨팅 서비스의 공급자입니다. |
| 클라우드 스냅숏 |클라우드에 저장된 볼륨 데이터의 지정 시간 복사본입니다. 클라우드 스냅숏은 다른 오프 사이트 저장소 시스템에 복제된 스냅숏과 같습니다. 클라우드 스냅숏은 특히 재해 복구 상황에서 유용합니다. |
| 클라우드 저장소 암호화 키 |StorSimple 디바이스가 디바이스에서 클라우드로 보낸 암호화된 데이터에 액세스하는 데 사용하는 암호 또는 키입니다. |
| 클러스터 인식 업데이트 |업데이트가 서비스 가용성에 영향을 주지 않거나 최소한의 영향을 주도록 장애 조치(failover) 클러스터의 서버에 대한 소프트웨어 업데이트를 관리합니다. |
| 데이터 경로 |상호 연결된 데이터 처리 작업을 수행하는 기능 단위 컬렉션입니다. |
| 비활성화 |StorSimple 디바이스 및 연결된 클라우드 서비스 간의 연결을 끊는 영구적인 작업입니다. 디바이스의 클라우드 스냅숏은 이 프로세스 후에도 유지되며, 복제하거나 재해 복구에 사용할 수 있습니다. |
| 디스크 미러링 |지속적인 가용성을 보장하기 위해 논리 디스크 볼륨을 별도의 하드 드라이브에 실시간으로 복제합니다. |
| 동적 디스크 미러링 |동적 디스크에 논리 디스크 볼륨을 복제합니다. |
| 동적 디스크 |LDM(논리 디스크 관리자)을 사용하여 여러 실제 디스크에 데이터를 저장하고 관리하는 디스크 볼륨 형식입니다. 사용 가능한 추가 공간을 제공하기 위해 동적 디스크를 확장할 수 있습니다. |
| EBOD(Extended Bunch of Disks) 엔클로저 |추가 저장소를 위해 여분의 하드 드라이브 디스크를 포함하는 Microsoft Azure StorSimple 디바이스의 보조 엔클로저입니다. |
| 팻 프로비저닝 |예상 요구를 기준으로 저장소 공간이 할당되며 일반적으로 현재 요구를 초과하는 기존 저장소 프로비저닝입니다. *씬 프로비저닝*도 참조하세요. |
| 하드 디스크 드라이브(HDD) |회전하는 플래터를 사용하여 데이터를 저장하는 드라이브입니다. |
| 하이브리드 클라우드 저장소 |클라우드 저장소를 포함하여 로컬 및 오프사이트 리소스를 사용하는 저장소 아키텍처입니다. |
| Internet Small Computer System Interface(iSCSI) |데이터 저장소 장비 또는 시설을 연결하기 위한 IP(인터넷 프로토콜) 기반 저장소 네트워킹 표준입니다. |
| iSCSI 초기자 |Windows를 실행하는 호스트 컴퓨터가 외부 iSCSI 기반 저장소 네트워크에 연결할 수 있게 해주는 소프트웨어 구성 요소입니다. |
| iSCSI 정규화된 이름(IQN) |ISCSI 대상 또는 초기자를 식별하는 고유 이름입니다. |
| iSCSI 대상 |저장소 영역 네트워크에서 중앙 집중화된 iSCSI 디스크 하위 시스템을 제공하는 소프트웨어 구성 요소입니다. |
| 라이브 보관 |보관 데이터에 항상 액세스할 수 있는 저장소 접근 방법입니다(예: 오프사이트 테이프에 저장되지 않음). Microsoft Azure StorSimple은 라이브 보관을 사용합니다. |
| 로컬로 고정된 볼륨 |디바이스에 상주하며 클라우드로 계층화되지 않는 볼륨입니다. |
| 로컬 스냅숏 |Microsoft Azure StorSimple 디바이스에 저장된 볼륨 데이터의 지정 시간 복사본입니다. |
| Microsoft Azure StorSimple |IT 조직이 데이터 센터 저장소인 것처럼 클라우드 저장소를 활용하고 비용을 절감하게 해주는 데이터 센터 저장소 어플라이언스 및 소프트웨어로 구성된 강력한 솔루션입니다. StorSimple은 비용을 절감하는 동시에 데이터 보호 및 데이터 관리를 간소화합니다. 솔루션은 클라우드와의 원활한 통합을 통해 기본 저장소, 보관, 백업 및 DR(재해 복구)을 통합합니다. 엔터프라이즈급 플랫폼에서 SAN 저장소 및 클라우드 데이터 관리를 결합함으로써 StorSimple 디바이스는 모든 저장소 관련 요구에 대해 속도, 단순성 및 안정성을 가능하게 합니다. |
| 전원 및 냉각 모듈(PCM) |전원 공급 디바이스 및 냉각 팬으로 구성되어 전원 및 냉각 모듈이란 이름이 지정된 StorSimple 디바이스의 하드웨어 구성 요소입니다. 디바이스의 기본 엔클로저에는 764W PCM 2개가 있는 반면, EBOD 엔클로저에는 580W PCM 2개가 있습니다. |
| 기본 엔클로저 |애플리케이션 플랫폼 컨트롤러를 포함하는 StorSimple 장치의 기본 엔클로저입니다. |
| 복구 시간 목표(RTO) |재해 발생 후 비즈니스 프로세스 또는 시스템이 완전히 복원되기 전에 연장해야 하는 최대 시간입니다. |
| Serial Attached SCSI(SAS) |HDD(하드 디스크 드라이브)의 한 유형입니다. |
| 서비스 데이터 암호화 키 |StorSimple 디바이스 관리자 서비스에 등록하는 모든 새 StorSimple 디바이스에서 사용할 수 있는 키입니다. StorSimple 디바이스 관리자 서비스와 디바이스 간에 전송되는 구성 데이터는 공개 키를 사용하여 암호화된 후 개인 키를 사용하는 디바이스에서만 해독할 수 있습니다. 서비스 데이터 암호화 키를 사용하면 서비스에서 암호 해독을 위해 이 개인 키를 얻을 수 있습니다. |
| 서비스 등록 키 |키는 StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 등록하도록 도와줌으로써 Azure Portal의 추가 관리 작업에 표시됩니다. |
| Small Computer System Interface(SCSI) |컴퓨터를 물리적으로 연결하고 컴퓨터 간에 데이터를 전달하기 위한 표준 집합입니다. |
| 반도체 드라이브(SSD) |움직이는 부분이 없는 디스크(예: 플래시 드라이브)입니다. |
| 저장소 계정 만들기 |지정된 클라우드 서비스 공급자에 대해 저장소 계정에 연결된 액세스 자격 증명 집합입니다. |
| SharePoint용 StorSimple 어댑터 |StorSimple 저장소 및 데이터 보호 기능을 SharePoint 서버 팜으로 투명하게 확장하는 Microsoft Azure StorSimple 구성 요소입니다. |
| StorSimple 디바이스 관리자 서비스 |Azure Portal의 확장은 StorSimple 온-프레미스 및 가상 디바이스를 관리할 수 있게 해줍니다. |
| StorSimple 스냅숏 관리자 |Microsoft Azure StorSimple에서 백업 및 복원 작업을 관리하기 위한 MMC(Microsoft Management Console) 스냅인입니다. |
| 백업 수행 |사용자가 대화형으로 볼륨을 백업할 수 있게 해주는 기능입니다. 정의된 정책을 통해 자동화된 백업을 수행하는 대신 볼륨의 수동 백업을 수행하는 대체 방법입니다. |
| 씬 프로비저닝 |사용 가능한 저장소 공간이 저장소 시스템에서 사용되는 효율성을 최적화하는 방법입니다. 씬 프로비저닝에서는 지정된 시간에 각 사용자에게 필요한 최소 공간을 기준으로 여러 사용자 간에 저장소가 할당됩니다. *팻 프로비저닝*도 참조하세요. |
| 계층화 |현재 사용량, 수명 및 다른 데이터에 대한 관계에 따라 논리적 그룹화에서 데이터를 정렬합니다. StorSimple은 계층에서 데이터를 자동으로 정렬합니다. |
| 볼륨 |드라이브 형태로 제공되는 논리 저장소 영역입니다. StorSimple 볼륨은 iSCSI 및 StorSimple 디바이스를 사용하여 검색된 볼륨을 포함하여 호스트에 의해 탑재된 볼륨에 해당합니다. |
| 볼륨 컨테이너 |볼륨 및 볼륨에 적용되는 설정의 그룹화입니다. StorSimple 디바이스의 모든 볼륨은 볼륨 컨테이너로 그룹화됩니다. 볼륨 컨테이너 설정에는 저장소 계정, 연결된 암호화 키와 함께 클라우드에 전송된 데이터에 대한 암호화 설정 및 클라우드 관련 작업에 사용된 대역폭이 포함됩니다. |
| 볼륨 그룹 |StorSimple 스냅숏 관리자에서 볼륨 그룹은 백업 처리가 용이하도록 구성된 볼륨 컬렉션입니다. |
| 볼륨 섀도 복사본 서비스(VSS) |VSS 인식 애플리케이션과의 통신을 통해 증분 스냅숏 생성을 조정하여 애플리케이션 일관성을 높이는 Windows Server 운영 체제 서비스입니다. VSS는 스냅숏을 만들 때 애플리케이션이 일시적으로 비활성화되도록 합니다. |
| StorSimple용 Windows PowerShell |StorSimple 디바이스를 작동 및 관리하는 데 사용되는 Windows PowerShell 기반 명령줄 인터페이스입니다. Windows PowerShell의 일부 기본 기능을 유지하는 동시에 이 인터페이스는 StorSimple 디바이스를 관리하기 위한 전용 cmdlet을 추가로 포함합니다. |

## <a name="next-steps"></a>다음 단계
[StorSimple 보안](storsimple-8000-security.md)에 대해 알아봅니다.

