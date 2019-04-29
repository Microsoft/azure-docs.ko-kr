---
title: Azure Application Gateway-Azure portal 사용 하 여 HTTP 요청 및 응답 헤더를 다시 작성 | Microsoft Docs
description: 요청 및 게이트웨이 통해 전달 하는 응답의 HTTP 헤더를 다시 작성 하는 Azure Application Gateway를 구성 하려면 Azure portal을 사용 하는 방법 알아보기
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6afc07f98905469b06622e7829ec4a215b94845e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716219"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Azure Application Gateway-Azure portal 사용 하 여 HTTP 요청 및 응답 헤더를 다시 작성

이 문서에서는 구성 하려면 Azure portal을 사용 하는 방법을 보여 줍니다.는 [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 요청 및 응답의 HTTP 헤더를 다시 작성 합니다.

> [!IMPORTANT]
> 자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이 SKU는 현재 공개 미리 보기로 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

Application Gateway v2는 v1 SKU에 대 한 헤더 재작성 기능 이므로 SKU를 사용할 수 없습니다를 해야 합니다. V2 SKU가 없는 경우 만듭니다는 [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) 시작 하기 전에 합니다.

## <a name="what-is-required-to-rewrite-a-header"></a>헤더를 다시 작성 하는 데 필요한 항목은

HTTP 헤더 다시 쓰기를 구성 해야 합니다.

1. http 헤더를 다시 쓰는 데 필요한 새 개체를 만듭니다.

   - **작업을 다시 작성**: 요청 및 요청 헤더 필드를 다시 작성 하려는 원본 헤더를 다시 작성 해야 하는 새 값을 지정 하는 데 사용 합니다. 연결할 하나 더 많은 다시 쓰기 조건을 다시 작성 작업을 선택할 수 있습니다.

   - **조건을 다시 작성**: 선택적 구성 이며 재작성 조건에 추가 하는 경우 HTTP (S) 요청 및 응답의 콘텐츠는 계산 합니다. 재작성 조건과 사용 하 여 연결을 다시 작성 작업을 실행 하는 결정 재작성 조건을 사용 하 여 HTTP (S) 요청 또는 응답와 일치 하는지 여부에 따라 달라 집니다. 

     둘 이상의 조건을 사용 하 여 연결 된 경우 작업을 다음 작업을 실행할지 즉, 모든 조건이 충족 되는 경우에, 논리적 AND 연산을 수행 됩니다.

   - **재작성 규칙**: 여러 다시 쓰기 작업을 포함 하는 다시 쓰기 규칙-조건 조합을 다시 작성 합니다.

   - **규칙 순서**: 실행 되는 다양 한 다시 쓰기 규칙 순서를 확인할 수 있습니다. 다시 쓰기 집합의 다시 쓰기 규칙이 여러 개 있을 때 유용 합니다. 낮은 규칙 순서 값을 사용 하 여 다시 쓰기 규칙을 먼저 실행 됩니다. 두 다시 쓰기 규칙을 규칙 순서를 제공 하는 경우 실행 순서 비결 수 됩니다.

   - **집합을 다시 작성**: 요청 라우팅 규칙에 연결 될 여러 다시 쓰기 규칙을 포함 합니다.

2. 라우팅 규칙을 사용 하 여 설정 다시 쓰기를 연결 해야 합니다. 즉, 재작성 구성이 라우팅 규칙을 통해 원본 수신기에 연결 되었습니다. 기본 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 원본 수신기와 연결되어 글로벌 헤더 다시 쓰기가 됩니다. 경로 기반 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 따라서 사이트의 특정 경로 영역에만 적용됩니다.

여러 http 헤더 재작성 집합을 만들 수 있습니다 하 고 각 다시 쓰기 집합 리스너가 여러 개 적용할 수 있습니다. 그러나 특정 수신기로 다시 작성 하나에 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

## <a name="configure-header-rewrite"></a>헤더 다시 쓰기를 구성 합니다.

이 예제에서는 백 엔드 응용 프로그램에서 보낸 http 응답의 location 헤더를 다시 작성 하 여 리디렉션 URL을 수정 하겠습니다. 

1. 선택 **모든 리소스**, 한 다음 application gateway를 선택 합니다.

2. 선택 **다시 작성** 왼쪽된 메뉴에서.

3. 클릭할 **집합을 다시 작성 +** 합니다. 

   ![다시 쓰기 집합 추가](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 재작성 집합에는 이름을 제공 하 고 라우팅 규칙을 사용 하 여 연결 합니다.

   - 다시 쓰기에서 설정의 이름을 입력 합니다 **이름을** 텍스트 상자에 붙여넣습니다.
   - 에 나열 된 하나 이상의 규칙을 선택 합니다 **라우팅 규칙을 연결 된** 목록입니다. 만 다른 재작성 집합과 연결 되지 않은 이러한 규칙을 선택할 수 있습니다. 이미 다른 재작성 집합과 연결 하는 규칙 흐리게 표시 됩니다.
   - 다음을 클릭합니다.
   
     ![이름 및 연결 추가](media/rewrite-http-headers-portal/name-and-association.png)

5. 다시 쓰기 규칙을 만듭니다.

   - 클릭할 **+ 추가 재작성 규칙**.![ 다시 쓰기 규칙 추가](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - 다시 쓰기 규칙 이름 텍스트 상자에 다시 쓰기 규칙 이름을 입력 하 고 규칙 시퀀스를 제공 합니다.![규칙 이름 추가](media/rewrite-http-headers-portal/rule-name.png)

6. 이 예제에서는 "azurewebsites.net"에 대 한 참조를 포함 하는 경우에 위치 헤더를 다시 작성 됩니다. 이 위해 응답의 location 헤더 azurewebsites.net 포함 되는지 여부를 평가 하는 조건을 추가 합니다.

   - 클릭할 **+ 조건 추가** 포함 하는 섹션에서 클릭 하 고는 **하는 경우** 하십시오를 확장 하는 지침은![ 규칙 이름 추가](media/rewrite-http-headers-portal/add-condition.png)

   - 선택 **HTTP 헤더** 에서 합니다 **검사할 변수의 형식을** 드롭다운 합니다. 

   - 선택 **헤더 형식** 으로 **응답**합니다.

   - 이 예에서 선택 일반적인 헤더가 발생 하는 location 헤더를 평가 하는 것 이므로 **공용 헤더** 라디오 단추를 **헤더 이름**합니다.

   - 선택 **위치** 에서 합니다 **공용 헤더** 드롭다운 합니다.

   - 선택 **No** 으로 **대/소문자 구분** 설정 합니다.

   - 선택 **등호 (=)** 에서 합니다 **연산자** 드롭다운 합니다.

   - 정규식 패턴을 입력 합니다. 이 예에서는 사용 하 여 패턴 `(https?):\/\/.*azurewebsites\.net(.*)$` 합니다.

   - **확인**을 클릭합니다.

     ![위치 헤더를 수정 합니다.](media/rewrite-http-headers-portal/condition.png)

7. 위치 헤더를 다시 작성 작업을 추가 합니다.

   - 선택 **설정할** 으로 **동작 유형**합니다.

   - 선택 **응답** 으로 **헤더 형식**합니다.

   - 선택 **공용 헤더** 으로 **헤더 이름을**입니다.

   - 선택 **위치** 에서 합니다 **공용 헤더** 드롭다운 합니다.

   - 헤더 값을 입력 합니다. 이 예에서는 사용 하 여 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 헤더 값으로. 바뀝니다 *azurewebsites.net* 사용 하 여 *contoso.com* location 헤더에 있습니다.

   - **확인**을 클릭합니다.

     ![위치 헤더를 수정 합니다.](media/rewrite-http-headers-portal/action.png)

8. 클릭할 **만들기** 다시 쓰기를 만들려면 다음을 설정 합니다.

   ![위치 헤더를 수정 합니다.](media/rewrite-http-headers-portal/create.png)

9. 다시 쓰기 집합 보기로 이동 합니다. 위에서 만든 재작성 집합 재작성 세트의 목록에 있는지 확인 합니다.

   ![위치 헤더를 수정 합니다.](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>다음 단계

일반적인 몇 가지를 수행 하는 데 필요한 구성에 대 한 더 많은 사용 사례에 알아보려면 [공용 헤더 시나리오를 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)합니다.

   
