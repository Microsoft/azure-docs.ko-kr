---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 49150daa783280f47cb9401434ebfab8f6204090
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224341"
---
>[!NOTE]
>수정 되지 않은 리소스의 경우 할당량 증가를 요청 하는 지원 티켓을 엽니다. 더 높은 한도를 얻기 위해 추가 Azure Media Services 계정을 만들지 마세요.

| 리소스 | 기본 제한 | 
| --- | --- | 
| 단일 구독의 Azure Media Services 계정 | 25(고정) |
| Media Services 계정 당 미디어 예약 단위 |25(S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Media Services 계정 작업당 작업 | 50,000<sup>2</sup> |
| 작업당 연결된 태스크 | 30(고정) |
| Media Services 계정 당 자산 | 1,000,000|
| 태스크당 자산 | 50 |
| 작업당 자산 | 100 |
| 한번에 자산과 연결된 고유 로케이터 | 5<sup>4</sup> |
| Media Services 계정 당 라이브 채널 |5|
| 채널당 중지 상태인 프로그램 |50|
| 채널당 실행 상태인 프로그램 |3|
| Media Services 계정 마다 중지 또는 실행 되는 스트리밍 엔드포인트|2|
| 스트리밍 엔드포인트당 스트리밍 단위 |10 |
| Storage 계정 | 1,000<sup>5</sup>(고정) |
| 정책 | 1,000,000<sup>6</sup> |
| 파일 크기| 일부 시나리오에서는 Media Services 처리에 대해 지원 되는 최대 파일 크기에 제한이 있습니다. <sup>7</sup> |

<sup>1</sup> 예를 들어 s 2에서 s 1로 형식을 변경 하면 예약 된 최대 단위 제한이 다시 설정 됩니다.

<sup>2</sup> 이 수에는 대기 중, 완료, 활성 및 취소 된 작업이 포함 됩니다. 삭제 된 작업은 포함 되지 않습니다. **Ijob. delete** 또는 **delete** HTTP 요청을 사용 하 여 이전 작업을 삭제할 수 있습니다.

2017 년 4 월 1 일부 터, 계정에서 90 일 보다 오래 된 작업 레코드는 연결 된 태스크 레코드와 함께 자동으로 삭제 됩니다. 레코드의 총 수가 최대 할당량 보다 낮은 경우에도 자동 삭제가 발생 합니다. 작업 및 작업 정보를 보관 하려면 [Media Services .NET SDK를 사용 하 여 자산 관리](../articles/media-services/previous/media-services-dotnet-manage-entities.md)에 설명 된 코드를 사용 합니다.

<sup>3</sup> 작업 엔터티를 나열 하도록 요청 하면 요청당 최대 1000 작업이 반환 됩니다. 제출 된 모든 작업을 추적 하려면 [OData 시스템 쿼리 옵션](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))에 설명 된 대로 top 또는 skip 쿼리를 사용 합니다.

<sup>4</sup> 로케이터는 사용자별 액세스 제어를 관리 하도록 설계 되지 않았습니다. 개별 사용자에 게 서로 다른 액세스 권한을 부여 하려면 DRM (디지털 권한 관리) 솔루션을 사용 합니다. 자세한 내용은 [Azure Media Services를 사용 하 여 콘텐츠 보호](../articles/media-services/previous/media-services-content-protection-overview.md)를 참조 하세요.

<sup>5</sup> 저장소 계정은 동일한 Azure 구독에 속해야 합니다.

<sup>6</sup> Media Services 정책 마다 100만 정책 제한이 있습니다. 예를 들어 로케이터 정책 또는 ContentKeyAuthorizationPolicy를 사용할 수 있습니다. 

>[!NOTE]
> 항상 동일한 날짜와 액세스 권한을 사용 하는 경우 동일한 정책 ID를 사용 합니다. 자세한 내용과 예제는 [Media Services .NET SDK를 사용 하 여 자산 관리](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)를 참조 하세요.

<sup>7</sup> 단일 blob에 대해 지원 되는 최대 크기는 현재 Azure Blob Storage의 최대 5TB입니다. 서비스에서 사용 하는 VM 크기에 따라 Media Services에 추가 제한이 적용 됩니다. 크기 제한은 업로드 하는 파일 및 Media Services 처리 (인코딩 또는 분석)의 결과로 생성 되는 파일에도 적용 됩니다. 원본 파일이 260GB보다 크면 작업이 실패할 수 있습니다. 

다음 표에서는 미디어 예약 단위 S1, S2 및 S3의 제한을 보여 줍니다. 원본 파일이 테이블에 정의 된 제한 보다 큰 경우 인코딩 작업이 실패 합니다. 장시간의 4K 해상도 원본을 인코딩하는 경우 필요한 성능을 얻으려면 S3 미디어 예약 단위를 사용 해야 합니다. S3 미디어 예약 단위에서 260-GB 제한 보다 큰 4K 콘텐츠가 있는 경우 지원 티켓을 엽니다.

|미디어 예약 단위 유형   |최대 입력 크기 (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
