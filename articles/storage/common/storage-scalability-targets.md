---
title: Azure Storage 확장성 및 성능 목표-저장소 계정
description: 용량, 요청 속도 및 Azure storage 계정에 대 한 인바운드 및 아웃 바운드 대역폭을 포함 하 여 확장성 및 성능 목표에 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: e3e0e9ae4a1939aad9ab2ae42a1b51b1b00e2462
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101491"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>저장소 계정에 대 한 azure Storage 확장성 및 성능 목표

이 문서에서는 Azure Storage 계정의 확장성 및 성능 목표에 대해 자세히 설명합니다. 여기에 나열된 확장성 및 성능 목표는 최첨단 목표이지만 달성할 수 있습니다. 모든 경우, 계정 사용량에 따라 달성된 요청 속도 및 대역폭은 저장된 개채의 크기 및 애플리케이션이 수행한 작업 형태에 따릅니다.

해당 성능이 요구 사항을 충족시키는지 여부를 확인 하려면 서비스를 반드시 테스트해야 합니다. 가능하면 트래픽 속도가 갑자기 증가하지 않고 파티션 간의 트래픽이 적절하게 분산되도록 보장합니다.

애플리케이션이 파티션의 작업 처리 가능한 제한에 도달하면 Azure Storage는 오류 코드 503(서버 작업 중) 또는 오류 코드 500(작업 시간 제한) 응답을 반송하기 시작합니다. 503 오류가 발생하는 경우 재시도를 위해 지수 백오프 정책을 사용하도록 애플리케이션을 수정하는 것이 좋습니다. 지수 백오프는 파티션에 대한 부하를 감소시키고 해당 파티션에 트래픽의 급증을 완화할 수 있습니다.

## <a name="storage-account-scale-limits"></a>저장소 계정 크기 한도

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Premium 성능 storage 계정 크기 제한

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>스토리지 리소스 공급자 크기 조정 제한

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 크기 조정 목표

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files 크기 조정 목표

Azure File 및 Azure 파일 동기화의 크기 조정 및 성능 목표에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](../files/storage-files-scale-targets.md)를 참조하세요.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>프리미엄 파일 확장 대상

세 가지 종류의 프리미엄 파일용 고려할 제한 사항: 저장소 계정, 공유 및 파일입니다.

예를 들면 다음과 같습니다. 단일 공유 100,000 IOPS를 달성할 수 있습니다 및 단일 파일에서 최대 5,000 개의 IOPS를 확장할 수 있습니다. 따라서 예를 들어, 하나의 공유에 3 개의 파일에 있는 경우 해당 공유에서 가져올 수 있습니다 최대 IOPs는 15,000입니다.

#### <a name="premium-file-share-limits"></a>프리미엄 파일 공유 제한

> [!IMPORTANT]
> 저장소 계정 한도 모든 공유에 적용 됩니다. 최대 저장소 계정에 대 한 최대 크기 조정은 경우 저장소 계정당 하나의 공유 달성 합니다.

|영역  |대상  |
|---------|---------|
|프로 비전 하는 최소 크기                        |100GiB      |
|최대 크기를 프로 비전                        |100tib      |
|최소 크기 증가/감소    |1 GiB      |
|기준 IOPS    |최대 100,000 개 GiB 당 1 개 IOPS|
|버스팅 IOPS    |최대 100,000 개 GiB 당 3 x IOPS|
|송신 속도         |60 초 + 0.06 * GiB 프로 비전        |
|수신 속도| 40 초 + 0.04 * GiB 프로 비전 |
|최대 스냅숏 수        |200       |

#### <a name="premium-file-limits"></a>프리미엄 파일 제한

|영역  |대상  |
|---------|---------|
|크기                  |1TiB         |
|파일당 최대 IOPS     |5,000         |
|동시 핸들 수    |2,000         |

### <a name="azure-file-sync-scale-targets"></a>Azure 파일 동기화의 크기 조정 목표

Azure 파일 동기화는 무제한 사용을 목적으로 설계되었으나 무제한 사용이 가능하지 않은 경우도 있습니다. 아래 표에는 Microsoft의 테스트 경계와 하드 한도인 목표가 표시되어 있습니다.

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue 저장소 크기 조정 목표

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table Storage 크기 조정 목표

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>참고 항목

- [저장소 가격 정보](https://azure.microsoft.com/pricing/details/storage/)
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-subscription-service-limits.md)
- [Azure Storage 복제](../storage-redundancy.md)
- [Microsoft Azure Storage 성능 및 확장성 검사 목록](../storage-performance-checklist.md)