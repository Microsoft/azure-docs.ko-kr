---
title: Azure Stream Analytics의 테이블 저장소 출력
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력으로 Azure 테이블 저장소에 대해 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0f8da31dbe59412ebd57c1f3523089062e5089a6
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875914"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Azure Stream Analytics의 테이블 저장소 출력

[Azure Table Storage](../storage/common/storage-introduction.md)는 가용성이 높고 확장성이 큰 스토리지를 제공하므로, 애플리케이션이 사용자 요구에 맞게 자동으로 확장할 수 있습니다. Table 스토리지는 스키마에 대한 제약 조건이 더 적은 정형 데이터에 사용할 수 있는 Microsoft의 NoSQL 키/특성 저장소입니다. Azure Table Storage는 지속적이고 효율적인 검색을 위해 데이터를 저장하는 데 사용할 수 있습니다.

다음 표에는 테이블 출력을 만들기 위한 속성 이름 및 해당 설명이 나와 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 Table Storage로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 스토리지 계정 |출력을 보내는 스토리지 계정의 이름입니다. |
| Storage 계정 키 |스토리지 계정과 연결된 선택키입니다. |
| 테이블 이름 |테이블의 이름입니다. 테이블이 없는 경우 새로 만들어집니다. |
| 파티션 키 |파티션 키가 포함된 출력 열의 이름입니다. 파티션 키는 엔터티 기본 키의 첫 번째 부분을 구성하는 테이블 내의 파티션에 대한 고유 식별자입니다. 최대 1KB의 문자열 값입니다. |
| 행 키. |행 키가 포함된 출력 열의 이름입니다. 행 키는 파티션 내의 엔터티에 대한 고유 식별자입니다. 엔터티 기본 키의 두 번째 부분을 구성합니다. 행 키는 최대 1KB의 문자열 값입니다. |
| Batch 크기 |배치 작업에 대한 레코드 수입니다. 기본값(100)은 대부분의 작업에 충분합니다. 이 설정을 수정하는 방법에 대한 자세한 내용은 [테이블 일괄 처리 작업 사양](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation)을 참조하세요. |

## <a name="partitioning"></a>분할

파티션 키는 모든 출력 열입니다. 출력 작성기의 수는 [완전히 병렬화 된 쿼리에](stream-analytics-scale-jobs.md)대 한 입력 분할을 따릅니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 메시지 크기는 [Azure Storage 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)을 참조 하세요. 기본값은 단일 트랜잭션 당 100 엔터티 이지만 필요에 따라 더 작은 값으로 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
