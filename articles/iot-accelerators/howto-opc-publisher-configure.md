---
title: OPC 게시자 구성 - Azure | Microsoft Docs
description: 이 문서에서는 OPC UA 노드 데이터 변경, 게시할 OPC UA 이벤트 및 원격 분석 형식을 지정하도록 OPC 게시자를 구성하는 방법을 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0ebbf0d41c05f71c571d9665903ba4ba44f71bd0
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198806"
---
# <a name="configure-opc-publisher"></a>OPC 게시자 구성

다음을 지정하도록 OPC 게시자를 구성할 수 있습니다.

- 게시할 OPC UA 노드 데이터 변경 내용
- 게시할 OPC UA 이벤트
- 원격 분석 형식

구성 파일을 사용하거나 메서드 호출을 사용하여 OPC 게시자를 구성할 수 있습니다.

## <a name="use-configuration-files"></a>구성 파일 사용

이 섹션에서는 구성 파일을 사용하여 OPC UA 노드 게시를 구성하는 옵션을 설명합니다.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>구성 파일을 사용하여 데이터 변경 내용 게시 구성

게시하도록 OPC UA 노드를 구성하는 가장 쉬운 방법은 구성 파일을 사용하는 것입니다. 구성 파일 형식은 리포지토리의 [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json)에 문서화되어 있습니다.

시간이 지나면서 구성 파일 구문이 변경되었습니다. OPC 게시자는 계속 이전 형식을 읽지만 구성을 저장할 때 이 형식을 최신 형식으로 변환합니다.

다음 예제에서는 구성 파일 형식을 보여 줍니다.

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>구성 파일을 사용하여 이벤트 게시 구성

OPC UA 이벤트를 게시하려면 데이터 변경 내용에 동일한 구성 파일을 사용합니다.

다음 예제에서는 [SimpleEvents 서버](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server)에서 생성한 이벤트의 게시를 구성하는 방법을 보여 줍니다. SimpleEvents 서버는 [OPC Foundation 리포지토리](https://github.com/OPCFoundation/UA-.NETStandard)에서 찾을 수 있습니다.

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>메서드 호출 사용

이 섹션에서는 OPC 게시자를 구성하는 데 사용할 수 있는 메서드 호출을 설명합니다.

### <a name="configure-using-opc-ua-method-calls"></a>OPC UA 메서드 호출 사용 구성

OPC 게시자에는 포트 62222에서 액세스할 수 있는 OPC UA 서버가 포함됩니다. 호스트 이름이 **publisher**이면 엔드포인트 URI는 `opc.tcp://publisher:62222/UA/Publisher`입니다.

이 엔드포인트는 다음 네 가지 메서드를 공개합니다.

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>IoT Hub 직접 메서드 호출 사용 구성

OPC 게시자는 다음 IoT Hub 직접 메서드 호출을 구현합니다.

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

메서드 요청 및 응답의 JSON 페이로드 형식은 [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs)에 정의됩니다.

모듈에서 알 수 없는 메서드를 호출하면 메서드가 구현되지 않음을 나타내는 문자열이 표시됩니다. 모듈을 ping하는 방법으로 알 수 없는 메서드를 호출할 수 있습니다.

### <a name="configure-username-and-password-for-authentication"></a>인증을 위해 사용자 이름 및 암호 구성

인증 모드는 IoT Hub 직접 메서드 호출을 통해 설정할 수 있습니다. 페이로드에는 **OpcAuthenticationMode** 속성 및 사용자 이름과 암호가 포함되어야 합니다.

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

암호는 IoT Hub 워크로드 클라이언트에서 암호화되며 게시자의 구성에 저장됩니다. 인증을 다시 익명으로 변경하려면 다음 페이로드와 함께 메서드를 사용합니다.

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

**OpcAuthenticationMode** 속성이 페이로드에 설정되어 있지 않으면 인증 설정이 구성에서 변경되지 않은 상태로 유지됩니다.

## <a name="configure-telemetry-publishing"></a>원격 분석 게시 구성

OPC 게시자는 게시된 노드에서 값 변경 알림을 수신하면 IoT Hub로 전송되는 JSON 형식 메시지를 생성합니다.

구성 파일을 사용하여 이 JSON 형식 메시지의 콘텐츠를 구성할 수 있습니다. 구성 파일이 `--tc` 옵션을 사용하여 지정되지 않은 경우에는 [연결된 팩터리 솔루션 가속기](https://github.com/Azure/azure-iot-connected-factory)와 호환되는 기본 구성이 사용됩니다.

OPC 게시자가 메시지를 일괄 처리하도록 구성되면 해당 메시지는 유효한 JSON 배열로 전송됩니다.

원격 분석은 다음 원본에서 파생됩니다.

- 노드에 대한 OPC 게시자 노드 구성
- OPC 게시자가 알림을 받은 OPC UA 스택의 **MonitoredItem** 개체
- 데이터 값 변경에 대한 세부 정보를 제공하는 이 알림에 전달된 인수

JSON 형식 메시지에 삽입되는 원격 분석은 선택된 이 개체의 중요 속성입니다. 추가 속성이 필요한 경우 OPC 게시자 코드베이스를 변경해야 합니다.

구성 파일의 구문은 다음과 같습니다.

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is omitted (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

지금까지 OPC 게시자를 구성하는 방법을 알아보았고, 제안된 다음 단계에서는 [OPC 게시자를 실행](howto-opc-publisher-run.md)하는 방법을 알아봅니다.
