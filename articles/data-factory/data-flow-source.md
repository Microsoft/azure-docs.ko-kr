---
title: 매핑 데이터 흐름의 원본 변환
description: 매핑 데이터 흐름에서 원본 변환을 설정 하는 방법에 대해 알아봅니다.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/18/2020
ms.openlocfilehash: 0f6b1789a5148af37ddd2d0895cec348c27f663a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182499"
---
# <a name="source-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 원본 변환 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

원본 변환은 데이터 흐름에 대 한 데이터 원본을 구성 합니다. 데이터 흐름을 디자인할 때 첫 번째 단계는 항상 원본 변환을 구성 합니다. 원본을 추가 하려면 데이터 흐름 캔버스에서 **원본 추가** 상자를 클릭 합니다.

모든 데이터 흐름에는 하나 이상의 원본 변환이 필요 하지만 데이터 변환을 완료 하는 데 필요한 만큼의 원본을 추가할 수 있습니다. 이러한 소스를 조인, 조회 또는 공용 구조체 변환과 함께 조인할 수 있습니다.

각 원본 변환은 정확히 하나의 데이터 집합 또는 연결 된 서비스와 연결 됩니다. 데이터 집합은 쓰거나 읽고 싶은 데이터의 모양과 위치를 정의 합니다. 파일 기반 데이터 집합을 사용 하는 경우 소스에서 와일드 카드 및 파일 목록을 사용 하 여 한 번에 두 개 이상의 파일을 사용할 수 있습니다.

## <a name="inline-datasets"></a>인라인 데이터 집합

원본 변환을 만들 때의 첫 번째 결정은 원본 정보가 dataset 개체 내에 정의 되어 있는지 아니면 원본 변환 내에 정의 되어 있는지 여부입니다. 대부분의 형식은 한 경우에만 사용할 수 있습니다. 특정 커넥터를 사용 하는 방법을 알아보려면 적절 한 커넥터 문서를 참조 하세요.

인라인 및 데이터 집합 개체에서 형식이 모두 지원 되는 경우 두 가지 이점이 있습니다. 데이터 집합 개체는 복사와 같은 다른 데이터 흐름과 작업에서 활용할 수 있는 재사용 가능한 엔터티입니다. 이러한 기능은 강화 된 스키마를 사용할 때 특히 유용 합니다. 데이터 집합은 Spark를 기반으로 하지 않으며 때때로 원본 변환에서 특정 설정 또는 스키마 프로젝션을 재정의 해야 할 수도 있습니다.

유연한 스키마, 일회용 원본 인스턴스 또는 매개 변수화 된 원본을 사용 하는 경우 인라인 데이터 집합을 사용 하는 것이 좋습니다. 소스가 많이 매개 변수화 된 경우 인라인 데이터 집합을 사용 하 여 "더미" 개체를 만들 수 없습니다. 인라인 데이터 집합은 spark를 기반으로 하며 해당 속성은 데이터 흐름의 기본입니다.

인라인 데이터 집합을 사용 하려면 **소스 형식** 선택기에서 원하는 형식을 선택 합니다. 원본 데이터 집합을 선택 하는 대신 연결할 연결 된 서비스를 선택 합니다.

![인라인 데이터 집합](media/data-flow/inline-selector.png "인라인 데이터 집합")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> 지원 되는 원본 유형

매핑 데이터 흐름은 ELT (추출, 로드, 변환) 접근 방식을 따르며, 모든 Azure의 *준비* 데이터 집합에서 작동 합니다. 현재 원본 변환에 사용할 수 있는 데이터 집합은 다음과 같습니다.

