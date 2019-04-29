---
title: 서비스 모듈-Azure Maps를 사용 하 여 | Microsoft Docs
description: Azure Maps 서비스 모듈을 사용 하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: f3650d4db06a763308939e9fb1a98fddb0eaa04a
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738779"
---
# <a name="use-the-azure-maps-services-module"></a>Azure Maps 서비스 모듈을 사용

Azure Maps 웹 SDK는 제공 된 *services 모듈*합니다. 이 모듈은 손쉽게 JavaScript 또는 TypeScript를 사용 하 여 웹 또는 Node.js 응용 프로그램에서 Azure Maps REST 서비스를 사용 하는 도우미 라이브러리입니다.

## <a name="use-the-services-module-in-a-webpage"></a>웹 페이지에서 서비스 모듈을 사용 합니다.

1. 새 HTML 파일 만들기
1. Azure Maps 서비스 모듈을 로드 합니다. 두 가지 방법 중 하나에서 로드할 수 있습니다.
    - Azure Maps 서비스 모듈의 전역적으로 호스팅되는 Azure Content Delivery Network 버전을 사용 합니다. 에 대 한 스크립트 참조를 추가 합니다 `<head>` 파일의 요소:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
        ```

    - 사용 하 여 로컬로 또는 Azure 매핑합니다 웹 SDK 소스 코드를 로드 합니다 [azure-맵-rest](https://www.npmjs.com/package/azure-maps-rest) npm 패키지를 선택한 후 앱과 함께 호스트할 합니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 이 명령을 사용 합니다.
    
        > **npm install azure-maps-rest**
    
        그런 다음에 대 한 스크립트 참조를 추가 합니다 `<head>` 파일의 요소:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. 인증 파이프라인을 만듭니다. 서비스 URL 클라이언트 끝점을 초기화 하기 전에 파이프라인을 만들어야 합니다. 사용자 고유의 Azure Maps 계정 키 또는 Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 Azure Maps 검색 서비스 클라이언트를 인증 합니다. 이 예제에서는 검색 서비스 URL 클라이언트가 만들어집니다. 

    인증에 대 한 구독 키를 사용 하면:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    인증 Azure AD를 사용 하는 경우:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
    try {
      console.log("Renewing token");
      var token = await getAadToken();
      tokenCredential.token = token;
      tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
    } catch (error) {
      console.log("Caught error when renewing token");
      clearTimeout(tokenRenewalTimer);
      throw error;
    }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
    retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        // Use the signed-in auth context to get a token.
        return new Promise((resolve, reject) => {
            // The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        // Decode the JSON Web Token (JWT) to get the expiration time stamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        // Return the milliseconds remaining until the token must be renewed.
        // Reduce the time until renewal by 5 minutes to avoid using an expired token.
        // The exp property is the time stamp of the expiration, in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    자세한 내용은 [Azure Maps를 사용 하 여 인증](azure-maps-authentication.md)합니다.

1. 다음 코드는 새로 만든된 Azure Search 서비스 URL 클라이언트 geocode 주소를 사용합니다. "1 Microsoft Way, Redmond, WA". 코드를 사용 하는 `searchAddress` 함수 및 페이지의 본문에는 테이블로 결과 표시 합니다.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];

      // Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');

      // Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
          response.results[i].address.freeformAddress, 
          '</td><td>', 
          response.results[i].position.lat,
          '</td><td>', 
          response.results[i].position.lon,
          '</td></tr>');
      }

      html.push('</table>');

      // Add the resulting HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    코드 샘플을 실행 하 고 전체는 다음과 같습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="서비스 모듈을 사용 하 여" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>Services 모듈을 사용 하 여</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

서비스 모듈을 사용 하는 추가 코드 샘플, 이러한 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [지도에 검색 결과 표시](./map-search-location.md)

> [!div class="nextstepaction"]
> [좌표에서 정보 가져오기](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](./map-route.md)