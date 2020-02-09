---
title: Azure Machine Learning 파이프라인 실행
description: Azure Data Factory 파이프라인에서 Azure Machine Learning 파이프라인을 실행 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: b54504cf8ca7b32bf14bd4b7e0c561ffd56d4098
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155166"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Data Factory에서 Azure Machine Learning 파이프라인 실행

Azure Data Factory 파이프라인의 단계로 Azure Machine Learning 파이프라인을 실행 합니다. Machine Learning 파이프라인 실행 작업을 사용 하면 가능한 대출 기본값 식별, 감정 확인 및 고객 동작 패턴 분석과 같은 일괄 처리 예측 시나리오를 사용할 수 있습니다.

아래 비디오는이 기능에 대한 6 분의 소개와 데모를 제공 합니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>구문

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | 파이프라인의 작업 이름입니다. | String | 예
type | 작업 유형이 ' AzureMLExecutePipeline '입니다. | String | 예
linkedServiceName | Azure Machine Learning 연결 된 서비스 | 연결된 서비스 참조 | 예
mlPipelineId | 게시 된 Azure Machine Learning 파이프라인의 ID입니다. | 문자열(또는 resultType 문자열이 있는 식) | 예
ExperimentName | Machine Learning 파이프라인 실행의 실행 기록 실험 이름 | 문자열(또는 resultType 문자열이 있는 식) | 아닙니다.
mlPipelineParameters | 게시 된 Azure Machine Learning 파이프라인 엔드포인트에 전달 되는 키, 값 쌍입니다. 키가 게시 된 Machine Learning 파이프라인에 정의 된 파이프라인 매개 변수의 이름과 일치 해야 합니다. | 키 값 쌍 (또는 resultType 개체가 있는 식)이 포함 된 개체입니다. | 아닙니다.
mlParentRunId | 부모 Azure Machine Learning 파이프라인 실행 ID | 문자열(또는 resultType 문자열이 있는 식) | 아닙니다.
continueOnStepFailure | 단계가 실패 하는 경우 Machine Learning 파이프라인 실행에서 다른 단계의 실행을 계속할지 여부 | boolean | 아닙니다.

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요.

* [데이터 흐름 작업 실행](control-flow-execute-data-flow-activity.md)
* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
