---
title: Blob에 대 한 핫, 쿨 및 archive 액세스 계층-Azure Storage
description: Azure Blob 저장소에 대 한 핫, 쿨 및 보관 액세스 계층에 대해 읽어 보세요. 계층화를 지 원하는 저장소 계정을 검토 합니다. 블록 blob 저장소 옵션을 비교 합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 59a0433a3b22877808fbe2b8371258e00f214d10
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226185"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층

Azure Storage는 가장 비용 효율적인 방식으로 Blob 개체 데이터를 저장할 수 있도록 여러 액세스 계층을 제공합니다. 사용 가능한 액세스 계층은 다음과 같습니다.

- 자주 액세스 하는 데이터를 저장 하기 위한 **핫** 최적화.
- 자주 액세스 하지 않으며 30 일 동안 저장 되는 데이터를 저장 하기 위한 **쿨** 최적화입니다.
- 거의 액세스 하지 않는 데이터를 저장 하는 데 최적화 되어 **있으며, 시간** 순서에 따라 유연한 대기 시간을 요구 하 여 최소 180 일 동안 저장 됩니다.

다음은 다양 한 액세스 계층에 적용 되는 고려 사항입니다.

- 핫 및 쿨 액세스 계층만 계정 수준에서 설정할 수 있습니다. 보관 액세스 계층은 계정 수준에서 사용할 수 없습니다.
- 핫, 쿨 및 archive 계층은 업로드 중 또는 업로드 후 blob 수준에서 설정할 수 있습니다.
- 쿨 액세스 계층의 데이터는 약간 낮은 가용성을 허용할 수 있지만 핫 데이터와 유사한 높은 내구성, 검색 대기 시간 및 처리량 특성이 필요 합니다. 쿨 데이터의 경우 핫 데이터와 비교할 때 약간 낮은 가용성 SLA (서비스 수준 계약) 및 액세스 비용이 더 낮은 저장소 비용에 대 한 적절 한 장단점입니다.
- 보관 저장소는 데이터를 오프 라인으로 저장 하 고 가장 낮은 저장소 비용 뿐만 아니라 가장 높은 데이터 리하이드레이션 및 액세스 비용을 제공 합니다.

클라우드에 저장된 데이터는 기하급수적으로 증가합니다. 스토리지 확장 요구에 대한 비용을 관리하려면 비용 최적화를 위해 액세스 빈도 및 계획 보존 기간과 같은 속성을 기반으로 하여 데이터를 구성하는 것이 좋습니다. 클라우드에 저장 된 데이터는 수명 동안 생성, 처리 및 액세스 하는 방법에 따라 다를 수 있습니다. 일부 데이터는 수명 기간 전반에 걸쳐 활발하게 액세스되고 수정됩니다. 일부 데이터는 수명 기간 초반에는 빈번하게 액세스되지만 데이터가 오래될수록 액세스 빈도가 급격하게 떨어집니다. 일부 데이터는 클라우드에서 유휴 상태를 유지 하 고 저장 된 후에 액세스 하는 경우에 거의 발생 하지 않습니다.

이러한 각 데이터 액세스 시나리오는 특정 액세스 패턴에 맞게 최적화 된 다른 액세스 계층의 이점을 활용 합니다. Azure Blob storage는 핫, 쿨 및 보관 액세스 계층을 사용 하 여 별도의 가격 책정 모델을 통해 차별화 된 액세스 계층에 이러한 요구 사항을 해결 합니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>계층을 지원하는 스토리지 계정

핫, 쿨 및 보관 간의 개체 저장소 데이터 계층화는 Blob storage 및 범용 v2 (GPv2) 계정 에서만 지원 됩니다. 범용 v1 (GPv1) 계정은 계층화를 지원 하지 않습니다. 고객은 Azure Portal를 통해 기존 GPv1 또는 Blob storage 계정을 GPv2 계정으로 쉽게 변환할 수 있습니다. GPv2는 blob, 파일 및 큐에 대 한 새로운 가격 책정 및 기능을 제공 합니다. 일부 기능 및 가격 컷은 GPv2 계정에만 제공 됩니다. 가격 책정을 포괄적으로 검토 한 후 GPv2 계정을 사용 하 여 평가 합니다. 일부 워크 로드는 GPv1 보다 GPv2 비용이 더 많이 들 수 있습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

