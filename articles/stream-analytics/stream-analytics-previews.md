---
title: Azure Stream Analytics 미리 보기 기능
description: 이 문서에는 현재 미리 보기에 있는 Azure Stream Analytics 기능이 나열되어 있습니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: 08430f3eee858cdb6c9a7fbdfe11bd4c00ef148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485678"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="public-previews"></a>공개 미리 보기

다음 기능은 공개 미리 보기에 있습니다. 현재 이러한 기능을 활용할 수 있지만 프로덕션 환경에서는 사용하지 마세요.

### <a name="anomaly-detection"></a>이상 감지

Azure Stream Analytics에는 양방향, 느린 긍정 및 느린 부정 추세 검색 외에도 *spike* 및 *dips* 검색을 지원하는 새로운 기계 학습 모델이 도입되었습니다. 자세한 내용은 [Azure Stream Analytics의 변칙 검색](stream-analytics-machine-learning-anomaly-detection.md)합니다.

### <a name="sql-database-reference-data"></a>SQL Database 참조 데이터

Azure Stream Analytics는 참조 데이터 입력 원본으로 Azure SQL Database를 지원합니다. Azure Portal 및 Stream Analytics 도구가 있는 Visual Studio에서 Stream Analytics 작업에 대한 참조 데이터로 SQL Database를 사용할 수 있습니다. 자세한 내용은 [Azure Stream Analytics 작업에 SQL Database의 참조 데이터 사용](sql-reference-data.md)을 참조하세요.

### <a name="integration-with-azure-machine-learning"></a>Azure Machine Learning과 통합

ML(Machine Learning) 함수로 Stream Analytics 작업을 확장할 수 있습니다. Stream Analytics 작업에서 ML 함수를 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning 함수를 사용하여 Stream Analytics 작업의 크기 조정](stream-analytics-scale-with-machine-learning-functions.md)을 참조하세요. [Azure Stream Analytics 및 Azure Machine Learning을 사용한 감정 분석 수행](stream-analytics-machine-learning-integration-tutorial.md)을 통해 실제 시나리오를 확인하세요.

### <a name="javascript-user-defined-aggregate"></a>JavaScript 사용자 정의 집계

Azure Stream Analytics는 JavaScript로 작성된 UDA(사용자 정의 집계)를 지원하고 이를 통해 복잡한 상태 저장 비즈니스 논리를 구현할 수 있습니다. UDA를 사용하는 방법은 [Azure Stream Analytics JavaScript 사용자 정의 집계](stream-analytics-javascript-user-defined-aggregates.md) 설명서를 참조하세요. 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge의 .NET 사용자 정의 함수

.NET Standard 사용자 정의 함수를 사용하면 스트리밍 파이프라인의 일부로 .NET Standard 코드를 실행할 수 있습니다. 단순 C# 클래스를 작성하거나 전체 프로젝트 및 라이브러리를 가져올 수 있습니다. 전체 작성 및 디버깅 환경은 Visual Studio에서 지원됩니다. 자세한 내용은 [Azure Stream Analytics Edge 작업에 대한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)을 참조하세요.

## <a name="private-previews"></a>비공개 미리 보기

다음 기능은 비공개 미리 보기에 있습니다.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>IoT Edge의 Azure Stream Analytics용 C# 사용자 지정 역직렬 변환기

개발자는 이제 Azure Stream Analytics에서 수신한 이벤트를 역직렬화하기 위해 C#에서 사용자 지정 역직렬 변환기를 구현할 수 있습니다. 역직렬화할 수 있는 형식의 예로는 Parquet, Protobuf, XML 또는 임의의 이진 형식이 있습니다.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics용 Visual Studio Code

Azure Stream Analytics 작업은 Visual Studio Code에서 작성할 수 있습니다. 에 대 한 액세스 비공개 미리 보기 기능을 도구에 문의 *ASAToolsfeedback\@microsoft.com*합니다.

## <a name="next-steps"></a>다음 단계

* [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)(Azure Stream Analytics의 8가지 새 기능)

* [이제 Azure Stream Analytics에서 사용 가능한 네 가지 새 기능](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
