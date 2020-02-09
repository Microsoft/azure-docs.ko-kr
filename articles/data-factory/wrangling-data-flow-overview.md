---
title: Azure Data Factory 데이터 흐름 랭 글 링
description: Azure Data Factory의 랭 글 링 데이터 흐름에 대한 개요
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8f91ed926c733b211443805722b6817b1ce005b6
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048355"
---
# <a name="what-are-wrangling-data-flows"></a>랭 글 링 데이터 흐름은 무엇 인가요?

조직은 매일 계속 증가 하는 복잡 한 데이터를 정확 하 게 분석 하기 위해 데이터 준비 및 랭 글 링을 수행 해야 합니다. 조직에서 다양 한 비즈니스 프로세스의 데이터를 사용 하 고 시간 값을 줄일 수 있도록 데이터를 준비 해야 합니다.

Azure Data Factory의 랭 글 링 데이터 흐름을 사용 하면 클라우드 규모에서 코드 없는 데이터 준비를 반복적으로 수행할 수 있습니다. 랭 글 링 데이터 흐름은 [파워 쿼리 Online](https://docs.microsoft.com/power-query/) 과 통합 되며, 파워 쿼리 M 함수를 data factory 사용자가 사용할 수 있도록 합니다.

랭 글 링 데이터 흐름은 파워 쿼리 온라인 매시업 편집기에 의해 생성 된 M을 클라우드 규모 실행을 위한 spark 코드로 변환 합니다.

랭 글 링 데이터 흐름은 데이터 엔지니어 또는 ' 시민 데이터 통합자 '에 게 특히 유용 합니다.

## <a name="use-cases"></a>사용 사례

### <a name="fast-interactive-data-exploration-and-preparation"></a>신속한 대화형 데이터 탐색 및 준비

여러 데이터 엔지니어와 시민 데이터 통합자는 클라우드 규모에서 데이터 집합을 대화형으로 탐색 하 고 준비할 수 있습니다. 데이터 레이크 데이터의 양이 증가 하 고 다양 하 고 속도가 증가 함에 맞게 사용자는 데이터 집합을 탐색 하 고 준비 하는 효과적인 방법이 필요 합니다. 예를 들어 ' 2017 ' 이후의 새 고객에 대한 모든 고객 인구 통계 정보를 포함 하는 데이터 집합을 만들어야 할 수 있습니다. 알려진 대상에 매핑되지 않습니다. Lake에 게시 하기 전에 요구 사항을 충족 하기 위해 데이터 집합을 탐색, 랭 글 링 및 prepping 합니다. 랭 글 링 데이터 흐름은 더 짧은 공식 분석 시나리오에 사용 되는 경우가 많습니다. 준비 데이터 집합을 사용 하 여 변환 및 기계 학습 작업을 수행할 수 있습니다.

### <a name="code-free-agile-data-preparation"></a>코드 없는 agile 데이터 준비

시민 데이터 통합자는 데이터를 찾고 준비 하는 시간을 60% 넘게 소비 합니다. 운영 생산성을 개선 하기 위해 코드를 무료로 사용 하 고 있습니다. 사용자가 확장 가능한 방식으로 온라인 파워 쿼리 같은 알려진 도구를 사용 하 여 데이터를 보강 하 고, 셰이핑 하 고, 게시할 수 있도록 하 여 생산성을 크게 향상 시킵니다. Azure Data Factory의 데이터 흐름을 사용 하면 친숙 한 파워 쿼리 온라인 매시업 편집기를 사용 하 여 시민 데이터 통합 자가 오류를 신속 하 게 수정 하 고 데이터를 표준화 하 고 비즈니스 의사 결정을 지 원하는 고품질 데이터를 생성할 수 있습니다.

### <a name="data-validation"></a>데이터 유효성 검사

코드를 사용 하지 않는 방식으로 데이터를 시각적으로 검사 하 여 이상 값, 이상 값을 제거 하 고 빠른 분석을 위해 셰이프를 준수 합니다.

## <a name="supported-sources"></a>지원 되는 원본

| 커넥터 | 데이터 형식 | 인증 형식 |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | 계정 키 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV 형식입니다. | 서비스 주체 |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | 계정 키, 서비스 주체 |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 인증 |
| [Azure Synapse 분석](connector-azure-sql-data-warehouse.md) | - | SQL 인증 |

## <a name="the-mashup-editor"></a>매시업 편집기

랭 글 링 데이터 흐름을 만들 때 모든 원본 데이터 집합은 데이터 집합 쿼리가 되며 **Adfresource** 폴더에 배치 됩니다. 기본적으로 UserQuery는 첫 번째 데이터 집합 쿼리를 가리킵니다. 데이터 집합 쿼리에 대한 변경 내용이 지원 되지 않거나 유지 되지 않으므로 UserQuery에서 모든 변환을 수행 해야 합니다. 현재는 쿼리 이름을 바꾸고 쿼리를 추가 및 삭제 하는 것은 지원 되지 않습니다.

![랭 글 링](media/wrangling-data-flow/editor.png)

현재 모든 파워 쿼리 M 함수는 제작 중에도 사용할 수 있는 데이터 랭 글 링 지원 되지 않습니다. 랭 글 링 데이터 흐름을 작성 하는 동안 함수가 지원 되지 않으면 다음과 같은 오류 메시지가 표시 됩니다.

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

지원 되는 변환에 대한 자세한 내용은 [랭 글 링 data flow 함수](wrangling-data-flow-functions.md)를 참조 하세요.

현재 랭 글 링 데이터 흐름은 한 싱크에만 쓸 수 있도록 지원 합니다.

## <a name="next-steps"></a>다음 단계

[랭 글 링 데이터 흐름을 만드는](wrangling-data-flow-tutorial.md)방법에 대해 알아봅니다.