Blob storage 및 GPv2 계정은 계정 수준에서 **액세스 계층** 특성을 노출 합니다. 이 특성을 사용 하면 개체 수준에서 명시적으로 설정 하지 않은 모든 blob에 대 한 기본 액세스 계층을 지정할 수 있습니다. 개체 수준에서 계층이 설정 된 개체의 경우 계정 계층이 적용 되지 않습니다. 보관 계층은 개체 수준 에서만 적용할 수 있습니다. 언제 든 지 이러한 액세스 계층 간을 전환할 수 있습니다.

## <a name="hot-access-tier"></a>핫 액세스 계층

핫 액세스 계층은 쿨 및 archive 계층 보다 더 높은 저장소 비용이 있지만 액세스 비용은 가장 낮습니다. 핫 액세스 계층에 대 한 사용 시나리오 예는 다음과 같습니다.

- 활성 상태 이거나 액세스 해야 하는 데이터 (읽기 및 쓰기)가 자주 사용 되는 데이터입니다.
- 처리를 위해 준비 된 데이터 및 쿨 액세스 계층으로의 최종 마이그레이션

## <a name="cool-access-tier"></a>쿨 액세스 계층

쿨 액세스 계층은 핫 저장소에 비해 저장소 비용이 낮고 액세스 비용이 높습니다. 이 계층은 최소 30일 동안 쿨 계층에 유지되는 데이터를 위한 계층입니다. 쿨 액세스 계층에 대 한 사용 시나리오 예는 다음과 같습니다.

- 단기 백업 및 재해 복구 데이터 세트
- 자주 감상하지 않으나, 액세스할 때 즉시 사용할 수 있어야 하는 오래된 미디어 콘텐츠
- 향후 처리를 위해 더 많은 데이터를 수집하는 동안 경제적으로 저장되어야 하는 대용량 데이터 집합 (*예:* 과학적 데이터의 장기 스토리지, 제조 설비의 원시 원격 분석 데이터)

## <a name="archive-access-tier"></a>보관 액세스 계층

보관 액세스 계층의 저장소 비용은 가장 낮습니다. 그러나 핫 및 쿨 계층에 비해 데이터 검색 비용이 더 높습니다. 데이터는 최소 180 일 동안 보관 계층에 유지 되거나 초기 삭제 요금이 적용 되어야 합니다. 보관 계층의 데이터는 리하이드레이션의 우선 순위에 따라 검색 하는 데 몇 시간 정도 걸릴 수 있습니다. 작은 개체의 경우 우선 순위가 높은 리하이드레이션 1 시간 이내에 보관에서 개체를 검색할 수 있습니다. 자세한 내용은 [리하이드레이션 blob data in archive 계층](storage-blob-rehydration.md) 을 참조 하세요.

Blob이 보관 저장소에 있는 동안 blob 데이터는 오프 라인 상태 이며 읽거나 덮어쓰거나 수정할 수 없습니다. 보관에서 blob을 읽거나 다운로드 하려면 먼저 온라인 계층으로 리하이드레이션 해야 합니다. 보관 저장소에서 blob의 스냅숏을 만들 수 없습니다. 그러나 blob 메타 데이터는 온라인 상태를 유지 하므로 blob, 해당 속성, 메타 데이터 및 blob 인덱스 태그를 나열할 수 있습니다. 보관 중에는 blob 메타 데이터를 설정 또는 수정할 수 없습니다. 그러나 blob 인덱스 태그를 설정 하 고 수정할 수 있습니다. 보관에 있는 blob의 경우 유효한 유일한 작업은 GetBlobProperties, Getblobproperties, SetBlobTags, Getblobproperties, FindBlobsByTags, ListBlobs, Setblobtags, CopyBlob 및 DeleteBlob입니다.

