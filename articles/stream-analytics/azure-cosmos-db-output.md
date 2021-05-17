---
title: Azure Stream Analytics에서 Azure Cosmos DB 출력
description: 이 문서에서는 Azure Stream Analytics에서 Azure Cosmos DB로 데이터를 출력하는 방법을 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016580"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Stream Analytics에서 Azure Cosmos DB 출력

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)는 전 세계의 무제한 탄력적 크기 조정, 풍부한 쿼리 및 스키마와 관련 없는 데이터 모델에 대한 자동 인덱싱을 제공하는 전역적으로 분산된 데이터베이스 서비스입니다. Stream Analytics의 Azure Cosmos DB 컨테이너 옵션에 대해 알아보려면 [Azure Cosmos DB를 출력으로 사용하는 Stream Analytics](stream-analytics-documentdb-output.md) 문서를 참조하세요.

Stream Analytics의 Azure Cosmos DB 출력은 현재 Azure 중국 21Vianet 및 Azure 독일(T-Systems International) 지역에서 사용할 수 없습니다.

> [!Note]
> Azure Stream Analytics는 SQL API를 사용한 Azure Cosmos DB에 대한 연결만 지원합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. Azure Stream Analytics를 다른 API로 만든 Azure Cosmos DB 계정에 지정한 경우 데이터는 올바르게 저장되지 않을 수도 있습니다.

다음 표에서는 Azure Cosmos DB 출력을 만드는 속성에 대해 설명합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 | Stream Analytics 쿼리에서 이 출력을 참조할 별칭입니다. |
| sink | Azure Cosmos DB |
| 가져오기 옵션 | **구독에서 Cosmos DB 선택** 또는 **Cosmos DB 설정 수동 제공** 중 하나를 선택합니다.
| 계정 ID | Azure Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다. |
| 계정 키 | Azure Cosmos DB 계정에 대한 공유 액세스 키입니다. |
| 데이터베이스 | Azure Cosmos DB 데이터베이스 이름입니다. |
| 컨테이너 이름 | 사용할 컨테이너 이름이며, Cosmos DB에 있어야 합니다. 예제:  <br /><ul><li> _MyContainer_: "MyContainer"라는 컨테이너가 있어야 합니다.</li>|
| 문서 ID |(선택 사항) 삽입 또는 업데이트 작업의 기반이 되는 기본 키를 지정하는 데 사용되는 출력 이벤트의 필드 이름입니다.

## <a name="partitioning"></a>분할

partition 키 값은 쿼리의 PARTITION BY 절에 기반합니다. 출력 작성기의 수는 [완전히 병렬화된 쿼리](stream-analytics-scale-jobs.md)의 입력 분할을 따릅니다. Stream Analytics는 Cosmos DB 출력 파티션 키를 문자열로 변환합니다. 예를 들어 bigint 형식의 값이 1인 파티션 키가 있는 경우 문자열 형식의 "1"로 변환됩니다. 이 변환은 파티션 속성이 Cosmos DB 기록되는지에 관계없이 항상 발생합니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 메시지 크기는 [Azure Cosmos DB 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)을 참조하세요. 일괄 처리 크기 및 쓰기 빈도는 Azure Cosmos DB 응답에 따라 동적으로 조정됩니다. Stream Analytics에서 미리 결정된 제한이 없습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
