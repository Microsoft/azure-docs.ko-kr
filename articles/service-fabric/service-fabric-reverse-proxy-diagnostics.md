---
title: Azure Service Fabric 역방향 프록시 진단 | Microsoft Docs
description: 역방향 프로세스에서의 요청 처리 모니터링 및 진단 방법에 대해 알아봅니다.
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
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: c9c8c649208cff95f4ee515d39cc8cca3e2c64bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726845"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>역방향 프로세스에서의 요청 처리 모니터링 및 진단 

Service Fabric 5.7 버전부터 역방향 프록시 이벤트를 컬렉션에 사용할 수 있습니다. 이벤트는 역방향 프록시에서의 요청 처리 실패와 관련한 오류 이벤트만을 담은 한 채널과, 성공한 요청과 실패한 요청 모두에 대한 항목을 모두 포함하는 상세 이벤트가 담긴 두 번째 채널 등, 두 채널로 제공됩니다.

로컬 및 Azure Service Fabric 클러스터에서 이러한 채널로부터의 이벤트 수집을 활성화하려면 [역방향 프록시 이벤트 수집](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)을 참조하세요.

## <a name="troubleshoot-using-diagnostics-logs"></a>진단 로그를 사용한 문제 해결
다음은 발생할 수 있는 일반적인 실패 로그를 해석하는 방법에 대한 몇 가지 예입니다.

1. 역방향 프록시에서 응답 상태 코드 504(시간 제한) 반환

    서비스가 요청 시간 제한 안에 응답하지 못하는 것이 한 원인이 될 수 있습니다.
   아래의 첫 번째 이벤트는 역방향 프록시에서 수신한 요청의 상세 정보를 기록합니다. 
   두 번째 이벤트는 서비스로 전달하는 도중 "internal error = ERROR_WINHTTP_TIMEOUT"으로 인해 요청이 실패했음을 표시합니다. 

    페이로드는 다음을 포함합니다.

   * **traceId**: 이 GUID는 단일 요청에 해당 하는 모든 이벤트를 상관 관계를 사용할 수 있습니다. 아래 두 이벤트에서 traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**은 이들이 같은 요청에 속함을 나타냅니다.
   * **requestUrl**: URL (역방향 프록시 URL)을 요청을 보냈습니다.
   * **verb**: HTTP 동사입니다.
   * **remoteAddress**: 요청을 보내는 클라이언트의 주소입니다.
   * **resolvedServiceUrl**: 들어오는 요청을 해결 된 서비스 끝점 URL입니다. 
   * **errorDetails**: 오류에 대 한 추가 정보입니다.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. 역방향 프록시가 응답 상태 코드 404(없음)를 반환합니다. 
    
    다음은 역방향 프록시가 일치하는 서비스 엔드포인트를 찾지 못해 404를 반환하는 예제입니다.
    몇 가지 흥미로운 페이로드 항목은 다음과 같습니다.
   * **processRequestPhase**: 오류가 발생 한 요청을 처리 하는 도중 ***TryGetEndpoint*** 즉, 전달할 서비스 엔드포인트를 가져오려고 시도하는 중에 실패가 발생한 단계입니다. 
   * **errorDetails**: 끝점 검색 조건을 나열합니다. 여기서 listenerName specified = **FrontEndListener**이고 복제본 엔드포인트 목록은 이름 **OldListener**를 포함하는 수신기만 포함합니다.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     역방향 프록시가 404를 반환할 수 있는 또 다른 예로 찾을 수 없습니다. ApplicationGateway\Http configuration 매개 변수 **SecureOnlyMode** 역방향 프록시를 사용 하 여 true로 설정에서 수신 대기 **HTTPS**이지만 모든 복제본 끝점 (HTTP 수신) 보호 되지 않습니다.
     요청을 전달하기 위해 HTTPS에서 수신 대기하는 엔드포인트를 찾을 수 없으므로 역방향 프록시가 404를 반환합니다. 이벤트 페이로드의 매개 변수를 분석하면 문제의 범위를 좁히는 데 도움이 됩니다.
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. 역방향 프록시 요청이 시간 제한 오류로 실패합니다. 
    이벤트 로그에 수신된 요청 상세 정보와 이벤트가 포함됩니다(여기에 표시 안 됨).
    다음 이벤트에서는 서비스가 404 상태 코드로 응답했음을 표시하고 역방향 프록시가 재확인을 시작합니다. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    모든 이벤트를 수집할 때 모든 확인 및 전달 시도를 나타내는 이벤트의 학습을 확인하게 됩니다.
    이 시리즈의 마지막 이벤트에서는 성공한 확인 횟수와 함께, 시간 제한으로 요청 처리에 실패했음을 나타냅니다. 
    
    > [!NOTE]
    > 기본적으로 상세 채널 이벤트 수집은 비활성 상태를 유지하고 필요할 때 문제 해결을 위해 활성화하는 것이 좋습니다.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    위험/오류 이벤트에만 수집을 활성화할 경우 시간 제한 및 해결 시도 수에 대한 상세 정보가 있는 하나의 이벤트가 표시됩니다. 
    
    404 상태 코드를 사용자에게 다시 전송하려고 하는 서비스는 응답에 "X-ServiceFabric" 헤더를 추가해야 합니다. 이 헤더가 응답에 추가되면 역방향 프록시가 상태 코드를 클라이언트로 다시 보냅니다.  

4. 클라이언트가 요청에서 분리된 경우.

    다음 이벤트는 역방향 프록시가 클라이언트에 응답을 전달하지만 클라이언트가 분리된 경우 기록됩니다.

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. 역방향 프록시는 404 FABRIC_E_SERVICE_DOES_NOT_EXIST를 반환합니다.

    서비스 매니페스트에서 서비스 엔드포인트에 대해 URI 체계를 지정하지 않으면 FABRIC_E_SERVICE_DOES_NOT_EXIST 오류가 반환됩니다.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    이 문제를 해결하려면 매니페스트에 URI 체계를 지정합니다.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> 웹 소켓 요청 처리와 관련한 이벤트는 현재 기록되지 않습니다. 이것은 다음 릴리스에 추가될 것입니다.

## <a name="next-steps"></a>다음 단계
* Azure 클러스터에서의 로그 수집을 활성화하기 위해 [Windows Azure 진단을 사용한 이벤트 집계 및 수집](service-fabric-diagnostics-event-aggregation-wad.md) 
* Visual Studio에서 Service Fabric 이벤트를 보려면 [로컬 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)을 참조하세요.
* 다른 서비스 인증서 유효성 검사 옵션을 사용하여 보안 역방향 프록시를 구성하려면 [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services)(보안 서비스에 연결하도록 역방향 프록시 구성)에서 Azure Resource Manager 템플릿 샘플을 참조하세요.
* 자세한 내용은 [Service Fabric 역방향 프록시](service-fabric-reverseproxy.md)를 참조하세요.
