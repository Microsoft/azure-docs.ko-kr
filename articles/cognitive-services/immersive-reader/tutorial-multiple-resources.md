---
title: '자습서: 여러 몰입형 판독기 리소스 통합'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 여러 몰입형 판독기 리소스를 사용하여 몰입형 판독기를 시작하는 Node.js 애플리케이션을 만듭니다.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-javascript
ms.openlocfilehash: 1abd1052c33346d949809d7c35a99ef97bde2c03
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516285"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>자습서: 여러 몰입형 판독기 리소스 통합

[개요](./overview.md)에서는 몰입형 판독기란 무엇이며 어떻게 언어 학습자, 신흥 독자 및 학습 차이가 있는 사람들의 독해력 향상을 위해 입증된 기술을 구현하는지에 대해 알아보았습니다. [Node.js 빠른 시작](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)에서 단일 리소스를 사용하여 몰입형 판독기를 사용하는 방법을 알아보았습니다. 이 자습서에서는 여러 개의 몰입형 판독기 리소스를 같은 애플리케이션에 통합하는 방법에 대해 설명합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 기존 리소스 그룹에 여러 몰입형 판독기 리소스 만들기
> * 여러 리소스를 사용하여 몰입형 판독기 시작

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* [빠른 시작](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)을 수행하여 Node.js를 사용하여 몰입형 판독기를 시작하는 웹앱을 만듭니다. 이 빠른 시작에서는 단일 몰입형 판독기 리소스를 구성합니다. 이 자습서의 첫 부분에서 빌드합니다.

## <a name="create-the-immersive-reader-resources"></a>몰입형 판독기 리소스 만들기

[이러한 지침](./how-to-create-immersive-reader.md)을 따라 각 몰입형 판독기 리소스를 만듭니다. **Create-ImmersiveReaderResource** 스크립트에는 매개 변수로 `ResourceName`, `ResourceSubdomain` 및 `ResourceLocation`이 있습니다. 이러한 리소스는 생성되는 각 리소스에 대해 고유해야 합니다. 나머지 매개 변수는 첫 번째 몰입형 판독기 리소스를 설정할 때 사용한 것과 동일해야 합니다. 이러한 방식으로 각 리소스는 동일한 Azure 리소스 그룹 및 Azure AD 애플리케이션에 연결될 수 있습니다.

아래 예제에서는 하나는 WestUS에, 다른 하나는 EastUS에 있는 두 개의 리소스를 만드는 방법을 보여줍니다. `ResourceName`, `ResourceSubdomain` 및 `ResourceLocation`에 대한 고유한 값을 확인합니다.

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>환경 구성에 리소스 추가

빠른 시작에서는 `TenantId`, `ClientId`, `ClientSecret` 및 `Subdomain` 매개 변수를 포함하는 환경 구성 파일을 만들었습니다. 모든 리소스는 동일한 Azure AD 애플리케이션을 사용하므로 `TenantId`, `ClientId` 및 `ClientSecret`에 동일한 값을 사용할 수 있습니다. 각 리소스에 대한 각 하위 도메인을 나열하기만 하면 됩니다.

이제 __.env__ 파일이 다음 예제와 같이 표시됩니다.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

이 파일에는 공개되어서는 안 되는 비밀이 있으므로 이 파일을 소스 제어에 커밋하지 않아야 합니다.

다음으로, 여러 리소스를 지원하기 위해 만든 _routes\index.js_ 파일을 수정하겠습니다. 해당 콘텐츠를 다음 코드로 바꿉니다.

이전과 마찬가지로, 이 코드는 서비스 주체 암호를 사용하여 Azure AD 인증 토큰을 획득하는 API 엔드포인트를 만듭니다. 이번에는 사용자가 리소스 위치를 지정하고 이를 쿼리 매개 변수로 전달하도록 허용합니다. 그런 다음, 토큰과 해당하는 하위 도메인을 포함하는 개체를 반환합니다.

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

권한 없는 사용자가 토큰을 획득하여 몰입형 리더 서비스 및 청구에 사용하지 못하도록 **getimmersivereaderlaunchparams** API 엔드포인트를 인증 형식(예: [OAuth](https://oauth.net/2/))으로 보호해야 하며, 이 작업은 이 자습서의 범위를 벗어납니다.

## <a name="launch-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 판독기 시작

1. _views\index.pug_를 열고 콘텐츠를 다음 코드로 바꿉니다. 이 코드는 일부 샘플 콘텐츠로 페이지를 입력하고, 몰입형 판독기를 시작하는 단추를 두 개 추가합니다. 하나는 EastUS 리소스에 대한 몰입형 판독기를 시작하기 위한 것이고 다른 하나는 WestUS 리소스를 위한 것입니다.

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. 이제 웹앱이 준비되었습니다. 다음을 실행하여 앱을 실행합니다.

    ```bash
    npm start
    ```

4. 브라우저를 열고 `http://localhost:3000`으로 이동합니다. 위 콘텐츠가 페이지에 표시됩니다. **EastUS 몰입형 판독기** 단추나 **WestUS 몰입형 판독기** 단추를 클릭하여 해당 리소스를 사용하여 몰입형 판독기를 시작합니다.

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)에서 코드 샘플 보기