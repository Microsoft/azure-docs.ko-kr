---
title: Azure Media Services v3 API-Node.js에 연결
description: Node.js 사용 하 여 Media Services v3 API에 연결 하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 40880a2c28ce28a671930ef8837082247e61e24b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733133"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API-Node.js에 연결

이 문서에서는 Azure Media Services v3 node.js SDK 메서드는 서비스 보안 주체 로그인을 사용 하 여 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- [Node.js](https://nodejs.org/en/download/)를 설치합니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md) Media Services 계정 이름과 리소스 그룹 이름을 기억해 두어야 합니다.

## <a name="create-packagejson"></a>Package.json을 만들려면

1. 즐겨 찾는 편집기를 사용 하 여 package.json 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여 넣습니다.

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

다음 패키지를 지정 해야 합니다.

|패키지|설명|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>확인 하려면 최신 Azure Media Services 패키지를 사용 하 고 있는지 검사 하십시오 [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)합니다.|
|`azure-storage`|Storage SDK입니다. 자산에 파일을 업로드 하는 경우 사용 합니다.|
|`ms-rest-azure`| 로그인 하는 데 사용 합니다.|

최신 패키지를 사용 중인지 확인 하려면 다음 명령을 실행할 수 있습니다.

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Node.js 클라이언트에 연결

1. 즐겨 찾는 편집기를 사용 하 여.js 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여넣습니다.
1. 가져온 값으로 "끝점 구성" 섹션에서 값을 설정 [Api에 액세스](access-api-cli-how-to.md)합니다.

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>앱 실행

명령 프롬프트를 엽니다. 샘플의 디렉터리로 이동 하 고 다음 명령을 실행 합니다.

```
npm install 
node index.js
```

## <a name="see-also"></a>참고 항목

- [Media Services 개념](concepts-overview.md)
- [azure-arm-mediaservices NPM 설치](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>다음 단계

Media Services [Node.js 참조](https://aka.ms/ams-v3-nodejs-ref) 설명서를 살펴보고 node.js와 함께 Media Services API를 사용하는 방법을 보여주는 [샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)을 확인하세요.

