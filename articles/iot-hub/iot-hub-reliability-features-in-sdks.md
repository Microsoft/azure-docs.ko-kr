---
title: Azure IoT Hub 디바이스 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법
description: Azure IoT Hub 디바이스 SDK를 사용할 때 디바이스 연결 및 메시지를 개선하는 방법을 알아봅니다.
services: iot-hub
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 9180c27e64f26c05e6e16007b74f9aa8a98bcfe5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440304"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Azure IoT Hub 디바이스 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징 관리

이 문서에서는 고급 지침을 제공하여 복원력이 향상된 디바이스 애플리케이션을 설계하는 데 도움이 됩니다. 또한 Azure IoT 디바이스 SDK에서 연결 및 신뢰할 수 있는 메시징 기능을 활용하는 방법을 보여줍니다. 이 지침의 목적은 다음과 같은 시나리오를 관리하는 데 도움이 되는 것입니다.

* 삭제된 네트워크 연결 수정

* 서로 다른 네트워크 연결 간의 전환

* 서비스의 일시적 연결 오류로 인한 다시 연결

세부 정보 구현은 언어에 따라 달라질 수 있습니다. 자세한 내용은 API 설명서 또는 특정한 SDK를 참조하세요.

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>복원력을 위한 디자인

IoT 디바이스는 주로 불연속 네트워크 연결 또는 불안정한 네트워크 연결(예: GSM 또는 위성)을 사용합니다. 일시적인 서비스 가용성 및 인프라 수준 또는 일시적 오류 때문에 디바이스가 클라우드 기반 서비스와 상호 작용할 때 오류가 발생할 수 있습니다. 장치에서 실행 되는 응용 프로그램 연결, 다시 연결 및 메시지 송수신에 대 한 재시도 논리에 대 한 메커니즘을 관리 해야 합니다. 또한 다시 시도 전략 요구 사항은 디바이스의 IoT 시나리오, 컨텍스트, 기능에 따라 크게 달라집니다.

Azure IoT Hub 디바이스 SDK의 목적은 클라우드-디바이스 및 디바이스-클라우드에서 연결 및 통신을 간소화하는 데 있습니다. 이러한 SDK는 Azure IoT Hub에 연결하는 강력한 방법 및 메시지 송신 및 수신에 대한 포괄적인 옵션 세트를 제공합니다. 개발자가 기존 구현을 수정하여 지정된 시나리오에 대한 향상된 재시도 전략을 사용자 지정할 수도 있습니다.

연결 및 신뢰할 수 있는 메시징을 지원하는 관련 SDK 기능은 다음 섹션에서 설명합니다.

## <a name="connection-and-retry"></a>연결 및 재시도

이 섹션에서는 연결을 관리할 때 사용할 수 있는 다시 연결 하 고 다시 시도 패턴의 개요를 제공 합니다. 또한 디바이스 애플리케이션에서 서로 다른 재시도 정책을 사용하기 위한 구현 지침에 대해 자세히 설명하고 디바이스 SDK에서 관련 API를 나열합니다.

### <a name="error-patterns"></a>오류 패턴

연결 실패는 다음과 같은 여러 수준에서 발생할 수 있습니다.

* 네트워크 오류: 연결이 끊어진 소켓 및 이름 확인 오류

* HTTP, AMQP 및 MQTT 전송에 대한 프로토콜 수준 오류: 연결 분리 또는 세션 만료

* 로컬 실수에서 발생하는 애플리케이션 수준 오류: 잘못된 자격 증명 또는 서비스 동작(예: 할당량 또는 제한 초과)

