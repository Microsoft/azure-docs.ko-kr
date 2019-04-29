---
title: Microsoft Azure StorSimple 가상 배열 개요 | Microsoft Docs
description: 온-프레미스 가상 배열과 Microsoft Azure 클라우드 저장소 간의 저장소 작업을 관리하는 통합된 저장소 솔루션인 StorSimple 가상 배열을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: e5713af737a6d9d190814b4155a8e772deea06bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630367"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>StorSimple 가상 배열 소개

## <a name="overview"></a>개요

Microsoft Azure StorSimple 가상 배열은 하이퍼바이저 및 Microsoft Azure 클라우드 저장소에서 실행되는 온-프레미스 가상 배열 간의 저장소 작업을 관리하는 통합 저장소 솔루션입니다. 가상 배열은 다양한 문제와 엔터프라이즈 저장소 및 데이터 보호와 관련된 비용을 해소하는 효율적이고, 비용 효과적이며, 관리가 용이한 파일 서버 또는 iSCSI 서버 솔루션입니다. 가상 배열은 자주 액세스하지 않는 보관 데이터를 저장하는 데 특히 적합합니다.

이 문서에서는 가상 배열에 대한 개요를 제공합니다. 다음은 몇 가지 추가 리소스입니다.

* 모범 사례에 대해서는 [StorSimple 가상 배열 모범 사례](storsimple-ova-best-practices.md)를 참조하세요.
* StorSimple 8000 시리즈 디바이스에 대한 개요는 [StorSimple 8000 시리즈: 하이브리드 클라우드 솔루션](storsimple-overview.md)을 참조하세요.
* StorSimple 5000/7000 시리즈 디바이스에 대한 정보는 [StorSimple 온라인 도움말](http://onlinehelp.storsimple.com/)을 참조하세요.

가상 배열은 iSCSI 또는 SMB(서버 메시지 블록) 프로토콜을 지원합니다. 기존 하이퍼바이저 인프라에서 실행되며 클라우드, 클라우드 백업, 빠른 복원, 항목 수준 복원, 재해 복구 기능에 계층화를 제공합니다.

다음 테이블에는 StorSimple 가상 배열의 중요한 기능이 요약되어 있습니다.

| 기능 | StorSimple 가상 배열 |
| --- | --- |
| 설치 요구 사항 |가상화 인프라(Hyper-V 또는 VMware)  사용 |
| 가용성 |단일 노드 |
| 총 용량(클라우드 포함) |가상 배열당 사용 가능 용량 최대 64TB |
| 로컬 용량 |가상 배열당 사용 가능 용량 390GB~6.4TB(500GB~8TB의 디스크 공간을 프로비전해야 함) |
| 네이티브 프로토콜 |iSCSI 또는 SMB |
| 복구 시간 목표(RTO) |iSCSI: 크기에 관계없이 2분 미만 |
| 복구 지점 목표(RPO) |매일 백업 및 주문형 백업 |
| 저장소 계층화 |열 매핑을 사용하여 계층화에 포함시킬 데이터와 그렇지 않은 데이터를 결정 |
| 지원 |공급업체에 의해 지원되는 가상화 인프라 |
| 성능 |기본 인프라에 따라 달라짐 |
| 데이터 이동성 |동일한 디바이스에 복원할 수 있음 또는 항목 수준 복원을 수행함(파일 서버) |
| 저장소 계층 |로컬 하이퍼바이저 저장소 및 클라우드 |
| 공유 크기 |계층화: 최대 20TB; 로컬로 고정: 최대 2TB |
| 볼륨 크기 |계층화: 500GB~5TB(로컬로 고정): 50GB~200GB <br> 계층화된 볼륨에 대한 최대 로컬 예약은 200GB입니다. |
| 스냅숏 |충돌 일관성 |
| 항목 수준 복구 |예; 사용자가 공유에서 복원 가능 |

## <a name="why-use-storsimple"></a>StorSimple을 사용하는 이유

StorSimple은 애플리케이션을 수정하지 않고 사용자와 서버를 몇 분 안에 Azure 저장소에 연결합니다.

다음 테이블은 StorSimple 가상 배열 솔루션에서 제공하는 주요 이점 중 일부를 설명합니다.

| 기능 | 혜택 |
| --- | --- |
| 투명한 통합 |가상 배열은 iSCSI 또는 SMB 프로토콜을 지원합니다. 로컬 계층과 클라우드 계층 간의 데이터 이동은 원활하고 사용자에게 투명하게 진행됩니다. |
| 저장소 비용 감소 |StorSimple을 통해 가장 많이 사용되는 핫 데이터에 대한 현재 요구를 충족하기에 충분한 로컬 저장소를 프로비전합니다. 저장소에 대한 수요가 증가하면, StorSimple은 콜드 데이터를 비용 효과적인 클라우드 저장소로 계층화합니다. 저장소 요구 사항 및 비용을 좀 더 줄이기 위해 데이터를 클라우드로 보내기 전에 중복 제거 및 압축이 진행됩니다. |
| 단순화된 저장소 관리 |StorSimple은 StorSimple Device Manager를 사용하여 여러 디바이스를 관리하는 중앙 집중식 관리를 클라우드에 제공합니다. |
| 향상된 재해 복구 및 규정 준수 |StorSimple은 메타데이터를 즉시 복원하고 필요에 따라 데이터를 복원하여 보다 빠른 재해 복구를 지원합니다. 즉, 정상 작업 중단을 최소화하면서 계속할 수 있습니다. |
| 데이터 이동성 |복구 및 데이터 마이그레이션을 위해 클라우드에 계층화된 데이터를 다른 사이트에서 액세스할 수 있습니다. 원본 가상 배열에만 데이터를 복원할 수 있습니다. 하지만, 재해 복구 기능을 사용하면 전체 가상 배열을 또 다른 가상 배열에 복원합니다. |

## <a name="storsimple-workload-summary"></a>StorSimple 워크로드 요약

지원되는 StorSimple 워크로드가 아래 표에 요약되어 있습니다.

|시나리오     |워크로드     |지원됨      |제한               | 적용 가능한 버전|
|-------------|-------------|---------------|---------------------------|--------------------|
|원격 사무실/지사(ROBO)  |파일 공유     |예      |[파일 서버의 최대 제한](storsimple-ova-limits.md)을 참조하세요.<br></br>[지원되는 SMB 버전에 대한 시스템 요구 사항](storsimple-ova-system-requirements.md).| 모든 버전     |
|클라우드 보관  |보관 파일 공유     |예      |[파일 서버의 최대 제한](storsimple-ova-limits.md)을 참조하세요.<br></br>[지원되는 SMB 버전에 대한 시스템 요구 사항](storsimple-ova-system-requirements.md).| 모든 버전     |

StorSimple 가상 배열은 자주 액세스되지 않는 데이터에 가장 적합합니다. 가상 배열에는 성능 향상을 위한 로컬 캐시가 있지만 사용자는 디바이스가 저장소의 가장 낮은 계층(클라우드)에서 파일을 서비스한다고 가정해야 합니다. 각 가상 배열은 약 100Mbps로 Azure 저장소에 쓰고 읽을 수 있습니다. 해당 링크는 디바이스에 들어오는 모든 요청 간에 공유되며 아래 다이어그램에 나와 있는 것처럼 병목 상태가 될 수 있습니다.

![클라우드 보관](./media/storsimple-ova-overview/cloud-archiving.png)

여러 동시 사용자가 가상 배열에 액세스하는 경우 모두 Azure에 대한 연결을 공유하므로 성능이 저하됩니다. 사용자마다 보장된 성능은 없으며 디바이스는 도착한 개별 요청을 처리합니다.

StorSimple 가상 배열은 고가용성이 필요한 워크로드에는 적합하지 않습니다. 가상 배열은 소프트웨어 업데이트가 설치될 때 가동 중지 시간을 경험하는 단일 노드 디바이스입니다. 관리자는 1년에 3-4회의, 30분 유지 관리 기간을 계획해야 합니다.

## <a name="workflows"></a>워크플로

StorSimple 가상 배열은 다음과 같은 워크플로에 특히 적합합니다.

* [클라우드 기반 저장소 관리](#cloud-based-storage-management)
* [위치와 무관한 백업](#location-independent-backup)
* [데이터 보호 및 재해 복구](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>클라우드 기반 저장소 관리
Azure Portal에서 실행되는 StorSimple Device Manager 서비스를 사용하여 여러 디바이스와 여러 위치에 저장된 데이터를 관리할 수 있습니다. 이것은 분산된 지점 시나리오에 특히 유용합니다. 가상 배열 및 물리적인 StorSimple 디바이스를 관리하려면 StorSimple Device Manager 서비스 인스턴스를 별도로 만들어야 합니다. 또한 가상 배열은 이제 Azure 클래식 포털 대신 새 Azure Portal을 사용합니다.

![클라우드 기반 저장소 관리](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>위치와 무관한 백업
가상 배열을 사용하여, 클라우드 스냅숏은 위치와 무관한 지정 시간 볼륨 또는 공유 복사를 제공합니다. 클라우드 스냅숏은 기본적으로 사용하도록 설정되며 사용하지 않도록 설정할 수 없습니다. 모든 볼륨과 공유는 하나의 매일 백업 정책을 통해 같은 시간에 백업 하 고 필요할 때마다 추가 임시 백업을 수행할 수 있습니다.

### <a name="data-protection-and-disaster-recovery"></a>데이터 보호 및 재해 복구
가상 배열은 다음과 같은 데이터 보호 및 재해 복구 시나리오를 지원합니다.

* **볼륨 또는 공유 복원** – 복원을 새로운 워크플로로 사용하여 볼륨 또는 공유를 복원합니다. 이 접근 방식을 사용하여 전체 볼륨 또는 공유를 복원합니다.
* **항목 수준 복구** – 공유를 통해 최근 백업에 간편하게 액세스할 수 있습니다. 클라우드에서 사용할 수 있는 특별한 *.backup* 폴더에서 개별 파일을 손쉽게 복원할 수 있습니다. 이러한 복원 기능은 사용자를 기반으로 하며 관리자의 개입이 필요하지 않습니다.
* **재해 복구** – 장애 조치(failover) 기능을 사용하여 모든 볼륨 또는 공유를 새로운 가상 배열에 복구합니다. 새로운 가상 배열을 만들고 StorSimple Device Manager 서비스에 등록한 후에 원본 가상 배열을 장애 조치합니다. 새로운 가상 배열은 프로비전된 리소스를 맡게 됩니다.

## <a name="storsimple-virtual-array-components"></a>StorSimple 가상 배열 구성 요소

가상 배열에는 다음 구성 요소가 포함됩니다.

* [가상 배열](#virtual-array) – 가상화된 환경 또는 하이퍼바이저에 프로비전된 가상 머신을 기반으로 하는 하이브리드 클라우드 저장 디바이스입니다.
* [StorSimple 디바이스 관리자 서비스](#storsimple-device-manager-service) – 다양한 지리적 위치에서 액세스할 수 있는 단일 웹 인터페이스에서 하나 이상의 StorSimple 디바이스를 관리할 수 있는 Azure Portal의 확장입니다. StorSimple Device Manager 서비스를 사용하여 서비스를 만들고 관리하고, 디바이스와 경고를 보고 관리하고, 볼륨, 공유, 기존 스냅숏을 관리할 수 있습니다.
* [로컬 웹 사용자 인터페이스](#local-web-user-interface) – 로컬 네트워크에 연결할 수 있도록 디바이스를 구성한 후에 StorSimple 디바이스 관리자 서비스에 디바이스를 등록하는 데 사용되는 웹 기반 UI입니다. 
* [명령줄 인터페이스](#command-line-interface) – 가상 배열의 지원 세션을 시작하는 데 사용할 수 있는 Windows PowerShell 인터페이스입니다.
  다음 섹션에서는 이러한 각 구성 요소를 자세히 설명하고 솔루션이 데이터를 정렬하고, 저장소를 할당하고 저장소 관리 및 데이터 보호를 용이하게 방법에 대해 설명합니다.

### <a name="virtual-array"></a>가상 배열

가상 배열은 기본 저장소를 제공하고, 클라우드 저장소와의 통신을 관리하고, 디바이스에 저장된 모든 데이터의 보안과 기밀성을 보장하는 데 도움을 주는 단일 노드 저장소 솔루션입니다.

가상 배열은 다운로드가 가능한 하나의 모델로 제공됩니다. 가상 배열에 대해 디바이스의 최대 용량은 6.4TB(기본 저장소 요구 사항 8TB)이고 클라우드 저장소를 포함하여 64TB입니다.

가상 배열에는 다음과 같은 기능이 있습니다.

* 비용 효과적입니다. 기존 가상화 인프라를 활용하며 기존 Hyper-V 또는 VMware 하이퍼바이저에 배포될 수 있습니다.
* 데이터 센터에 상주하고 iSCSI 서버 또는 파일 서버로 구성될 수 있습니다.
* 클라우드와 통합됩니다.
* Backup이 클라우드에 저장되기 때문에, 재해 복구를 수월하게 하고 항목 수준 복구(ILR)를 간소화시킬 수 있습니다.
* 업데이트를 물리적 디바이스에 적용하듯이 가상 배열에 적용할 수 있습니다.

> [!NOTE]
> 가상 배열은 확장할 수 없습니다. 따라서 가상 배열을 만들 때 충분한 저장소를 프로비전하는 것이 중요합니다.

### <a name="storsimple-device-manager-service"></a>StorSimple Device Manager 서비스

Microsoft Azure StorSimple는 StorSimple 저장소를 중앙에서 관리하기 위해 사용하는 웹 기반 사용자 인터페이스인 StorSimple Device Manager 서비스를 제공합니다. 다음 작업을 수행하는 데 StorSimple Device Manager 서비스를 사용할 수 있습니다.

* 단일 서비스에서 다수의 StorSimple 가상 배열을 관리합니다.
* StorSimple 가상 배열에 대한 보안 설정을 구성하고 관리합니다. (클라우드의 암호화는 Microsoft Azure API에 따라 달라집니다.)
* 저장소 계정 자격 증명 및 속성을 구성합니다.
* 볼륨 또는 공유를 구성하고 관리합니다.
* 볼륨 또는 공유의 데이터를 백업하고 복원합니다.
* 성능을 모니터링합니다.
* 시스템 설정을 검토하고 발생 가능한 문제를 식별합니다.

StorSimple Device Manager 서비스를 사용하여 가상 배열의 일상적인 관리를 수행합니다.

자세한 내용은 [StorSimple Device Manager 서비스를 사용하여 StorSimple 디바이스 관리](storsimple-virtual-array-manager-service-administration.md)로 이동합니다.

### <a name="local-web-user-interface"></a>로컬 웹 사용자 인터페이스

가상 배열은 디바이스의 일회성 구성 및 StorSimple Device Manager 서비스 등록에 사용되는 웹 기반 UI를 포함합니다. 이것을 사용하여 가상 배열을 종료하고 다시 시작하고, 진단 테스트를 실행하고, 소프트웨어를 업데이트하고, 디바이스 관리자 암호를 변경하고, 시스템 로그를 보고, 서비스 요청을 위해 Microsoft 지원으로 문의할 수 있습니다.

웹 기반 UI 사용에 대한 자세한 내용은 [웹 기반 UI를 사용하여 StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)를 참조하세요.

### <a name="command-line-interface"></a>명령줄 인터페이스

포함된 Windows PowerShell 인터페이스를 사용하여, 가상 배열에서 발생할 수 있는 문제에 대한 해결을 지원할 수 있게 Microsoft 지원의 지원 세션을 시작할 수 있습니다.

## <a name="storage-management-technologies"></a>저장소 관리 기술

가상 배열 및 기타 구성 요소 외에도 StorSimple 솔루션은 중요한 데이터에 대한 빠른 액세스를 제공하고, 저장소 사용량을 줄이고, 가상 배열에 저장된 데이터를 보호하기 위해 다음과 같은 소프트웨어 기술을 사용합니다.

* [자동 저장소 계층화](#automatic-storage-tiering) 
* [로컬로 고정된 공유 및 볼륨](#locally-pinned-shares-and-volumes)
* 클라우드에 백업되거나 계층화된 데이터에 대한 중복 제거 및 압축 
* [예약된 백업 및 주문형 백업](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>자동 저장소 계층화
가상 배열은 가상 배열과 클라우드에 저장된 데이터를 관리하기 위해 새로운 계층화 메커니즘을 사용합니다. 계층은 로컬 가상 배열 및 Azure 클라우드 저장소 이렇게 두 가지뿐입니다. StorSimple 가상 배열은 현재 사용량, 기간, 다른 데이터와의 관계를 추적하는 열 지도를 기반으로 데이터를 계층에 자동으로 배열합니다. 가장 활성화된(인기있는) 데이터는 로컬로 저장되지만, 덜 활성화된 데이터 및 비활성 데이터는 자동으로 클라우드로 마이그레이션됩니다. (모든 백업은 클라우드에 저장됩니다.) StorSimple는 데이터와 저장소 할당을 조정하여 사용량 패턴 변경으로 다시 지정합니다. 예를 들어 일부 정보는 시간이 지남에 따라 덜 활성화될 수 있습니다. 점차적으로 활성이 낮아지면 클라우드로 계층화됩니다. 동일한 데이터가 다시 활성화되면, 저장소 배열에 계층화됩니다.

계층화된 특정 공유 또는 볼륨에 대한 데이터는 자체 로컬 계층 공간이 보장됩니다(전체 프로비전된 공간의 약 10%가 해당 공유 또는 볼륨용으로 제공). 이렇게 하면 해당 공유 또는 볼륨에 대한 가상 배열에 사용할 수 있는 저장소가 감소되지만 특정 공유 또는 볼륨에 대한 계층화가 다른 공유 또는 볼륨에 대한 계층화 수요에 의해 영향을 받지 않도록 보장됩니다. 따라서 특정한 공유 또는 볼륨에 매우 많이 사용되는 워크로드가 다른 모든 워크로드를 클라우드에 강제로 보낼 수 없습니다.

iSCSI용으로 만든 계층화된 볼륨은 볼륨의 크기에 관계 없이 200GB의 최대 로컬 예약 공간을 포함합니다.

![자동 저장소 계층화](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> 볼륨이 로컬로 고정되도록 지정할 수 있으며, 이런 경우 해당 데이터는 가상 배열에 머무르고 절대 클라우드에 계층화되지 않습니다. 자세한 내용은 [로컬로 고정된 공유 및 볼륨](#locally-pinned-shares-and-volumes)을 참조하세요.


### <a name="locally-pinned-shares-and-volumes"></a>로컬로 고정된 공유 및 볼륨

적절한 공유 및 볼륨을 로컬로 고정되도록 만들 수 있습니다. 이 기능은 중요한 애플리케이션에 필요한 데이터가 가상 배열에 유지되고 절대 클라우드로 계층화되지 않도록 합니다. 로컬로 고정된 공유 및 볼륨에는 다음과 같은 기능이 있습니다.

* 클라우드 대기 시간 또는 연결 문제가 적용되지 않습니다.
* StorSimple 클라우드 백업 및 재해 복구 기능의 이점이 여전히 존재합니다.

로컬로 고정된 공유 또는 볼륨을 로컬로 고정된 것처럼 계층화된 공유 또는 볼륨으로 복원할 수 있습니다. 

로컬로 고정된 볼륨에 대한 자세한 내용은 [StorSimple Device Manager 서비스를 사용하여 볼륨 관리](storsimple-virtual-array-manage-volumes.md)를 참조하세요.

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>클라우드에 백업되거나 계층화된 데이터에 대한 중복 제거 및 압축

StorSimple은 중복 제거 및 데이터 압축을 사용하여 클라우드의 저장소 요구 사항 많이 감소시킵니다. 중복 제거는 저장된 데이터 집합에서 중복을 제거하여 저장된 데이터의 전체 크기를 줄일 수 있습니다. 정보가 변경되면 StorSimple은 변경되지 않은 데이터를 무시하고 변경 내용만 캡처합니다. 또한 StorSimple은 중복 정보를 식별하고 제거하여 저장된 데이터의 양을 줄입니다.

> [!NOTE]
> 가상 배열에 저장되는 데이터는 중복 제거되거나 압축되지 않습니다. 모든 중복 제거 및 압축은 데이터를 클라우드로 보내기 바로 전에 발생합니다.

### <a name="scheduled-and-on-demand-backups"></a>예약된 백업 및 주문형 백업

StorSimple 데이터 보호 기능을 통해 주문형 백업을 만들 수 있습니다. 또한, 기본 백업 일정은 매일 데이터가 백업되도록 보장합니다. Backup은 증분 스냅숏의 형태로 생성되고 클라우드에 저장됩니다. 스냅숏은 마지막 백업 이후의 변경 사항만을 기록하기 때문에 신속한 생성과 복원이 가능합니다. 이러한 스냅숏은 보조 저장소 시스템(예: 테이프 백업)을 대체하기 때문에 재해 복구 시나리오에서 매우 중요할 수 있으며, 필요한 경우 데이터 센터 또는 대체 사이트에 데이터를 복원할 수 있게 해줍니다.

## <a name="managing-personal-information"></a>개인 정보 관리

가상 계열에 대한 StorSimple 디바이스 관리자는 두 가지 키 인스턴스에서 개인 정보를 수집합니다.
 - 사용자의 이메일 주소가 구성된 사용자 설정을 변경합니다. 이 정보는 관리자가 지울 수 있습니다. 
 - 공유 폴더에 있는 데이터에 액세스할 수 있는 사용자입니다. 공유 데이터에 액세스할 수 있는 사용자의 목록을 표시하고 내보낼 수 있습니다. 공유가 삭제되는 경우 이 목록도 삭제됩니다.

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 검토합니다.

## <a name="next-steps"></a>다음 단계

[가상 배열 포털을 준비](storsimple-virtual-array-deploy1-portal-prep.md)하는 방법을 알아봅니다.
