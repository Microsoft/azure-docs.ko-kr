---
title: Azure Media Services v3 API에 연결-node.js
description: 이 문서에서는 node.js를 사용 하 여 Media Services v3 API에 연결 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896111"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API에 연결-node.js

이 문서에서는 서비스 사용자 로그인 메서드를 사용 하 여 Azure Media Services v3 node.js SDK에 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>전제 조건

- [Node.js](https://nodejs.org/en/download/)를 설치합니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md) 리소스 그룹 이름 및 Media Services 계정 이름을 명심 해야 합니다.

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토 합니다.

## <a name="create-packagejson"></a>Create package. json

1. 즐겨 사용 하는 편집기를 사용 하 여 package json 파일을 만듭니다.
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
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>최신 Azure Media Services 패키지를 사용 하 고 있는지 확인 하려면 [NPM 설치 windowsazure.mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)를 확인 합니다.|
|`azure-storage`|저장소 SDK. 자산에 파일을 업로드할 때 사용 됩니다.|
|`ms-rest-azure`| 로그인 하는 데 사용 됩니다.|

다음 명령을 실행 하 여 최신 패키지를 사용 하 고 있는지 확인할 수 있습니다.

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Node.js 클라이언트에 연결

1. 즐겨 사용 하는 편집기를 사용 하 여 .js 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여넣습니다.
1. "엔드포인트 구성" 섹션의 값을 [액세스 api](access-api-cli-how-to.md)에서 가져온 값으로 설정 합니다.

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

명령 프롬프트를 엽니다. 샘플 디렉터리로 이동 하 여 다음 명령을 실행 합니다.

```
npm install 
node index.js
```

## <a name="see-also"></a>참고 항목

- [Media Services 개념](concepts-overview.md)
- [azure-arm-mediaservices NPM 설치](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>다음 단계

Media Services [Node.js 참조](/javascript/api/overview/azure/mediaservices/management) 설명서를 살펴보고 node.js와 함께 Media Services API를 사용하는 방법을 보여주는 [샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)을 확인하세요.

