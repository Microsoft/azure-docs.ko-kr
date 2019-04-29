---
title: 요청을 라우팅하도록 게이트웨이 구성 | Microsoft Docs
description: Service Fabric Mesh에서 실행 중인 애플리케이션에 대해 들어오는 트래픽을 처리하는 게이트웨이를 구성하는 방법에 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2e2502e35b3720ddbfe5950b89e2388de378f2ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583644"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>요청을 라우팅하도록 게이트웨이 리소스 구성 | Microsoft Docs

게이트웨이 리소스는 사용자 애플리케이션을 할당하는 네트워크에 들어오는 트래픽을 라우팅하는 데 사용 됩니다. 요청의 구조를 기반으로 특정 서비스 또는 엔드포인트에 안내되는 규칙을 지정하도록 구성합니다. 메시의 네트워크 및 게이트웨이에 대한 자세한 내용은 [ Service Fabric Mesh의 네트워킹 소개](service-fabric-mesh-networks-and-gateways.md)를 참조하세요. 

게이트웨이 리소스는 배포 템플릿(JSON 또는 yaml)의 일부로 선언해야 하고 네트워크 리소스에 종속됩니다. 이 설명서에서는 게이트웨이를 설정할 수 있는 다양한 속성에 대해 설명하며 샘플 게이트웨이 구성을 제공합니다.

## <a name="options-for-configuring-your-gateway-resource"></a>게이트웨이 리소스 구성에 대한 옵션

게이트웨이 리소스는 애플리케이션의 네트워크 및 기본 인프라의 네트워크(`open` 네트워크) 간 가교 역할을 하므로 게이트웨이 리소스를 구성해야 합니다(메시 미리 보기에는 앱 당 한 개의 게이트웨이로 제한). 리소스에 대한 선언은 두 개의 주요 부분 리소스 메타데이터 및 속성으로 구성됩니다. 

### <a name="gateway-resource-metadata"></a>게이트웨이 리소스 메타데이터

