---
title: HTTP 및 HTTPS 간의 리디렉션으로 Azure portal을 사용 하는 첫 번째 관문 만들기
description: Azure portal을 사용 하 여 HTTPS로 리디렉션된 트래픽으로 http에서를 사용 하 여 프런트 도어를 만드는 방법에 알아봅니다.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: a07b19c49630cc925e719aaa1d46476a1edc58f5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332901"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>HTTP 및 HTTPS 간의 리디렉션으로 Azure portal을 사용 하는 첫 번째 관문 만들기

Azure portal을 사용 하 여 만들기는 [프런트 도어](front-door-overview.md) SSL 종료를 위한 인증서를 사용 하 여 합니다. HTTP 트래픽을 HTTPS로 리디렉션하는 라우팅 규칙 사용 됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 기존 웹 앱 리소스를 사용 하 여 프런트 도어 만들기
> * SSL 인증서를 사용 하 여 사용자 지정 도메인 추가 
> * 사용자 지정 도메인에서 HTTPS 리디렉션 설정

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>기존 웹 앱 리소스를 사용 하 여 프런트 도어 만들기

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기**를 클릭합니다.
3. 검색할 **첫 번째 관문** 검색을 사용 하 여 막대형 및 리소스 종류를 찾은 후 클릭 **만들기**합니다.
4. 구독을 선택 하 고 기존 리소스 그룹을 사용 하 여 또는 새로 만듭니다. Note UI에서 묻는 위치가 리소스 그룹에 대 한 합니다. 첫 번째 관문 구성의 모든 배포 될 [Azure 프런트 도어 POP 위치](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service)합니다.

    ![새 프런트 도어에 대 한 기본 사항 구성](./media/front-door-url-redirect/front-door-create-basics.png)

5. 클릭 **다음** 구성 탭을 입력 합니다. 첫 번째 관문에 대 한 구성 기본 프런트 엔드 호스트 추가 백 엔드 백 엔드 풀에 추가한 다음 프런트 엔드 호스트에 대 한 라우팅 동작을 매핑할 라우팅 규칙을 만드는 세 가지 단계에서 발생 합니다. 

     ![첫 번째 관문 구성 디자이너](./media/front-door-url-redirect/front-door-designer.png)

