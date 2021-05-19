---
title: Azure Marketplace에 제안 게시
description: 지정한 제품을 게시하는 API를 소개합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272048"
---
# <a name="publish-an-offer"></a>제안 게시

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되었으며, 계속 파트너 센터에서 작동합니다. 이러한 전환으로 인해 몇 가지 작은 사항이 변경되었습니다. [Cloud 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md)에 나열된 변경 사항을 검토하여 파트너 센터로 전환한 후에도 코드가 계속 작동하는지 확인하세요. CPP API는 파트너 센터로 전환하기 전에 이미 통합된 기존 제품에만 사용해야 합니다. 새 제품은 파트너 센터 제출 API를 사용해야 합니다.

지정한 제품의 게시 프로세스를 시작합니다. 이 호출은 장기 실행 작업입니다.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 매개 변수
--------------

|  **이름**      |    **설명**                               |  **데이터 형식** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | 게시자 식별자입니다(예: `contoso`).      |   String       |
|  offerId       | 제안 식별자입니다.                                 |   String       |
|  api-version   | 최신 버전의 API입니다.                        |   Date         |
|  |  |

## <a name="header"></a>헤더
------

|  **이름**        |    **값**          |
|  --------        |    ---------          |
|  콘텐츠 형식    | `application/json`    |
|  권한 부여   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>본문 예제
------------

### <a name="request"></a>요청

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>요청 본문 속성

|  **이름**               |   **설명**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 게시 작업의 진행률 알림을 받을 전자 메일 주소의 쉼표로 구분된 목록입니다. |
|  |  |

### <a name="response"></a>응답

#### <a name="migrated-offers"></a>마이그레이션된 제품

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>마이그레이션되지 않은 제품

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>응답 헤더

|  **이름**             |    **값**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Location    | 이 작업의 상태를 검색할 상대 경로     |
|  |  |

### <a name="response-status-codes"></a>응답 상태 코드

| ‘코드’ |  **설명**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - 요청이 성공적으로 수락되었습니다. 응답에는 시작된 작업을 추적하는 데 사용할 수 있는 위치가 포함됩니다. |
| 400   | `Bad/Malformed request` - 오류 응답 본문이 자세한 정보를 제공할 수 있습니다.                                                               |
| 422   | `Un-processable entity` - 게시할 엔터티의 유효성 검사가 실패했음을 나타냅니다.                                                        |
| 404   | `Not found` - 클라이언트가 지정한 엔터티가 없습니다.                                                                              |
|  |  |
