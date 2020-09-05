---
title: '자습서: Node.js를 사용하여 몰입형 판독기 시작'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 몰입형 판독기를 시작하는 Node.js 애플리케이션을 만듭니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript
ms.openlocfilehash: a8cd8bacc749ef44ee23e98709f04f75757f5919
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516336"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>자습서: 몰입형 판독기(Node.js) 시작

[개요](./overview.md)에서는 몰입형 판독기란 무엇이며 어떻게 언어 학습자, 신흥 독자 및 학습 차이가 있는 사람들의 독해력 향상을 위해 입증된 기술을 구현하는지에 대해 알아보았습니다. 이 자습서에서는 몰입형 판독기를 시작하는 Node.js 애플리케이션을 만드는 방법을 설명합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Express에서 Node.js 웹앱 만들기
> * 액세스 토큰 획득
> * 샘플 콘텐츠를 사용하여 몰입형 판독기 시작
> * 콘텐츠 언어 지정
> * 몰입형 판독기 인터페이스의 언어 지정
> * 수학 콘텐츠를 사용하여 몰입형 판독기 시작

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Active Directory 인증에 대해 구성된 몰입형 판독기 리소스입니다. [다음 지침](./how-to-create-immersive-reader.md)에 따라 설정하세요. 환경 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.
* [Node.js](https://nodejs.org/) 및 [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/)와 같은 IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express에서 Node.js 웹앱 만들기

`express-generator` 도구로 Node.js를 만듭니다.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Yarn 종속성을 설치하고, 자습서에서 나중에 사용하게 될 `request` 및 `dotenv` 종속성을 추가합니다.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 인증 토큰 획득

다음으로, Azure AD 인증 토큰을 검색하는 백 엔드 API를 작성합니다.

이 부분에는 위의 Azure AD 인증 구성 필수 요소 단계의 값이 필요합니다. 해당 세션에서 저장한 텍스트 파일을 다시 참조합니다.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

값을 얻었으면 _.env_라는 새 파일을 만들고, 다음 코드를 이 파일에 붙여넣고, 위의 사용자 지정 속성 값을 입력합니다. 따옴표 또는 "{" 및 "}" 문자를 포함하지 마세요.

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

_routes\index.js_ 파일을 열고 콘텐츠를 다음 코드로 바꿉니다.

이 코드는 서비스 사용자 암호를 사용하여 Azure AD 인증 토큰을 획득하는 API 엔드포인트를 만듭니다. 하위 도메인도 검색합니다. 그런 다음, 토큰과 하위 도메인을 포함하는 개체를 반환합니다.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
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
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

권한 없는 사용자가 토큰을 획득하여 몰입형 리더 서비스 및 청구에 사용하지 못하도록 **getimmersivereaderlaunchparams** API 엔드포인트를 인증 형식(예: [OAuth](https://oauth.net/2/))으로 보호해야 하며, 이 작업은 이 자습서의 범위를 벗어납니다.

## <a name="launch-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 판독기 시작

1. _views\layout.pug_를 열고 `head` 태그 아래, `body` 태그 앞에 다음 코드를 추가합니다. 이 `script` 태그는 [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 jQuery를 로드합니다.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. _views\index.pug_를 열고 콘텐츠를 다음 코드로 바꿉니다. 이 코드는 일부 샘플 콘텐츠로 페이지를 입력하고, 몰입형 판독기를 시작하는 단추를 추가합니다.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. 이제 웹앱이 준비되었습니다. 다음을 실행하여 앱을 실행합니다.

    ```bash
    npm start
    ```

4. 브라우저를 열고, _http://localhost:3000_ 으로 이동합니다. 위 콘텐츠가 페이지에 표시됩니다. **몰입형 판독기** 단추를 클릭하여 콘텐츠로 몰입형 판독기를 시작합니다.

## <a name="specify-the-language-of-your-content"></a>콘텐츠 언어 지정

몰입형 판독기는 여러 언어를 지원합니다. 아래의 단계에 따라 콘텐츠 언어를 지정할 수 있습니다.

1. _views\index.pug_를 열고 이전 단계에서 추가한 `p(id=content)` 태그 아래에 다음 코드를 추가합니다. 이 코드는 페이지에 스페인어 콘텐츠를 추가합니다.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. JavaScript 코드에서 `ImmersiveReader.launchAsync` 호출 위에 다음을 추가합니다. 이 코드는 몰입형 판독기에 스페인어 콘텐츠를 전달합니다.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. _http://localhost:3000_ 으로 다시 이동합니다. 페이지에 스페인어 텍스트가 표시되며 **몰입형 판독기**를 클릭하면 몰입형 판독기도 표시됩니다.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>몰입형 판독기 인터페이스의 언어 지정

기본적으로 몰입형 판독기 인터페이스의 언어는 브라우저의 언어 설정과 일치합니다. 다음 코드를 통해 몰입형 판독기 인터페이스의 언어를 지정할 수도 있습니다.

1. _views\index.pug_에서 `ImmersiveReader.launchAsync(token, subdomain, content)` 호출을 아래 코드로 바꿉니다.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. _http://localhost:3000_ 으로 이동합니다. 몰입형 판독기를 시작하면 인터페이스가 프랑스어로 표시됩니다.

## <a name="launch-the-immersive-reader-with-math-content"></a>수학 콘텐츠를 사용하여 몰입형 판독기 시작

[MathML](https://developer.mozilla.org/en-US/docs/Web/MathML)을 사용하여 몰입형 판독기에 수학 콘텐츠를 포함시킬 수 있습니다.

1. `ImmersiveReader.launchAsync` 호출 위에 다음 코드를 포함하여 _views\index.pug_를 수정합니다.

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. _http://localhost:3000_ 으로 이동합니다. 몰입형 판독기를 시작하고 아래쪽으로 스크롤하면 수학 수식이 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)에서 코드 샘플 보기