6. 클릭는 ' **+** '에서 아이콘을 _프런트 엔드 호스트_ 프런트 엔드 호스트를 만들려면 첫 번째 관문 프로그램에 대 한 기본 프런트 엔드 호스트에 대 한 전역적으로 고유한 이름을 입력 (`\<**name**\>.azurefd.net`). 클릭 **추가** 다음 단계로 진행 합니다.

     ![프런트 엔드 호스트 추가](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 클릭는 ' **+** ' 아이콘을를 _백 엔드 풀_ 백 엔드 풀을 만듭니다. 백 엔드 풀에 대 한 이름을 입력 하 고 클릭 한 다음 '**백 엔드 추가**'.
8. 백 엔드 호스트 유형으로 _App service_합니다. 웹 앱 호스트 되는 구독을 선택 하 고 특정 웹 앱에 대 한 드롭다운 목록에서 선택한 **백 엔드 호스트 이름**합니다.
9. 클릭 **추가** 를 백 엔드를 저장 하 고 클릭 **추가** 다시 백 엔드 풀 구성을 저장 하도록 합니다.   ![백 엔드 풀에서 백 엔드 추가](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 클릭는 ' **+** ' 아이콘을 _라우팅 규칙_ 경로를 만들고 합니다. 설정한 후 'HttpToHttpsRedirect' 예를 들어 경로 대 한 이름을 제공 합니다 _수락 프로토콜_ 필드를 **'HTTP만 '** 합니다. 적절 한 했는지 _프런트 엔드 호스트_ 을 선택 합니다.  
11. 에 _경로 세부 정보_ 으로 설정를 _경로 유형_ 를 **리디렉션**, 했는지를 _리디렉션 형식_ 로 설정 되어  **(302)를 찾을 수** 하 고 _프로토콜을 리디렉션_ 로 설정 되어 **HTTPS만**합니다. 
12. HTTPS로 리디렉션 HTTP에 대 한 라우팅 규칙을 저장 하려면 추가 클릭 합니다.
     ![HTTP HTTPS 리디렉션 경로에 추가](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. HTTPS 트래픽을 처리 하는 것에 대 한 다른 라우팅 규칙을 추가 합니다. 클릭는 ' **+** ' 로그온 합니다 _라우팅 규칙_ 'DefaultForwardingRoute' 예를 들어 경로 대 한 이름을 제공 하 고 설정한 합니다 _프로토콜 허용_ 필드 하 **'HTTPS만 '** 합니다. 적절 한 했는지 _프런트 엔드 호스트_ 을 선택 합니다.
14. 경로 세부 정보 섹션에서 설정 합니다 _경로 유형_ 를 **전달**, 적합 한 백 엔드 풀 선택 되어 있는지 확인 및 _전달 프로토콜_ 로 설정 되어  **HTTPS만**합니다. 
15. 요청 전달에 대 한 라우팅 규칙을 저장 하려면 추가 클릭 합니다.
     ![HTTPS 트래픽에 대 한 정방향 경로 추가](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 클릭 **검토 + 만들기** 차례로 **만들기**프런트 도어 프로필을 만듭니다. 만든 리소스로 이동 합니다.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>사용자 지정 도메인에 첫 번째 관문을 추가 및 HTTPS를 사용 하도록 설정
다음 단계를 기존 프런트 도어 리소스에서 사용자 지정 도메인 추가 HTTP 및 HTTPS 간의 리디렉션으로를 사용 하도록 설정 하는 방법을 소개 합니다. 

### <a name="add-a-custom-domain"></a>사용자 지정 도메인 추가

이 예제에서는 CNAME 레코드를 추가 합니다 `www` 하위 도메인 (예를 들어 `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

하위 도메인에 첫 번째 관문의 기본 프런트 엔드 호스트에 매핑할 CNAME 레코드를 추가 (`<name>.azurefd.net`여기서 `<name>` 프런트 도어 프로필의 이름입니다).

에 대 한 합니다 `www.contoso.com` 도메인 예를 들어 이름을 매핑하는 CNAME 레코드를 추가 `www` 에 `<name>.azurefd.net`입니다.

CNAME을 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![첫 번째 관문을 사용자 지정 도메인 CNAME](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>등록 하면 첫 번째 관문에서 사용자 지정 도메인

1. 첫 번째 관문 디자이너 탭 클릭 ' +' 아이콘 프런트 엔드 호스트에 새 사용자 지정 도메인을 추가 하려면 섹션입니다. 
2. 사용자 지정 호스트 이름 필드에는 정규화 된 사용자 지정 DNS 이름을 입력 예제 `www.contosonews.com`합니다. 
3. 클릭 하 여 첫 번째 관문에서 도메인 CNAME 매핑 유효성이 검사 되 면 **추가** 사용자 지정 도메인을 추가 합니다.
4. 클릭 **저장할** 변경 내용을 제출 합니다.

![사용자 지정 도메인 메뉴](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>사용자 지정 도메인에서 HTTPS를 사용 하도록 설정

1. 추가 된 사용자 지정 도메인에 섹션에서 클릭 **사용자 지정 도메인 HTTPS**, 상태를 변경 **Enabled**합니다.
2. 그대로 둘 수 있습니다 합니다 **인증서 관리 유형** 로 설정 _관리 되는 첫 번째 관문_ 무료 인증서에 대 한 유지 관리, 관리 및 첫 번째 관문에서 autorotated 합니다. 또한 Azure Key Vault를 사용 하 여 저장 된 사용자 고유의 사용자 지정 SSL 인증서를 사용할 수 있습니다. 이 자습서는 첫 번째 관문의를 사용 하 여 인증서를 관리 하는 것을 가정 합니다.
![사용자 지정 도메인 HTTPS를 사용 하도록 설정](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 클릭할 **업데이트** 선택 항목을 저장 한 다음 클릭 **저장**합니다.
4. 클릭 **새로 고침** 후 몇 분이 고 사용자 지정 도메인 인증서 프로 비전의 진행 상태를 다시 클릭 합니다. 

> [!WARNING]
> 여러 사용자 지정 도메인을 걸릴 수 있습니다에 대 한 HTTPS를 사용 하도록 설정 시간 (분) 및 CNAME 프런트 도어 호스트에 직접 매핑되지 않은 경우 도메인 소유권 유효성 검사에 따라서도 다릅니다 `<name>.azurefd.net`합니다. 에 대해 자세히 알아보세요 [사용자 지정 도메인에 대 한 HTTPS를 사용 하는 방법](./front-door-custom-domain-https.md)합니다.

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>사용자 지정 도메인에 대 한 라우팅 규칙 구성

1. 이전에 만든 리디렉션 라우팅 규칙을 클릭 합니다.
2. 프런트 엔드 호스트에 대 한 드롭다운 목록을 클릭 하 고도 도메인에 대해이 경로 적용할 사용자 지정 도메인을 선택 합니다.
3. **업데이트**를 클릭합니다.
4. 즉, 사용자 지정 도메인을 추가 하 여 전달 경로 대 한 다른 라우팅 규칙에도 동일한 작업을 수행 합니다.
5. 클릭 **저장할** 여 변경 내용을 제출 합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
- 에 대해 자세히 알아보세요 [첫 번째 관문에서 URL 리디렉션](front-door-url-redirect.md)합니다.
