---
title: Azure Stream Analytics에서 Blob 저장소 및 Azure Data Lake Gen2 출력
description: 이 문서에서는 blob storage 및 Azure Data Lake Gen 2를 Azure Stream Analytics에 대 한 출력으로 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 2ab45f4c64e6993f70f08f04ee413211abb0307d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875903"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Azure Stream Analytics에서 Blob 저장소 및 Azure Data Lake Gen2 출력

Data Lake Storage Gen2는 Azure에서 Azure Storage를 엔터프라이즈 데이터 레이크를 구축하기 위한 기반으로 만듭니다. Data Lake Storage Gen2는 처음부터 수백 기가비트의 처리량을 유지하면서 수 페타바이트의 정보에 대한 서비스를 제공하도록 설계되어 대량의 데이터를 쉽게 관리할 수 있습니다. Data Lake Storage Gen2의 기본 부분은 Blob 스토리지에 대한 계층 구조 네임스페이스를 추가하는 것입니다.

Azure Blob Storage는 클라우드에서 대량의 비정형 데이터를 저장하기 위한 비용 효율적이고 확장성 있는 솔루션을 제공합니다. Blob 스토리지 및 이를 사용하는 방법에 대한 소개는 [Azure Portal을 사용하여 Blob 업로드, 다운로드 및 나열](../storage/blobs/storage-quickstart-blobs-portal.md)을 참조하세요.

## <a name="output-configuration"></a>출력 구성

다음 표에는 Blob 또는 ADLS Gen2 출력을 만들기 위한 속성 이름 및 해당 설명이 나와 있습니다.

| 속성 이름       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 출력 별칭        | 쿼리 출력을 이 Blob Storage로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 스토리지 계정     | 출력을 보내는 스토리지 계정의 이름입니다.               |
| Storage 계정 키 | 스토리지 계정과 연결된 비밀 키입니다.                              |
| 스토리지 컨테이너   | Azure Blob service에 저장된 Blob에 대한 논리적 그룹화입니다. Blob service에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다. |
| 경로 패턴 | (선택 사항) 지정된 컨테이너 내에서 Blob을 작성하는 데 사용되는 파일 경로 패턴입니다. <br /><br /> 경로 패턴에서 하나 이상의 날짜 및 시간 변수를 사용하여 Blob이 작성되는 빈도를 지정하도록 선택할 수 있습니다. <br /> {date}, {time} <br /><br />사용자 지정 Blob 분할을 사용하여 이벤트 데이터에서 파티션 Blob까지 하나의 사용자 지정 {field} 이름을 지정할 수 있습니다. 필드 이름은 영숫자이며 공백, 하이픈 및 밑줄을 포함할 수 있습니다. 사용자 지정 필드에 대한 제한은 다음을 포함합니다. <ul><li>필드 이름은 대/소문자를 구분하지 않습니다. 예를 들어 서비스는 "ID" 열과 "id" 열을 구분할 수 없습니다.</li><li>중첩 필드는 허용되지 않습니다. 대신 작업 쿼리에서 별칭을 사용하여 필드를 "평면화"합니다.</li><li>식은 필드 이름으로 사용할 수 없습니다.</li></ul> <br />이 기능을 사용하면 경로에 사용자 지정 날짜/시간 형식 지정자 구성을 사용할 수 있습니다. 사용자 지정 날짜 및 시간 형식은 {datetime:} 키워드로 묶어 한 번에 하나씩 지정 해야 합니다 \<specifier> . 에 허용 \<specifier> 되는 입력은 yyyy, MM, M, dd, d, HH, H, MM, M, ss 또는 s입니다. {Datetime: \<specifier> } 키워드는 경로에서 여러 번 사용 하 여 사용자 지정 날짜/시간 구성을 구성할 수 있습니다. <br /><br />예제: <ul><li>예제 1: cluster1/logs/{date}/{time}</li><li>예제 2: cluster1/logs/{date}</li><li>예제 3: cluster1/{client_id}/{date}/{time}</li><li>예제 4: cluster1/{datetime:ss}/{myField} where the query is: SELECT data.myField AS myField FROM 입력</li><li>예제 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />만든 폴더 구조의 타임스탬프는 현지 시간이 아닌 UTC를 따릅니다.<br /><br />파일 명명에서 사용되는 규칙은 다음과 같습니다. <br /><br />{경로 접두사 패턴}/schemaHashcode_Guid_Number.extension<br /><br />예제 출력 파일:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />이 기능에 대한 자세한 내용은 [Azure Stream Analytics 사용자 지정 Blob 출력 분할](stream-analytics-custom-path-patterns-blob-storage-output.md)을 참조하세요. |
| 날짜 형식 | (선택 사항) 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예제: YYYY/MM/DD |
| 시간 형식 | (선택 사항) 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, Avro 및 Parquet가 지원됩니다. |
|최소 행 |일괄 처리당 최소 행 수입니다. Parquet의 경우 모든 일괄 처리에서 새 파일을 만듭니다. 현재 기본값은 2,000개 행이고, 허용되는 최댓값은 10,000개 행입니다.|
|최대 시간 |일괄 처리당 최대 대기 시간입니다. 이 시간이 지나면 최소 행 수 요구 사항이 충족되지 않는 경우에도 일괄 처리가 출력에 기록됩니다. 현재 기본값은 1분이고, 허용되는 최댓값은 2시간입니다. Blob 출력에 경로 패턴 빈도가 있는 경우 대기 시간은 파티션 시간 범위보다 클 수 없습니다.|
| Encoding    | CSV 또는 JSON 형식을 사용하는 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호   | CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식      | JSON 직렬화에만 적용됩니다. **줄로 구분됨**은 각 JSON 개체를 새 줄로 구분된 형식의 출력이 되도록 지정합니다. **줄로 구분됨**을 선택하는 경우 JSON에서 한 번에 하나의 개체를 읽습니다. 전체 콘텐츠 자체는 유효한 JSON이 아닙니다. **배열**은 JSON 개체 배열 형식의 출력이 되도록 지정합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 출력 파일을 쓰는 동안 특별한 처리가 필요하지 않으므로 줄로 구분된 JSON을 사용하는 것이 좋습니다. |

