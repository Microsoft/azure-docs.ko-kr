---
title: Azure Files 확장성 및 성능 목표 | Microsoft Docs
description: 용량, 요청 속도 및 인바운드/아웃바운드 대역폭 제한을 포함하여 Azure Files의 확장성 및 성능 목표를 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd60d6453b71387578b880ad580fb1741e6e512b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763832"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure Files 확장성 및 성능 목표

[Azure Files](storage-files-introduction.md)는 산업 표준 SMB 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. 이 문서에서는 Azure Files 및 Azure 파일 동기화의 확장성 및 성능 목표에 대해 설명합니다.

여기서 나열하는 확장성 및 성능 목표는 고성능 목표이지만 다른 배포 변수의 영향을 받을 수 있습니다. 예를 들어 Azure Files 서비스를 호스팅하는 서버뿐만 아니라 사용 가능한 네트워크 대역폭에 따라 파일 처리량이 제한될 수 있습니다. Azure Files의 확장성 및 성능이 요구 사항을 충족하는지 확인하려면 사용 패턴을 테스트하는 것이 좋습니다. 또한 시간이 지남에 따라 이러한 제한을 높이기 위해 노력하고 있습니다. 아래의 설명 또는 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에서 주저하지 마시고 늘리려는 제한에 대한 의견을 보내주세요.

## <a name="azure-storage-account-scale-targets"></a>Azure 저장소 계정의 크기 조정 목표

Azure 파일 공유에 대한 부모 리소스는 Azure 저장소 계정입니다. 저장소 계정은 Azure에 있는 저장소 풀을 나타내며, Azure Files를 포함한 여러 저장소 서비스에서 데이터를 저장하는 데 사용할 수 있습니다. 스토리지 계정에 데이터를 저장하는 다른 서비스로 Azure Blob Storage, Azure Queue Storage 및 Azure Table Storage가 있습니다. 저장소 계정에 데이터를 저장하는 모든 저장소 서비스를 적용하는 목표는 다음과 같습니다.

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> 다른 저장소 서비스에서 범용 저장소 계정을 활용 저장소 계정의 Azure 파일 공유를 영향을 줍니다. 예를 들어 Azure Blob Storage를 사용하여 최대 스토리지 계정 용량에 도달하면 Azure 파일 공유가 최대 공유 크기보다 작은 경우에도 Azure 파일 공유에 새 파일을 만들 수 없습니다.

## <a name="azure-files-scale-targets"></a>Azure Files 크기 조정 목표

### <a name="premium-files-scale-targets"></a>프리미엄 파일 확장 대상

세 가지 종류의 프리미엄 파일용 고려할 제한 사항: 저장소 계정, 공유 및 파일입니다.

예를 들면 다음과 같습니다. 단일 공유 100,000 IOPS를 달성할 수 있습니다 및 단일 파일에서 최대 5,000 개의 IOPS를 확장할 수 있습니다. 따라서 예를 들어, 하나의 공유에 3 개의 파일에 있는 경우 해당 공유에서 가져올 수 있습니다 최대 IOPS는 15,000입니다.

### <a name="premium-filestorage-account-limits"></a>프리미엄 filestorage 계정 제한