게이트웨이는 다음 메타데이터를 사용하여 선언 됩니다.
* `apiVersion` - "2018-09-01-미리 보기"(또는 그 이후의 날짜)로 설정해야 합니다.
* `name` - 이 게이트웨이에 대한 문자열 이름입니다.
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` - 앱/네트워크의 위치를 설정해야 합니다. 일반적으로 배포의 위치 매개 변수를 참조합니다.
* `dependsOn` - 이 게이트웨이에 수신 지점으로 역할을 할 네트워크입니다.

Azure Resource Manager(JSON) 배포 템플릿은 다음과 같습니다. 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>게이트웨이 속성

속성 섹션은 게이트웨이 위치 및 라우팅 요청에 대한 규칙에서 네트워크를 정의하는 데 사용합니다. 

#### <a name="source-and-destination-network"></a>원본 및 대상 네트워크 

각 게이트웨이는 `sourceNetwork` 및 `destinationNetwork`가 필요합니다. 원본 네트워크는 앱이 인바운드 요청을 수신할 네트워크로 정의 됩니다. 이름 속성은 "Open"으로 항상 설정해야 합니다. 대상 네트워크는 요청을 대상으로 하는 네트워크입니다. 이에 대한 이름 값은 앱의 로컬 네트워크의 리소스 이름으로 설정해야 합니다(리소스에 대한 전체 참조 포함). "myNetwork"라고 불리는 네트워크에서 배포를 어떻게 하는 지에 대한 샘플 구성은 아래를 참조하세요.

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>규칙 

게이트웨이에는 들어오는 트래픽을 핸들링하는 방법을 지정하는 다중 라우팅 규칙이 있습니다. 라우팅 규칙은 지정된 애플리케이션에 대한 수신 대기 포트와 대상 엔드포인트 간의 관계를 정의합니다. TCP 라우팅 규칙의 경우 포트: 엔드포인트 간 1:1 매핑이 있습니다. HTTP 라우팅 규칙의 경우 요청의 경로와 헤더(선택 옵션)을 검사하는 보다 복잡한 라우팅 규칙을 설정하여 요청이 라우팅되는 방법을 결정할 수 있습니다. 

라우팅 규칙은 포트 별로 지정됩니다. 각 수신 포트는 게이트웨이 구성의 속성 섹션 내 규칙의 배열을 가지고 있습니다. 

#### <a name="tcp-routing-rules"></a>TCP 라우팅 규칙 

TCP 라우팅 규칙은 다음 속성으로 구성됩니다. 
* `name` - 원하는 문자열을 사용할 수 있는 규칙을 참조합니다. 
* `port` - 들어오는 요청을 수신 대기하는 포트입니다. 
* `destination` - 요청이 라우팅될 `applicationName`, `serviceName` 및 `endpointName`를 포함하는 엔드포인트 사양입니다.

TCP 라우팅 규칙에 대한 예제입니다.

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP 라우팅 규칙 

HTTP 라우팅 규칙은 다음 속성으로 구성됩니다. 
* `name` - 원하는 문자열을 사용할 수 있는 규칙을 참조합니다. 
* `port` - 들어오는 요청을 수신 대기하는 포트입니다. 
* `hosts` -위에서 지정한 포트에서 다양한 "호스트"로 들어오는 요청에 적용되는 정책의 배열입니다. 호스트는 네트워크에서 실행하고 들어오는 요청, 즉 웹앱을 처리할 수 있는 애플리케이션 및 서비스의 집합입니다. 호스트 정책은 순서대로 해석되므로 구체적인 수준으로 다음과 같이 만들어야 합니다.
    * `name` -다음 라우팅 규칙이 지정된 호스트의 DNS 이름입니다. "*"를 사용하여 모든 호스트에 대한 라우팅 규칙을 만듭니다.
    * `routes` - 특정 호스트에 대한 정책의 배열입니다.
        * `match` - `path`에 기반하여 적용할 이 규칙에 대해 들어오는 요청 구조의 사양입니다.
            * `path` - `value`(들어오는 URI), `rewrite`(요청을 전달하는 방법) 및 `type` (현재 "접두사"에만 사용 가능)을 포함합니다.
            * `header` - 요청이 경로 사양과 일치하면 요청의 헤더에 일치시킬 헤더 값의 선택적 배열입니다.
              * 각 항목은 `name`(일치시킬 헤더의 문자열 이름), `value`(요청에서 헤더의 문자열 값) 및 `type`(현재 "Exact" 에만 사용 가능)을 포함합니다.
        * `destination` - 요청이 일치하는 경우 `applicationName`, `serviceName` 및 `endpointName`를 사용하여 지정된 이 대상으로 라우팅됩니다.

포트 80을 통해 들어오는 요청을 이 네트워크에서 앱으로 처리되는 모든 호스트에 적용할 HTTP 라우팅 규칙의 예제입니다. 요청 URL이 `<IPAddress>:80/pickme/<requestContent>`과 같은 경로 사양과 일치하는 구조를 가지고 있으면 `myListener` 엔드포인트로 이동합니다.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>게이트웨이 리소스에 대한 샘플 구성 

전체 게이트웨이 리소스 구성은 다음과 같습니다 ([메시 예제 리포지토리](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)에서 사용할 수 있는 수신 예제에서 참고 ).

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

이 게이트웨이는 포트 80에서 수신 대기하는 Linux 애플리케이션, 두 개 이상의 서비스로 구성된 "meshAppLinux" , "helloWorldService" 및 "counterService"에 대해 구성됩니다. 들어오는 요청의 URL 구조에 따라 요청을 이러한 서비스 중 하나로 라우팅합니다. 
* "\<Ip 주소 >: 80/helloWorld/\<요청\>"는 helloWorldService에서 "helloWorldListener"에 보내는 요청을 초래 합니다. 
* "\<Ip 주소 >: 80/카운터/\<요청\>"는 counterService에서 "counterListener"에 보내는 요청을 초래 합니다. 

## <a name="next-steps"></a>다음 단계
* [수신 예제](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress)를 배포하여 실행 중인 게이트웨이를 참조합니다.
