---
title: 포털에서 HTTP 요청 및 응답 헤더 재작성-Azure 애플리케이션 게이트웨이
description: Azure Portal를 사용 하 여 게이트웨이를 통해 전달 되는 요청 및 응답에서 HTTP 헤더를 다시 작성 하도록 Azure 애플리케이션 게이트웨이를 구성 하는 방법을 알아봅니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012852"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Azure 애플리케이션 게이트웨이를 사용 하 여 HTTP 요청 및 응답 헤더 재작성-Azure Portal

이 문서에서는 Azure Portal를 사용 하 여 요청 및 응답에서 HTTP 헤더를 다시 작성 하도록 [Application Gateway V2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 인스턴스를 구성 하는 방법을 설명 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료 하려면 Application Gateway v2 SKU 인스턴스가 있어야 합니다. 헤더 재작성은 v1 SKU에서 지원 되지 않습니다. V2 SKU가 없는 경우 시작 하기 전에 [Application Gateway V2 sku](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) 인스턴스를 만듭니다.

## <a name="create-required-objects"></a>필수 개체 만들기

HTTP 헤더 재작성을 구성 하려면 다음 단계를 완료 해야 합니다.

1. HTTP 헤더 재작성에 필요한 개체를 만듭니다.

   - **다시 작성 작업**: 재작성 하려는 요청 및 요청 헤더 필드와 헤더의 새 값을 지정 하는 데 사용 됩니다. 재작성 작업과 하나 이상의 재작성 조건을 연결할 수 있습니다.

   - **다시 쓰기 조건**: 선택적 구성입니다. 다시 쓰기 조건은 HTTP 요청 및 응답의 콘텐츠를 평가 합니다. HTTP (S) 요청 또는 응답이 재작성 조건과 일치 하면 재작성 작업이 수행 됩니다.

     작업에 둘 이상의 조건을 연결 하는 경우 모든 조건이 충족 되는 경우에만 작업이 수행 됩니다. 즉, 작업은 논리적 AND 연산입니다.

   - **재작성 규칙**: 여러 재작성 작업/재작성 조건 조합을 포함 합니다.

   - **규칙 순서**: 재작성 규칙이 실행 되는 순서를 결정 하는 데 도움이 됩니다. 이 구성은 재작성 집합에 다시 쓰기 규칙이 여러 개 있는 경우에 유용 합니다. 규칙 시퀀스 값이 낮은 재작성 규칙이 먼저 실행 됩니다. 두 재작성 규칙에 동일한 규칙 시퀀스 값을 할당 하는 경우 실행 순서는 비결 정적입니다.

   - **재작성 집합**: 요청 라우팅 규칙과 연결 되는 재작성 규칙을 여러 개 포함 합니다.

2. 다시 쓰기 집합을 라우팅 규칙에 연결 합니다. 재작성 구성은 라우팅 규칙을 통해 원본 수신기에 연결 됩니다. 기본 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 원본 수신기와 연결 되며 전역 헤더 재작성입니다. 경로 기반 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 URL 경로 맵에 정의 됩니다. 이 경우 사이트의 특정 경로 영역에만 적용 됩니다.

여러 HTTP 헤더 재작성 집합을 만들고 각 재작성 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에 재작성 집합을 하나만 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

## <a name="configure-header-rewrite"></a>헤더 재작성 구성

이 예제에서는 백 엔드 응용 프로그램에서 보낸 HTTP 응답의 위치 헤더를 다시 작성 하 여 리디렉션 URL을 수정 합니다.

1. **모든 리소스**를 선택 하 고 응용 프로그램 게이트웨이를 선택 합니다.

2. 왼쪽 창에서 다시 **쓰기** 를 선택 합니다.

3. **재작성 집합**선택:

   ![재작성 집합 추가](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 재작성 집합의 이름을 제공 하 고 라우팅 규칙에 연결 합니다.

   - **이름** 상자에 재작성 집합의 이름을 입력 합니다.
   - **연결 된 라우팅 규칙** 목록에 나열 된 규칙을 하나 이상 선택 합니다. 다른 재작성 집합과 연결 되지 않은 규칙만 선택할 수 있습니다. 다른 재작성 집합과 이미 연결 된 규칙은 흐리게 표시 됩니다.
   - **다음**을 선택합니다.
   
     ![이름 및 연결 추가](media/rewrite-http-headers-portal/name-and-association.png)

5. 재작성 규칙을 만듭니다.

   - **재작성 규칙 추가**를 선택 합니다.

     ![재작성 규칙 추가](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 재작성 규칙 **이름** 상자에 재작성 규칙의 이름을 입력 합니다. **규칙 시퀀스** 상자에 숫자를 입력 합니다.

     ![재작성 규칙 이름 추가](media/rewrite-http-headers-portal/rule-name.png)

6. 이 예제에서는 azurewebsites.net에 대한 참조를 포함 하는 경우에만 위치 헤더를 다시 작성 합니다. 이렇게 하려면 응답의 location 헤더에 azurewebsites.net가 포함 되어 있는지 여부를 평가 하는 조건을 추가 합니다.

   - **조건 추가** 를 선택한 다음, **If** 명령이 포함 된 상자를 선택 하 여 확장 합니다.

     ![조건 추가](media/rewrite-http-headers-portal/add-condition.png)

   - **확인할 변수 형식** 목록에서 **HTTP 헤더**를 선택 합니다.

   - **헤더 형식** 목록에서 **응답**을 선택 합니다.

   - 이 예제에서는 공용 헤더 인 location 헤더를 평가 하 고 **헤더 이름**아래에서 **공용 헤더** 를 선택 합니다.

   - **공용 헤더** 목록에서 **위치**를 선택 합니다.

   - **대/소문자 구분**에서 **아니요**를 선택 합니다.

   - **연산자** 목록에서 **같음 (=)** 을 선택 합니다.

   - 정규식 패턴을 입력 합니다. 이 예제에서는 `(https?):\/\/.*azurewebsites\.net(.*)$`패턴을 사용 합니다.

   - **확인**을 선택합니다.

     ![If 조건 구성](media/rewrite-http-headers-portal/condition.png)

7. 위치 헤더를 다시 작성 하는 작업을 추가 합니다.

   - **작업 유형** 목록에서 **설정**을 선택 합니다.

   - **헤더 형식** 목록에서 **응답**을 선택 합니다.

   - **헤더 이름**아래에서 **공용 헤더**를 선택 합니다.

   - **공용 헤더** 목록에서 **위치**를 선택 합니다.

   - 헤더 값을 입력 합니다. 이 예제에서는 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`를 헤더 값으로 사용 합니다. 이 값은 location 헤더에서 *azurewebsites.net* 을 *contoso.com* 로 바꿉니다.

   - **확인**을 선택합니다.

     ![작업 추가](media/rewrite-http-headers-portal/action.png)

8. **만들기** 를 선택 하 여 재작성 집합을 만듭니다.

   ![만들기 선택](media/rewrite-http-headers-portal/create.png)

9. 재작성 집합 뷰가 열립니다. 만든 재작성 집합이 재작성 집합 목록에 있는지 확인 합니다.

   ![Set 뷰 다시 작성](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례를 설정 하는 방법에 대한 자세한 내용은 [common header 재작성 시나리오](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)를 참조 하십시오.