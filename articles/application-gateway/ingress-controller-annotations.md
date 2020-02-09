---
title: Application Gateway 수신 컨트롤러 주석
description: 이 문서에서는 Application Gateway 수신 컨트롤러와 관련 된 주석에 대한 설명서를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a3583a5efd120733ce7f6b71a7594b5636593f99
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795997"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러에 대한 주석 

## <a name="introductions"></a>기한과

Kubernetes 수신 리소스는 임의의 키/값 쌍으로 주석을 달 수 있습니다. AGIC는 Application Gateway 기능에 대한 주석을 사용 하 여 수신 YAML를 통해 구성할 수 없습니다. 수신 주석은 모든 HTTP 설정, 백 엔드 풀 및 수신 리소스에서 파생 된 수신기에 적용 됩니다.

## <a name="list-of-supported-annotations"></a>지원 되는 주석 목록

AGIC에서 수신 리소스를 관찰 하려면 `kubernetes.io/ingress.class: azure/application-gateway`로 **주석을 추가 해야 합니다** . 그런 다음 AGIC는 해당 수신 리소스를 사용 하 여 작업 합니다.

| 주석 키 | 값 형식 | 기본값 | 허용되는 값
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#ssl-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (초) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (초) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>백 엔드 경로 접두사

이 주석을 사용 하면 수신 리소스에 지정 된 백 엔드 경로를이 주석에 지정 된 접두사를 사용 하 여 다시 작성할 수 있습니다. 이를 통해 사용자는 엔드포인트이 수신 리소스에서 서비스를 노출 하는 데 사용 되는 엔드포인트 이름과 다른 서비스를 노출할 수 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>예제

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
위의 예제에서는 주석이 `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` 인 `go-server-ingress-bkprefix` 이라는 수신 리소스를 정의 했습니다. 주석은 응용 프로그램 게이트웨이에 HTTP 설정을 만들도록 지시 하며,이는 `/test/``/hello` 경로에 대한 경로 접두사를 재정의 합니다.

> [!NOTE] 
> 위의 예에는 하나의 규칙만 정의 되어 있습니다. 그러나 주석은 전체 수신 리소스에 적용 되므로 사용자가 여러 규칙을 정의한 경우 지정 된 각 경로에 대해 백 엔드 경로 접두사가 설정 됩니다. 따라서 사용자가 다른 경로 접두사 (동일한 서비스의 경우에도)를 사용 하는 다른 규칙을 원하는 경우 다른 수신 리소스를 정의 해야 합니다.

## <a name="ssl-redirect"></a>SSL 리디렉션

HTTP Url을 HTTPS 대응으로 자동으로 리디렉션하도록 Application Gateway를 [구성할 수 있습니다](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) . 이 주석이 있고 TLS가 적절히 구성 되 면 Kubernetes 수신 컨트롤러는 [리디렉션 구성으로 라우팅 규칙](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) 을 만들고 변경 내용을 Application Gateway에 적용 합니다. 만든 리디렉션은 HTTP `301 Moved Permanently`됩니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>예제

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>연결 드레이닝

`connection-draining`:이 주석을 사용 하면 사용자가 연결 드레이닝을 사용할지 여부를 지정할 수 있습니다.
`connection-draining-timeout`:이 주석을 사용 하면 사용자가 제한 시간을 지정 하 여 드레이닝 백 엔드 엔드포인트에 대한 요청을 종료할 Application Gateway 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>예제

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>쿠키 기반 선호도

이 주석을 사용 하 여 쿠키 기반 선호도를 사용 하도록 설정할지 여부를 지정할 수 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>예제

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>요청 시간 초과

이 주석을 사용 하면 요청 시간 제한 (초)을 지정 하 여 응답을 받지 못한 경우 요청이 실패 하는 경우이를 Application Gateway 수 있습니다.

### <a name="usage"></a>사용

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>예제

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>개인 IP 사용

이 주석을 사용 하면 Application Gateway의 개인 IP에서이 엔드포인트을 노출할 지 여부를 지정할 수 있습니다.

> [!NOTE]
> * Application Gateway는 동일한 포트에서 여러 Ip를 지원 하지 않습니다 (예: 80/443). 주석 `appgw.ingress.kubernetes.io/use-private-ip: "false"`와 `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP`에 대한 수신으로 인해 AGIC에서 Application Gateway를 업데이트 하지 못합니다.
> * 개인 IP가 없는 Application Gateway의 경우 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 조절기는 무시 됩니다. 이는 `NoPrivateIP` 경고와 함께 해당 조절기의 컨트롤러 로그 및 수신 이벤트에 반영 됩니다.


### <a name="usage"></a>사용
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>예제
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>백 엔드 프로토콜

이 주석을 사용 하면 Application Gateway Pod 통신 하는 동안 사용 해야 하는 프로토콜을 지정할 수 있습니다. 지원 되는 프로토콜: `http`, `https`

> [!NOTE]
> * Application Gateway에서 자체 서명 된 인증서가 지원 되지만, Pod는 잘 알려진 CA에서 서명한 인증서를 사용 하는 경우에만 AGIC를 `https` 지원 합니다.
> * Pod에서 HTTP와 함께 포트 80을 사용 하지 않도록 하 고 포트 443을 사용 하지 않도록 설정 합니다.

### <a name="usage"></a>사용
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>예제
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```