---
title: 수신 엔드포인트에 대한 내부 라우팅에 개인 IP 주소 사용
description: 이 문서에서는 내부 라우팅에 개인 Ip를 사용 하 여 클러스터 내의 수신 엔드포인트를 나머지 VNet에 노출 하는 방법에 대한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795486"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>수신 엔드포인트에 대한 내부 라우팅에 개인 IP 사용 

이 기능을 사용 하면 개인 IP를 사용 하 여 `Virtual Network` 내에서 수신 엔드포인트을 노출할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소  
[개인 IP 구성을](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip) 사용 하 여 Application Gateway

수신에 개인 IP를 사용 하도록 컨트롤러를 구성 하는 방법에는 두 가지가 있습니다.

## <a name="assign-to-a-particular-ingress"></a>특정 수신에 할당
개인 IP를 통해 특정 수신을 노출 하려면 수신에서 주석 [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) 를 사용 합니다.

### <a name="usage"></a>사용
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

개인 IP가 없는 응용 프로그램 게이트웨이의 경우 `appgw.ingress.kubernetes.io/use-private-ip: "true"`로 주석이 지정 된 조절기 무시 됩니다. 이는 수신 이벤트와 AGIC pod 로그에 표시 됩니다.

* 수신 이벤트에 표시 된 오류

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC 로그에 표시 된 오류

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>전역 할당
경우에 따라 개인 IP를 통해 모든 조절기 노출 되도록 제한 하는 것이 요구 사항이 며 `helm` 구성에서 `appgw.usePrivateIP: true`을 사용 합니다.

### <a name="usage"></a>사용
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

그러면 Application Gateway에서 프런트 엔드 수신기를 구성할 때 수신 컨트롤러에서 개인 IP에 대한 IP 주소 구성을 필터링 하 게 됩니다.
`usePrivateIP: true` 하 고 개인 IP가 할당 되지 않은 경우에는 AGIC가 작동 하지 않습니다.

> [!NOTE]
> Application Gateway v2 SKU에는 공용 IP가 필요 합니다. Application Gateway 개인으로 요구 하는 경우 트래픽을 제한 하기 위해 Application Gateway의 서브넷에 [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) 을 연결 합니다.
