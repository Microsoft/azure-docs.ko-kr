---
title: 자습서 - Azure App Service 웹앱에 Azure CDN 추가 | Microsoft Docs
description: 이 자습서에서는 CDN(Content Delivery Network)가 Azure App Service 웹앱에 추가되어 전 세계의 고객에게 가까운 서버에서 정적 파일을 캐시하고 제공합니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 470324799cd157c8b33311e1cae8b5b698433e1f
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079912"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>자습서: Azure App Service 웹앱에 Azure CDN 추가

이 자습서에서는 [Azure CDN(Content Delivery Network)](cdn-overview.md)을 [Azure App Service의 웹앱](../app-service/overview.md)에 추가하는 방법을 보여줍니다. 웹앱은 웹 애플리케이션, REST API 및 모바일 백 엔드를 호스팅하는 서비스입니다. 

사용하게 될 샘플 정적 HTML 사이트의 홈 페이지는 다음과 같습니다.

![샘플 앱 홈 페이지](media/cdn-add-to-web-app/sample-app-home-page.png)

학습할 내용:

> [!div class="checklist"]
> * CDN 엔드포인트 만들기
> * 캐시된 자산 새로 고침
> * 쿼리 문자열을 사용하여 캐시된 버전 제어


## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

- [Git 설치](https://git-scm.com/)
- [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>웹앱 만들기

사용할 웹앱을 만들려면 **앱 찾아보기** 단계를 통해 [정적 HTML 빠른 시작](../app-service/quickstart-html.md)을 수행합니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저를 열고 [Azure Portal](https://portal.azure.com)로 이동합니다.

### <a name="dynamic-site-acceleration-optimization"></a>동적 사이트 가속 최적화
DSA(동적 사이트 가속)에 대한 CDN 엔드포인트를 최적화하려는 경우 [CDN 포털](cdn-create-new-endpoint.md)을 사용하여 프로필 및 엔드포인트를 만들어야 합니다. [DSA(동적 사이트 가속) 최적화](cdn-dynamic-site-acceleration.md)를 사용하여 동적 콘텐츠가 포함된 웹 페이지의 성능이 크게 향상되었습니다. CDN 포털에서 DSA에 대한 CDN 엔드포인트를 최적화하는 방법에 대한 지침은 [동적 파일의 전송을 가속화하도록 CDN 엔드포인트 구성](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files)을 참조하세요. 그렇지 않으면 새 엔드포인트를 최적화하지 않으려는 경우 다음 섹션의 단계를 수행하여 만들려는 웹앱 포털을 사용할 수 있습니다. **Verizon의 Azure CDN** 프로필의 경우 CDN 엔드포인트를 만든 후에 최적화를 변경할 수 없습니다.

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN 프로필 및 엔드포인트 만들기

왼쪽 탐색 영역에서 **App Services**를 선택한 다음 [정적 HTML 빠른 시작](../app-service/quickstart-html.md)에서 만든 앱을 선택합니다.

![포털에서 App Service 앱 선택](media/cdn-add-to-web-app/portal-select-app-services.png)

**App Service** 페이지의 **설정** 섹션에서 **네트워킹 > 앱에서 Azure CDN 구성**을 선택합니다.

![포털에서 CDN 선택](media/cdn-add-to-web-app/portal-select-cdn.png)

**Azure Content Delivery Network** 페이지에서 테이블에 지정된 대로 **새 엔드포인트** 설정을 제공합니다.

![포털에서 프로필 및 엔드포인트 만들기](media/cdn-add-to-web-app/portal-new-endpoint.png)

| 설정 | 제안 값 | Description |
| ------- | --------------- | ----------- |
| **CDN 프로필** | myCDNProfile | CDN 프로필은 동일한 가격 책정 계층을 가진 CDN 엔드포인트의 컬렉션입니다. |
| **가격 책정 계층** | Standard Akamai | [가격 책정 계층](cdn-features.md)은 공급자 및 사용 가능한 기능을 지정합니다. 이 자습서에서는 *Standard Akamai*를 사용합니다. |
| **CDN 엔드포인트 이름** | azureedge.net 도메인에서 고유한 이름 | 도메인 *&lt;endpointname&gt;* .azureedge.net에서 캐시된 리소스에 액세스합니다.

**만들기**를 선택하여 CDN 프로필을 만듭니다.

Azure에서는 프로필 및 엔드포인트를 만듭니다. 새 엔드포인트는 **Endpoints** 목록에서 표시되고 프로비전될 때 상태가 **Running**입니다.

![목록의 새 엔드포인트](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>CDN 엔드포인트 테스트

 등록이 전파되는 등록에 시간이 걸리기 때문에, 엔드포인트를 즉시 사용할 수는 없습니다. 
   - **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
   - **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
   - **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 90분 이내에 전파가 완료됩니다. 

샘플 앱에는 *index.html* 파일 및 다른 정적 자산을 포함하는 *css*, *img* 및 *js* 폴더가 있습니다. 이러한 파일에 대한 콘텐츠 경로는 CDN 엔드포인트와 동일합니다. 예를 들어 다음 URL은 모두 *css* 폴더의 *bootstrap.css* 파일에 액세스합니다.

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

브라우저를 다음 URL로 이동합니다.

```
http://<endpointname>.azureedge.net/index.html
```

![CDN에서 제공되는 샘플 앱 홈 페이지](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Azure 웹앱에서 이전에 실행한 것과 동일한 페이지가 표시됩니다. Azure CDN에서 원본 웹앱의 자산을 검색하고 CDN 엔드포인트에서 제공하고 있습니다.

이 페이지가 CDN에서 캐시된다는 것을 보장하기 위해 페이지를 새로 고칩니다. 동일한 자산에 대한 두 개의 요청에서 CDN은 요청된 콘텐츠를 캐시해야 합니다.

Azure CDN 프로필 및 엔드포인트를 만드는 방법에 대한 자세한 내용은 [Azure CDN 시작](cdn-create-new-endpoint.md)을 참조하세요.

## <a name="purge-the-cdn"></a>CDN 제거

CDN은 TTL(time-to-live) 구성을 기반으로 원본 웹앱의 자원을 주기적으로 새로 고칩니다. 기본 TTL은 7일입니다.

예를 들어 때때로 웹앱에 업데이트된 콘텐츠를 배포할 때 TTL이 만료되기 전에 CDN을 새로 고쳐야 합니다. 새로 고침을 트리거하기 위해 CDN 리소스를 수동으로 제거합니다. 

자습서의 이 섹션에서는 웹앱에 대한 변경 내용을 배포하고 CDN을 삭제하여 캐시를 새로 고치기 위해 CDN을 트리거합니다.

### <a name="deploy-a-change-to-the-web-app"></a>웹앱에 대한 변경 내용 배포

다음 예제와 같이 *index.html* 파일을 열고 *- V2*를 H1 제목에 추가합니다. 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

변경 내용을 커밋하고 웹앱에 배포합니다.

```bash
git commit -am "version 2"
git push azure master
```

배포가 완료되면 웹앱 URL을 찾아 변경 사항을 확인합니다.

```
http://<appname>.azurewebsites.net/index.html
```

![웹앱의 제목에서 V2](media/cdn-add-to-web-app/v2-in-web-app-title.png)

홈 페이지의 CDN 엔드포인트 URL로 이동한다면 CDN에서 캐시된 버전이 아직 만료되지 않았기 때문에 변경 내용을 표시하지 않습니다. 

```
http://<endpointname>.azureedge.net/index.html
```

![CDN의 제목에서 V2 없음](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>포털에서 CDN 제거

CDN을 트리거하여 캐시된 버전을 업데이트하려면 CDN을 제거합니다.

포털 왼쪽 탐색 영역에서 **리소스 그룹**을 선택한 다음 웹앱(myResourceGroup)에 만든 리소스 그룹을 선택합니다.

![리소스 그룹 선택](media/cdn-add-to-web-app/portal-select-group.png)

리소스의 목록에서 CDN 엔드포인트를 선택합니다.

![엔드포인트 선택](media/cdn-add-to-web-app/portal-select-endpoint.png)

**Endpoint** 페이지의 맨 위에서 **제거**를 선택합니다.

![제거 선택](media/cdn-add-to-web-app/portal-select-purge.png)

제거하려는 콘텐츠 경로를 입력합니다. 전체 파일 경로를 전달하여 개별 파일을 제거하거나 경로 세그먼트를 전달하여 폴더에서 모든 콘텐츠를 제거하고 새로 고칠 수 있습니다. *index.html*을 변경했으므로 해당 경로 중 하나에 있는지 확인합니다.

페이지 맨 아래에서 **제거**를 선택합니다.

![페이지 제거](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>CDN이 업데이트되었는지 확인

제거 요청 처리가 완료될 때까지 일반적으로 몇 분 정도를 기다립니다. 현재 상태를 보려면 페이지 맨 아래에 있는 벨 아이콘을 선택합니다. 

![제거 알림](media/cdn-add-to-web-app/portal-purge-notification.png)

*index.html*의 CDN 엔드포인트 URL을 탐색할 때 홈 페이지의 제목에 추가한 *V2*가 표시되는 데, 이는 CDN 캐시가 새로 고쳐졌음을 나타냅니다.

```
http://<endpointname>.azureedge.net/index.html
```

![CDN의 제목에서 V2](media/cdn-add-to-web-app/v2-in-cdn-title.png)

자세한 내용은 [Azure CDN 엔드포인트 제거](../cdn/cdn-purge-endpoint.md)를 참조하세요. 

## <a name="use-query-strings-to-version-content"></a>버전 콘텐츠에 쿼리 문자열 사용

Azure CDN은 다음과 같은 캐싱 동작 옵션을 제공합니다.

* 쿼리 문자열 무시
* 쿼리 문자열에 대한 캐싱 우회
* 모든 고유한 URL 캐시 

첫 번째 옵션이 기본값입니다. 즉 액세스하는 URL의 쿼리 문자열에 관계없이 캐시된 자산의 버전이 하나뿐임을 나타냅니다. 

자습서의 이 섹션에서는 캐싱 동작을 변경하여 고유한 URL을 캐시합니다.

### <a name="change-the-cache-behavior"></a>캐시 동작 변경

Azure Portal의 **CDN 엔드포인트** 페이지에서 **캐시**를 선택합니다.

**쿼리 문자열 캐싱 동작** 드롭다운 목록에서 **모든 고유한 URL 캐시**를 선택합니다.

**저장**을 선택합니다.

![쿼리 문자열 캐싱 동작 선택](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>고유 URL을 별도로 캐시했는지 확인

브라우저의 CDN 엔드포인트에서 홈 페이지로 이동하고, 쿼리 문자열을 포함합니다. 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN은 제목에 *V2*를 포함하는 현재 웹앱 콘텐츠를 반환합니다. 

이 페이지가 CDN에서 캐시된다는 것을 보장하기 위해 페이지를 새로 고칩니다. 

*index.html*을 열고 *V2*를 *V3*로 변경한 다음, 변경 사항을 배포합니다. 

```bash
git commit -am "version 3"
git push azure master
```

브라우저에서 `q=2`과 같은 새 쿼리 문자열이 있는 CDN 엔드포인트 URL로 이동합니다. Azure CDN은 현재 *index.html* 파일을 가져오고 *V3*를 표시합니다. 그러나 `q=1` 쿼리 문자열을 사용하여 CDN 엔드포인트로 이동하는 경우 *V2*를 참조하세요.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![CDN에서 제목에 V3, 쿼리 문자열 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![CDN에서 제목에 V2, 쿼리 문자열 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

이 출력에서는 각 쿼리 문자열이 다르게 처리됨을 보여 줍니다.

* 이전에는 q=1이 사용되어 캐시된 내용(V2)을 반환합니다.
* q=2는 새로운 쿼리 문자열이므로 최신 웹앱 내용(V3)을 검색하여 반환합니다.

자세한 내용은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](../cdn/cdn-query-string.md)를 참조하세요.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * CDN 엔드포인트 만들기
> * 캐시된 자산 새로 고침
> * 쿼리 문자열을 사용하여 캐시된 버전 제어

다음 문서에서 CDN 성능을 최적화하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Azure CDN 엔드포인트에 사용자 지정 도메인 추가](cdn-map-content-to-custom-domain.md)