| 커넥터 | 서식 | 데이터 집합/인라인 |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)<br>[델타 (미리 보기)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties)  | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model (미리 보기)](format-common-data-model.md#source-properties)<br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)<br>[델타 (미리 보기)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB(SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

이러한 커넥터와 관련 된 설정은 **원본 옵션** 탭에 있습니다. 이러한 설정에 대 한 정보 및 데이터 흐름 스크립트 예제는 커넥터 설명서에 있습니다. 

Azure Data Factory는 [90가지의 네이티브 커넥터](connector-overview.md)를 통해 액세스할 수 있습니다. 데이터 흐름에 이러한 다른 원본의 데이터를 포함 하려면 복사 작업을 사용 하 여 지원 되는 준비 영역 중 하나에 해당 데이터를 로드 합니다.

## <a name="source-settings"></a>원본 설정

소스를 추가한 후에는 **소스 설정** 탭을 통해를 구성 합니다. 여기서 원본 지점의 데이터 집합을 선택 하거나 만들 수 있습니다. 데이터에 대 한 스키마 및 샘플링 옵션을 선택할 수도 있습니다.

![원본 설정 탭](media/data-flow/source1.png "원본 설정 탭")

**출력 스트림 이름:** 원본 변환의 이름입니다.

**원본 유형:** 인라인 데이터 집합 또는 기존 dataset 개체를 사용할지 여부를 선택 합니다.

**연결 테스트:** 데이터 흐름의 spark 서비스가 원본 데이터 집합에 사용 된 연결 된 서비스에 성공적으로 연결 될 수 있는지 여부를 테스트 합니다. 이 기능을 사용 하려면 디버그 모드가 on 이어야 합니다.

**스키마 드리프트:** [스키마 드리프트](concepts-data-flow-schema-drift.md) 는 열 변경 내용을 명시적으로 정의할 필요 없이 데이터 흐름에서 유연한 스키마를 고유 하 게 처리 하는 data factory의 기능입니다.

* 원본 열이 자주 변경 되는 경우에는 **스키마 드리프트 허용** 확인란을 선택 합니다. 이 설정을 사용 하면 들어오는 모든 원본 필드가 싱크로 변환을 통과할 수 있습니다.

* **데이터베이스가 드리프트 열 유형 유추** 는 검색 된 각 새 열의 데이터 형식을 검색 하 고 정의 하도록 데이터 팩터리에 지시 합니다. 이 기능을 끄면 모든 데이터베이스가 드리프트 열이 문자열 형식이 됩니다.

**스키마 유효성 검사:** 스키마 유효성 검사를 선택 하면 들어오는 원본 데이터가 데이터 집합의 정의 된 스키마와 일치 하지 않는 경우 데이터 흐름이 실행 되지 않습니다.

**줄 수 건너뛰기:** 줄 수 건너뛰기 필드는 데이터 집합의 시작 부분에서 무시할 줄 수를 지정 합니다.

**샘플링:** 샘플링을 사용 하 여 원본의 행 수를 제한 합니다. 디버깅을 위해 소스에서 데이터를 테스트 하거나 샘플링할 때이 설정을 사용 합니다.

소스가 올바르게 구성 되었는지 확인 하려면 디버그 모드를 설정 하 고 데이터 미리 보기를 인출 합니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조 하세요.

> [!NOTE]
> 디버그 모드가 설정 된 경우 디버그 설정의 행 제한 구성은 데이터 미리 보기 중에 원본의 샘플링 설정을 덮어씁니다.

## <a name="source-options"></a>원본 옵션

원본 옵션 탭에는 선택한 커넥터 및 형식과 관련 된 설정이 포함 되어 있습니다. 자세한 내용 및 예제는 관련 [커넥터 설명서](#supported-sources)를 참조 하세요.

## <a name="projection"></a>프로젝션

데이터 집합의 스키마와 마찬가지로 원본의 프로젝션은 원본 데이터의 데이터 열, 형식 및 형식을 정의 합니다. SQL 및 Parquet와 같은 대부분의 데이터 집합 형식에 대해 원본 프로젝션은 데이터 집합에 정의 된 스키마를 반영 하도록 수정 됩니다. 원본 파일이 강력 하 게 형식화 되지 않은 경우 (예: Parquet 파일이 아닌 플랫 csv 파일) 원본 변환의 각 필드에 대 한 데이터 형식을 정의할 수 있습니다.

![투영 탭의 설정](media/data-flow/source3.png "프로젝션")

텍스트 파일에 정의 된 스키마가 없는 경우 데이터 형식 **검색** 을 선택 하 여 Data Factory에서 데이터 형식을 샘플링 하 고 유추 하도록 합니다. 기본 데이터 형식을 자동으로 검색 하려면 **기본 형식 정의** 를 선택 합니다.

**스키마 다시 설정** 은 투영을 참조 된 데이터 집합에 정의 된 것으로 다시 설정 합니다.

하위 스트림 파생 열 변환에서 열 데이터 형식을 수정할 수 있습니다. 열 이름을 수정 하려면 선택 변환을 사용 합니다.

### <a name="import-schema"></a>스키마 가져오기

**투영** 탭의 **스키마 가져오기** 단추를 사용 하면 활성 디버그 클러스터를 사용 하 여 스키마 프로젝션을 만들 수 있습니다. 모든 원본 형식에서 사용할 수 있습니다. 여기에서 스키마를 가져오면 데이터 집합에 정의 된 프로젝션이 재정의 됩니다. Dataset 개체는 변경 되지 않습니다.

이는 데이터 집합에 스키마 정의가 존재 하지 않아도 되는 Avro 및 복잡 한 데이터 구조를 지 원하는 Azure Cosmos DB 같은 데이터 집합에 유용 합니다. 인라인 데이터 집합의 경우이 방법은 스키마 드리프트 없이 열 메타 데이터를 참조 하는 유일한 방법입니다.

## <a name="optimize-the-source-transformation"></a>원본 변환 최적화

**최적화** 탭에서는 각 변환 단계에서 파티션 정보를 편집할 수 있습니다. 대부분의 경우 **현재 분할을 사용** 하면 원본의 이상적인 분할 구조에 맞게 최적화할 수 있습니다.

Azure SQL Database 원본에서 읽는 경우 사용자 지정 **원본** 분할이 가장 빠르게 데이터를 읽을 가능성이 높습니다. ADF는 데이터베이스에 대 한 연결을 병렬로 만들어 대량 쿼리를 읽습니다. 열 또는 쿼리를 사용 하 여이 원본 분할을 수행할 수 있습니다.

![원본 파티션 설정](media/data-flow/sourcepart3.png "분할")

데이터 흐름 매핑 내의 최적화에 대 한 자세한 내용은 [최적화 탭](concepts-data-flow-overview.md#optimize)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[파생 열 변환과](data-flow-derived-column.md) [선택 변환을](data-flow-select.md)사용 하 여 데이터 흐름을 작성 하기 시작 합니다.
