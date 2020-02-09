---
title: Application Gateway에 WebSocket 서버 노출
description: 이 문서에서는 AKS 클러스터에 대한 수신 컨트롤러와 Application Gateway 위해 WebSocket 서버를 노출 하는 방법에 대한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 01fde82e69917f59f6519524c4c8828feb84a4f9
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795961"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Application Gateway에 WebSocket 서버 노출

Application Gateway v2 설명서에 설명 된 대로 [WebSocket 및 HTTP/2 프로토콜에 대한 기본 지원을 제공](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic)합니다. Application Gateway 및 Kubernetes 수신-WebSocket 지원을 선택적으로 사용 하거나 사용 하지 않도록 설정할 수 있는 사용자 구성 설정이 없습니다.

아래 Kubernetes 배포 YAML는 WebSocket 서버를 배포 하는 데 사용 되는 최소 구성을 보여 줍니다 .이 구성은 일반 웹 서버를 배포 하는 것과 같습니다.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

모든 필수 구성 요소가 충족 되 고 AKS의 Kubernetes 수신에 의해 제어 되는 Application Gateway 있는 경우 위의 배포는 Application Gateway의 공용 IP 및 `ws.contoso.com`의 포트 80에 Websocket 서버를 노출 합니다. 도메인.

다음의 말아 명령은 WebSocket 서버 배포를 테스트 합니다.
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket 상태 프로브

배포가 상태 프로브를 명시적으로 정의 하지 않으면 Application Gateway WebSocket 서버 엔드포인트에서 HTTP GET을 시도 합니다.
서버 구현에 따라 (예[는 선호](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) WebSocket 특정 헤더가 필요할 수 있습니다`Sec-Websocket-Version` (예를 들어).
Application Gateway는 WebSocket 헤더 Application Gateway를 추가 하지 않으므로 WebSocket 서버의 상태 프로브 응답이 대부분 `400 Bad Request`될 가능성이 높습니다.
Application Gateway 결과적으로 pod는 비정상으로 표시 됩니다. 그러면 결국 WebSocket 서버의 소비자에 대한 `502 Bad Gateway` 발생 합니다.
이 문제를 방지 하려면 서버에 상태 검사에 대한 HTTP GET 처리기를 추가 해야 합니다 (`200 OK`을 반환 하는 인스턴스에 대한`/health`).
