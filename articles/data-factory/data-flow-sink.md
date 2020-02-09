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
ms.date: 12/12/2019
ms.openlocfilehash: 3b631c068d1a444691345e054219208c4c8b0b8c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020049"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 싱크 변환

데이터를 변환한 후 데이터를 대상 데이터 집합으로 싱크할 수 있습니다. 모든 데이터 흐름에는 하나 이상의 싱크 변환이 필요 하지만 변환 흐름을 완료 하는 데 필요한 만큼의 싱크를 작성할 수 있습니다. 추가 싱크에 쓰려면 새 분기 및 조건부 분할을 통해 새 스트림을 만듭니다.

각 싱크 변환은 정확히 하나의 Data Factory 데이터 집합에 연결 됩니다. 데이터 집합은 작성 하려는 데이터의 모양과 위치를 정의 합니다.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>매핑 데이터 흐름에서 지원 되는 싱크 커넥터

현재 다음과 같은 데이터 집합을 싱크 변환에 사용할 수 있습니다.
    
* [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, 텍스트, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, 텍스트, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, 텍스트, Parquet)
* [Azure Synapse 분석](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

이러한 커넥터에 해당 하는 설정은 **설정** 탭에 있습니다. 이러한 설정에 대한 정보는 커넥터 설명서에 있습니다. 

Azure Data Factory는 [90 개의 기본 커넥터](connector-overview.md)에 액세스할 수 있습니다. 데이터 흐름의 다른 원본에 데이터를 쓰려면 복사 작업을 사용 하 여 데이터 흐름이 완료 된 후 지원 되는 준비 영역 중 하나에서 해당 데이터를 로드 합니다.

## <a name="sink-settings"></a>싱크 설정

싱크를 추가한 후에는 **싱크** 탭을 통해를 구성 합니다. 여기에서 싱크가 기록 하는 데이터 집합을 선택 하거나 만들 수 있습니다. 

![싱크 설정](media/data-flow/sink-settings.png "싱크 설정")

**스키마 드리프트:** [스키마 드리프트](concepts-data-flow-schema-drift.md) 는 열 변경 내용을 명시적으로 정의할 필요 없이 데이터 흐름에서 유연한 스키마를 고유 하 게 처리 하는 data factory의 기능입니다. 싱크 데이터 스키마에 정의 된 내용 위에 추가 열을 쓰도록 **허용 schema 드리프트** 를 사용 하도록 설정 합니다.

**스키마 유효성 검사:** 스키마 유효성 검사를 선택 하면 정의 된 데이터 집합 스키마의 열이 없는 경우 데이터 흐름이 실패 합니다.

## <a name="field-mapping"></a>필드 매핑

선택 변환과 마찬가지로 싱크의 **매핑** 탭에서 들어오는 열 중 어떤 열을 쓸지 결정할 수 있습니다. 기본적으로 데이터베이스가 드리프트 열을 포함 하 여 모든 입력 열이 매핑됩니다. 이를 **자동 매핑**이라고 합니다.

자동 매핑을 해제할 때 고정 열 기반 매핑 또는 규칙 기반 매핑을 추가 하는 옵션이 제공 됩니다. 규칙 기반 매핑을 사용 하면 패턴 일치를 포함 하는 식을 작성할 수 있으며 고정 매핑은 논리적 및 물리적 열 이름을 매핑합니다. 규칙 기반 매핑에 대한 자세한 내용은 [데이터 흐름 매핑의 열 패턴](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)을 참조 하세요.

## <a name="custom-sink-ordering"></a>사용자 지정 싱크 순서 지정

기본적으로 데이터는 비결 정적 순서로 여러 싱크에 기록 됩니다. 변환 논리가 완료 되 고 싱크 순서가 각 실행 마다 다를 수 있으므로 실행 엔진은 데이터를 병렬로 작성 합니다. 싱크 순서를 정확 하 게 지정 하려면 데이터 흐름의 일반 탭에서 **사용자 지정 싱크 순서** 를 사용 하도록 설정 합니다. 사용 하도록 설정 하면 싱크는 오름차순으로 순차적으로 작성 됩니다.

![사용자 지정 싱크 순서 지정](media/data-flow/custom-sink-ordering.png "사용자 지정 싱크 순서 지정")

## <a name="data-preview-in-sink"></a>싱크의 데이터 미리 보기

디버그 클러스터에서 데이터 미리 보기를 가져오는 경우 싱크에 데이터가 기록 되지 않습니다. 데이터의 모양에 대한 스냅숏은 반환 되지만 대상에는 아무것도 기록 되지 않습니다. 싱크에 대한 데이터 쓰기를 테스트 하려면 파이프라인 캔버스에서 파이프라인 디버그를 실행 합니다.

## <a name="next-steps"></a>다음 단계
이제 데이터 흐름을 만들었으므로 [파이프라인에 데이터 흐름 활동](concepts-data-flow-overview.md)을 추가 합니다.
