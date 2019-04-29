---
title: Azure Service Fabric 역방향 프록시 보안 통신 | Microsoft Docs
description: 안전한 종단 간 통신을 사용하려면 역방향 프록시를 구성합니다.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: d8a11a3289037602535d1b5727d041e376012bd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837846"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>역방향 프록시를 사용하여 보안 서비스 연결

이 문서에서는 역방향 프록시와 서비스 간에 보안 연결을 설정하여 종단 간 보안 채널을 사용하도록 설정하는 방법에 대해 설명합니다. 역방향 프록시에 대한 자세한 내용은 [Azure Service Fabric의 역방향 프록시](service-fabric-reverseproxy.md)를 참조하세요.

보안 서비스에 연결하는 것은 역방향 프록시가 HTTPS에서 수신하도록 구성된 경우에만 지원됩니다. 이 문서에서는 이러한 경우를 가정합니다.
Service Fabric에서 역방향 프록시를 구성하려면 [Azure Service Fabric의 역방향 프록시 설정](service-fabric-reverseproxy-setup.md)을 참조하세요.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>역방향 프록시와 서비스 간의 안전한 연결 설정 

### <a name="reverse-proxy-authenticating-to-services"></a>서비스를 인증하는 역방향 프록시:
역방향 프록시는 해당 인증서를 사용하여 서비스에 대해 역방향 프록시 자체를 식별합니다. Azure 클러스터의 경우 인증서는 Resource Manager 템플릿의 [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [리소스 유형 섹션](../azure-resource-manager/resource-group-authoring-templates.md)에서 ***reverseProxyCertificate*** 속성을 사용하여 지정됩니다. 독립 실행형 클러스터의 경우 인증서는 ClusterConfig.json의 **보안** 섹션에서 ***ReverseProxyCertificate*** 또는 ***ReverseProxyCertificateCommonNames*** 속성 중 하나를 사용하여 지정됩니다. 자세한 내용은 [독립 실행형 클러스터에서 역방향 프록시 사용](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters)을 참조하세요. 

서비스는 역방향 프록시에서 제공한 인증서를 확인하기 위한 논리를 구현할 수 있습니다. 이 서비스는 허용된 클라이언트 인증서 세부 정보를 구성 패키지의 구성 설정으로 지정할 수 있습니다. 이는 런타임 시 읽을 수 있으며 역방향 프록시에서 제공한 인증서의 유효성을 검사하는 데 사용됩니다. 구성 설정을 추가하려면 [애플리케이션 매개 변수 관리](service-fabric-manage-multiple-environment-app-configuration.md)를 참조하세요. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>서비스에서 제공한 인증서를 통해 서비스의 ID를 확인하는 역방향 프록시:
역방향 프록시 서비스에서 제공 하는 인증서의 서버 인증서 유효성 검사를 수행 하려면 다음 정책을 지원 합니다. None, ServiceCommonNameAndIssuer 및 ServiceCertificateThumbprints 합니다.
사용할 역방향 프록시에 대한 정책을 선택하려면 [fabricSettings](service-fabric-cluster-fabric-settings.md) 아래에 있는 **ApplicationGateway/Http** 섹션에서 **ApplicationCertificateValidationPolicy**를 지정합니다.

다음 섹션에서는 이러한 옵션 각각에 대한 구성 세부 정보를 보여줍니다.

### <a name="service-certificate-validation-options"></a>서비스 인증서 유효성 검사 옵션 

- **없음**: 역방향은 프록시 설정 된 서비스 인증서의 확인을 건너뛰고 보안 연결을 설정 합니다. 기본 동작입니다.
[**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 섹션에서 값이 **None**인 **ApplicationCertificateValidationPolicy**를 지정합니다.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: 역방향 프록시 인증서의 일반 이름과 직접 발급자의 지문을 기반 서비스에서 제공 하는 인증서를 확인 합니다. 지정 된 **ApplicationCertificateValidationPolicy** 값으로 **ServiceCommonNameAndIssuer** 에 [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 섹션입니다.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   서비스 일반 이름 및 발급자 지문 목록을 지정하려면 아래 표시된 것처럼 **fabricSettings** 아래에 [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) 섹션을 추가합니다. 여러 인증서 일반 이름 및 발급자 지문 쌍을 **매개 변수** 배열에 추가할 수 있습니다. 

   엔드포인트 역방향 프록시가 연결되어 일반 이름과 발급자 지문이 여기에 지정된 값과 일치하는 인증서가 제시되면 SSL 채널이 설정됩니다. 
   인증서 세부 정보가 일치하지 않으면 502(잘못된 게이트웨이) 상태 코드와 함께 역방향 프록시가 클라이언트 요청에 대해 실패합니다. HTTP 상태 줄에 "잘못된 SSL 인증서"라는 구문이 포함됩니다. 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: 역방향 프록시가 지문을 기반으로 프록시 설정 된 서비스 인증서를 확인 합니다. 서명 된 인증서는 서비스 자체를 사용 하 여 구성 된 경우이 경로 이동 하도록 선택할 수 있습니다. 지정 된 **ApplicationCertificateValidationPolicy** 값으로 **ServiceCertificateThumbprints** 에 [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 섹션입니다.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   또한 **ApplicationGateway/Http** 섹션에서 **ServiceCertificateThumbprints** 항목이 있는 지문을 지정합니다. 값 필드에서 여러 개의 지문을 쉼표로 구분된 목록으로 지정할 수 있습니다(아래 그림 참조).

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   이 구성 항목 목록에 서버 인증서의 지문이 있으면 역방향 프록시가 SSL 연결에 성공합니다. 그렇지 않으면 연결을 종료하고 502(잘못된 게이트웨이)와 함께 클라이언트의 요청에 실패합니다. HTTP 상태 줄에 "잘못된 SSL 인증서"라는 구문이 포함됩니다.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>서비스가 보안 엔드포인트와 비보안 엔드포인트를 노출할 때 엔드포인트 선택 논리
Service Fabric은 서비스에 대한 다중 엔드포인트 구성을 지원합니다. 자세한 내용은 [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)을 참조하세요.

역방향 프록시는 [서비스 URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy)에서 **ListenerName** 쿼리 매개 변수를 기반으로 요청을 전달할 엔드포인트 중 하나를 선택합니다. **ListenerName** 매개 변수를 지정하지 않은 경우 역방향 프록시는 엔드포인트 목록에서 모든 엔드포인트를 선택할 수 있습니다. 서비스에 대해 구성된 엔드포인트에 따라 선택한 엔드포인트는 HTTP 또는 HTTPS 엔드포인트가 될 수 있습니다. 역방향 프록시가 "보안 전용 모드"에서 작동하기를 원하는 시나리오 또는 요구 사항이 있을 수 있습니다. 즉, 보안 역방향 프록시가 비보안 엔드포인트에 요청을 전달하지 않도록 하고 싶습니다. 역방향 프록시를 보안 전용 모드로 설정하려면 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 섹션에서 값이 **true**인 **SecureOnlyMode** 구성 항목을 지정합니다.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> **SecureOnlyMode**에서 작동할 때 클라이언트가 HTTP(비보안) 엔드포인트에 해당하는 **ListenerName**을 지정한 경우 역방향 프록시는 404(찾을 수 없음) HTTP 상태 코드와 함께 요청에 실패합니다.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>역방향 프록시를 통한 클라이언트 인증서 인증 설정
SSL 종료는 역방향 프록시에서 발생하며 모든 클라이언트 인증서 데이터가 손실됩니다. 서비스가 클라이언트 인증서 인증을 수행하려면 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 섹션에서 **ForwardClientCertificate** 설정을 지정합니다.

1. **ForwardClientCertificate**가 **false**로 설정되면 역방향 프록시가 클라이언트와의 SSL 핸드셰이크 중 클라이언트 인증서를 요청하지 않습니다.
기본 동작입니다.

2. **ForwardClientCertificate**가 **true**로 설정되면 역방향 프록시가 클라이언트와의 SSL 핸드셰이크 중 클라이언트 인증서를 요청합니다.
그런 다음 **X-Client-Certificate**라는 사용자 지정 HTTP 헤더에 클라이언트 인증서 데이터를 전달합니다. 헤더 값은 클라이언트 인증서의 base64로 인코딩된 PEM 형식의 문자열입니다. 서비스는 인증서 데이터를 검사한 후 요청에 성공하거나/적절한 상태 코드와 함께 실패할 수 있습니다.
클라이언트가 인증서를 제시하지 않으면 역방향 프록시는 빈 헤더를 전달하고 서비스에서 처리하도록 합니다.

> [!NOTE]
> 역방향 프록시는 단지 전달자일 뿐입니다. 클라이언트 인증서의 유효성 검사를 수행하지는 않습니다.


## <a name="next-steps"></a>다음 단계
* [클러스터에서 역방향 프록시를 설정 및 구성합니다](service-fabric-reverseproxy-setup.md).
* 다른 서비스 인증서 유효성 검사 옵션을 사용하여 보안 역방향 프록시를 구성하려면 [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services)(보안 서비스에 연결하도록 역방향 프록시 구성)에서 Azure Resource Manager 템플릿 샘플을 참조하세요.
* [GitHub의 샘플 프로젝트](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)에서 서비스 간 HTTP 통신의 예제를 참조하세요.
* [Reliable Services 원격을 사용하여 원격 프로시저 호출](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)
* [클러스터 인증서 관리](service-fabric-cluster-security-update-certs-azure.md)
