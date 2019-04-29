---
title: 포함 파일
description: 포함 파일
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553965"
---
### <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

릴레이를 만들 때 [클라이언트 인증 필요] 옵션을 사용하지 않도록 설정한 경우 임의 브라우저로 요청을 하이브리드 연결 URL에 보낼 수 있습니다. 보호된 엔드포인트에 액세스하기 위해서는 여기 표시된 `ServiceBusAuthorization` 헤더에 토큰을 만들고 전달해야 합니다.

시작하려면 `sender.js`라는 새 JavaScript 파일을 만듭니다.

### <a name="add-the-relay-npm-package"></a>릴레이 NPM 패키지 추가

프로젝트 폴더에 있는 노드 명령 프롬프트에서 `npm install hyco-https`를 실행합니다. 이 패키지는 일반 `https` 패키지도 가져옵니다. 클라이언트 쪽에서 중요한 차이점은 패키지가 릴레이 URI 및 토큰을 생성하는 함수를 제공한다는 점입니다.

### <a name="write-some-code-to-send-messages"></a>메시지를 보내는 코드 작성

1. `sender.js` 파일 맨 위에 다음 `constants`를 추가합니다.
   
    ```js
    const https = require('hyco-https');
    ```

2. 다음 상수를 하이브리드 연결 정보에 대한 `sender.js` 파일에 추가합니다. 대괄호 안의 자리 표시자를 하이브리드 연결을 만들 때 얻은 값으로 바꿉니다.
   
   1. `const ns` - 릴레이 네임스페이스 정규화된 네임스페이스 이름을 사용해야 합니다. 예: `{namespace}.servicebus.windows.net`
   2. `const path` - 하이브리드 연결의 이름
   3. `const keyrule` - SAS 키의 이름
   4. `const key` - SAS 키 값

3. `sender.js` 파일에 다음 코드를 추가합니다. 코드가 Node.js HTTPS 클라이언트의 일반적인 사용과 크게 다르지 않다는 것을 알 수 있습니다. 단지 인증 헤더를 추가합니다.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    sender.js 파일은 다음과 같아야 합니다.
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

