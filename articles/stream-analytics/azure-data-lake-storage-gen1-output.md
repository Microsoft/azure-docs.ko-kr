---
title: Azure Stream Analytics에서 Azure Data Lake Storage Gen 1 출력
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력 옵션으로 Gen 1 Azure Data Lake Storage 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 98dfd51783ad3bc0b89f441f89436d3c3d928104
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875879"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Stream Analytics에서 Azure Data Lake Storage Gen 1 출력

Stream Analytics는 [Gen 1 출력 Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md) 지원 합니다. Azure Data Lake Storage는 빅 데이터 분석 워크로드를 위한 엔터프라이즈 수준 하이퍼스케일 리포지토리입니다. Data Lake Storage를 사용하여 운영 및 예비 분석을 위해 모든 크기, 형식 및 수집 속도의 데이터를 저장할 수 있습니다. Stream Analytics에는 Data Lake Storage에 액세스할 수 있는 권한이 있어야 합니다.

Stream Analytics의 Azure Data Lake Storage 출력은 Azure 중국 21Vianet 및 Azure 독일 (T-sql 국제) 지역에서 사용할 수 없습니다.

## <a name="output-configuration"></a>출력 구성

다음 표에는 Data Lake Storage Gen 1 출력을 구성하기 위한 속성 이름 및 해당 설명이 나와 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 | 쿼리에서 쿼리 출력을 Data Lake Store로 보내는 데 사용되는 식별 이름입니다. |
| Subscription | Azure Data Lake Storage 계정이 포함된 구독입니다. |
| 계정 이름 | 출력을 보내는 Data Lake Store 계정의 이름입니다. 구독에서 사용할 수 있는 Data Lake Store 계정의 드롭다운 목록에 표시됩니다. |
| 경로 접두사 패턴 | 지정된 Data Lake Store 계정 내에서 파일을 쓰는 데 사용되는 파일 경로입니다. 하나 이상의 {date} 및 {time} 변수 인스턴스를 지정할 수 있습니다.<br /><ul><li>예제 1: folder1/logs/{date}/{time}</li><li>예제 2: folder1/logs/{date}</li></ul><br />만든 폴더 구조의 타임스탬프는 현지 시간이 아닌 UTC를 따릅니다.<br /><br />파일 경로 패턴에 후행 슬래시(/)가 포함되지 않으면 파일 경로의 마지막 패턴이 파일 이름 접두사로 처리됩니다. <br /><br />이 경우 새 파일이 만들어집니다.<ul><li>출력 스키마의 변경</li><li>작업의 외부 또는 내부 다시 시작</li></ul> |
| 날짜 형식 | (선택 사항) 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예제: YYYY/MM/DD |
|시간 형식 | (선택 사항) 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV 및 Avro를 지원합니다.|
| Encoding | CSV 또는 JSON 형식을 사용하는 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.|
| 구분 기호 | CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.|
| 형식 | JSON 직렬화에만 적용됩니다. **줄로 구분됨**은 각 JSON 개체를 새 줄로 구분된 형식의 출력이 되도록 지정합니다. **줄로 구분됨**을 선택하는 경우 JSON에서 한 번에 하나의 개체를 읽습니다. 전체 콘텐츠 자체는 유효한 JSON이 아닙니다.  **배열**은 JSON 개체 배열 형식의 출력이 되도록 지정합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 출력 파일을 쓰는 동안 특별한 처리가 필요하지 않으므로 줄로 구분된 JSON을 사용하는 것이 좋습니다.|
| 인증 모드 | [관리 ID](stream-analytics-managed-identities-adls.md) 또는 사용자 토큰을 사용하여 Data Lake Storage 계정에 대한 액세스 권한을 부여할 수 있습니다. 액세스 권한이 부여되면 사용자 계정 암호를 변경하거나, 이 작업에 대한 Data Lake Storage 출력을 삭제하거나, Stream Analytics 작업을 삭제하여 액세스 권한을 철회할 수 있습니다. |

## <a name="partitioning"></a>분할

파티션 키의 경우 경로 접두사 패턴에 {date} 및 {time} 토큰을 사용 합니다. 날짜 형식 (예: YYYY/MM/DD, DD/MM/YYYY 또는 MM-DD-YYYY)을 선택 합니다. 시간 형식으로 HH를 사용 합니다. 출력 기록기의 수는 [완전히 병렬화 쿼리에](stream-analytics-scale-jobs.md)대 한 입력 분할을 따릅니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 메시지 크기는 [Data Lake Storage 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)을 참조 하세요. 일괄 처리 크기를 최적화 하려면 쓰기 작업당 최대 4mb를 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)