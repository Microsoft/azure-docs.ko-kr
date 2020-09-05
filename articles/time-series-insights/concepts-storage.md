---
title: 저장소 개요-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 데이터 저장소에 대해 알아봅니다.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: a0f1e7789c0cebdd1cb5b22f21151020a0be09c9
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855124"
---
# <a name="data-storage"></a>데이터 스토리지

Azure Time Series Insights Gen2 환경을 만들 때 다음 두 가지 Azure 리소스를 만듭니다.

* 웜 데이터 저장소에 대해 구성할 수 있는 Azure Time Series Insights Gen2 환경입니다.
* 콜드 데이터 저장소에 대 한 Azure Storage 계정.

웜 저장소의 데이터는 [시계열 쿼리 api](./time-series-insights-update-tsq.md) 및 [Azure Time Series Insights tsi 탐색기](./time-series-insights-update-explorer.md)를 통해서만 사용할 수 있습니다. 웜 스토어는 Azure Time Series Insights Gen2 환경을 만들 때 선택한 [보존 기간](./time-series-insights-update-plan.md#the-preview-environment) 내의 최근 데이터를 포함 합니다.

Azure Time Series Insights Gen2는 콜드 스토어 데이터를 [Parquet 파일 형식](#parquet-file-format-and-folder-structure)으로 Azure Blob storage에 저장 합니다. Azure Time Series Insights Gen2는이 콜드 저장소 데이터를 독점적으로 관리 하지만 표준 Parquet 파일로 직접 읽을 수 있습니다.

> [!WARNING]
> 콜드 저장소 데이터가 있는 Azure Blob 스토리지 계정의 소유자는 계정의 모든 데이터에 대한 모든 액세스 권한을 갖습니다. 이 액세스 권한에는 쓰기 및 삭제 권한이 포함됩니다. 데이터 손실이 발생할 수 있으므로 Gen2 쓰기를 Azure Time Series Insights 하는 데이터를 편집 하거나 삭제 하지 마세요.

## <a name="data-availability"></a>데이터 가용성

최적의 쿼리 성능을 위해 Gen2 파티션 및 인덱스 데이터를 Azure Time Series Insights 합니다. 데이터는 두 웜 (설정 된 경우) 및 콜드 스토어 (인덱스 된 후) 모두에서 쿼리할 수 있게 됩니다. 수집되는 데이터의 양은 이 가용성에 영향을 줄 수 있습니다.

> [!IMPORTANT]
> 데이터를 사용할 수 있게 될 때까지 최대 60 초 동안 발생할 수 있습니다. 60초가 넘는 긴 대기 시간이 발생하면 Azure Portal을 통해 지원 티켓을 제출하세요.

## <a name="azure-storage"></a>Azure Storage

이 섹션에서는 Azure Time Series Insights Gen2 관련 된 Azure Storage 세부 정보에 대해 설명 합니다.

Azure Blob 스토리지에 대한 자세한 설명은 [Storage Blob 소개](../storage/blobs/storage-blobs-introduction.md)를 참조하세요.

### <a name="your-storage-account"></a>스토리지 계정

Azure Time Series Insights Gen2 환경을 만들 때 장기 콜드 스토어로 Azure Storage 계정이 만들어집니다.  

Azure Time Series Insights Gen2는 Azure Storage 계정에서 각 이벤트의 최대 두 개 복사본을 유지 합니다. 한 복사본은 수집 시간을 기준으로 정렬된 이벤트를 저장하므로 항상 시간 순서가 지정된 시퀀스로 이벤트에 액세스할 수 있습니다. 시간이 지남에 따라 Azure Time Series Insights Gen2는 성능 쿼리에 최적화 하기 위해 데이터의 다시 분할 복사본을 만듭니다.

모든 데이터는 Azure Storage 계정에 무기한 저장 됩니다.

#### <a name="writing-and-editing-blobs"></a>Blob 작성 및 편집

쿼리 성능 및 데이터 가용성을 보장 하려면 Gen2가 만드는 Azure Time Series Insights는 blob을 편집 하거나 삭제 하지 마세요.

#### <a name="accessing-cold-store-data"></a>콜드 저장소 데이터 액세스

[Azure Time Series Insights TSI 탐색기](./time-series-insights-update-explorer.md) 및 [시계열 쿼리 api](./time-series-insights-update-tsq.md)를 사용 하 여 데이터에 액세스 하는 것 외에도 콜드 저장소에 저장 된 Parquet 파일에서 직접 데이터에 액세스할 수 있습니다. 예를 들어 Jupyter Notebook에서 데이터를 읽고 변환하고 정리한 다음, 이를 사용하여 동일한 Spark 워크플로에서 Azure Machine Learning 모델을 학습시킬 수 있습니다.

Azure Storage 계정에서 직접 데이터에 액세스 하려면 Azure Time Series Insights Gen2 데이터를 저장 하는 데 사용 되는 계정에 대 한 읽기 권한이 있어야 합니다. 그러면 [Parquet 파일 형식](#parquet-file-format-and-folder-structure) 섹션 아래에서 설명하는 `PT=Time` 폴더에 있는 Parquet 파일의 만든 시간을 기준으로 선택한 데이터를 읽을 수 있습니다.  스토리지 계정에 대한 읽기 액세스를 사용하도록 설정하는 방법에 대한 자세한 내용은 [스토리지 계정 리소스에 대한 액세스 관리](../storage/blobs/storage-manage-access-to-resources.md)를 참조하세요.

#### <a name="data-deletion"></a>데이터 삭제

Azure Time Series Insights Gen2 파일을 삭제 하지 마세요. Azure Time Series Insights Gen2 내에서 관련 데이터를 관리 합니다.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 파일 형식 및 폴더 구조

Parquet는 효율적인 스토리지와 성능을 위해 설계된 오픈 소스 열 형식 파일 형식입니다. Azure Time Series Insights Gen2는 Parquet를 사용 하 여 규모에 따라 시계열 ID 기반 쿼리 성능을 활성화 합니다.  

Parquet 파일 형식에 대한 자세한 내용은 [Parquet 설명서](https://parquet.apache.org/documentation/latest/)를 참조하세요.

Azure Time Series Insights Gen2는 다음과 같이 데이터의 복사본을 저장 합니다.

* 첫 번째 초기 복사본은 수집 시간을 기준으로 분할되며, 대략 도착 순서대로 데이터를 저장합니다. 이 데이터는 `PT=Time` 폴더에 있습니다.

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 다시 분할된 두 번째 복사본은 시계열 ID를 기준으로 그룹화되며 `PT=TsId` 폴더에 있습니다.

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

폴더에 있는 blob 이름의 타임 스탬프는 `PT=Time` 이벤트의 타임 스탬프가 아니라 Azure Time Series Insights Gen2에 대 한 데이터 도착 시간에 해당 합니다.

`PT=TsId` 폴더의 데이터는 시간이 지남에 따라 쿼리에 최적화되며 정적이지 않습니다. 다시 분할 하는 동안 일부 이벤트는 여러 blob에 있을 수 있습니다. 이 폴더에 있는 blob의 이름 지정은 동일 하 게 유지 되지 않을 수 있습니다.

일반적으로 Parquet 파일을 통해 데이터에 직접 액세스 해야 하는 경우 폴더를 사용 `PT=Time` 합니다.  이후 기능을 사용 하면 폴더에 효율적으로 액세스할 수 `PT=TsId` 있습니다.

> [!NOTE]
>
> * `<YYYY>`는 4자리 연도 표현에 매핑됩니다.
> * `<MM>`은 두 자리 월 표현에 매핑됩니다.
> * `<YYYYMMDDHHMMSSfff>`는 네 자리 연도(`YYYY`), 두 자리 월(`MM`), 두 자리 일(`DD`), 두 자리 시(`HH`), 두 자리 분(`MM`), 두 자리 초(`SS`) 및 세 자리 밀리초(`fff`)의 타임스탬프 표현에 매핑됩니다.

Azure Time Series Insights Gen2 이벤트는 다음과 같이 Parquet 파일 내용에 매핑됩니다.

* 각 이벤트는 단일 행에 매핑됩니다.
* 모든 행에는 이벤트 타임스탬프가 있는 **timestamp** 열이 포함됩니다. 타임스탬프 속성은 null이 아닙니다. 이벤트 원본에 타임 스탬프 속성이 지정 되지 않은 경우에는 **이벤트 큐** 에 대기 된 시간으로 기본 설정 됩니다. 저장된 타임스탬프는 항상 UTC 형식입니다.
* 모든 행에는 Azure Time Series Insights Gen2 환경을 만들 때 정의 된 시계열 ID (TSID) 열이 포함 됩니다. TSID 속성 이름에는 `_string` 접미사가 포함됩니다.
* 원격 분석 데이터로 전송 되는 다른 모든 속성은 `_bool` `_datetime` 속성 형식에 따라 (부울), (타임 스탬프), `_long` (long), `_double` (double), `_string` (string) 또는 `dynamic` (동적)로 끝나는 열 이름에 매핑됩니다.  자세한 내용은 [지원 되는 데이터 형식](./concepts-supported-data-types.md)을 참조 하세요.
* 이 매핑 스키마는 **V=1**로 참조되고, 동일한 이름의 기본 폴더에 저장된 파일 형식의 첫 번째 버전에 적용됩니다. 이 기능이 진화함에 따라 이 매핑 스키마가 변경되고 참조 이름이 증가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [데이터 모델링](./time-series-insights-update-tsm.md)에 대해 읽어 보세요.

* [Azure Time Series Insights Gen2 환경을](./time-series-insights-update-plan.md)계획 합니다.
