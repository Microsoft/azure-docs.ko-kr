---
title: Azure Media Services 원격 분석 | Microsoft Docs
description: 이 문서에서는 Azure Media Services 원격 분석에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e8b493881662483e66dd835d1cc68a471b18454
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545523"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services 원격 분석  


> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

AMS(Azure Media Services)를 사용하면 해당 서비스에 대한 원격 분석/메트릭 데이터에 액세스할 수 있습니다. 현재 버전의 AMS를 사용하면 라이브 **Channel**, **StreamingEndpoint** 및 라이브 **Archive** 엔터티에 대한 원격 분석 데이터를 수집할 수 있습니다. 

원격 분석은 지정된 Azure Storage 계정의 스토리지 테이블에 기록됩니다(일반적으로 AMS 계정과 연결된 스토리지 계정 사용). 

원격 분석 시스템은 데이터 보존을 관리하지 않습니다. 저장소 테이블을 삭제하여 이전 원격 분석 데이터를 제거할 수 있습니다.

이 항목에서는 AMS 원격 분석을 구성 및 사용하는 방법을 설명합니다.

## <a name="configuring-telemetry"></a>원격 분석 구성

구성 요소 세분성 수준에서 원격 분석을 구성할 수 있습니다. "Normal" 및 "Verbose"라는 두 가지 세부 수준이 있습니다. 현재 두 수준 모두 동일한 정보를 반환합니다. "Normal”을 사용하는 것이 좋습니다. 

다음 항목에서는 원격 분석을 사용하도록 설정하는 방법을 보여 줍니다.

[.NET에서 원격 분석 사용](media-services-dotnet-telemetry.md) 

