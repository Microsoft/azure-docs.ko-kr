---
title: Application Gateway를 사용 하 여 HTTP 또는 HTTPS를 통해 AKS 서비스 노출
description: 이 문서에서는 Application Gateway를 사용 하 여 HTTP 또는 HTTPS를 통해 AKS 서비스를 노출 하는 방법에 대한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795584"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Application Gateway를 사용 하 여 HTTP 또는 HTTPS를 통해 AKS 서비스 노출 

이러한 자습서는 [Kubernetes 수신 리소스](https://kubernetes.io/docs/concepts/services-networking/ingress/) 를 사용 하 여 HTTP 또는 HTTPS를 통해 [Azure 애플리케이션 게이트웨이](https://azure.microsoft.com/services/application-gateway/) 를 통해 예제 Kubernetes 서비스를 노출 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- `ingress-azure` 투구 차트를 설치 했습니다.
  - [**최적의 배포**](ingress-controller-install-new.md): 처음부터 시작 하는 경우 Application Gateway를 사용 하 여 AKS 클러스터를 배포 하는 단계를 설명 하 고 AKS 클러스터에 응용 프로그램 게이트웨이 수신 컨트롤러를 설치 하는 단계를 설명 하는 설치 지침을 참조 하세요.
  - [**Brownfield 배포**](ingress-controller-install-existing.md): 기존 AKS 클러스터와 Application Gateway 있는 경우이 지침을 참조 하 여 AKS 클러스터에 응용 프로그램 게이트웨이 수신 컨트롤러를 설치 합니다.
- 이 응용 프로그램에서 HTTPS를 사용 하려면 x509 인증서 및 해당 개인 키가 필요 합니다.

## <a name="deploy-guestbook-application"></a>응용 프로그램 `guestbook` 배포

방명록 응용 프로그램은 웹 UI 프런트 엔드, 백 엔드 및 Redis 데이터베이스를 작성 하는 정식 Kubernetes 응용 프로그램입니다. 기본적으로 `guestbook`는 포트 `80`에서 이름이 `frontend` 인 서비스를 통해 해당 응용 프로그램을 노출 합니다. Kubernetes 수신 리소스가 없으면 AKS 클러스터 외부에서 서비스에 액세스할 수 없습니다. 응용 프로그램을 사용 하 고 수신 리소스를 설정 하 여 HTTP 및 HTTPS를 통해 응용 프로그램에 액세스 합니다.

아래 지침에 따라 방명록 응용 프로그램을 배포 합니다.

1. [여기](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml) 에서 `guestbook-all-in-one.yaml` 다운로드
1. 을 실행 하 여 AKS 클러스터에 `guestbook-all-in-one.yaml` 배포

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

이제 `guestbook` 응용 프로그램이 배포 되었습니다.

## <a name="expose-services-over-http"></a>HTTP를 통해 서비스 노출

방명록 응용 프로그램을 노출 하기 위해 다음 수신 리소스를 사용 합니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

이 수신은 `guestbook-all-in-one` 배포의 `frontend` 서비스를 Application Gateway의 기본 백 엔드로 노출 합니다.

`ing-guestbook.yaml`로 위의 수신 리소스를 저장 합니다.

1. 다음을 실행 하 여 `ing-guestbook.yaml`를 배포 합니다.

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. 배포 상태에 대한 수신 컨트롤러의 로그를 확인 합니다.

이제 `guestbook` 응용 프로그램을 사용할 수 있습니다. Application Gateway의 공용 주소를 방문 하 여이를 확인할 수 있습니다.

## <a name="expose-services-over-https"></a>HTTPS를 통해 서비스 노출

### <a name="without-specified-hostname"></a>지정 된 호스트 이름 없음

Hostname을 지정 하지 않으면 응용 프로그램 게이트웨이를 가리키는 모든 호스트 이름에서 방명록 서비스를 사용할 수 있습니다.

1. 수신을 배포 하기 전에 인증서 및 개인 키를 호스트 하는 kubernetes 암호를 만들어야 합니다. 을 실행 하 여 kubernetes 암호를 만들 수 있습니다.

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 다음 수신을 정의 합니다. 수신의 `secretName` 섹션에서 암호의 이름을 지정 합니다.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > 위의 수신 리소스의 `<guestbook-secret-name>`을 비밀 이름으로 바꿉니다. 위의 수신 리소스를 파일 이름 `ing-guestbook-tls.yaml`에 저장 합니다.

1. 을 실행 하 여-방명록-tls.

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. 배포 상태에 대한 수신 컨트롤러의 로그를 확인 합니다.

이제 HTTP와 HTTPS 모두에서 `guestbook` 응용 프로그램을 사용할 수 있습니다.

### <a name="with-specified-hostname"></a>지정 된 호스트 이름 사용

또한 TLS 구성 및 서비스를 멀티플렉싱 위해 수신에 대한 호스트 이름을 지정할 수 있습니다.
호스트 이름을 지정 하면 지정 된 호스트 에서만 방명록 서비스를 사용할 수 있습니다.

1. 다음 수신을 정의 합니다.
    수신에서 `secretName` 섹션의 비밀 이름을 지정 하 고 `hosts` 섹션에서 호스트 이름을 적절 하 게 바꿉니다.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. 를 실행 하 여 `ing-guestbook-tls-sni.yaml` 배포

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. 배포 상태에 대한 수신 컨트롤러의 로그를 확인 합니다.

이제 지정 된 호스트 에서만 `guestbook` 응용 프로그램을 HTTP와 HTTPS 모두에서 사용할 수 있습니다 (이 예제에서는`<guestbook.contoso.com>`).

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

다음 수신에서는이 수신에 추가 경로를 추가 하 고 해당 경로를 다른 서비스로 리디렉션할 수 있습니다.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
