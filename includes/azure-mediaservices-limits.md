---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: b275a86f8fd35c43865fd920d1bfc9994a796a9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557161"
---
>[!NOTE]
>고정 되지 않는 리소스에 대 한 할당량 증가 요청 하려면 지원 티켓을 엽니다. 더 높은 한도 가져오려고 시도할 때 추가 Azure Media Services 계정을 만들지 마세요.

| 리소스 | 기본 제한 | 
| --- | --- | 
| 단일 구독의 azure Media Services 계정 | 25(고정) |
| 미디어 예약 단위 Media Services 계정당 |25(S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Media Services 계정 작업당 작업 | 50,000<sup>2</sup> |
| 작업당 연결된 태스크 | 30(고정) |
| Media Services 계정당 자산 | 1,000,000|
| 태스크당 자산 | 50 |
| 작업당 자산 | 100 |
| 한번에 자산과 연결된 고유 로케이터 | 5<sup>4</sup> |
| Media Services 계정당 라이브 채널 |5|
| 채널당 중지 상태인 프로그램  |50|
| 채널당 실행 상태인 프로그램  |3|
| 스트리밍 끝점은 중지 하거나 Media Services 계정당 실행|2|
| 스트리밍 엔드포인트당 스트리밍 단위 |10 |
| Storage 계정 | 1,000<sup>5</sup>(고정) |
| 정책 | 1,000,000<sup>6</sup> |
| 파일 크기| 일부 시나리오에서는 Media Services에서 처리를 위해 지원 되는 최대 파일 크기에서 제한이 됩니다 있습니다. <sup>7</sup> |

<sup>1</sup>종류를 변경 하는 경우 예를 들어, s1, s2에서 예약된 단위 최대 제한 다시 설정 됩니다.

<sup>2</sup>이 번호 큐에 대기 중인, 완료, 활성 및 취소 된 작업도 포함 됩니다. 삭제 된 작업 포함 되지 않습니다. 사용 하 여 이전 작업을 삭제할 수 있습니다 **IJob.Delete** 또는 **삭제** HTTP 요청입니다.

2017 년 4 월 1 일부 터 계정의 90 일이 지난 작업 레코드는 자동으로 삭제 된 연결 된 태스크 레코드와 함께 합니다. 자동으로 삭제할 레코드 총 수가 최고 할당량 미만인 경우에 발생 합니다. 작업 및 태스크 정보를 보관 하려면에 설명 된 코드를 사용 [Media Services.NET SDK를 사용 하 여 자산 관리](../articles/media-services/previous/media-services-dotnet-manage-entities.md)합니다.

<sup>3</sup>목록 작업 엔터티를 요청 하기 요청당 최대 1,000 개 작업이 반환 됩니다. 제출 된 모든 작업 한 추적을 위쪽을 사용 하거나에 설명 된 대로 쿼리를 건너뛸 [OData 시스템 쿼리 옵션](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))합니다.

<sup>4</sup>로케이터 사용자별 액세스 제어를 관리 하도록 설계 되지 않습니다. 개별 사용자에 게 서로 다른 액세스 권한을 부여 하려면 디지털 권한 관리 (DRM) 솔루션을 사용 합니다. 자세한 내용은 [Azure Media Services를 사용 하 여 콘텐츠 보호](../articles/media-services/previous/media-services-content-protection-overview.md)합니다.

<sup>5</sup>동일한 Azure 구독에서 저장소 계정 이어야 합니다.

<sup>6</sup>1,000,000 개의 정책 다른 Media Services 정책 제한 됩니다. 로케이터 정책 또는 contentkeyauthorizationpolicy의 경우에 대 한 예가입니다. 

>[!NOTE]
> 항상 동일한 날짜를 사용 하 고 액세스 권한, 하는 경우 동일한 정책 ID을 사용 합니다. 정보 및 예제를 참조 하세요 [Media Services.NET SDK를 사용 하 여 자산 관리](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)합니다.

<sup>7</sup>처리 서비스에서 미디어 프로세서 중 하나를 사용 하 여 Media Services에서 자산에 콘텐츠를 업로드 하는 경우 지원 되는 최대 파일 크기를 확인 합니다. 같은 미디어 인코더 표준 인코더 및 미디어 인코더 Premium 워크플로 또는 분석 엔진 Face Detector와 같은 자산에 포함 됩니다.

단일 blob에 대해 지원 되는 최대 크기는 현재 최대 5TB Azure Blob Storage에 있습니다. Media services 서비스에서 사용 되는 VM 크기에 따라 추가 제한이 적용 됩니다. 다음 표에서 미디어 예약 단위 S1, S2 및 S3에서 제한을 보여 줍니다. 원본 파일이 표에 정의 된 제한 보다 큰 경우 인코딩 작업이 실패 합니다. 4k 확인 원본을 긴 기간을 인코딩하면 필요한 성능을 얻기 위해 S3 미디어 예약 단위를 사용 해야 하 고 있습니다. S3 미디어 예약 단위에 대 한 260 10GB 제한을 보다 큰 4k 콘텐츠가 경우 문의 amshelp@microsoft.com 에 잠재적인 완화 방법을 시나리오를 지원 하도록 합니다.

| 미디어 예약 단위 유형 | 최대 입력된 크기 (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
