---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553854"
---
### <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

`listener.js`라는 새 JavaScript 파일을 만듭니다.

### <a name="add-the-relay-npm-package"></a>릴레이 NPM 패키지 추가

프로젝트 폴더에 있는 노드 명령 프롬프트에서 `npm install hyco-ws`를 실행합니다.

### <a name="write-some-code-to-receive-messages"></a>메시지를 수신하는 코드 작성

1. 다음 상수를 `listener.js` 파일의 맨 위에 추가합니다.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. 다음 상수를 하이브리드 연결 정보에 대한 `listener.js` 파일에 추가합니다. 대괄호 안의 자리 표시자를 하이브리드 연결을 만들 때 얻은 값으로 바꿉니다.
   
   1. `const ns` - 릴레이 네임스페이스 정규화된 네임스페이스 이름을 사용해야 합니다. 예: `{namespace}.servicebus.windows.net`
   2. `const path` - 하이브리드 연결의 이름
   3. `const keyrule` - SAS 키의 이름
   4. `const key` - SAS 키 값

3. `listener.js` 파일에 다음 코드를 추가합니다.
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    listener.js 파일은 다음과 같아야 합니다.
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