## <a name="blob-output-files"></a>Blob 출력 파일

Blob 스토리지를 출력으로 사용할 때 새 파일이 Blob에 만들어지는 경우는 다음과 같습니다.

* 파일이 허용되는 최대 블록 수(현재 50,000)를 초과합니다. 허용되는 최대 Blob 크기에 도달하지 않고 허용되는 최대 블록 수에 도달할 수 있습니다. 예를 들어 출력 속도가 높으면 블록당 더 많은 바이트를 볼 수 있으며 파일 크기는 더 커집니다. 출력 속도가 낮으면 각 블록의 데이터가 줄어들고 파일 크기도 작아집니다.
* 출력의 스키마가 변경되고 출력 형식에 고정 스키마(CSV 및 Avro)가 필요한 경우
* 작업이 다시 시작되는 경우 사용자가 외부적으로 또는 시스템 유지 관리 또는 오류 복구의 경우 내부적으로 중지하고 시작합니다.
* 쿼리가 완전히 분할되고 각 출력 파티션에 대해 새 파일이 만들어지는 경우
* 사용자가 스토리지 계정의 파일 또는 컨테이너를 삭제하는 경우
* 경로 접두사 패턴을 사용하여 출력 시간이 분할되고 쿼리가 다음 시간으로 이동할 때 새 Blob이 사용되는 경우
* 출력이 사용자 지정 필드에 따라 분할되고 Blob이 없을 때 파티션 키마다 새 Blob이 만들어지는 경우
* 출력이 파티션 키 카디널리티가 8,000을 초과하는 사용자 지정 필드에 따라 분할되고 파티션 키마다 새 Blob이 만들어지는 경우

## <a name="partitioning"></a>분할

파티션 키의 경우 경로 패턴의 이벤트 필드에서 {date} 및 {time} 토큰을 사용 합니다. 날짜 형식을 선택합니다(예: YYYY/MM/DD, DD/MM/YYYY 또는 MM-DD-YYYY). HH는 시간 형식으로 사용됩니다. Blob 출력은 단일 사용자 지정 이벤트 특성 {fieldname} 또는 {datetime:} (으)로 분할할 수 있습니다 \<specifier> . 출력 기록기의 수는 [완전히 병렬화 쿼리에](stream-analytics-scale-jobs.md)대 한 입력 분할을 따릅니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 메시지 크기는 [Azure Storage 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)을 참조 하세요. 최대 blob 블록 크기는 4mb이 고 최대 blob bock 수는 5만입니다. |

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