프리미엄 파일 라는 고유한 저장소 계정 사용 **filestorage (미리 보기)**,이 계정에는 표준 파일에서 사용 하는 저장소 계정 보다 약간 다른 크기 조정 목표입니다. 저장소 계정 크기 조정 목표에 대 한 참조 테이블에 [Azure storage 계정 크기 조정 목표](#azure-storage-account-scale-targets) 섹션입니다.

> [!IMPORTANT]
> 저장소 계정 한도 모든 공유에 적용 됩니다. 최대 저장소 계정에 대 한 최대 크기 조정은 경우 저장소 계정당 하나의 공유 달성 합니다.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure 파일 동기화의 크기 조정 목표

Azure 파일 동기화를 사용하면 최대한 무제한으로 사용할 수 있도록 디자인하려고 했지만 가능한 것은 아닙니다. 아래 표에서는 테스트의 한계 및 실제적인 목표인 하드 한도를 나타냅니다.

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Azure 파일 동기화 성능 메트릭

Azure 파일 동기화 에이전트는 Azure 파일 공유에 연결하는 Windows Server 머신에서 실행되기 때문에 효과적인 동기화 성능은 인프라의 여러 요소에 따라 달라집니다. 이러한 요소는 Windows Server 및 기본 디스크 구성, 서버와 Azure Storage 간의 네트워크 대역폭, 파일 크기, 전체 데이터 세트 크기 및 데이터 세트에 대한 작업 등입니다. Azure 파일 동기화가 파일 수준에서 작동하므로 Azure 파일 동기화 기반 솔루션의 성능 특성은 초당 처리된 개체(예: 파일 및 디렉터리)의 수에서 정확하게 측정됩니다.

Azure 파일 동기화의 경우 다음과 같은 두 단계에서 성능이 중요합니다.

1. **일회성 초기 프로비전**: 초기 프로비전에 대한 성능을 최적화하려면 [Azure 파일 동기화에 온보딩](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync)에서 최적의 배포 세부 정보를 참조하세요.
2. **진행 중인 동기화**: Azure 파일 공유에서 데이터를 처음으로 시드한 후에 Azure 파일 동기화는 여러 엔드포인트를 동기화된 상태로 유지합니다.

각 단계에 대한 배포를 계획하기 위해 구성이 포함된 시스템의 내부 테스트 중에 확인되는 결과는 아래와 같습니다.

| 시스템 구성 |  |
|-|-|
| CPU | 64개의 MiB L3 캐시를 포함한 64개의 가상 코어 |
| 메모리 | 128GiB |
| 디스크 | 배터리 지원 캐시를 사용하는 RAID 10을 포함한 SAS 디스크 |
| 네트워크 | 1Gbps 네트워크 |
| 워크로드 | 범용 파일 서버|

| 일회성 초기 프로비전  |  |
|-|-|
| 개체 수 | 2500만 개 개체 |
| 데이터 세트 크기| ~4.7 TiB |
| 평균 파일 크기 | ~ 200 3:n2}kib/ (가장 큰 파일: 100GiB) |
| 처리량 업로드 | 초당 20개 개체 |
| 네임스페이스 다운로드 처리량* | 초당 개체 400개 |

*새 서버 엔드포인트를 만들 때 Azure 파일 동기화 에이전트는 파일 콘텐츠를 다운로드하지 않습니다. 먼저 전체 네임스페이스를 동기화한 다음, 백그라운드 회수를 트리거하여 전체 파일을 다운로드하거나 클라우드 계층화를 사용하는 경우 서버 엔드포인트에서 설정된 클라우드 계층화 정책에 파일을 다운로드합니다.

| 진행 중인 동기화  |   |
|-|--|
| 동기화된 개체 수| 125000개 개체(~1% 변동) |
| 데이터 세트 크기| 50GiB |
| 평균 파일 크기 | ~500KiB |
| 처리량 업로드 | 초당 30개 개체 |
| 전체 다운로드 처리량* | 초당 개체 60개 |

*클라우드 계층화를 사용하면 일부 파일 데이터만을 다운로드할 때 성능이 더 개선될 수도 있습니다. Azure 파일 동기화는 엔드포인트 중 하나에서 캐시된 파일의 데이터가 변경될 때에만 해당 데이터를 다운로드합니다. 계층되거나 새로 생성된 파일의 경우 에이전트는 파일 데이터를 다운로드하지 않습니다. 대신 모든 서버 엔드포인트에 네임스페이스만을 동기화합니다. 에이전트는 사용자가 액세스할 때 계층화된 파일의 부분 다운로드도 지원합니다. 

> [!Note]  
> 위의 숫자는 발생한 성능을 나타내지 않습니다. 이 섹션의 시작 부분에 설명된 대로 실제 성능은 여러 요인에 따라 달라집니다.

배포에 대한 일반 지침으로 몇 가지 사항에 유의해야 합니다.

- 개체 처리량은 서버의 동기화 그룹 수에 비례하여 크기를 조정합니다. 서버의 여러 동기화 그룹으로 데이터를 분할하면 처리량이 향상됩니다. 처리량은 서버 및 네트워크에 의해서도 제한됩니다
- 개체 처리량은 초당 MiB 처리량에 반비례합니다. 더 작은 파일의 경우 초당 처리된 개체 수 측면에서 더 높은 처리량이 발생하지만 초당 MiB 처리량은 더 낮습니다. 반대로 큰 파일의 경우 초당 처리되는 개체는 적지만 초당 MiB 처리량은 높습니다. 초당 MiB 처리량은 Azure Files 크기 조정 목표에 의해 제한됩니다.

## <a name="see-also"></a>참고 항목

- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [다른 저장소 서비스에 대한 확장성 및 성능 목표](../common/storage-scalability-targets.md)
