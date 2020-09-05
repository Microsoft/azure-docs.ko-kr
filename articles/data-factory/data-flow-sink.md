---
title: 데이터 흐름 매핑의 싱크 변환
description: 데이터 흐름 매핑에서 싱크 변환을 구성 하는 방법에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/18/2020
ms.openlocfilehash: b2a356f8ee6c8e3d11548a3c7c17a868e4c057c4
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179184"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 싱크 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 변환을 완료 한 후에는 싱크 변환을 사용 하 여 대상 저장소에 데이터를 씁니다. 모든 데이터 흐름에는 하나 이상의 싱크 변환이 필요 하지만 변환 흐름을 완료 하는 데 필요한 만큼의 싱크를 작성할 수 있습니다. 추가 싱크에 쓰려면 새 분기 및 조건부 분할을 통해 새 스트림을 만듭니다.

각 싱크 변환은 정확히 하나의 Azure Data Factory 데이터 집합 개체 또는 연결 된 서비스와 연결 됩니다. 싱크 변환은 작성 하려는 데이터의 모양과 위치를 결정 합니다.

## <a name="inline-datasets"></a>인라인 데이터 집합

싱크 변환을 만들 때 싱크 정보가 데이터 집합 개체 내부에 정의 되는지 싱크 변환 내에 정의 되어 있는지 여부를 선택 합니다. 대부분의 형식은 한 경우에만 사용할 수 있습니다. 특정 커넥터를 사용 하는 방법을 알아보려면 적절 한 커넥터 문서를 참조 하세요.

인라인 및 데이터 집합 개체에서 형식이 모두 지원 되는 경우 두 가지 이점이 있습니다. 데이터 집합 개체는 복사와 같은 다른 데이터 흐름과 작업에서 활용할 수 있는 재사용 가능한 엔터티입니다. 이러한 기능은 강화 된 스키마를 사용할 때 특히 유용 합니다. 데이터 집합은 Spark를 기반으로 하지 않으며 경우에 따라 싱크 변환에서 특정 설정 또는 스키마 프로젝션을 재정의 해야 할 수도 있습니다.

유연한 스키마, 일회용 싱크 인스턴스 또는 매개 변수가 있는 싱크를 사용 하는 경우 인라인 데이터 집합을 사용 하는 것이 좋습니다. 싱크가 매우 매개 변수화 된 경우 인라인 데이터 집합을 사용 하 여 "더미" 개체를 만들 수 없습니다. 인라인 데이터 집합은 spark를 기반으로 하며 해당 속성은 데이터 흐름의 기본입니다.

인라인 데이터 집합을 사용 하려면 **싱크 형식** 선택기에서 원하는 형식을 선택 합니다. 싱크 데이터 집합을 선택 하는 대신 연결할 연결 된 서비스를 선택 합니다.

![인라인 데이터 집합](media/data-flow/inline-selector.png "인라인 데이터 집합")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> 지원 되는 싱크 형식

매핑 데이터 흐름은 ELT (추출, 로드, 변환) 접근 방식을 따르며, 모든 Azure의 *준비* 데이터 집합에서 작동 합니다. 현재 원본 변환에 사용할 수 있는 데이터 집합은 다음과 같습니다.

| 커넥터 | 서식 | 데이터 집합/인라인 |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties) <br> [델타 (미리 보기)](format-delta.md) <br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties) <br> [델타 (미리 보기)](format-delta.md) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (미리 보기)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

이러한 커넥터에 해당 하는 설정은 **설정** 탭에 있습니다. 이러한 설정에 대 한 정보 및 데이터 흐름 스크립트 예제는 커넥터 설명서에 있습니다. 

Azure Data Factory는 [90가지의 네이티브 커넥터](connector-overview.md)를 통해 액세스할 수 있습니다. 데이터 흐름에서 다른 원본으로 데이터를 쓰려면 복사 작업을 사용 하 여 지원 되는 싱크에서 해당 데이터를 로드 합니다.

## <a name="sink-settings"></a>싱크 설정

싱크를 추가한 후에는 **싱크** 탭을 통해를 구성 합니다. 여기서 싱크가 쓰는 데이터 집합을 선택 하거나 만들 수 있습니다. 다음은 텍스트 구분 파일 형식에 대 한 여러 가지 싱크 옵션을 설명 하는 비디오입니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![싱크 설정](media/data-flow/sink-settings.png "싱크 설정")

**스키마 드리프트:** [스키마 드리프트](concepts-data-flow-schema-drift.md) 는 열 변경 내용을 명시적으로 정의할 필요 없이 데이터 흐름에서 유연한 스키마를 고유 하 게 처리 하는 data factory의 기능입니다. 싱크 데이터 스키마에 정의 된 내용 위에 추가 열을 쓰도록 **허용 schema 드리프트** 를 사용 하도록 설정 합니다.

**스키마 유효성 검사:** 스키마 유효성 검사를 선택 하면 들어오는 원본 스키마의 열을 원본 프로젝션에서 찾을 수 없거나 데이터 형식이 일치 하지 않는 경우 데이터 흐름이 실패 합니다. 이 설정을 사용 하 여 원본 데이터가 정의 된 프로젝션의 계약을 충족 하도록 적용 합니다. 데이터베이스 원본 시나리오에서 열 이름이 나 형식이 변경 되었다는 신호를 보내는 것이 매우 유용 합니다.

## <a name="field-mapping"></a>필드 매핑

선택 변환과 마찬가지로 싱크의 **매핑** 탭에서 들어오는 열 중 어떤 열을 쓸지 결정할 수 있습니다. 기본적으로 데이터베이스가 드리프트 열을 포함 하 여 모든 입력 열이 매핑됩니다. 이를 **자동 매핑**이라고 합니다.

자동 매핑을 해제할 때 고정 열 기반 매핑 또는 규칙 기반 매핑을 추가 하는 옵션이 제공 됩니다. 규칙 기반 매핑을 사용 하면 패턴 일치를 포함 하는 식을 작성할 수 있으며 고정 매핑은 논리적 및 물리적 열 이름을 매핑합니다. 규칙 기반 매핑에 대 한 자세한 내용은 [데이터 흐름 매핑의 열 패턴](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)을 참조 하세요.

## <a name="custom-sink-ordering"></a>사용자 지정 싱크 순서 지정

기본적으로 데이터는 비결 정적 순서로 여러 싱크에 기록 됩니다. 변환 논리가 완료 되 고 싱크 순서가 각 실행 마다 다를 수 있으므로 실행 엔진은 데이터를 병렬로 작성 합니다. 싱크 순서를 정확 하 게 지정 하려면 데이터 흐름의 일반 탭에서 **사용자 지정 싱크 순서** 를 사용 하도록 설정 합니다. 사용 하도록 설정 하면 싱크는 오름차순으로 순차적으로 작성 됩니다.

![사용자 지정 싱크 순서 지정](media/data-flow/custom-sink-ordering.png "사용자 지정 싱크 순서 지정")

## <a name="data-preview-in-sink"></a>싱크의 데이터 미리 보기

디버그 클러스터에서 데이터 미리 보기를 가져오는 경우 싱크에 데이터가 기록 되지 않습니다. 데이터의 모양에 대 한 스냅숏은 반환 되지만 대상에는 아무것도 기록 되지 않습니다. 싱크에 대 한 데이터 쓰기를 테스트 하려면 파이프라인 캔버스에서 파이프라인 디버그를 실행 합니다.

## <a name="next-steps"></a>다음 단계
이제 데이터 흐름을 만들었으므로 [파이프라인에 데이터 흐름 활동](concepts-data-flow-overview.md)을 추가 합니다.
