---
title: 예제를 통해 Azure Data Factory 가격 책정 이해 | Microsoft Docs
description: 이 문서에서는 자세한 예제와 함께 Azure Data Factory 가격 책정 모델을 설명하고 보여줍니다.
documentationcenter: ''
author: shlo
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: 80b1f90ee0d9f5003c39eb6a853a07d2d64ca482
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787481"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>예제를 통해 Data Factory 가격 책정 이해

이 문서에서는 자세한 예제와 함께 Azure Data Factory 가격 책정 모델을 설명하고 보여줍니다.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>AWS S3에서 Azure Blob Storage로 매시간 데이터 복사

이 시나리오에서는 매시간 일정으로 AWS S3에서 Azure Blob Storage로 데이터를 복사하려고 합니다.

시나리오를 달성하려면 다음 항목을 사용하여 파이프라인을 만들어야 합니다.

1. AWS S3에서 복사될 데이터에 대한 입력 데이터 세트가 있는 복사 작업

2. Azure Storage의 데이터에 대한 출력 데이터 세트

3. 파이프라인을 1시간마다 실행하는 일정 트리거

   ![시나리오 1](media/pricing-concepts/scenario1.png)

| **작업** | **형식 및 단위** |
| --- | --- |
| 연결된 서비스 만들기 | 2개의 읽기/쓰기 엔터티  |
| 데이터 세트 만들기 | 4개의 읽기/쓰기 엔터티(데이터 세트 만들기에 대해 2개, 연결된 서비스 참조에 대해 2개) |
| 파이프라인 만들기 | 3개의 읽기/쓰기 엔터티(파이프라인 만들기에 대해 1개, 데이터 세트 참조에 대해 2개) |
| 파이프라인 가져오기 | 1개의 읽기/쓰기 엔터티 |
| 파이프라인 실행 | 2개의 활동 실행(트리거 실행에 대해 1개, 활동 실행에 대해 1개) |
| 데이터 복사 가정: 실행 시간 = 10분 | 10 \* 4 Azure Integration Runtime(기본 DIU 설정 = 4) 데이터 통합 단위 및 복사 성능 최적화에 대한 자세한 내용은 [이 문서](copy-activity-performance.md)를 참조하세요. |
| 파이프라인 모니터링 가정: 하나의 실행만 발생했습니다. | 다시 시도되는 2개의 모니터링 실행 기록(파이프라인 실행에 대해 1개, 활동 실행에 대해 1개) |

**총 시나리오 가격 책정: $0.16811**

- Data Factory 작업 = **$0.0001**
  - 읽기/쓰기 = 10\*00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - 모니터링 = 2\*000005 = $0.00001 [1 Monitoring = $0.25/50000 = 0.000005]
- 파이프라인 오케스트레이션 &amp; 실행 = **$0.168**
  - 활동 실행 = 001\*2 = 0.002 [1 run = $1/1000 = 0.001]
  - 데이터 이동 활동 = $0.166(실행 시간의 10분에 대해 비례합니다. Azure Integration Runtime에서 $0.25/시간)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>데이터를 복사하고 Azure Databricks를 사용하여 시간별 변환

이 시나리오에서는 매시간 일정으로 AWS S3에서 Azure Blob Storage로 데이터를 복사하고 Azure Databricks를 사용하여 매시간 일정으로 데이터를 변환하려고 합니다.

시나리오를 달성하려면 다음 항목을 사용하여 파이프라인을 만들어야 합니다.

1. AWS S3에서 복사될 데이터에 대한 입력 데이터 세트 및 Azure 저장소의 데이터에 대한 출력 데이터 세트가 있는 하나의 복사 작업
2. 데이터 변환에 대한 하나의 Azure Databricks 작업
3. 파이프라인을 1시간마다 실행하는 하나의 일정 트리거

![시나리오 2](media/pricing-concepts/scenario2.png)

| **작업** | **형식 및 단위** |
| --- | --- |
| 연결된 서비스 만들기 | 3개의 읽기/쓰기 엔터티  |
| 데이터 세트 만들기 | 4개의 읽기/쓰기 엔터티(데이터 세트 만들기에 대해 2개, 연결된 서비스 참조에 대해 2개) |
| 파이프라인 만들기 | 3개의 읽기/쓰기 엔터티(파이프라인 만들기에 대해 1개, 데이터 세트 참조에 대해 2개) |
| 파이프라인 가져오기 | 1개의 읽기/쓰기 엔터티 |
| 파이프라인 실행 | 3개의 활동 실행(트리거 실행에 대해 1개, 활동 실행에 대해 2개) |
| 데이터 복사 가정: 실행 시간 = 10분 | 10 \* 4 Azure Integration Runtime(기본 DIU 설정 = 4) 데이터 통합 단위 및 복사 성능 최적화에 대한 자세한 내용은 [이 문서](copy-activity-performance.md)를 참조하세요. |
| 파이프라인 모니터링 가정: 하나의 실행만 발생했습니다. | 다시 시도되는 3개의 모니터링 실행 기록(파이프라인 실행에 대해 1개, 활동 실행에 대해 2개) |
| Databricks 활동 실행 가정: 실행 시간 = 10분 | 10분 외부 파이프라인 활동 실행 |

