---
title: AKS pod에 상태 프로브 추가
description: 이 문서에서는 Application Gateway를 사용 하 여 AKS pod에 상태 프로브 (준비 및/또는 선거의)를 추가 하는 방법에 대한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795587"
---
# <a name="add-health-probes-to-your-service"></a>서비스에 상태 프로브 추가
기본적으로 수신 컨트롤러는 노출 된 pod에 대한 HTTP GET 프로브를 프로 비전 합니다.
`deployment`/`pod` 사양에 [준비 또는 선거의 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) 를 추가 하 여 프로브 속성을 사용자 지정할 수 있습니다.

## <a name="with-readinessprobe-or-livenessprobe"></a>`readinessProbe` 또는 `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API 참조:
* [컨테이너 프로브](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet 작업](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `httpGet`를 사용 하 여 구성 하는 경우 `readinessProbe` 및 `livenessProbe` 지원 됩니다.
> * Pod에 노출 된 포트 이외의 포트에서의 검색은 현재 지원 되지 않습니다.
> * `HttpHeaders`, `InitialDelaySeconds``SuccessThreshold` 지원 되지 않습니다.

##  <a name="without-readinessprobe-or-livenessprobe"></a>`readinessProbe` 또는 `livenessProbe` 없음
위의 프로브를 제공 하지 않으면 수신 컨트롤러는 서비스에 대한 `ingress` 정의에 지정 된 `path` 또는 `backend-path-prefix` 주석에 지정 된 `Path`에서 서비스에 연결할 수 있다고 가정 합니다.

## <a name="default-values-for-health-probe"></a>상태 프로브에 대한 기본값
준비/선거의 프로브에서 유추할 수 없는 모든 속성의 경우 기본값이 설정 됩니다.

| Application Gateway 프로브 속성 | 기본값 |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |