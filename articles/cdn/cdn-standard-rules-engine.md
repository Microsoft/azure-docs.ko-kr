---
title: 규칙 엔진을 사용 하 여 표준 Azure CDN에서 HTTPS 적용 | Microsoft Docs
description: Microsoft Standard Azure Content Delivery Network (Azure CDN)에 대한 규칙 엔진을 사용 하 여 특정 유형의 콘텐츠 배달 차단을 차단 하 고, 캐싱 정책을 정의 하 고, HTTP 헤더를 수정 하는 등 Azure CDN HTTP 요청을 처리 하는 방법을 사용자 지정할 수 있습니다. 이 문서에서는 사용자를 HTTPS로 리디렉션하는 규칙을 만드는 방법에 대해 알아봅니다.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171580"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진 설정

이 문서에서는 Azure Content Delivery Network (Azure CDN)에 대한 표준 규칙 엔진을 설정 하 고 사용 하는 방법을 설명 합니다.

## <a name="standard-rules-engine"></a>표준 규칙 엔진

Azure CDN에 대한 표준 규칙 엔진을 사용 하 여 HTTP 요청을 처리 하는 방법을 사용자 지정할 수 있습니다. 예를 들어 규칙 엔진을 사용 하 여 콘텐츠 배달을 적용 하 여 특정 프로토콜을 사용 하거나, 캐싱 정책을 정의 하거나, HTTP 헤더를 수정할 수 있습니다. 이 문서에서는 사용자를 자동으로 HTTPS로 리디렉션하는 규칙을 만드는 방법을 보여 줍니다. 

> [!NOTE]
> 이 문서에서 설명 하는 규칙 엔진은 Microsoft의 표준 Azure CDN에만 사용할 수 있습니다. 

## <a name="redirect-users-to-https"></a>사용자를 HTTPS로 리디렉션

1. Microsoft 프로필에서 Azure Content Delivery Network로 이동 합니다.

1. **CDN 프로필** 페이지에서 규칙을 만들 엔드포인트을 선택 합니다.
  
1. **규칙 엔진** 탭을 선택 합니다.
   
    **규칙 엔진** 창이 열리고 사용 가능한 전역 규칙의 목록이 표시 됩니다. 
   
    [![Azure CDN 새 규칙 페이지](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 여러 규칙이 나열 되는 순서는 규칙이 처리 되는 방식에 영향을 줍니다. 규칙에 지정 된 작업은 후속 규칙에 따라 덮어쓸 수 있습니다.
   >

1. **규칙 추가** 를 선택 하 고 규칙 이름을 입력 합니다. 규칙 이름은 문자로 시작 해야 하며 숫자 및 문자만 포함할 수 있습니다.

1. 규칙이 적용 되는 요청의 유형을 식별 하려면 일치 조건을 만듭니다.
    1. **조건 추가**를 선택한 다음 **프로토콜 일치 요청** 조건을 선택 합니다.
    1. **연산자**에 대해 **같음**을 선택 합니다.
    1. **값**에 대해 **HTTP**를 선택 합니다.
   
   [![Azure CDN 규칙 일치 조건](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > **조건 추가** 드롭다운 목록에서 여러 일치 조건에서 선택할 수 있습니다. 일치 조건의 자세한 목록은 [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)을 참조 하세요.
   
1. 일치 조건을 충족 하는 요청에 적용할 작업을 선택 합니다.
   1. **작업 추가**를 선택한 다음, **URL 리디렉션**을 선택 합니다.
   1. **형식**에 대해 **찾음 (302)** 을 선택 합니다.
   1. **프로토콜**에서 **HTTPS**를 선택합니다.
   1. 다른 모든 필드는 들어오는 값을 사용 하도록 비워 둡니다.
   
   [![Azure CDN 규칙 동작](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > **작업 추가** 드롭다운 목록에서 여러 작업을 선택할 수 있습니다. 자세한 작업 목록은 [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)을 참조 하세요.

6. **저장** 을 선택 하 여 새 규칙을 저장 합니다. 이제 규칙을 사용할 수 있습니다.
   
   > [!IMPORTANT]
   > 규칙을 변경 하면 Azure CDN를 통해 전파 되는 데 최대 15 분이 걸릴 수 있습니다.
   >
   

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
