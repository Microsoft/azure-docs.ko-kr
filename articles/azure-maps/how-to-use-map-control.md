---
title: Azure Maps 맵 컨트롤을 사용하는 방법 | Microsoft Docs
description: Azure Maps 맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하는 방법을 알아봅니다.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9e3a442a3d6c420c548979327c193628efbee5aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769875"
---
# <a name="use-the-azure-maps-map-control"></a>Azure Maps 맵 컨트롤 사용

맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하면 맵 및 포함된 Azure Maps 기능을 웹 또는 모바일 애플리케이션에 렌더링할 수 있습니다.

## <a name="create-a-new-map-in-a-web-page"></a>웹 페이지에 새 맵 만들기

맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하여 웹 페이지에 지도를 포함할 수 있습니다.

1. 새 HTML 파일 만들기

2. Azure Maps 웹 SDK에서 로드합니다. 두 가지 옵션 중 하나를 사용하여 수행할 수 있습니다.

    a. 파일의 `<head>` 요소에 있는 스타일시트 및 스크립트 참조에 URL 엔드포인트를 추가하여 Azure Maps 웹 SDK의 글로벌 호스팅 CDN 버전을 사용합니다.

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. 아니면 [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) NPM 패키지를 사용하여 로컬에서 Azure Maps 웹 SDK 소스 코드를 로드하여 앱으로 호스트합니다. 이 패키지에는 TypeScript 정의도 포함됩니다.

    > npm install azure-maps-control

    그런 다음, Azure Maps 스타일시트에 대한 참조와 스크립트 소스 참조를 파일의 `<head>` 요소에 추가합니다.

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. 맵이 페이지 본문 전체를 채우도록 렌더링하려면 `<head>` 요소에 다음 `<style>` 요소를 추가합니다.

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. 페이지 본문에 `<div>` 요소를 추가하고 **myMap**이라는 `id`를 부여합니다.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. 맵 컨트롤을 초기화하려면 HTML 본문에 새 섹션을 정의하고 스크립트를 만듭니다. 자신의 Azure Maps 계정 키나 AAD(Azure Active Directory) 자격 증명으로 [인증 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)을 사용하여 맵을 인증합니다. 계정을 만들거나 키를 찾아야 하는 경우 [Azure Maps 계정과 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요. **언어** 옵션은 맵 레이블 및 컨트롤에 사용할 언어를 지정합니다. 지원되는 언어에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요. 인증에 구독 키를 사용하는 경우입니다.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    인증에 AAD(Azure Active Directory)를 사용하는 경우입니다.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    자세한 내용은 [Azure Maps를 사용 하 여 인증](azure-maps-authentication.md) 대 한 자세한 내용은 합니다.

6. 선택적으로 다음 메타 태그 요소를 페이지 머리글에 추가하는 것이 도움이 될 수 있습니다.

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. 전체 과정 HTML 파일에 표시 됩니다 다음 코드와 비슷합니다.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. 웹 브라우저에서 파일을 열고 렌더링된 맵을 봅니다. 다음 코드와 같아야 합니다.

    <iframe height="700" style="width: 100%;" scrolling="no" title="맵 컨트롤을 사용하는 방법" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>맵 컨트롤을 사용 하는 방법</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
    </iframe>

## <a name="next-steps"></a>다음 단계

맵을 만들고 상호 작용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

맵 스타일을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [지도 스타일 선택](choose-map-style.md)
