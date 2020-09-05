---
title: Azure CDN 끝점 다중 원본
description: Azure CDN 끝점의 여러 원본을 시작 합니다.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 8/20/2020
ms.author: allensu
ms.openlocfilehash: c7e6733079dbd867255e604f6f8d4459f647cc93
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870465"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN 끝점 다중 원본

다중 원본 지원은 가동 중지 시간을 제거 하 고 전역 중복성을 설정 합니다. 

Azure CDN 끝점 내에서 여러 원본을 선택 하 여 제공 되는 중복성은 각 원본의 상태를 검색 하 고 필요한 경우 장애 조치 (failover) 하 여 위험을 분산 합니다.

하나 이상의 원본 그룹을 설정 하 고 기본 원본 그룹을 선택 합니다. 각 원본 그룹은 유사한 작업을 수행할 수 있는 하나 이상의 원본 컬렉션입니다.

> [!NOTE]
> 현재이 기능은 Microsoft의 Azure CDN 에서만 사용할 수 있습니다. 

## <a name="create-the-origin-group"></a>원본 그룹 만들기

1. [Azure 포털](https://portal.azure.com)

2. Azure CDN 프로필을 선택 하 고 다중 원본에 대해 구성할 끝점을 선택 합니다.

3. 끝점 구성의 **설정** 아래에서 **원본** 을 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN 엔드포인트" border="true":::

4. 다중 원본을 사용 하도록 설정 하려면 원본 그룹이 하나 이상 필요 합니다. **원본 그룹 만들기**를 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="원본 설정" border="true":::

5. **원본 추가 그룹** 구성에서 다음 정보를 입력 하거나 선택 합니다.

   | 설정           | 값                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 원본 그룹 이름 | 원본 그룹의 이름을 입력 합니다.                                   |
   | 프로브 상태      | **사용**을 선택합니다. </br> Azure CDN은 전 세계 여러 점에서 상태 프로브를 실행 하 여 원본 상태를 확인 합니다. 추가 비용을 방지 하기 위해 현재 원본 그룹이 활성 상태가 아닌 경우에는 사용 하지 마세요.
   | 프로브 경로        | 상태를 확인 하는 데 사용 되는 원본의 경로입니다. |
   | 프로브 간격    | 프로브 간격으로 1, 2 또는 4 분을 선택 합니다.                        |
   | 프로브 프로토콜    | **HTTP** 또는 **HTTPS**를 선택 합니다.                                         |
   | 프로브 방법      | **헤드** 또는 **가져오기**를 선택 합니다.                                           |
   | 기본 원본 그룹 | 기본 원본 그룹으로 설정할 상자를 선택 합니다.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="원본 그룹 추가" border="true":::

6. **추가**를 선택합니다.

## <a name="add-multiple-origins"></a>여러 원본 추가

1. 끝점의 원본 설정에서 **+ 원본 만들기**를 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="원본 만들기" border="true":::

2. **원본 추가** 구성에서 다음 정보를 입력 하거나 선택 합니다.

   | 설정           | 값                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | 이름        | 원본 이름을 입력 합니다.        |
   | 원본 형식 | **저장소**, **클라우드 서비스**, **웹 앱**또는 **사용자 지정 원본을**선택 합니다.                                   |
   | 원본 호스트 이름        | 원본 호스트 이름을 선택 하거나 입력 합니다.  드롭다운은 이전 설정에서 지정한 형식의 사용 가능한 모든 원본을 나열 합니다. 원본 유형으로 **사용자 지정 원본** 을 선택한 경우 고객 원본 서버의 도메인을 입력 합니다. |
   | 원본 호스트 헤더    | 각 요청과 함께 보내려는 Azure CDN 호스트 헤더를 입력 하거나 기본값을 그대로 사용 합니다.                        |
   | HTTP 포트   | HTTP 포트를 입력 합니다.                                         |
   | HTTPS 포트     | HTTPS 포트를 입력 합니다.                                           |
   | 우선 순위    | 1에서 5 사이의 숫자를 입력 하십시오.       |
   | 무게      | 1에서 1000 사이의 숫자를 입력 하십시오.   |

    > [!NOTE]
    > 원본이 원본 그룹 내에 생성 되 면 우선 순위와 가중치를 가져와야 합니다. 원본 그룹에 원본이 하나만 있는 경우 기본 우선 순위와 가중치는 1로 설정 됩니다. 원본이 정상 상태 이면 트래픽이 가장 높은 우선 순위 원본으로 라우팅됩니다. 원본이 비정상으로 확인 되 면 연결이 우선 순위에 따라 다른 원본으로 전환 됩니다. 두 원본에 동일한 우선 순위가 있는 경우 트래픽은 원본에 대해 지정 된 가중치에 따라 배포 됩니다. 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="다른 원본 추가" border="true":::

3. **추가**를 선택합니다.

4. **원본 구성** 을 선택 하 여 모든 원본에 대 한 원본 경로를 설정 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="원본 경로 구성" border="true":::

5. **확인**을 선택합니다.

## <a name="configure-origins-and-origin-group-settings"></a>원본 및 원본 그룹 설정 구성

원본 및 원본 그룹이 여러 개 있는 경우에는 원본을 다른 그룹에 추가 하거나 제거할 수 있습니다. 동일한 그룹 내의 원본은 유사한 워크 로드를 처리 해야 합니다. 트래픽은 정상 상태, 우선 순위 및 가중치 값에 따라 이러한 원본에 배포 됩니다. 

1. Azure CDN 끝점의 원본 설정에서 구성 하려는 원본 그룹의 이름을 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="원본 및 원본 그룹 설정 구성" border="true":::

2. **업데이트 원본 그룹**에서 **+ 원본 선택**을 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="업데이트 원본 그룹" border="true":::

4. 풀 다운 상자에서 그룹에 추가할 원본을 선택 하 고 **확인**을 선택 합니다.

5. 원본이 그룹에 추가 되었는지 확인 하 고 **저장**을 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="그룹에 추가 된 추가 원본 확인" border="true":::

## <a name="remove-origin-from-origin-group"></a>원본 그룹에서 원본 제거

1. Azure CDN 끝점의 원본 설정에서 원본 그룹의 이름을 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="그룹에서 원점 제거" border="true":::

2. 원본 그룹에서 원본을 제거 하려면 원본 옆에 있는 휴지통 아이콘을 선택 하 고 **저장**을 선택 합니다.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="원본 그룹 업데이트 원본 삭제" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>규칙 엔진을 사용 하 여 원본 그룹 재정의

표준 규칙 엔진을 사용 하 여 트래픽이 다른 원본 그룹에 배포 되는 방식을 사용자 지정 합니다.

요청 URL을 기준으로 다른 그룹에 트래픽을 분산 합니다.

1. CDN 끝점의 **설정**에서 **규칙 엔진** 을 선택 합니다.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="규칙 엔진" border="true":::

2. **+ 규칙 추가**를 선택 합니다.

3. **이름**에 규칙의 이름을 입력 합니다.

4. **+ 조건**을 선택 하 고 **URL 경로**를 선택 합니다.

5. **연산자** 풀에서 **포함**을 선택 합니다.

6. **값**에 **/images**를 입력 합니다.

7. **+ 작업 추가**를 선택 하 고 **원본 그룹 재정의**를 선택 합니다.

8. **원본 그룹**의 풀 다운 상자에서 원본 그룹을 선택 합니다.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="규칙 엔진 조건" border="true":::

URL 경로에 **/이미지가**포함 된 경우 들어오는 모든 요청에 대해 요청은 작업 섹션의 원본 그룹에 할당 됩니다 **(myorigingroup)**. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 끝점 다중 원본 Azure CDN를 사용 하도록 설정 했습니다.

Azure CDN 및 이 문서에 언급된 다른 Azure 서비스에 대한 자세한 내용은 다음을 참조하세요.

* [Azure CDN](./cdn-overview.md)
* [Azure CDN 제품 기능 비교](./cdn-features.md)