디바이스 SDK는 세 수준의 오류를 모두 검색합니다. OS 관련 오류 및 하드웨어 오류는 디바이스 SDK에서 검색 및 처리되지 않습니다. SDK 디자인은 Azure 아키텍처 센터의 [일시적 오류 처리 지침](/azure/architecture/best-practices/transient-faults#general-guidelines)을 기반으로 합니다.

### <a name="retry-patterns"></a>재시도 패턴

연결 오류가 검색되면 다음 단계에서 재시도 프로세스에 대해 설명합니다.

1. SDK가 네트워크, 프로토콜 또는 애플리케이션에서 오류 및 관련 오류를 검색합니다.

2. SDK는 오류 필터를 사용하여 오류 유형을 결정하고 재시도가 필요한지를 결정 합니다.

3. SDK가 **복구할 수 없는 오류**를 검색하는 경우 연결, 송신 및 수신과 같은 작업은 중지됩니다. SDK는 사용자에게 알립니다. 복구할 수 없는 오류의 예제에는 인증 오류 및 잘못된 엔드포인트 오류가 포함됩니다.

4. SDK는 **복구할 수 있는 오류**를 검색하는 경우 정의된 제한 시간이 경과할 때까지 지정된 재시도 정책에 따라 다시 시도합니다.  SDK는 기본적으로 **지터를 사용한 지수적 백오프** 재시도 정책을 사용합니다.
5. 정의된 시간 제한이 만료되면 SDK는 연결 또는 전송 시도를 중지하고 사용자에게 알립니다.

6. SDK를 사용하면 사용자가 콜백을 연결하여 연결 상태 변경을 수신할 수 있습니다.

SDK는 다음 세 가지 재시도 정책을 제공합니다.

* **지터를 사용한 지수적 백오프**: 이 기본 재시도 정책은 적극적으로 시작하고 최대 지연 시간에 도달할 때까지 시간이 지남에 따라 저하되는 경향이 있습니다. 이 디자인은 [Azure 아키텍처 센터의 재시도 지침](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)을 기반으로 합니다. 

* **사용자 지정 재시도**: 일부 SDK 언어의 경우 시나리오에 더 적합한 사용자 지정 재시도 정책을 설계한 다음, RetryPolicy에 삽입할 수 있습니다. 사용자 지정 재시도는 C SDK에서 사용할 수 없습니다.

* **재시도 안 함**: 재시도 정책을 “재시도 안 함”으로 설정하여 재시도 논리를 사용하지 않도록 설정할 수 있습니다. SDK는 한 번 연결을 시도하고, 연결이 설정되면 한 번 메시지를 보냅니다. 이 정책은 일반적으로 대역폭 또는 비용 문제가 있는 시나리오에 사용됩니다. 이 옵션을 선택하면 보내지 못한 메시지가 손실되며 복구될 수 없습니다.

### <a name="retry-policy-apis"></a>재시도 정책 API

   | SDK) | SetRetryPolicy 메서드 | 정책 구현 | 구현 지침 |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **기본값**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**사용자 지정:** 사용 가능한 [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) 사용<BR>**재시도 안 함**: [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS 구현](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | 자바| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **기본값**: [ExponentialBackoffWithJitter 클래스](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**사용자 지정:** [RetryPolicy 인터페이스](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java) 구현<BR>**재시도 안 함**: [NoRetry 클래스](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java 구현](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **기본값**: [ExponentialBackoff 클래스](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**사용자 지정:** [IRetryPolicy 인터페이스](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet) 구현<BR>**재시도 안 함**: [NoRetry 클래스](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# 구현](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | 노드| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **기본값**: [ExponentialBackoffWithJitter 클래스](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**사용자 지정:** [RetryPolicy 인터페이스](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest) 구현<BR>**재시도 안 함**: [NoRetry 클래스](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Node 구현](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

다음 코드 샘플은 이 흐름을 보여 줍니다.

#### <a name="net-implementation-guidance"></a>.NET 구현 지침

다음 코드 샘플은 기본 재시도 정책을 정의하고 설정하는 방법을 보여 줍니다.

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

코드가 즉각 실패하는 경우 높은 CPU 사용량을 방지하기 위해 재시도가 제한됩니다. 예를 들어 네트워크 또는 대상 경로가 없을 경우입니다. 다음 재시도를 실행하는 최소 시간은 1초입니다.

서비스가 제한 오류로 응답하는 경우 재시도 정책이 서로 다르며 공용 API를 통해 변경할 수 없습니다.

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

재시도 메커니즘은 현재 4분으로 설정된 `DefaultOperationTimeoutInMilliseconds` 이후에 중지됩니다.

#### <a name="other-languages-implementation-guidance"></a>기타 언어 구현 지침

다른 언어의 코드 샘플은 다음 구현을 문서를 검토합니다. 리포지토리에는 재시도 정책 API의 사용법을 보여 주는 샘플이 들어 있습니다.

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>다음 단계

* [디바이스 및 서비스 SDK 사용](./iot-hub-devguide-sdks.md)

* [C용 IoT 디바이스 SDK 사용](./iot-hub-device-sdk-c-intro.md)

* [제한된 디바이스에 대한 개발](./iot-hub-devguide-develop-for-constrained-devices.md)

* [모바일 디바이스용 개발](./iot-hub-how-to-develop-for-mobile-devices.md)

* [디바이스 연결 끊김 문제 해결](iot-hub-troubleshoot-connectivity.md)