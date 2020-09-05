---
title: 몰입형 리더 Node.js 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 리더 API 기능을 추가합니다.
services: cognitive-services
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/20/2020
ms.author: pasta
ms.custom: devx-track-javascript
ms.openlocfilehash: 91adff9a8330defb6c2b9f1e915c46886649fed3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602527"
---
[몰입형 판독기](https://www.onenote.com/learningtools)는 읽기 이해도를 향상시키기 위해 검증된 기술을 구현하는 포괄적으로 설계된 도구입니다.

이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 리더 클라이언트 라이브러리를 사용하여 몰입형 리더를 통합합니다. 이 빠른 시작의 전체 작동 샘플은 [여기](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)에서 확인할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Active Directory 인증에 대해 구성된 몰입형 판독기 리소스입니다. [다음 지침](../../how-to-create-immersive-reader.md)에 따라 설정하세요. 환경 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.
* [Node.js](https://nodejs.org/) 및 [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/)와 같은 IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express에서 Node.js 웹앱 만들기

`express-generator` 도구로 Node.js를 만듭니다.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Yarn 종속성을 설치하고, 빠른 시작에서 나중에 사용하게 될 `request` 및 `dotenv` 종속성을 추가합니다.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>인증 설정

### <a name="configure-authentication-values"></a>인증 값 구성

프로젝트 루트에 _.env_라는 새 파일을 만듭니다. 몰입형 판독기 리소스를 만들 때 지정된 값을 제공하여 다음 코드를 붙여넣습니다.
따옴표 또는 "{" 및 "}" 문자를 포함하지 마세요.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

이 파일에는 공개되어서는 안 되는 비밀이 있으므로 이 파일을 소스 제어에 커밋하지 않아야 합니다.

이제 _app.js_를 열고 파일 맨 위에 다음을 추가합니다. 이렇게 하면 env 파일에서 정의한 속성이 Node에 환경 변수로 로드됩니다.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>토큰을 획득하도록 라우터 업데이트
_routes\index.js_ 파일을 열고 자동으로 생성된 코드를 다음 코드로 바꿉니다.

이 코드는 서비스 사용자 암호를 사용하여 Azure AD 인증 토큰을 획득하는 API 엔드포인트를 만듭니다. 하위 도메인도 검색합니다. 그런 다음, 토큰과 하위 도메인을 포함하는 개체를 반환합니다.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

권한 없는 사용자가 토큰을 획득하여 몰입형 판독기 서비스 및 청구에 사용하지 못하도록 **GetTokenAndSubdomain** 백 엔드 API 엔드포인트를 인증 형식(예: [OAuth](https://oauth.net/2/))으로 보호해야 합니다. 이 작업은 이 빠른 시작의 범위를 벗어납니다.

## <a name="add-sample-content"></a>샘플 콘텐츠 추가

이제 이 웹앱에 샘플 콘텐츠를 추가합니다. _views\index.pug_를 열고 자동으로 생성된 코드를 다음 샘플로 바꿉니다.

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


모든 텍스트에는 텍스트의 언어를 설명하는 **lang** 특성이 있습니다. 이 특성은 몰입형 판독기가 관련 언어와 문법 기능을 제공하는 데 도움이 됩니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

이제 웹앱이 준비되었습니다. 다음을 실행하여 앱을 실행합니다.

```bash
npm start
```

브라우저를 열고, _http://localhost:3000_ 으로 이동합니다. 다음이 표시되어야 합니다.

![샘플 앱](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>몰입형 판독기 시작

“몰입형 판독기” 단추를 클릭하면 페이지의 콘텐츠와 함께 몰입형 판독기가 시작됩니다.

![몰입형 판독기](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](../../reference.md) 살펴보기