[REST에서 원격 분석 사용](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>이

원격 분석은 Media Services 계정에 대해 원격 분석을 구성할 때 지정된 스토리지 계정의 Azure Storage 테이블에 기록됩니다. 이 섹션에서는 메트릭에 대한 저장소 테이블을 설명합니다.

다음 방법 중 하나를 사용하여 원격 분석 데이터를 사용할 수 있습니다.

- Azure Table Storage에서 직접 데이터를 읽습니다(예: Storage SDK 사용). 원격 분석 저장소 테이블에 대한 설명을 보려면 **이** 항목의 [원격 분석 정보 사용](https://msdn.microsoft.com/library/mt742089.aspx) 을 참조하세요.

또는

- [이](media-services-dotnet-telemetry.md) 항목에 설명된 것처럼 저장소 데이터를 읽기 위한 Media Services .NET SDK의 지원을 사용합니다. 


아래에 설명된 원격 분석 스키마는 Azure Table Storage 한도 내에서 적절한 성능을 제공하도록 설계되었습니다.

- 각 서비스의 원격 분석이 독립적으로 쿼리될 수 있도록 데이터가 계정 ID 및 서비스 ID별로 분할됩니다.
- 파티션에는 파티션 크기에 적절한 상한 값을 제공하기 위해 날짜가 포함됩니다.
- 행 키가 역시간 순서로 나열되므로 지정된 서비스에 대해 가장 최근의 원격 분석 항목을 쿼리할 수 있습니다.

따라서 다음과 같은 여러 일반 쿼리를 효율적으로 진행할 수 있습니다.

- 별도 서비스에 대한 데이터를 병렬로, 독립적으로 다운로드
- 날짜 범위 내에서 지정된 서비스에 대한 모든 데이터 검색
- 서비스에 대한 가장 최근 데이터 검색

### <a name="telemetry-table-storage-output-schema"></a>원격 분석 Table Storage 출력 스키마

원격 분석 데이터는 단일 테이블 "TelemetryMetrics20160321"에 집계되어 저장됩니다. 여기서 "20160321"은 생성된 테이블의 날짜입니다. 원격 분석 시스템은 00:00 UTC 기반으로 각 날마다 별도의 테이블을 만듭니다. 이 테이블은 지정된 기간 내의 수집 비트 전송률, 보낸 바이트 수 등의 되풀이 값을 저장하는 데 사용됩니다. 

자산|값|예제/참고 사항
---|---|---
PartitionKey|{account ID}_{entity ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>계정 ID는 여러 Media Services 계정이 동일한 저장소 계정에 기록되는 워크플로를 간소화하기 위해 파티션 키에 포함됩니다.
RowKey|{seconds to midnight}_{random value}|01688_00199<br/><br/>행 키는 파티션 내의 상위 n개 스타일 쿼리를 허용하기 위해 자정까지 남은 시간(초)부터 시작됩니다. 자세한 내용은 [이](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) 문서를 참조하세요. 
타임 스탬프|날짜/시간|Azure 테이블에서의 자동 타임스탬프 2016-09-09T22:43:42.241Z
Type|원격 분석 데이터를 제공하는 엔터티 형식|Channel/StreamingEndpoint/Archive<br/><br/>이벤트 형식은 문자열 값입니다.
이름|원격 분석 이벤트의 이름입니다.|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|원격 분석 이벤트가 발생한 시간(UTC)|2016-09-09T22:42:36.924Z<br/><br/>원격 분석을 보내는 엔터티(예: 채널)가 관측된 시간을 제공합니다. 구성 요소 간에 시간 동기화 문제가 있을 수 있으므로 이 값은 근사치입니다.
ServiceID|{service ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
엔터티별 속성|이벤트에 의해 정의|StreamName: stream1, Bitrate 10123, …<br/><br/>나머지 속성은 지정된 이벤트 형식에 대해 정의됩니다. Azure 테이블 콘텐츠는 키/값 쌍입니다.  즉, 테이블 행마다 서로 다른 속성 집합을 갖습니다.

### <a name="entity-specific-schema"></a>엔터티별 스키마

각각이 다음 빈도로 푸시되는 세 가지 유형의 엔터티별 원격 분석 데이터 항목이 있습니다.

- 스트리밍 엔드포인트: 30초마다
- 라이브 채널: 매분
- 라이브 보관: 매분

**스트리밍 엔드포인트**

자산|값|예
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
타임 스탬프|타임 스탬프|Azure 테이블에서의 자동 타임스탬프 2016-09-09T22:43:42.241Z
Type|Type|StreamingEndpoint
이름|이름|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|서비스 ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|엔드포인트의 호스트 이름|builddemoserver.origin.mediaservices.windows.net
StatusCode|레코드 HTTP 상태|200
ResultCode|결과 코드 세부 정보|S_OK
RequestCount|집계의 총 요청|3
BytesSent|집계된 보낸 바이트 수|2987358
ServerLatency|평균 서버 대기 시간(저장소 포함)|129
E2ELatency|평균 종단 간 대기 시간|250

**라이브 채널**

자산|값|예제/참고 사항
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
타임 스탬프|타임 스탬프|Azure 테이블에서의 자동 타임스탬프 2016-09-09T22:43:42.241Z
Type|Type|채널
이름|이름|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|서비스 ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|비디오/오디오/텍스트 트랙 유형|비디오/오디오
TrackName|트랙의 이름|video/audio_1
Bitrate|트랙 비트 전송률|785000
CustomAttributes||   
IncomingBitrate|실제 들어오는 비트 전송률|784548
OverlapCount|수집의 겹침|0
DiscontinuityCount|트랙 불연속성|0
LastTimestamp|마지막으로 수집된 데이터 타임스탬프|1800488800
NonincreasingCount|증가하지 않는 타임스탬프로 인해 삭제된 조각 수|2
UnalignedKeyFrames|키 프레임이 정렬되지 않은 조각을 수신했는지 여부(전체 품질 수준) |True 
UnalignedPresentationTime|프레젠테이션 시간이 정렬되지 않은 조각을 수신했는지 여부(전체 품질 수준/트랙)|True 
UnexpectedBitrate|오디오/비디오 트랙의 계산된/실제 비트 전송률이 40,000bps보다 크고 IncomingBitrate == 0, 또는 IncomingBitrate 및 actualBitrate이 50% 다른 경우 True |True 
Healthy|다음과 같은 경우 True <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> 위 항목이 모두 0|True <br/><br/>Healthy는 다음 조건 중 하나라도 충족되면 false를 반환하는 복합 함수입니다.<br/><br/>- OverlapCount > 0<br/>- DiscontinuityCount > 0<br/>- NonincreasingCount > 0<br/>- UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- UnexpectedBitrate == True

**라이브 보관**

자산|값|예제/참고 사항
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
타임 스탬프|타임 스탬프|Azure 테이블에서의 자동 타임스탬프 2016-09-09T22:43:42.241Z
Type|Type|보관
이름|이름|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|서비스 ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|프로그램 URL|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|트랙의 이름|audio_1
TrackType|트랙 유형|오디오/비디오
CustomAttribute|동일한 이름 및 비트 전송률을 갖는 다른 트랙 간을 구분하는 16진수 문자열(다중 카메라 각도)|
Bitrate|트랙 비트 전송률|785000
Healthy|FragmentDiscardedCount == 0 && ArchiveAcquisitionError == False인 경우 True|True(이러한 두 값은 메트릭으로 표시되지 않고 소스 이벤트에 있음)<br/><br/>Healthy는 다음 조건 중 하나라도 충족되면 false를 반환하는 복합 함수입니다.<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>일반 질문과 답변

### <a name="how-to-consume-metrics-data"></a>메트릭 데이터를 사용하는 방법

메트릭 데이터는 고객의 저장소 계정의 여러 Azure 테이블에 저장됩니다. 이 데이터는 다음과 같은 도구로 사용할 수 있습니다.

- AMS SDK
- Microsoft Azure Storage 탐색기(쉼표로 구분된 값 형식으로 내보내고 Excel에서 처리되도록 지원)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>평균 대역폭 소비량을 확인하는 방법

평균 대역폭 사용량은 특정 기간 동안의 BytesSent 평균입니다.

### <a name="how-to-define-streaming-unit-count"></a>스트리밍 단위 수를 정의하는 방법

스트리밍 단위 수는 서비스 스트리밍 엔드포인트의 최대 처리량을 한 스트리밍 엔드포인트의 최대 처리량으로 나누어 정의할 수 있습니다. 한 스트리밍 엔드포인트의 최대 사용 가능한 처리량은 160Mbps입니다.
예를 들어 고객 서비스의 최대 처리량을 40MBps(특정 기간 동안의 BytesSent 최대값)로 가정합니다. 그러면 스트리밍 단위 수는 (40MBps)*(8비트/바이트)/(160Mbps) = 2개의 스트리밍 단위가 됩니다.

### <a name="how-to-find-average-requestssecond"></a>평균 요청 수/초를 확인하는 방법

평균 요청 수/초를 확인하려면 특정 기간 동안의 평균 요청 수(RequestCount)를 계산합니다.

### <a name="how-to-define-channel-health"></a>채널 상태를 정의하는 방법

채널 상태는 다음 조건 중 하나가 충족될 때 false가 되도록 복합 부울 함수로 정의될 수 있습니다.

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>불연속성을 검색하는 방법

불연속성을 검색하려면 DiscontinuityCount > 0인 모든 채널 데이터 항목을 찾습니다. 해당 ObservedTime 타임스탬프는 불연속성이 발생한 시간을 나타냅니다.

### <a name="how-to-detect-timestamp-overlaps"></a>타임스탬프 겹침을 검색하는 방법

타임스탬프 겹침을 검색하려면 OverlapCount > 0인 모든 채널 데이터 항목을 찾습니다. 해당 ObservedTime 타임스탬프는 타임스탬프 겹침이 발생한 시간을 나타냅니다.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>스트리밍 요청 실패 및 이유를 찾는 방법

스트리밍 요청 실패 및 이유를 찾으려면 ResultCode가 S_OK가 아닌 모든 스트리밍 엔드포인트 데이터 항목을 찾습니다. 해당 StatusCode 필드는 요청 실패의 이유를 나타냅니다.

### <a name="how-to-consume-data-with-external-tools"></a>외부 도구에서 데이터를 사용하는 방법

원격 분석 데이터는 다음 도구로 처리하고 시각화할 수 있습니다.

- PowerBI
- Application Insights
- Azure Monitor(이전의 Shoebox)
- AMS 라이브 대시보드
- Azure Portal(릴리스 보류 중)

### <a name="how-to-manage-data-retention"></a>데이터 보존을 관리하는 방법

원격 분석 시스템에서는 데이터 보존 관리 또는 이전 레코드의 자동 삭제를 제공하지 않습니다. 따라서 저장소 테이블에서 이전 레코드를 관리하고 삭제해야 합니다. 해당 방법을 보려면 저장소 SDK를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
