---
title: Azure Stream Analytics 미리 보기 기능
description: 이 문서에는 현재 미리 보기에 있는 Azure Stream Analytics 기능이 나열되어 있습니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: e11d5b14bdf6b134fefea79a1f709ec73499bd20
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815711"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="public-previews"></a>공개 미리 보기

다음 기능은 공개 미리 보기에 있습니다. 현재 이러한 기능을 활용할 수 있지만 프로덕션 환경에서는 사용하지 마세요.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>관리 ID를 사용하여 SQL Database 출력에 인증

Azure Stream Analytics는 Azure SQL Database 출력 싱크에 대한 [관리 ID 인증](../active-directory/managed-identities-azure-resources/overview.md)을 지원합니다. 관리 id를 사용 하면 암호 변경으로 인해 다시 인증 해야 하는 것과 같은 사용자 기반 인증 방법의 제한 사항이 제거 됩니다. 

### <a name="output-to-azure-synapse-analytics"></a>Azure Synapse Analytics에 출력

Azure Stream Analytics 작업은 [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics)의 SQL 풀 테이블로 출력될 수 있으며 최대 200MB/초의 속도로 처리할 수 있습니다. 이는 보고 및 대시보드와 같은 워크로드를 위한 가장 까다로운 실시간 분석 및 핫 경로 데이터 처리 요구를 지원합니다.  

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning에서 관리하는 사용자 지정 ML 모델을 사용하는 실시간 고성능 점수 매기기

Azure Stream Analytics는 코드를 작성할 필요가 없는 워크플로를 사용하는 Azure Machine Learning에서 관리하며, AKS(Azure Kubernetes Service) 또는 ACI(Azure Container Instances)에서 호스팅되는 사용자 지정의 미리 학습된 Machine Learning 모델을 활용하여 고성능의 실시간 점수 매기기를 지원합니다. 미리 보기에 [등록](https://aka.ms/asapreview1)하세요.

### <a name="c-custom-de-serializers"></a>C# 사용자 지정 역직렬 변환기
개발자는 Azure Stream Analytics 기능을 활용하여 Protobuf, XML 또는 사용자 지정 형식의 데이터를 처리할 수 있습니다. C#에서 [사용자 지정 역직렬 변환기](custom-deserializer-examples.md)를 구현하여 Azure Stream Analytics에서 수신한 이벤트를 역직렬화하는 데 사용할 수 있습니다.

### <a name="extensibility-with-c-custom-code"></a>C# 사용자 지정 코드를 사용한 확장 가능 여부

클라우드 또는 IoT Edge에서 Stream Analytics 모듈을 만드는 개발자는 사용자 지정 C# 함수를 작성하거나 다시 사용할 수 있으며, [사용자 정의 함수](stream-analytics-edge-csharp-udf-methods.md)를 통해 쿼리에서 직접 호출할 수 있습니다.

### <a name="debug-query-steps-in-visual-studio"></a>Visual Studio에서의 쿼리 디버그 단계

Visual Studio용 Azure Stream Analytics 도구에서 로컬 테스트를 수행할 때 데이터 다이어그램의 중간 행 세트를 손쉽게 미리 볼 수 있습니다. 


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics용 Visual Studio Code

Azure Stream Analytics 작업은 Visual Studio Code에서 작성할 수 있습니다. [VS Code 시작 자습서](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)를 참조하세요.

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Visual Studio Code의 라이브 데이터를 사용하여 로컬 테스트

Azure에 작업을 제출하기 전에 로컬 컴퓨터의 라이브 데이터에 대해 쿼리를 테스트할 수 있습니다. 각 테스트 반복은 평균 2 ~ 3초 미만이 소요되므로 개발 프로세스가 매우 효율적입니다.

## <a name="other-previews"></a>기타 미리 보기

다음 기능은 요청 시 미리 보기에서도 사용할 수 있습니다.

### <a name="support-for-azure-stack"></a>Azure Stack 지원
Azure IoT Edge 런타임에 사용하도록 설정된 이 기능은 Azure Stack에서 실행되는 로컬 입력 및 출력에 대한 기본 지원과 같은 사용자 지정 Azure Stack 기능을 활용합니다(예: 이벤트 허브, IoT 허브, Blob Storage). 이 새로운 통합을 사용하면 데이터를 생성 위치에 가깝게 분석하여 대기 시간을 줄이고 인사이트를 극대화하는 하이브리드 아키텍처를 구축할 수 있습니다.
이 기능을 사용하면 데이터를 생성 위치와 가깝게 분석하여 대기 시간을 낮추고 인사이트를 극대화하는 하이브리드 아키텍처를 구축할 수 있습니다. 이 미리 보기에 [등록](https://aka.ms/asapreview1)해야 합니다.