보관 액세스 계층의 예제 사용 시나리오는 다음과 같습니다.

- 장기 백업, 보조 백업 및 보관 데이터 세트
- 사용 가능한 최종 형태로 처리된 후에도 보존할 필요가 있는 원래(원시) 데이터.
- 장기간 저장할 필요가 있거나 거의 액세스하지 않는 규정 준수 및 보관 데이터.

> [!NOTE]
> 보관 계층은 현재 ZRS, GZRS 또는 RA-GZRS 계정에 대해 지원 되지 않습니다.

## <a name="account-level-tiering"></a>계정 수준 계층화

세 가지 액세스 계층의 blob은 모두 동일한 계정 내에 공존할 수 있습니다. 명시적으로 할당 된 계층이 없는 모든 blob은 계정 액세스 계층 설정에서 계층을 유추 합니다. 액세스 계층이 계정에서 제공 되는 경우 **유추 된 액세스 계층** blob 속성이 "true"로 설정 되 고 **액세스 계층** blob 속성이 계정 계층과 일치 하는 것을 볼 수 있습니다. Azure Portal에서 _유추 된 액세스 계층_ 속성은 blob 액세스 계층을 **핫 (유추)** 또는 **쿨 (유추)** 로 표시 합니다.

계정 액세스 계층을 변경 하는 것은 명시적 계층 집합이 없는 계정에 저장 된 모든 _액세스 계층 유추_ 개체에 적용 됩니다. 계정 계층을 핫에서 쿨로 전환 하는 경우 GPv2 계정의 집합 계층 없이 모든 blob에 대 한 쓰기 작업 (1만 당)에 대해 요금이 청구 됩니다. Blob storage 계정에는 이러한 변경 내용이 부과 되지 않습니다. Blob storage 또는 GPv2 계정에서 쿨에서 핫으로 전환 하는 경우 읽기 작업 (1만 당) 및 데이터 검색 (GB 당) 모두에 대 한 요금이 청구 됩니다.

## <a name="blob-level-tiering"></a>Blob 수준 계층화