**총 시나리오 가격 책정: $0.16916**

- Data Factory 작업 = **$0.00012**
  - 읽기/쓰기 = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - 모니터링 = 3\*000005 = $0.00001 [1 Monitoring = $0.25/50000 = 0.000005]
- 파이프라인 오케스트레이션 &amp; 실행 = **$0.16904**
  - 활동 실행 = 001\*3 = 0.003 [1 run = $1/1000 = 0.001]
  - 데이터 이동 활동 = $0.166(실행 시간의 10분에 대해 비례합니다. Azure Integration Runtime에서 $0.25/시간)
  - 외부 파이프라인 활동 = $0.000041(실행 시간의 10분에 대해 비례합니다. Azure Integration Runtime에서 $0.00025/시간)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>데이터를 복사하고 동적 매개 변수를 사용하여 시간별 변환

이 시나리오에서는 매시간 일정으로 AWS S3에서 Azure Blob Storage로 데이터를 복사하고 Azure Databricks(스크립트의 동적 매개 변수와 함께)를 사용하여 매시간 일정으로 변환하려고 합니다.

시나리오를 달성하려면 다음 항목을 사용하여 파이프라인을 만들어야 합니다.

1. AWS S3에서 복사될 데이터에 대한 입력 데이터 세트 및 Azure 저장소의 데이터에 대한 출력 데이터 세트가 있는 하나의 복사 작업
2. 변환 스크립트에 매개 변수를 동적으로 전달하는 하나의 조회 작업
3. 데이터 변환에 대한 하나의 Azure Databricks 작업
4. 파이프라인을 1시간마다 실행하는 하나의 일정 트리거

![시나리오 3](media/pricing-concepts/scenario3.png)

| **작업** | **형식 및 단위** |
| --- | --- |
| 연결된 서비스 만들기 | 3개의 읽기/쓰기 엔터티  |
| 데이터 세트 만들기 | 4개의 읽기/쓰기 엔터티(데이터 세트 만들기에 대해 2개, 연결된 서비스 참조에 대해 2개) |
| 파이프라인 만들기 | 3개의 읽기/쓰기 엔터티(파이프라인 만들기에 대해 1개, 데이터 세트 참조에 대해 2개) |
| 파이프라인 가져오기 | 1개의 읽기/쓰기 엔터티 |
| 파이프라인 실행 | 4개의 활동 실행(트리거 실행에 대해 1개, 활동 실행에 대해 3개) |
| 데이터 복사 가정: 실행 시간 = 10분 | 10 \* 4 Azure Integration Runtime(기본 DIU 설정 = 4) 데이터 통합 단위 및 복사 성능 최적화에 대한 자세한 내용은 [이 문서](copy-activity-performance.md)를 참조하세요. |
| 파이프라인 모니터링 가정: 하나의 실행만 발생했습니다. | 다시 시도되는 4개의 모니터링 실행 기록(파이프라인 실행에 대해 1개, 활동 실행에 대해 3개) |
| 조회 작업 실행 가정: 실행 시간 = 1분 | 1분 파이프라인 활동 실행 |
| Databricks 활동 실행 가정: 실행 시간 = 10분 | 10분 외부 파이프라인 활동 실행 |

**총 시나리오 가격 책정: $0.17020**

- Data Factory 작업 = **$0.00013**
  - 읽기/쓰기 = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - 모니터링 = 4\*000005 = $0.00002 [1 Monitoring = $0.25/50000 = 0.000005]
- 파이프라인 오케스트레이션 &amp; 실행 = **$0.17007**
  - 활동 실행 = 001\*4 = 0.004 [1 run = $1/1000 = 0.001]
  - 데이터 이동 활동 = $0.166(실행 시간의 10분에 대해 비례합니다. Azure Integration Runtime에서 $0.25/시간)
  - 파이프라인 활동 = $0.00003(실행 시간의 1분에 대해 비례합니다. Azure Integration Runtime에서 $0.002/시간)
  - 외부 파이프라인 활동 = $0.000041(실행 시간의 10분에 대해 비례합니다. Azure Integration Runtime에서 $0.00025/시간)

## <a name="next-steps"></a>다음 단계

Azure Data Factory에 대한 가격 책정을 이해했으므로 시작할 수 있습니다.

- [Azure Data Factory UI를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)

- [Azure Data Factory 소개](introduction.md)

- [Azure Data Factory에서 시각적 개체 작성](author-visually.md)