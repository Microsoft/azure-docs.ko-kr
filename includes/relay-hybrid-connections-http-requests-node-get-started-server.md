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
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553943"
---
### <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

`listener.js`라는 새 JavaScript 파일을 만듭니다.

### <a name="add-the-relay-npm-package"></a>릴레이 NPM 패키지 추가

프로젝트 폴더에 있는 노드 명령 프롬프트에서 `npm install hyco-https`를 실행합니다.

### <a name="write-some-code-to-handle-requests"></a>요청을 처리하는 일부 코드 작성

1. 다음 상수를 `listener.js` 파일의 맨 위에 추가합니다.

    ```js
    const https = require('hyco-https');
    ```
2. 다음 상수를 하이브리드 연결 정보에 대한 `listener.js` 파일에 추가합니다. 대괄호 안의 자리 표시자를 하이브리드 연결을 만들 때 얻은 값으로 바꿉니다.

   1. `const ns` - 릴레이 네임스페이스 정규화된 네임스페이스 이름을 사용해야 합니다. 예: `{namespace}.servicebus.windows.net`
   2. `const path` - 하이브리드 연결의 이름
   3. `const keyrule` - SAS 키의 이름
   4. `const key` - SAS 키 값

3. `listener.js` 파일에 다음 코드를 추가합니다. :

    일반적인 `createServer` 함수 대신 `createRelayedServer`를 사용하는 것을 제외하고, 코드가 Node.js 초급자를 위한 자습서에서 확인할 수 있는 간단한 HTTP 서버 예제와 크게 다르지 않다는 것을 알 수 있습니다.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    listener.js 파일은 다음과 같아야 합니다.
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