Blob 수준 계층화를 사용 하면 blob [배치](/rest/api/storageservices/put-blob) 또는 [블록 목록 배치](/rest/api/storageservices/put-block-list) 작업을 사용 하 여 선택한 액세스 계층에 데이터를 업로드 하 고 [Blob 계층 작업 설정](/rest/api/storageservices/set-blob-tier) 또는 [수명 주기 관리](#blob-lifecycle-management) 기능을 사용 하 여 개체 수준에서 데이터의 계층을 변경할 수 있습니다. 필요한 액세스 계층에 데이터를 업로드 한 다음 계정 간에 데이터를 이동할 필요 없이 사용 패턴이 변경 됨에 따라 핫, 쿨 또는 보관 계층 간에 blob 액세스 계층을 쉽게 변경할 수 있습니다. 모든 계층 변경 요청이 즉시 발생 하 고 핫 및 쿨 간의 계층 변경 내용이 즉시 발생 합니다. 그러나 보관에서 Blob을 리하이드레이션하는 데 몇 시간이 걸릴 수 있습니다.

마지막 Blob 계층 변경 시간은 **액세스 계층 변경 시간** Blob 속성을 통해 노출됩니다. 핫 또는 쿨 계층의 blob을 덮어쓸 때 새로 만든 blob은 새 blob 액세스 계층이 생성 시 명시적으로 설정 되지 않으면 덮어쓴 blob의 계층을 상속 합니다. Blob이 보관 계층에 있는 경우 덮어쓸 수 없으므로 동일한 blob을 업로드 하는 것은이 시나리오에서 허용 되지 않습니다. 

> [!NOTE]
> 보관 스토리지 및 Blob 수준 계층화는 블록 Blob만 지원합니다.

### <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob Storage 수명 주기 관리는 데이터를 최상의 액세스 계층으로 전환 하 고 수명 주기 종료 시 데이터를 만료 하는 데 사용할 수 있는 다양 한 규칙 기반 정책을 제공 합니다. [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)를 참조하여 자세히 알아보세요.  

> [!NOTE]
> 블록 blob storage 계정 (프리미엄 성능)에 저장 된 데이터는 현재 [Set Blob 계층](/rest/api/storageservices/set-blob-tier) 또는 Azure Blob Storage 수명 주기 관리를 사용 하 여 핫, 쿨 또는 보관으로 계층화 할 수 없습니다.
> 데이터를 이동 하려면 블록 blob 저장소 계정에서 blob을 사용 하 여 다른 계정에서 핫 액세스 계층으로 blob을 동기식으로 복사 해야 합니다. [URL Api의 Put 블록](/rest/api/storageservices/put-block-from-url) 또는이 api를 지 원하는 AzCopy의 버전입니다.
> *URL에서 블록 배치* API는 서버에서 데이터를 동기적으로 복사합니다. 이는 모든 데이터를 원래 서버 위치에서 대상 위치로 이동하면 호출이 완료된다는 의미입니다.

### <a name="blob-level-tiering-billing"></a>Blob 수준 계층화 청구

Blob을 핫, 쿨 또는 archive 계층으로 업로드 하거나 이동 하는 경우 계층 변경 시 즉시 해당 속도로 요금이 청구 됩니다.

BLOB이 쿨 계층으로 이동하는 경우(핫->쿨, 핫->아카이브, 또는 쿨->아카이브) 작업은 대상 계층 쓰기 작업으로 청구되며 대상 계층의 쓰기 작업(10,000개당) 및 데이터 쓰기(GB당) 요금이 적용됩니다.

Blob이 핫 계층으로 이동 하면 (archive->쿨, archive->핫 또는 쿨 >핫) 작업은 원본 계층에서 읽기로 청구 되며 원본 계층의 읽기 작업 (1만 당) 및 데이터 검색 (GB 당) 요금이 적용 됩니다. 쿨 또는 보관 계층에서 이동한 모든 Blob에 대한 초기 삭제 요금도 적용해야 합니다. [리하이드레이션](storage-blob-rehydration.md) 데이터를 보관 하는 데 시간이 걸리고 데이터를 온라인으로 복원 하 고 blob 계층을 핫 또는 쿨로 변경할 때까지 데이터를 보관 가격으로 청구 합니다. 다음 표에는 계층 변경이 청구 되는 방식이 요약 되어 있습니다.

| | **쓰기 요금(작업 + 액세스)** | **읽기 요금(작업 + 액세스)**
| ---- | ----- | ----- |
| **SetBlobTier 방향** | 핫 >쿨,<br> 핫 >보관<br> 쿨 >보관 | 보관->쿨,<br> 보관->핫,<br> 쿨 >핫

### <a name="cool-and-archive-early-deletion"></a>쿨 및 보관 초기 삭제

쿨 계층으로 이동 하는 모든 blob (GPv2 계정에만 해당)은 쿨 초기 삭제 기간인 30 일의 영향을 받습니다. 보관 계층으로 이동 하는 모든 blob에는 보관 초기 삭제 기간인 180 일이 적용 됩니다. 요금이 비례하여 배분됩니다. 예를 들어 blob가 보관으로 이동 된 다음 45 일 후에 삭제 되거나 핫 계층으로 이동 되 면 보관에 해당 blob을 저장 하는 135 (180-45) 일에 해당 하는 초기 삭제 요금이 청구 됩니다.

액세스 계층이 변경 되지 않은 경우에는 **마지막으로 수정한**blob 속성을 사용 하 여 초기 삭제를 계산할 수 있습니다. 그렇지 않으면 blob 속성: **액세스 계층 변경 시간**을 확인 하 여 액세스 계층이 쿨 또는 보관으로 마지막으로 수정 된 시간을 사용할 수 있습니다. Blob 속성에 대한 자세한 내용은 [Blob 속성 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)를 참조하세요.

## <a name="comparing-block-blob-storage-options"></a>블록 blob 저장소 옵션 비교

다음 표에서는 프리미엄 성능 블록 blob 저장소와 핫, 쿨 및 보관 액세스 계층의 비교를 보여 줍니다.

|                                           | **프리미엄 성능**   | **핫 계층** | **쿨 계층**       | **보관 계층**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **가용성**                          | 99.9%                     | 99.9%        | 99%                 | 오프라인           |
| **가용성** <br> **(RA-GRS 읽기)**  | 해당 없음                       | 99.99%       | 99.9%               | 오프라인           |
| **사용 요금**                         | 더 높은 저장소 비용, 낮은 액세스 및 트랜잭션 비용 | 스토리지 비용 더 높음, 액세스 및 트랜잭션 비용 더 낮음 | 스토리지 비용 더 낮음, 액세스 및 트랜잭션 비용 더 높음 | 스토리지 비용 가장 낮음, 액세스 및 트랜잭션 비용 가장 높음 |
| **최소 개체 크기**                   | 해당 없음                       | 해당 없음          | 해당 없음                 | 해당 없음               |
| **최소 스토리지 기간**              | 해당 없음                       | 해당 없음          | 30 일<sup>1</sup> | 180일
| **대기 시간** <br> **(첫 번째 바이트 까지의 시간)** | 1 자리 밀리초 | 밀리초 | 밀리초        | 시간<sup>2</sup> |

<sup>1</sup> GPv2 계정의 쿨 계층에 있는 개체의 최소 보존 기간은 30 일입니다. Blob storage 계정에는 쿨 계층에 대 한 최소 보존 기간이 없습니다.

<sup>2</sup> Archive Storage 현재는 서로 다른 검색 대기 시간을 제공 하는 2 리하이드레이션 우선 순위 (높음 및 표준)를 지원 합니다. 자세한 내용은 [리하이드레이션 blob data from the archive 계층](storage-blob-rehydration.md)항목을 참조 하세요.

> [!NOTE]
> Blob storage 계정은 범용 v2 저장소 계정과 동일한 성능 및 확장성 목표를 지원 합니다. 자세한 내용은 [Azure 스토리지의 확장성 및 성능 목표](scalability-targets.md)를 참조하세요.

## <a name="quickstart-scenarios"></a>빠른 시작 시나리오

이 섹션에서는 Azure Portal 및 PowerShell을 사용 하 여 다음 시나리오를 보여 줍니다.

- GPv2 또는 Blob Storage 계정의 기본 계정 액세스 계층을 변경하는 방법입니다.
- GPv2 또는 Blob Storage 계정의 계층을 변경하는 방법입니다.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2 또는 Blob Storage 계정의 기본 계정 액세스 계층을 변경합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 리소스**를 검색하여 선택합니다.

1. 사용자의 스토리지 계정을 선택합니다.

1. **설정**에서 **구성** 을 선택 하 여 계정 구성을 확인 하 고 변경 합니다.

1. 요구 사항에 적합 한 액세스 계층을 선택 합니다. **액세스 계층** 을 **쿨** 또는 **핫**으로 설정 합니다.

1. 위쪽에서 **저장** 을 클릭 합니다.

![Azure Portal에서 기본 계정 계층 변경](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell 스크립트를 사용 하 여 계정 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>GPv2 또는 Blob 저장소 계정에서 blob의 계층 변경
# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 리소스**를 검색하여 선택합니다.

1. 사용자의 스토리지 계정을 선택합니다.

1. 컨테이너를 선택한 다음 Blob을 선택합니다.

1. **Blob 속성**에서 **계층 변경**을 선택합니다.

1. **핫**, **쿨**또는 **보관** 액세스 계층을 선택 합니다. Blob이 현재 보관 된 상태이 고 온라인 계층으로 리하이드레이션 하는 경우 **표준** 또는 **높음**의 리하이드레이션 우선 순위를 선택할 수도 있습니다.

1. 아래쪽에서 **저장**을 선택합니다.

![Azure Portal에서 blob 계층 변경](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell 스크립트를 사용 하 여 blob 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다. `$containerName` 변수는 컨테이너 이름으로 초기화해야 합니다. `$blobName` 변수는 Blob 이름으로 초기화해야 합니다. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

모든 저장소 계정은 각 blob의 계층에 따라 블록 blob 저장소에 대 한 가격 책정 모델을 사용 합니다. 다음과 같은 비용 청구 고려 사항을 염두에 둡니다.

- **저장소 비용**: 저장 된 데이터의 양에 더하여 데이터를 저장 하는 비용은 액세스 계층에 따라 달라 집니다. 계층이 차가워질수록 기가바이트당 비용이 감소합니다.
- **데이터 액세스 비용**: 계층이 차가워질수록 데이터 액세스 요금이 증가합니다. 쿨 및 보관 액세스 계층의 데이터의 경우 읽기에 대 한 기가바이트 당 데이터 액세스 요금이 청구 됩니다.
- **트랜잭션 비용**: 계층이 증가 함에 따라 증가 하는 모든 계층에 대해 트랜잭션 별 요금이 청구 됩니다.
- **지역에서 복제 데이터 전송 비용**: 이 요금은 GRS 및 RA-GRS를 포함하여 지역에서 복제가 구성된 계정에만 해당합니다. 지역 복제 데이터 전송에는 기가바이트당 요금이 발생합니다.
- **아웃바운드 데이터 전송 비용**: 아웃바운드 데이터 전송(Azure 지역 밖으로 전송된 데이터)에서는 기가바이트당 요금을 기준으로 대역폭 사용 요금이 발생하며 범용 스토리지 계정과 같습니다.
- **액세스 계층 변경**: 계정 액세스 계층을 변경 하면 명시적 계층 집합이 없는 계정에 저장 된 _액세스 계층 유추_ blob에 대 한 계층 변경 요금이 발생 합니다. 단일 blob에 대 한 액세스 계층을 변경 하는 방법에 대 한 자세한 내용은 [blob 수준 계층화 청구](#blob-level-tiering-billing)를 참조 하세요.

    버전 관리를 사용 하도록 설정 하거나 blob에 스냅숏이 있는 경우 blob에 대 한 액세스 계층을 변경 하면 추가 요금이 발생할 수 있습니다. Blob 버전 관리를 사용 하도록 설정 하 고 blob 계층을 명시적으로 변경 하는 경우의 요금을 청구 하는 방법에 대 한 자세한 내용은 blob 버전 관리에 대 한 설명서의 [가격 책정 및 청구](versioning-overview.md#pricing-and-billing) 를 참조 하세요. Blob에 스냅숏이 있고 blob의 계층을 명시적으로 변경 하는 경우 비용이 청구 되는 방법에 대 한 자세한 내용은 blob 스냅숏에 대 한 설명서의 [가격 책정 및 청구](snapshots-overview.md#pricing-and-billing) 를 참조 하세요.

> [!NOTE]
> 블록 blob에 대 한 가격 책정에 대 한 자세한 내용은 [가격 책정 페이지 Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) 를 참조 하세요. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 페이지를 참조하세요.

## <a name="faq"></a>FAQ

**데이터를 계층화 하려는 경우 Blob storage 또는 GPv2 계정을 사용 해야 하나요?**

계층화에 Blob Storage 계정 대신 GPv2를 사용하는 것이 좋습니다. GPv2는 Blob Storage 계정이 지원하는 모든 기능을 지원합니다. Blob Storage와 GPv2 간의 가격 책정은 거의 동일하지만 몇 가지 새로운 기능 및 가격 구분은 GPv2 계정에서만 사용할 수 있습니다. GPv1 계정은 계층화를 지원 하지 않습니다.

GPv1과 GPv2 계정 간에 가격 책정 구조가 다르며 고객은 GPv2 계정을 사용하기 전에 둘을 신중하게 평가해야 합니다. 간단히 한 번 클릭하는 프로세스를 통해 기존 Blob Storage 또는 GPv1 계정을 GPv2로 쉽게 변환할 수 있습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

**동일한 계정에 있는 세 개의 모든 (핫, 쿨 및 보관) 액세스 계층에 개체를 저장할 수 있나요?**

예. 계정 수준에서 설정 된 **액세스 계층** 특성은 명시적 집합 계층이 없는 해당 계정의 모든 개체에 적용 되는 기본 계정 계층입니다. Blob 수준 계층화를 사용 하면 계정의 액세스 계층 설정에 관계 없이 개체 수준에서 액세스 계층을 설정할 수 있습니다. 세 가지 액세스 계층 (핫, 쿨 또는 보관) 중 하나에 있는 blob는 동일한 계정 내에 존재할 수 있습니다.

**내 Blob 또는 GPv2 저장소 계정의 기본 액세스 계층을 변경할 수 있나요?**

예, 저장소 계정에 **액세스 계층** 특성을 설정 하 여 기본 계정 계층을 변경할 수 있습니다. 계정 계층 변경은 명시적 계층 집합이 없는 계정에 저장 된 모든 개체 (예: **핫 (유추)** 또는 **쿨 (유추)**)에 적용 됩니다. 계정 계층을 핫에서 쿨로 전환 하면 GPv2 계정의 집합 계층 없이 모든 blob에 대해 쓰기 작업 (1만 당)이 발생 하 고 쿨에서 핫으로 전환 하면 Blob storage 및 GPv2 계정의 모든 blob에 대 한 읽기 작업 (1만 당) 및 데이터 검색 (GB 당) 요금이 발생 합니다.

**기본 계정 액세스 계층을 보관 계층으로 설정할 수 있나요?**

아니요. 핫 및 쿨 액세스 계층만 기본 계정 액세스 계층으로 설정할 수 있습니다. 보관은 개체 수준에서만 설정할 수 있습니다. Blob 업로드에서 기본 계정 계층에 관계 없이 핫, 쿨 또는 보관으로 선택할 액세스 계층을 지정 합니다. 이 기능을 사용 하면 데이터를 보관 계층에 직접 기록 하 여 blob 저장소에서 데이터를 만드는 순간부터 비용을 절감할 수 있습니다.

**에서 사용할 수 있는 핫, 쿨 및 보관 액세스 계층은 어디 인가요?**

Blob 수준 계층화와 함께 핫 및 쿨 액세스 계층은 모든 지역에서 사용할 수 있습니다. 보관 스토리지는 선택 영역에서만 사용할 수 있습니다. 전체 목록은 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

**쿨 액세스 계층의 blob이 핫 액세스 계층의 blob과 다르게 작동 하나요?**

핫 액세스 계층의 blob에는 GPv1, GPv2 및 Blob storage 계정의 blob과 동일한 대기 시간이 있습니다. 쿨 액세스 계층의 blob에는 GPv1, GPv2 및 Blob storage 계정의 blob과 유사한 대기 시간 (밀리초)이 있습니다. 보관 액세스 계층의 blob에는 GPv1, GPv2 및 Blob storage 계정에서 몇 시간의 대기 시간이 있습니다.

쿨 액세스 계층의 blob에는 핫 액세스 계층에 저장 된 blob 보다 약간 낮은 가용성 서비스 수준 (SLA)이 있습니다. 자세한 내용은 [스토리지에 대한 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)를 참조하세요.

**핫, 쿨 및 보관 계층 간에 작업은 동일한가요?**

핫 및 쿨 간의 모든 작업은 100% 일치합니다. GetBlobProperties, Getblobproperties, SetBlobTags, Getblobproperties, FindBlobsByTags, ListBlobs, Setblobtags 및 DeleteBlob을 비롯 한 모든 유효한 보관 작업은 핫 및 쿨와 100% 일치 합니다. Blob 데이터는 다시 사용할 때까지 보관 계층에서 읽거나 수정할 수 없습니다. 보관 중에는 blob 메타 데이터 읽기 작업만 지원 됩니다. 그러나 보관 중에는 blob 인덱스 태그를 읽거나 설정 하거나 수정할 수 있습니다.

**보관 계층에서 핫 또는 쿨 계층으로 blob을 리하이드레이션 때 리하이드레이션이 완료 되 면 어떻게 알 수 있나요?**

리하이드레이션 중에는 blob 속성 가져오기 작업을 사용 하 여 **보관 상태** 특성을 폴링하고 계층 변경이 완료 된 시간을 확인할 수 있습니다. 상태는 대상 계층에 따라 "rehydrate-pending-to-hot" 또는 "rehydrate-pending-to-cool"을 읽습니다. 완료되면 보관 상태 속성이 제거되고 **액세스 계층** Blob 속성은 새로운 핫 또는 쿨 계층을 반영합니다. 자세한 내용은 [리하이드레이션 blob data in archive 계층](storage-blob-rehydration.md) 을 참조 하세요.

**Blob의 계층을 설정한 후에 언제부터 해당하는 요금이 청구되기 시작하나요?**

각 Blob은 항상 Blob의 **액세스 계층** 속성에서 표시하는 계층에 따라 비용이 청구됩니다. Blob에 대 한 새 온라인 계층을 설정 하는 경우 **액세스 계층** 속성은 모든 전환에 대 한 새 계층을 즉시 반영 합니다. 그러나 오프 라인 보관 계층에서 핫 또는 쿨 계층으로 blob을 리하이드레이션 하는 데는 몇 시간이 걸릴 수 있습니다. 이 경우 리하이드레이션 완료 될 때까지 보관 요금으로 요금이 청구 됩니다. 이때 **액세스 계층** 속성은 새 계층을 반영 합니다. 온라인 계층으로 전환 되 면 핫 또는 쿨 요금으로 해당 blob에 대 한 요금이 청구 됩니다.

**쿨 또는 보관 계층에서 blob을 삭제 하거나 이동할 때 초기 삭제 요금이 발생 하는지 확인 하는 어떻게 할까요??**

쿨(GPv2 계정만) 또는 보관 계층에서 각각 30일 및 180일 이전에 삭제되거나 이동되는 모든 Blob은 비례 배분된 초기 삭제 요금이 발생합니다. 마지막 계층 변경의 스탬프를 제공 하는 **액세스 계층 변경 시간** blob 속성을 확인 하 여 blob이 쿨 또는 archive 계층에 얼마나 오래 되었는지를 확인할 수 있습니다. Blob 계층이 절대 변경 되지 않은 경우 **마지막으로 수정한** blob 속성을 확인할 수 있습니다. 자세한 내용은 [쿨 및 보관 초기 삭제](#cool-and-archive-early-deletion)를 참조 하세요.

**Blob 수준 계층화 및 보관 저장소를 지 원하는 Azure 도구 및 Sdk는 무엇 인가요?**

Azure Portal, PowerShell과 CLI 도구 및 .NET, Java, Python과 Node.js 클라이언트 라이브러리는 모두 Blob 수준 계층화 및 보관 스토리지를 지원합니다.  

**핫, 쿨 및 보관 계층에 저장할 수 있는 데이터는 얼마나 되나요?**

다른 제한과 함께 데이터 저장소는 계정 수준에서 설정 되며 액세스 계층 별로 설정 되지 않습니다. 한 계층 또는 3 계층 모두에서 한도를 모두 사용 하도록 선택할 수 있습니다. 자세한 내용은 [standard storage 계정에 대 한 확장성 및 성능 목표](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

GPv2 및 Blob 저장소 계정에서 핫, 쿨 및 보관 평가

- [지역별 핫, 쿨 및 보관의 가용성 확인](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
- [보관 계층에서 blob 데이터 리하이드레이션에 대 한 자세한 정보](storage-blob-rehydration.md)
- [프리미엄 성능이 앱에 이익이 되는지 확인](storage-blob-performance-tiers.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../common/storage-enable-and-view-metrics.md)
- [지역별 Blob Storage 및 GPv2 계정에서 핫, 쿨 및 보관 가격 책정 확인](https://azure.microsoft.com/pricing/details/storage/)
- [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
