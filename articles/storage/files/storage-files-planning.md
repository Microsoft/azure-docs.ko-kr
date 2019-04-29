---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포에 대한 계획 시 고려해야 할 사항에 대해 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e2b2621ac8ee5b9ee84aaa978e8b915c98c5b702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61095638"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획

[Azure Files](storage-files-introduction.md)는 산업 표준 SMB 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure Files은 완벽하게 관리되기 때문에 프로덕션 시나리오에서 이를 배포하면 파일 서버 또는 NAS 디바이스를 훨씬 쉽게 배포하고 관리할 수 있습니다. 이 문서에서는 조직 내에서 프로덕션 용도로 Azure 파일 공유를 배포할 때 고려해야 할 항목을 다룹니다.

## <a name="management-concepts"></a>관리 개념

 다음 다이어그램에서는 Azure Files 관리 구조를 보여 줍니다.

![파일 구조](./media/storage-files-introduction/files-concepts.png)

* **스토리지 계정**: Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 저장소 계정 용량에 대한 자세한 내용은 [확장성 및 성능 목표](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

* **공유**: Azure에서 File Storage 공유는 SMB 파일 공유입니다. 모든 디렉터리 및 파일을 부모 공유에 만들어야 합니다. 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 파일 공유의 최대 5TiB의 최대 용량까지 제한이 없습니다.

* **디렉터리**: 디렉터리 계층 구조(선택 사항)입니다.

* **파일**: 공유에 있는 파일입니다. 파일의 크기는 최대 1TiB일 수 있습니다.

* **URL 형식**: 파일 REST 프로토콜을 사용하여 Azure 파일 공유에 대해 수행하는 요청의 경우 다음 URL 형식을 사용하여 파일의 주소를 지정할 수 있습니다.

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>데이터 액세스 방법

Azure Files는 두 가지의 편리한 데이터 액세스 방법을 기본 제공하며, 이 두 가지 방법을 따로 또는 서로 조합하여 데이터에 액세스할 수 있습니다.

1. **직접 클라우드 액세스**: [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및/또는 [Linux](storage-how-to-use-files-linux.md)를 에서 업계 표준 SMB(서버 메시지 블록) 프로토콜 또는 파일 REST API를 통해 Azure 파일 공유를 탑재할 수 있습니다. SMB를 사용하면 공유되는 파일의 읽기 및 쓰기는 Azure에서 파일 공유에서 직접 수행됩니다. Azure에서 VM으로 탑재하려면 운영 체제의 SMB 클라이언트가 SMB 2.1 이상을 지원해야 합니다. 사용자의 워크스테이션과 같은 온-프레미스를 탑재하려면 워크스테이션이 지원하는 SMB 클라이언트는 SMB 3.0 이상(암호화 사용)을 지원해야 합니다. SMB 외에도 새로운 애플리케이션 또는 서비스는 소프트웨어 개발을 위해 쉽고 확장 가능한 애플리케이션 인터페이스를 제공하는 파일 REST를 통해 파일 공유에 직접 액세스할 수 있습니다.
2. **Azure 파일 동기화**: Azure 파일 동기화를 사용하면 공유를 Azure에서/Windows Server 온-프레미스로 복제할 수 있습니다. 사용자는 Windows Server를 통해(예: SMB 또는 NFS 공유를 통해) 파일 공유에 액세스하게 됩니다. 이는 지사와 같이 Azure 데이터 센터에서 멀리 떨어진 곳에서 데이터에 액세스하고 수정하는 시나리오에 유용합니다. 여러 Windows Server 엔드포인트 간(예: 여러 지사 간)에 데이터가 복제될 수 있습니다. 마지막으로, 모든 데이터를 여전히 서버를 통해 액세스할 수 있지만 서버에는 데이터의 전체 복사본이 없도록 데이터는 Azure Files에 계층화될 수 있습니다. 오히려 사용자가 열 때 데이터 원활하게 호출됩니다.

다음 표는 사용자와 애플리케이션이 Azure 파일 공유에 액세스할 수 있는 방법을 보여줍니다.

| | 직접 클라우드 액세스 | Azure 파일 동기화 |
|------------------------|------------|-----------------|
| 어떤 프로토콜을 사용해야 합니까? | Azure Files는 SMB 2.1, SMB 3.0 및 파일 REST API를 지원합니다. | Windows Server(SMB, NFS, FTPS, 등)에서 지원되는 프로토콜을 통해 Azure 파일 공유 액세스 |  
| 어디에서 워크로드를 실행합니까? | **Azure**: Azure Files에서 데이터에 직접 액세스할 수 있습니다. | **네트워크 속도가 느린 온-프레미스**: Windows, Linux 및 macOS 클라이언트가 로컬 온-프레미스 Windows 파일 공유를 Azure 파일 공유의 빠른 캐시로 탑재할 수 있습니다. |
| ACL의 어떤 수준이 필요합니까? | 공유 및 파일 수준입니다. | 공유, 파일 및 사용자 수준입니다. |

## <a name="data-security"></a>데이터 보안

Azure Files에는 데이터 보안을 위한 몇 가지 기본 제공 옵션이 있습니다.

* 두 네트워크상 프로토콜에서 암호화 지원: SMB 3.0 암호화 및 HTTPS를 통한 파일 REST가 지원됩니다. 기본적으로: 
    * SMB 3.0 암호화를 지 원하는 클라이언트 데이터 전송 및 수신 된 암호화 된 채널을 통해.
    * SMB 3.0 암호화를 사용 하 여 지원 하지 않는 클라이언트는 암호화 되지 않은 SMB 2.1 또는 SMB 3.0을 통해 데이터 센터 간 통신할 수 있습니다. SMB 클라이언트는 암호화 기능이 없는 SMB 3.0 또는 SMB 2.1을 통해 데이터 센터 내에서 통신할 수 없습니다.
    * 클라이언트는 HTTP 또는 HTTPS를 사용하여 파일 REST를 통해 통신할 수 있습니다.
* 미사용 데이터 암호화([Azure 스토리지 서비스 암호화](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): 모든 스토리지 계정에 대해 SSE(스토리지 서비스 암호화)가 사용하도록 설정됩니다. 미사용 데이터는 완전히 관리되는 키로 암호화됩니다. 미사용 암호화를 사용할 경우 저장소 비용이 증가하거나 성능이 저하되지 않습니다. 
* 암호화된 데이터 전송 시 선택적 요구 사항: 이를 선택하면 Azure Files는 암호화되지 않은 채널을 통한 데이터 액세스를 거부합니다. 구체적으로 말하면, 암호화 연결을 통한 HTTPS 및 SMB 3.0만 허용됩니다.

    > [!Important]  
    > 데이터의 보안 전송이 필요한 경우 암호화된 SMB 3.0과 통신할 수 없는 이전 버전의 SMB 클라이언트는 실패합니다. 자세한 내용은 [Windows에 탑재](storage-how-to-use-files-windows.md), [Linux에 탑재](storage-how-to-use-files-linux.md) 및 [macOS에 탑재](storage-how-to-use-files-mac.md)를 참조하세요.

최상의 보안을 위해 최신 클라이언트를 사용하여 데이터에 액세스할 때마다 미사용 시 암호화와 전송 시 데이터 암호화 모두를 항상 사용하는 것이 좋습니다. 예를 들어, SMB 2.1만 지원하는 Windows Server 2008 R2 VM에 공유를 탑재하는 경우 SMB 2.1이 암호화를 지원하지 않기 때문에 저장소 계정에 암호화되지 않은 트래픽을 허용해야 합니다.

Azure 파일 공유에 액세스하기 위해 Azure 파일 동기화를 사용하는 경우, 미사용 시 데이터 암호화 필요 여부에 관계 없이 항상 암호화된 HTTPS 및 SMB 3.0을 사용하여 데이터를 Windows 서버에 동기화합니다.

## <a name="file-share-performance-tiers"></a>파일 공유 성능 계층

Azure Files는 두 가지 성능 계층이 제공: 표준 및 프리미엄입니다.

* **표준 파일 공유**는 범용 파일 공유, 개발/테스트 환경 등 성능 가변성의 영향을 그다지 받지 않는 IO 워크로드에 대해 안정적인 성능을 제공하는 회전식 HDD(하드 디스크 드라이브)를 통해 지원되며 종량제 청구 모델에서만 제공됩니다.
* **프리미엄 파일 공유(미리 보기)** 는 IO를 가장 많이 사용하는 워크로드에서 수행하는 대다수 IO 작업에 대해 우수한 성능과 짧은 대기 시간(몇 밀리초 단위)을 일관되게 제공하는 SSD(반도체 디스크)를 통해 지원됩니다. 따라서 데이터베이스, 웹 사이트 호스팅, 개발 환경 등의 다양한 워크로드에 적합합니다. 프리미엄 파일 공유는 프로비전된 청구 모델에서만 제공됩니다. 프리미엄 파일 공유는 표준 파일 공유에서 별도 배포 모델을 사용합니다. 프리미엄 파일 공유를 만드는 방법 알아보기 하려는 경우 주제에 문서를 참조 하세요. [Azure premium 파일 저장소 계정을 만드는 방법](storage-how-to-create-premium-fileshare.md)합니다.

> [!IMPORTANT]
> 프리미엄 파일 공유는 여전히 미리 보기로 제공 LRS를 사용 하 여 에서만 사용할 수 있습니다 및에 제공 되는 일부 지역에서 사용할 수 있는 Azure Backup 지원 영역을 선택 합니다.

|사용 가능한 지역  |Azure Backup 지원  |
|---------|---------|
|미국 동부2      | 예|
|미국 동부       | 예|
|미국 서부       | 아닙니다. |
|미국 서부2      | 아닙니다. |
|미국 중부    | 아닙니다. |
|유럽 북부  | 아닙니다. |
|서유럽   | 예|
|동남 아시아       | 예|
|동아시아     | 아닙니다. |
|일본 동부    | 아닙니다. |
|일본 서부    | 아닙니다. |
|한국 중부 | 아닙니다. |
|오스트레일리아 동부| 아닙니다. |

### <a name="provisioned-shares"></a>프로비전된 공유

프리미엄 파일 공유 (미리 보기)는 고정된 GiB/P s/처리량 비율에 따라 프로 비전 됩니다. 공유에 프로비전되는 각 GiB당 IOPS 1개, 초당 0.1MiB의 처리량이 공유당 최대 한도까지 지급됩니다. 허용되는 최소 프로비저닝 용량은 100GiB(최소 IOPS/처리량)입니다.

최상의 노력 원칙에 따라 모든 공유는 60분 이상의 기간 동안(공유 크기별로 다름) 프로비전된 저장소 용량(GiB)당 IOPS를 3개까지 버스트할 수 있습니다. 새 공유에는 프로비전된 용량을 기준으로 전체 버스트 크레딧이 초기 제공됩니다.

공유 1 GiB 단위로 프로 비전 되어야 합니다. 최소 크기가 100gib를 다음 크기 101 GIB 등.

> [!TIP]
> 기준 IOPS = 1 * GiB 프로 비전 합니다. (최대 최대 100,000 개의 IOPS).
>
> 제한 버스트할 = 3 * 기준 IOPS. (최대 최대 100,000 개의 IOPS).
>
> 송신 비율이 60 초 + 0.06 = * GiB 프로 비전
>
> 수신 속도 = 40 초 + 0.04 * GiB 프로 비전

공유 크기는 언제 든 지 늘릴 수 있지만 마지막 증가 이후 24 시간 후에 줄일 수 있습니다. 크기 증가 하지 않고 24 시간을 기다린 후 다시 늘릴 때까지 여러 번의 공유 크기를 줄일 수 있습니다. P s/처리량 규모 변경 크기 변경 된 후 몇 분 안에 적용 됩니다.

다음 표에서 이러한 공식 프로 비전 된 공유 크기의 몇 가지 예를 보여 줍니다.

(크기를 가리키는 *는 제한 된 공개 미리 보기에서)

|용량 (GiB) | 기준 IOPS | IOPS 버스트 | 송신 (초) | 수신 (초) |
|---------|---------|---------|---------|---------|
|100         | 100     | 최대 300     | 66   | 44   |
|500         | 500     | 최대 1,500 명의   | 90   | 60   |
|1,024       | 1,024   | 최대 3,072   | 122   | 81   |
|5,120       | 5,120   | 최대 15,360  | 368   | 245   |
|10,240 *     | 10,240  | 최대 30720  | 675 | 450   |
|33,792 *     | 33,792  | 최대 100,000 개의 | 2,088 | 1,392   |
|51,200 *     | 51,200  | 최대 100,000 개의 | 3,132 | 2,088   |
|102,400 *    | 100,000 | 최대 100,000 개의 | 6,204 | 4,136   |

현재 최대 5tib 파일 공유 크기는 공개 미리 보기에서 100tib 최대 크기는 제한 된 공개 미리 보기 완료에 대 한 액세스를 요청 하려면 제한 된 공개 미리 보기 동안 [이 설문 조사 합니다.](https://aka.ms/azurefilesatscalesurvey)

### <a name="bursting"></a>버스팅

프리미엄 파일 공유에는 해당 IOPS의 3 배까지 버스트할 수 있습니다. 버스팅 자동화 되 고 신용 체제에 따라 작동 합니다. 최선의 결과가 얻어지 및 버스트 용량 한도 작동을 버스팅은 보장 되지 않습니다, 파일 공유 버스트할 수 있습니다 *최대* 제한 합니다.

파일 공유에 대 한 트래픽은 IOPS 기준선 아래 때마다 크레딧 버스트 버킷에 누적 됩니다. 예를 들어 100 GiB 공유 100 기준 IOPS에 있습니다. 공유에서 실제 트래픽을 특정 1 초 간격에 대 한 40 IOPS 되었으면 60 사용 되지 않는 IOPS 버스트 버킷에 크레딧 됩니다. 이 크레딧은 다음 됩니다 나중에 작업 기준 IOPs를 초과 하는 경우.

> [!TIP]
> 버스트 버킷 크기인 Baseline_IOPS = * 2 * 3600 합니다.

공유 IOPS 기준선 초과 있고 버스트 버킷에서 크레딧, 때마다 전환 됩니다. 공유 계속 공유 50 TiB 보다 작은 최대 한 시간에 대 한 버스트 제한만 남아 있지만 크레딧, 남은으로 버스트할 수 있습니다. 공유 50 TiB 보다 큰이 1 시간 제한을 초과 기술적으로, 등록 하지만,이 두 시간을 기반으로 계산 버스트 학점 수입니다. IOPS 기준 외 각 IO 하나 크레딧을 사용 하 고 모든 크레딧을 소비 되므로 공유 IOPS 기준으로 반환 됩니다.

공유 크레딧은 세 가지 상태:

- 파일 공유 기준 IOPS 보다 적게 사용할 때 발생 합니다.
- 파일 공유는 버스팅 하는 경우 거부 합니다.
- 없는 크레딧 또는 기준 경우 상수, 남은 IOPS 사용 중입니다.

전체 해당 버스트 버킷의 학점 수를 사용 하 여 새 파일 공유 시작 합니다. 서버에서 제한으로 인해 초기 IOPS 보다 공유 IOPS 작으면 버스트 크레딧은 지급 되는 합니다.

## <a name="file-share-redundancy"></a>파일 공유 중복성

Azure Files 표준 공유 세 가지 데이터 중복성 옵션을 지원 합니다: 로컬 중복 저장소 (LRS), ZRS (영역 중복 저장소), 및 지역 중복 저장소 (GRS).

Azure 파일 프리미엄만 지 원하는 로컬 중복 저장소 (LRS)을 공유합니다.

다음 섹션에서는 서로 다른 중복 옵션의 차이점에 대해 설명합니다.

### <a name="locally-redundant-storage"></a>로컬 중복 저장소

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>영역 중복 저장소

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>지역 중복 저장소

> [!Warning]  
> Azure 파일 공유를 GRS 스토리지 계정의 클라우드 엔드포인트로 사용하는 경우 스토리지 계정 장애 조치(failover)를 시작하면 안 됩니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다. Azure 지역이 손실되는 경우 Microsoft는 Azure 파일 동기화와 호환되는 방식으로 스토리지 계정의 장애 조치(failover)를 트리거합니다.

GRS(지역 중복 저장소)는 기본 지역에서 수백 마일 떨어진 보조 지역에 데이터를 복제하여 지정된 1년에 걸쳐 99.99999999999999%(16개의 9) 이상의 개체 내구성을 제공하도록 설계되었습니다. 스토리지 계정에서 GRS를 활성화하면 전체 지역 가동 중단 또는 기본 지역을 복구할 수 없는 재해의 경우라도 데이터는 지속됩니다.

읽기 액세스 지역 중복 저장소 (RA-GRS)를 선택 하는 경우 알아야 할 Azure 파일 읽기 액세스 지역 중복 저장소 (RA-GRS)를 지원 하지 않습니다 모든 지역에서이 이번에 있습니다. RA-GRS 저장소 계정에 파일 공유는 GRS 계정에서 하 듯 작동 및 GRS 가격이 청구 합니다.

GRS는 데이터를 보조 지역의 다른 데이터 센터에 복제하지만, Microsoft에서 주 지역에서 보조 지역으로 장애 조치를 시작하는 경우에만 해당 데이터를 읽을 수 있습니다.

GRS가 활성화 된 저장소 계정에 대 한 모든 데이터는 먼저 로컬 중복 저장소 (LRS)를 사용 하 여 복제 됩니다. 업데이트는 먼저 기본 위치에 커밋되고 LRS를 사용하여 복제됩니다. 그런 다음, 업데이트는 GRS를 사용하여 보조 지역에 비동기적으로 복제됩니다. 데이터가 보조 위치에 기록되는 경우 LRS를 사용하여 해당 위치 내에도 복제됩니다.

주 지역 및 보조 지역에서 모두 별도의 장애 도메인에서 복제본을 관리하고, 저장소 배율 단위 내에서 도메인을 업그레이드합니다. 저장소 배율 단위는 데이터 센터 내의 기본 복제 단위입니다. 이 수준의 복제 LRS;에서 제공 되며 자세한 내용은 참조 하세요. [로컬 중복 저장소 (LRS): Azure Storage에 대한 저렴한 데이터 중복성](../common/storage-redundancy-lrs.md)을 참조하세요.

사용할 복제 옵션을 결정할 때 다음 사항에 유의하세요.

* 영역 중복 저장소 (ZRS) 동기 복제를 사용 하 여 높은 가용성을 제공 하 고 GRS 보다 몇 가지 시나리오에 보다 적합할 수 있습니다. ZRS에 대한 자세한 내용은 [ZRS](../common/storage-redundancy-zrs.md)를 참조하세요.
* 비동기 복제에는 데이터가 주 지역에 기록되는 시간에서 보조 지역으로 복제되는 시간까지의 지연이 발생합니다. 지역 재해의 경우 주 지역에서 해당 데이터를 복구할 수 없으면 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.
* GRS를 사용하면 Microsoft에서 보조 지역으로 장애 조치(failover)를 시작하지 않는 한 읽기 또는 쓰기를 위해 복제본에 액세스할 수 없습니다. 장애 조치(failover)의 경우 장애 조치가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스 권한이 생성됩니다. 자세한 내용은 [재해 복구 지침](../common/storage-disaster-recovery-guidance.md)을 참조하세요.

## <a name="data-growth-pattern"></a>데이터 증가 패턴

현재 Azure 파일 공유의 최대 크기는 5tib (100tib premium 파일에 대 한 제한 된 공개 미리 보기를 공유 하는 데 사용). 현재 이 제한으로 인해 Azure 파일 공유를 배포할 때 예상되는 데이터 증가를 고려해야 합니다.

Azure 파일 동기화를 사용하여 여러 Azure 파일 공유를 하나의 Windows 파일 서버에 동기화할 수 있습니다. 이를 통해 온-프레미스가 있을 수 있는 이전의 큰 파일 공유를 Azure 파일 동기화로 가져올 수 있습니다. 자세한 내용은 [Azure 파일 동기화 배포 계획](storage-files-planning.md)을 참조하세요.

## <a name="data-transfer-method"></a>데이터 전송 방법

온-프레미스 파일 공유와 같은 기존 파일 공유에서 Azure Files로 데이터를 대량으로 쉽게 전송할 수 있는 여러 옵션이 있습니다. 자주 사용되는 옵션 몇 가지는 다음과 같습니다(비 한정적 목록).

* **Azure 파일 동기화**: Azure 파일 동기화는 Azure 파일 공유("클라우드 엔드포인트")와 Windows 디렉터리 네임스페이스("서버 엔드포인트") 간 첫 번째 동기화의 일환으로 기존 파일 공유의 모든 데이터를 Azure Files에 복제합니다.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Azure Import/Export 서비스를 사용하면 하드 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure 파일 공유로 전송할 수 있습니다. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy는 Windows 및 Windows Server와 함께 제공되는 잘 알려진 복사 도구입니다. 파일 공유를 로컬로 탑재하고 탑재된 위치를 Robocopy 명령의 대상으로 사용하는 방식으로 Robocopy를 사용하여 데이터를 Azure Files로 전송할 수 있습니다.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Azure Files 및 Azure Blob Storage에서 복사하도록 디자인된 명령줄 유틸리티입니다. AzCopy는 Windows 및 Linux에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
