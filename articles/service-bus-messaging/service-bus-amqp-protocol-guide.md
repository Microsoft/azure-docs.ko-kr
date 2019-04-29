---
title: Azure Service Bus 및 Event Hubs 프로토콜 가이드의 AMQP 1.0 | Microsoft Docs
description: Azure Service Bus 및 Event Hubs의 AMQP 1.0 식 및 설명에 대한 프로토콜 가이드
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c99f4491af8fe3e5f0f0ed7a264995ae3ec5911f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749447"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Azure Service Bus 및 Event Hubs 프로토콜 가이드의 AMQP 1.0

Advanced Message Queueing Protocol 1.0은 비동기적으로 안전하고 안정적으로 두 대상 간에 메시지를 전송하기 위한 표준화된 프레이밍 및 전송 프로토콜입니다. 또한 Azure Service Bus 메시징 및 Azure Event Hubs의 기본 프로토콜입니다. 두 서비스 모두 HTTPS를 지원합니다. 지원되는 소유 SBMP 프로토콜은 AMQP를 기준으로 단계적으로 중단되고 있습니다.

AMQP 1.0은 금융 서비스 업계를 나타내는 많은 메시징 미들웨어 사용자(예: JP Morgan Chase)와 미들웨어 공급업체(예: Microsoft 및 Red Hat)를 연결한 광범위한 업계 공동 작업의 결과입니다. AMQP 프로토콜 및 확장 사양에 대한 기술 표준화 포럼은 OASIS이며 국제 표준 ISO/IEC 19494와 같은 공식 승인을 받았습니다.

## <a name="goals"></a>목표

이 문서에서는 현재 OASIS AMQP 기술 위원회에서 최종 마무리 단계에 있는 일부 확장 사양 초안과 AMQP 1.0 메시징 사양의 핵심 개념을 요약해서 설명하며, 이러한 사양을 토대로 Azure Service Bus가 구현되고 구축되는 방식을 알아봅니다.

이 문서는 모든 플랫폼에서 기존 AMQP 1.0 클라이언트 스택을 사용하는 개발자가 AMQP 1.0을 통해 Azure Service Bus와 상호 작용할 수 있도록 하기 위해 작성되었습니다.

일반적인 범용 AMQP 1.0 스택(예: Apache Proton 또는 AMQP.NET Lite)은 이미 모든 핵심 AMQP 1.0 프로토콜을 구현하고 있습니다. 이러한 기본적인 제스처는 상위 수준의 API로 래핑되기도 합니다. 그뿐 아니라 Apache Proton은 두 가지 명령적 Messenger API와 반응적 Reactor API를 모두 제공합니다.

다음 논의에서는 AMQP 연결, 세션 및 링크의 관리와 프레임 전송 및 흐름 제어의 처리가 해당 스택(예: Apache Proton-C)에서 처리되며, 애플리케이션 개발자의 많은 주의가 필요하지 않다고 가정합니다. 각각 `send()` 및 `receive()` 작업 모양을 갖는 특정 형식의 *발신자*와 *수신자* 추상화 개체를 만들고 연결하는 기능과 같은 몇 가지 API 기본형이 있다고 추상적으로 가정해보겠습니다.

메시지 찾아보기 또는 세션 관리와 같은 Azure Service Bus의 고급 기능을 설명할 때 AMQP 용어로 설명하기도 하지만 이와 같이 가정된 API 추상화를 기반으로 계층화된 의사 구현으로 설명하기도 합니다.

## <a name="what-is-amqp"></a>AMQP란?

AMQP는 프레이밍 및 전송 프로토콜입니다. 프레이밍은 네트워크 연결 방향으로 흐르는 이진 데이터 스트림의 구조를 제공한다는 것을 의미합니다. 이 구조는 *프레임*이라고 부르는 고유한 데이터 블록이 연결된 대상 간에 교환될 수 있도록 윤곽을 제공합니다. 전송 기능은 두 통신 당사자가 프레임이 전송될 때와 전송이 완료된 것으로 간주될 때에 대한 공유되는 이해를 설정할 수 있도록 합니다.

일부 메시지 브로커에서는 아직 사용되고 있는 AMQP 작업 그룹에서 생성한 만료된 이전 초안 이전 버전과 달리, 이 작업 그룹의 표준화된 최종 AMQP 1.0 프로토콜에서는 메시지 브로커의 존재 또는 메시지 브로커 내 엔터티에 대한 특정 토폴로지의 존재를 규정하지 않습니다.

이 프로토콜은 Azure Service Bus와 마찬가지로, 큐 및 게시/구독 엔터티를 지원하는 메시지 브로커와의 상호 작용을 위해 대칭 피어-투-피어 통신에 사용될 수 있습니다. 또한 Azure Event Hubs의 경우와 마찬가지로 상호 작용 패턴이 일반 큐와 다른 메시징 인프라와의 상호 작용에도 사용될 수 있습니다. 이벤트 허브는 이벤트가 전송될 때는 큐처럼 작동하지만, 이벤트가 읽혀질 때 직렬 저장소 서비스처럼 작동합니다. 따라서 테이프 드라이브와 유사한 부분이 있습니다. 클라이언트는 사용 가능한 데이터 스트림으로의 오프셋을 선택한 다음 모든 이벤트를 최신 해당 오프셋에서 사용 가능한 최신 오프셋으로 처리합니다.

AMQP 1.0 프로토콜은 확장 가능하게 설계되었으며 사양을 추가하여 기능을 향상할 수 있습니다. 이 문서에서 설명하는 세 가지 확장 사양이 이러한 경우를 보여 줍니다. 기존 HTTPS/WebSocket 인프라를 통한 통신에서는 네이티브 AMQP TCP 포트를 구성하기가 어려울 수 있습니다. WebSocket을 통해 AMQP를 계층화하는 방법은 바인딩 사양에서 정의됩니다. 관리를 위해 요청/응답 방식으로 메시지 인프라와 상호 작용하거나 고급 기능을 제공하기 위해 AMQP 관리 사양은 필요한 기본적인 상호 작용 기본 형식을 정의합니다. 페더레이션된 인증 모델 통합을 위해 AMQP 클레임 기반 보안 사양은 권한 부여 토큰을 연결하고 링크와 연결된 이러한 토큰을 갱신하는 방법을 정의합니다.

## <a name="basic-amqp-scenarios"></a>기본 AMQP 시나리오

이 섹션에서는 연결, 세션 및 링크의 생성과 큐, 토픽 및 구독 같은 Service Bus 엔터티와의 메시지 송수신 등 Azure Service Bus에서 AMQP 1.0을 사용하는 기본적인 방법을 설명합니다.

AMQP 작동 방식을 알기 위한 가장 신뢰할 수 있는 소스는 AMQP 1.0 사양이지만, 이 사양은 구현을 정확히 안내하기 위해 작성되었으며 프로토콜 학습용은 아닙니다. 이 섹션에서는 Service Bus가 AMQP 1.0을 사용하는 방법을 설명하는 데 필요한 다양한 용어를 중점적으로 소개합니다. AMQP를 좀 더 포괄적으로 소개하고 AMQP 1.0을 광범위하게 논의하려는 경우 [이 비디오 과정][this video course]을 검토할 수 있습니다.

### <a name="connections-and-sessions"></a>연결 및 세션

AMQP는 통신하는 프로그램을 *컨테이너*라고 합니다. 여기에는 해당 컨테이너 내의 통신하는 엔터티인 *노드*가 포함됩니다. 큐는 이러한 노드가 될 수 있습니다. AMQP는 멀티플렉싱을 허용합니다. 따라서 노드 간의 많은 통신 경로에 대해 단일 연결을 사용할 수 있습니다. 예를 들어 애플리케이션 클라이언트는 동일한 네트워크 연결을 통해 한 큐에서 수신하고 다른 큐로 전송하는 작업을 동시에 진행할 수 있습니다.

![][1]

따라서 네트워크 연결은 컨테이너에 고정됩니다. 이러한 연결은 클라이언트 역할의 컨테이너가 인바운드 TCP 연결을 수신하고 수락하는 수신자 역할의 컨테이너에 대해 아웃바운드 TCP 소켓 연결을 설정하는 것부터 시작됩니다. 연결 핸드셰이크에는 프로토콜 버전 협상, 전송 수준 보안(TLS/SSL) 사용에 대한 선언 또는 협상, SASL을 기반으로 하는 연결 범위의 인증/권한 부여 핸드셰이크가 포함됩니다.

Azure Service Bus에서는 항상 TLS를 사용해야 합니다. TLS는 TCP 포트 5671을 통한 연결을 지원하지만, TCP 연결은 AMQP 프로토콜 핸드셰이크를 시작하기 전에 먼저 TLS에 오버레이됩니다. 또한 TLS는 TCP 포트 5672를 통한 연결을 지원하지만 서버는 AMQP 규정 모델을 사용하여 TLS에 대한 필수 연결 업그레이드를 즉시 제공합니다. AMQP WebSocket 바인딩은 AMQP 5671 연결에 해당하는 TCP 포트 443을 통해 터널을 만듭니다.

Service Bus는 연결 및 TLS를 설정한 후 다음과 같은 두 가지 SASL 메커니즘 옵션을 제공합니다.

* SASL PLAIN은 일반적으로 서버에 사용자 이름 및 암호 자격 증명을 전달하는 데 사용됩니다. Service Bus에는 계정이 없지만, 권한을 부여하고 키와 연결되어 있는 [공유 액세스 보안 규칙](service-bus-sas.md)이 있습니다. 규칙의 이름은 사용자 이름으로 사용되고 키(base64로 인코딩된 텍스트)는 암호로 사용됩니다. 선택한 규칙에 연결된 권한에 따라 연결에서 허용되는 작업이 제어됩니다.
* SASL ANONYMOUS는 클라이언트가 이후에 설명되는 CBS(클레임 기반 보안) 모델을 사용하려는 경우 SASL 권한 부여를 무시하는 데 사용됩니다. 이 옵션을 사용하면 클라이언트가 CBS 엔드포인트와 상호 작용할 수 있는 정도의 짧은 시간 동안 클라이언트 연결이 익명으로 설정될 수 있으며 CBS 핸드셰이크를 완료해야 합니다.

전송 연결이 설정된 후에 각 컨테이너는 자발적으로 처리할 최대 프레임 크기를 선언하며, 유휴 시간이 초과한 후 연결에 대해 활동이 없는 경우 일반적으로 연결을 끊습니다.

또한 지원되는 동시 채널 수를 선언합니다. 채널은 연결 위에 있는 단방향, 아웃바운드, 가상 전송 경로입니다. 세션은 상호 연결된 각 컨테이너의 채널로 양방향 통신 경로를 구성합니다.

세션에는 기간에 따른 흐름 제어 모델이 있습니다. 세션이 만들어지면 각 상대방은 수신 기간에 수락할 프레임 수를 선언합니다. 상대방이 프레임을 교환할 때 전송된 프레임이 해당 기간을 채우고, 기간이 만료되면 기간이 다시 설정되거나 *흐름 수행*을 사용하여 확장될 때까지 전송이 중지됩니다(*수행*은 두 상대방 간에 교환되는 프로토콜 수준 제스처를 나타내는 AMQP 용어임).

이 기간 기반 모델은 TCP의 기간 기반 흐름 제어 개념과 유사하지만 소켓 내 세션 수준에서 진행됩니다. 이 프로토콜의 경우 여러 개의 동시 세션을 허용하는 개념이 존재하므로 고속도로의 경우처럼 우선 순위가 높은 트래픽이 제한된 일반 트래픽보다 빠르게 전달됩니다.

Azure Service Bus는 현재 각 연결에 대해 정확히 하나의 세션을 사용합니다. Service Bus 표준 및 Event Hubs에 대한 Service Bus 최대 프레임 크기는 262,144바이트(256K바이트)입니다. Service Bus 프리미엄의 경우는 1,048,576(1MB)입니다. Service Bus는 세션 수준의 특정 제한 기간을 적용하지 않지만 링크 수준 흐름 제어의 일부로 해당 기간을 정기적으로 다시 설정합니다([다음 섹션](#links) 참조).

연결, 채널 및 세션은 사용 후 삭제됩니다. 기본 연결이 축소되면 연결, TLS 터널, SASL 권한 부여 컨텍스트 및 세션을 다시 설정해야 합니다.

### <a name="links"></a>링크

AMQP는 링크를 통해 메시지를 전송합니다. 링크는 세션을 통해 만들어진 통신 경로로, 한 방향으로 메시지를 전송할 수 있도록 합니다. 전송 상태 협상은 링크를 통해 진행되며 연결 당사자 간에 양방향으로 진행됩니다.

![][2]

언제든지 기존 세션을 통해 한쪽 컨테이너에서 링크를 만들 수 있습니다. 이러한 특성은 이 AMQP가 전송 시작 및 전송 경로가 소켓 연결을 만드는 당사자의 배타적 권한에 해당하는 다른 프로토콜(예: HTTP 및 MQTT)과 다른 점입니다.

링크 시작 컨테이너는 반대쪽 컨테이너에 링크를 수락할 것을 요청하고 발신자 또는 수신자의 역할 중에서 선택합니다. 따라서 한쪽 컨테이너가 단방향 또는 양방향 통신 경로를 만들기 시작하고 다른 컨테이너는 링크의 쌍으로 모델링됩니다.

링크의 이름이 지정되고 노드와 연결됩니다. 맨 앞 부분에서 설명한 것처럼 노드는 컨테이너 내 통신 엔터티입니다.

Service Bus에서 노드는 큐, 토픽, 구독 또는 큐나 구독의 배달 못한 편지 하위 큐에 해당합니다. 따라서 AMQP에 사용되는 노드는 Service Bus 네임스페이스 내에 있는 엔터티의 상대적 이름을 갖습니다. 큐 이름이 `myqueue`이면 AMQP 노드 이름도 같습니다. 토픽 구독은 "구독" 리소스 컬렉션으로 정렬되어 HTTP API 규칙을 따르므로 구독 **sub** 또는 토픽 **mytopic**의 AMQP 노드 이름은 **mytopic/subscriptions/sub**입니다.

링크를 만들기 위해서는 연결하는 클라이언트가 로컬 노드 이름을 사용해야 합니다. Service Bus는 이러한 노드 이름을 강제적으로 규정하지 않으며 해석하지 않습니다. AMQP 1.0 클라이언트 스택은 일반적으로 클라이언트의 범위에서 이러한 임시 노드 이름이 고유하도록 하는 체계를 사용합니다.

### <a name="transfers"></a>전송

링크가 설정되면 해당 링크를 통해 메시지를 전송할 수 있습니다. AMQP에서는 링크를 통해 메시지를 발신자에서 수신자로 이동하는 명시적 프로토콜 제스처(*전송* 수행)를 통해 전송이 실행됩니다. 전송은 “합의에 도달하면” 완료됩니다. 즉, 양쪽 당사자가 해당 전송의 결과에 대해 공유된 이해를 설정해야 합니다.

![][3]

가장 간단한 경우, 발신자는 "합의 전" 메시지를 보내도록 선택할 수 있습니다. 즉, 클라이언트는 결과에 관심이 없고 수신자가 작업 결과에 대한 어떤 피드백도 제공하지 않는 경우를 의미합니다. 이 모드는 AMQP 프로토콜 수준의 Service Bus에서 지원되지만 클라이언트 API에서 공개되지 않습니다.

일반적인 경우는 메시지가 합의되지 않은 상태로 전송되고, 발신자가 *처리* 수행을 사용하여 수락 또는 거부를 나타내는 경우입니다. 거부는 어떤 이유로든 수신자가 메시지를 수락할 수 없을 때 발생하며, AMQP 정의한 오류 구조에 따라, 거부 메시지에는 이유에 대한 정보가 포함됩니다. Service Bus 내의 내부 오류로 인해 메시지가 거부되면 서비스는 지원 요청을 제출할 때 지원 담당자에게 진단 힌트를 제공하는 데 사용할 수 있는 해당 구조 내부의 추가 정보를 반환합니다. 오류에 대해서는 이후에 좀 더 자세히 살펴보겠습니다.

특별한 형태의 거부는 수신자가 전송에 대해 기술적으로 반대하지 않을 뿐만 아니라 전송 합의에 대해서도 관심이 없음을 나타내는 *해제된* 상태입니다. 예를 들어 메시지 배달 자체는 오류가 아니지만 메시지 처리에 따른 작업을 수행할 수 없기 때문에 메시지가 Service Bus 클라이언트에 전달된 후에 클라이언트가 메시지를 "중단"하도록 선택하는 경우가 있습니다. 해당 상태가 변형된 한 가지 경우는 메시지가 해제될 때 메시지 변경을 허용하는 *수정된* 상태입니다. 이 상태는 현재 Service Bus에서 사용되지 않습니다.

AMQP 1.0 사양은 링크 복구를 처리하는 데 특히 도움이 되는 추가 처리 상태인 *수신된* 상태를 정의합니다. 링크 복구는 이전 연결 및 세션이 손실된 경우와 새 연결 및 세션에 앞서, 링크 및 보류 중인 배달의 상태를 다시 구성할 수 있도록 합니다.

Service Bus는 링크 복구를 지원하지 않습니다. 클라이언트가 합의되지 않은 메시지 전송이 보류 중인 상태로 Service Bus에 대한 연결이 끊어지면 해당 메시지 전송이 손실되고, 클라이언트는 다시 연결하고 연결을 다시 설정하고, 전송을 다시 시도해야 합니다.

이와 같이 Service Bus 및 Event Hubs는 메시지가 저장되고 수락되었음을 발신자에게 확인시킬 수 있는 "한 번 이상" 전송을 지원하지만, AMQP 수준에서는 "정확히 한 번" 전송을 지원하지 않으므로 시스템은 링크 복구를 시도하고 메시지 전달이 중복되지 않도록 배달 상태를 계속 협상합니다.

가능한 중복 전송을 보상하기 위해 Service Bus는 큐 및 토픽에 대한 선택적 기능으로 중복 검색을 지원합니다. 중복 검색 기능은 사용자 정의 기간 동안 들어오는 모든 메시지의 메시지 ID를 기록한 후 동일한 기간 동안 동일한 메시지 ID를 사용해서 전송된 모든 메시지를 자동으로 삭제합니다.

### <a name="flow-control"></a>흐름 제어

앞서 설명한 세션 수준 흐름 제어 모델 외에, 각 링크에는 자체 흐름 제어 모델도 있습니다. 세션 수준 흐름 제어는 컨테이너가 한번에 너무 많은 프레임을 처리하지 못하게 하고, 링크 수준 흐름 제어는 링크에서 처리하려는 메시지 수 및 처리 시기가 애플리케이션에서 자동으로 결정되도록 합니다.

![][4]

링크에서 발신자에 충분한 *링크 크레딧*이 있을 때만 전송이 발생합니다. 링크 크레딧은 발신자가 링크 범위까지의 *흐름* 수행을 사용하여 설정한 카운터입니다. 발신자에게 링크 크레딧이 할당되어 있으면 발신자는 메시지를 전달하여 이 크레딧을 모두 사용하려고 시도합니다. 메시지를 배달할 때마다 남은 링크 크레딧이 1씩 줄어듭니다. 링크 크레딧이 다 소모되면 배달은 중지됩니다.

Service Bus는 수신자 역할을 수행할 때 발신자에게 충분한 링크 크레딧을 즉시 제공하므로 메시지를 즉시 전송할 수 있게 됩니다. 링크 크레딧이 사용될 때 Service Bus는 종종 발신자에게 *흐름* 수행을 전송하여 남은 링크 크레딧을 업데이트합니다.

발신자 역할일 때 Service Bus는 메시지를 전송하여 아직 처리되지 않은 링크 크레딧을 모두 소비합니다.

API 수준의 "수신" 호출은 클라이언트가 Service Bus로 보내는 *흐름* 수행으로 전환되고, Service Bus는 큐에서 사용 가능한 잠금 해제된 첫 번째 메시지를 가져와 잠근 다음 전송하여 해당 크레딧을 소비합니다. 바로 배달할 수 있는 메시지가 없는 경우 해당 특정 엔터티와 설정된 링크의 처리되지 않은 크레딧은 도착 순서대로 기록되고, 처리되지 않은 크레딧을 사용할 수 있게 될 때 메시지가 잠긴 후 전송됩니다.

전송이 최종 상태인 *수락됨*, *거부됨* 또는 *해제됨* 중 하나로 설정되면 메시지에 대한 잠금이 해제됩니다. 최종 상태가 *수락됨*이면 Service Bus에서 메시지가 제거됩니다. 해당 메시지는 Service Bus에 그대로 남아 있다가 전송이 다른 상태에 도달하면 다음 수신자에게 전달됩니다. Service Bus는 반복되는 거부 또는 해제로 인해 엔터티에 대해 허용되는 최대 배달 횟수에 도달하면 자동으로 메시지를 엔터티의 배달 못 한 편지 큐로 이동합니다.

Service Bus API는 현재 이러한 옵션을 직접적으로 제공하지 않지만, 하위 수준의 AMQP 프로토콜 클라이언트는 링크-크레딧 모델을 사용하여 대량의 링크 크레딧을 발행한 후 추가 상호 작용 없이 메시지를 사용할 수 있게 되면 메시지를 수신하여 각 수신 요청에 대해 크레딧 한 단위를 발행하는 "가져오기 스타일" 모델로 전환할 수 있습니다. [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 또는 [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) 속성 설정을 통한 가져오기가 지원됩니다. AMQP 클라이언트는 0이 아닌 크레딧을 링크 크레딧으로 사용합니다.

이 컨텍스트에서 엔터티 내 메시지 잠금 만료에 대한 클록은 메시지가 유선으로 전송될 때가 아니라 메시지를 엔터티에서 가져올 때 시작된다는 것을 이해해야 합니다. 클라이언트가 링크 크레딧을 발행하여 메시지를 수신할 준비가 되었음을 나타낼 때마다 네트워크를 통해 메시지를 능동적으로 끌어와 처리할 준비가 된 것으로 간주됩니다. 그렇지 않으면 메시지 배달도 되기 전에 메시지 잠금이 만료될 수 있습니다. 링크 크레딧 흐름 제어를 사용하게 되면 수신자에게 발송된 사용 가능한 메시지를 처리할 준비가 되었는지를 바로 알 수 있습니다.

다음 섹션에서는 다른 API 상호 작용 동안 수행 흐름의 구조적 개요를 제공합니다. 섹션마다 다른 논리 작업을 설명합니다. 이러한 상호 작용 중 일부는 "지연"될 수 있습니다. 즉, 필요할 때만 수행될 수 있습니다. 메시지 발신자를 만들어도 첫 번째 메시지가 전송 또는 요청될 때까지 네트워크 상호 작용이 발생하지 않습니다.

다음 테이블의 화살표는 수행 흐름 방향을 표시합니다.

#### <a name="create-message-receiver"></a>메시지 수신자 만들기

| 클라이언트 | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={entity name},<br/>target={client link ID}<br/>) |클라이언트는 수신자로서 엔터티에 연결합니다. |
| Service Bus는 응답하고 링크의 해당 끝을 연결합니다. |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={entity name},<br/>target={client link ID}<br/>) |

#### <a name="create-message-sender"></a>메시지 발신자 만들기

| 클라이언트 | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |작업 없음 |
| 작업 없음 |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={client link ID},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>메시지 발신자 만들기(오류)

| 클라이언트 | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |작업 없음 |
| 작업 없음 |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={numeric handle},<br/>closed=**true**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>메시지 수신자/발신자 닫기

| 클라이언트 | Service Bus |
| --- | --- |
| --> detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |작업 없음 |
| 작업 없음 |<-- detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>전송(성공)

| 클라이언트 | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |작업 없음 |
| 작업 없음 |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>전송(오류)

| 클라이언트 | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |작업 없음 |
| 작업 없음 |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>수신

| 클라이언트 | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |작업 없음 |
| 작업 없음 |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |작업 없음 |

#### <a name="multi-message-receive"></a>다중 메시지 수신

| 클라이언트 | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |작업 없음 |
| 작업 없음 |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| 작업 없음 |< transfer(<br/>delivery-id={numeric handle+1},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| 작업 없음 |< transfer(<br/>delivery-id={numeric handle+2},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |작업 없음 |

### <a name="messages"></a>메시지

다음 섹션에서는 Service Bus에서 사용되는 표준 AMQP 메시지 섹션의 속성과 이러한 속성이 어떤 Service Bus API 집합에 매핑되는지 설명합니다.

애플리케이션이 정의해야 하는 모든 속성은 AMQP의 `application-properties` 맵에 매핑되어야 합니다.

#### <a name="header"></a>머리글

| 필드 이름 | 사용 현황 | API 이름 |
| --- | --- | --- |
| 지속성 |- |- |
| 우선 순위 |- |- |
| ttl |이 메시지에 대한 TTL(Time to live) |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| delivery-count |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| 필드 이름 | 사용 현황 | API 이름 |
| --- | --- | --- |
| message-id |이 메시지에 대한 애플리케이션 정의 자유 형식 식별자입니다. 중복 검색에 사용됩니다. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Service Bus에서 해석되지 않는 애플리케이션 정의 사용자 식별자입니다. |Service Bus API를 통해 액세스할 수 없습니다. |
| to |Service Bus에서 해석되지 않는 애플리케이션 정의 대상 식별자입니다. |[To](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| 제목 |Service Bus에서 해석되지 않는 애플리케이션 정의 메시지 용도 식별자입니다. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| reply-to |Service Bus에서 해석되지 않는 애플리케이션 정의 회산 경로 식별자입니다. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Service Bus에서 해석되지 않는 애플리케이션 정의 상관 관계 식별자입니다. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type |Service Bus에서 해석되지 않는 본문에 대한 애플리케이션 정의 콘텐츠 형식 지표입니다. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Service Bus에서 해석되지 않는 본문에 대한 애플리케이션 정의 콘텐츠 인코딩 지표입니다. |Service Bus API를 통해 액세스할 수 없습니다. |
| absolute-expiry-time |메시지가 만료되는 절대 인스턴트를 선언합니다. 입력 중에는 무시되고(헤더 TTL이 확인됨), 출력 중에는 신뢰할 수 있습니다. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| creation-time |메시지가 만들어진 시간을 선언합니다. Service Bus에서 사용되지 않습니다. |Service Bus API를 통해 액세스할 수 없습니다. |
| group-id |관련된 메시지 집합에 대한 애플리케이션 정의 식별자입니다. Service Bus 세션에 사용됩니다. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| group-sequence |세션 내 메시지의 상대 시퀀스 번호를 식별하는 카운터입니다. Service Bus에서 무시됩니다. |Service Bus API를 통해 액세스할 수 없습니다. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>메시지 주석

AMQP 메시지 속성의 일부가 아니고, 메시지의 `MessageAnnotations`로 전달되는 다른 서비스 버스 메시지 속성이 몇 개 있습니다.

| 주석 맵 키 | 사용 현황 | API 이름 |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | 메시지가 엔터티에 표시되어야 하는 시간을 선언합니다. |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-partition-key | 메시지가 배치되어야 하는 파티션을 지정하는 애플리케이션 정의 키입니다. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-partition-key | 전송 큐를 통해 메시지를 전송하는 데 트랜잭션이 사용되어야 하는 경우의 애플리케이션 정의 파티션 키 값입니다. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-enqueued-time | 메시지를 큐에 넣는 데 걸리는 실제 시간을 나타내는 서비스 정의 UTC 시간입니다. 입력 시 무시됩니다. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-sequence-number | 메시지에 할당되는 서비스 정의 고유 번호입니다. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | 메시지의 큐에 넣은 서비스 정의 시퀀스 번호입니다. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-locked-until | 서비스 정의입니다. 큐/구독에서 메시지가 언제까지 잠기는지를 나타내는 날짜 및 시간입니다. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | 서비스 정의입니다. 메시지가 배달 못한 편지 큐에서 수신된 경우 원래 메시지의 원본입니다. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>트랜잭션 기능

트랜잭션 그룹은 두 개 이상의 작업을 실행 범위로 그룹화합니다. 기본적으로 이러한 트랜잭션은 지정된 작업 그룹에 속한 모든 작업이 성공 또는 실패해야 합니다.
작업은 식별자 `txn-id`로 그룹화됩니다.

트랜잭션 상호 작용의 경우 클라이언트가 그룹화되어야 하는 작업을 제어하는 `transaction controller` 역할을 합니다. Service Bus 서비스는 `transactional resource` 역할을 하며 `transaction controller`의 요청대로 작업을 수행합니다.

클라이언트와 서비스는 클라이언트가 설정한 `control link`를 통해 통신합니다. `declare` 및 `discharge` 메시지는 컨트롤러가 트랜잭션을 각각 할당하고 완료하기 위해 제어 링크를 통해 전송합니다(트랜잭션 작업의 경계를 나타내지 않음). 실제 전송/수신은 이 링크에서 수행되지 않습니다. 요청된 각 트랜잭션 작업은 원하는 `txn-id`를 사용하여 명시적으로 식별되므로 연결의 모든 링크에서 발생할 수 있습니다. 해제되지 않은 트랜잭션이 있을 때 제어 링크가 닫히면 이러한 모든 트랜잭션이 즉시 롤백되고, 이에 대한 추가적인 트랜잭션 작업을 수행하려는 시도는 실패하게 됩니다. 제어 링크의 메시지는 미리 고정되지 않아야 합니다.

모든 연결은 자체 제어 링크를 시작하여 트랜잭션을 시작하고 종료할 수 있어야 합니다. 서비스는 `coordinator`로 기능하는 특별한 대상을 정의합니다. 클라이언트/컨트롤러는 이 대상에 대한 제어 링크를 설정합니다. 제어 링크는 엔터티 경계 외부에 있습니다. 즉, 동일한 제어 링크를 사용하여 여러 엔터티의 트랜잭션을 시작하고 해제할 수 있습니다.

#### <a name="starting-a-transaction"></a>트랜잭션 시작

트랜잭션 작업을 시작하려면 컨트롤러가 코디네이터에서 `txn-id`를 가져와야 합니다. 이를 위해 `declare` 형식 메시지를 전송합니다. 선언에 성공하면 코디네이터가 다음과 같은 처리 결과로 응답을 합니다. 이 응답에 할당된 `txn-id`가 포함되어 있습니다.

| 클라이언트(컨트롤러) | | Service Bus(코디네이터) |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>... ,<br/>role=**sender**,<br/>target=**Coordinator**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=**Declared**(<br/>**txn-id**={transaction ID}<br/>))|

#### <a name="discharging-a-transaction"></a>트랜잭션 해제

컨트롤러는 코디네이터에 `discharge` 메시지를 전송하여 트랜잭션 작업을 끝냅니다. 컨트롤러는 해제 본문에 `fail` 플래그를 설정하여 트랜잭션 작업을 커밋하거나 롤백하려고 함을 알립니다. 코디네이터가 해제를 완료할 수 없는 경우 `transaction-error`를 전달하는 이 결과와 함께 메시지가 거부됩니다.

> 참고: fail=true는 트랜잭션 롤백을, fail=false는 커밋을 나타냅니다.

| 클라이언트(컨트롤러) | | Service Bus(코디네이터) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | . . . <br/>다른 링크의<br/>트랜잭션 작업<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**Discharge(txn-id=0,<br/>fail=false)**)}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state=**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>트랜잭션에서 메시지 전송

모든 트랜잭션 작업은 txn-id를 전달하는 트랜잭션 배달 상태 `transactional-state`와 함께 완료됩니다. 메시지를 전송하는 경우에는 메시지의 전송 프레임에서 트랜잭션 상태를 제공합니다. 

| 클라이언트(컨트롤러) | | Service Bus(코디네이터) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state=<br/>TransactionalState(<br/>txn-id=0)**)<br/>{ payload }| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>트랜잭션에서 메시지 배치

메시지 배치에는 `Complete` / `Abandon` / `DeadLetter` / `Defer` 같은 작업이 포함됩니다. 트랜잭션 내에서 이러한 작업을 수행하려면 disposition과 함께 `transactional-state`를 전달하세요.

| 클라이언트(컨트롤러) | | Service Bus(코디네이터) |
| --- | --- | --- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ payload }|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>고급 Service Bus 기능

이 섹션에서는 현재 AMQP의 OASIS 기술 위원회에서 개발 중인 AMQP의 확장 초안에 기반하는 Azure Service Bus의 고급 기능을 설명합니다. Service Bus는 이러한 초안의 최신 버전을 구현하며, 해당 초안이 표준 상태에 도달하면 도입된 변경 내용을 채택합니다.

> [!NOTE]
> Service Bus 메시징 고급 작업은 요청/응답 패턴을 통해 지원됩니다. 이러한 작업의 세부 정보는 [Service Bus의 AMQP 1.0: 요청-응답 기반 작업](service-bus-amqp-request-response.md) 문서에서 설명하고 있습니다.
> 
> 

### <a name="amqp-management"></a>AMQP 관리

AMQP 관리 사양은 이 문서에서 설명하는 초안 확장 중 첫 번째입니다. 이 사양은 AMQP를 통해 메시지 인프라와 관리 상호 작용을 수행할 수 있도록 하는 AMQP 프로토콜 위에 계층화된 프로토콜 집합을 정의합니다. 이 사양은 메시징 인프라 및 쿼리 작업 집합 내 엔터티 관리를 위한 *만들기*, *읽기*, *업데이트* 및 *삭제*와 같은 일반 작업을 정의합니다.

이러한 모든 제스처는 클라이언트와 메시지 인프라 간에 요청/응답 상호 작용을 필요로 하므로, 해당 사양에서는 AMQP를 토대로 이러한 상호 작용 패턴을 모델링하는 방법을 정의합니다. 클라이언트는 메시지 인프라에 연결하고, 세션을 시작하고, 링크 쌍을 만듭니다. 한 링크에서 클라이언트는 보낸 사람 역할을 하고, 다른 링크에서는 받는 사람 역할을 하므로 양방향 채널로 작동할 수 있는 링크 쌍이 만들어집니다.

| 논리 연산 | 클라이언트 | Service Bus |
| --- | --- | --- |
| 요청 응답 경로 만들기 |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=**null**,<br/>target=”myentity/$management”<br/>) |작업 없음 |
| 요청 응답 경로 만들기 |작업 없음 |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=null,<br/>target=”myentity”<br/>) |
| 요청 응답 경로 만들기 |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>) | |
| 요청 응답 경로 만들기 |작업 없음 |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=”myentity”,<br/>target=”myclient$id”<br/>) |

링크 쌍이 배치되면 요청/응답 구현은 간단합니다. 요청은 이 패턴을 파악하는 메시징 인프라 내부의 엔터티로 전송되는 메시지입니다. 해당 요청 메시지에서 *속성* 섹션의 *회신* 필드가 응답을 전달할 링크의 *대상* 식별자로 설정됩니다. 처리 엔터티에서는 요청을 처리한 다음 해당 *대상* 식별자가 지정된 *회신* 식별자와 일치하는 링크를 통해 회신을 전달합니다.

이 패턴은 클라이언트 컨테이너 및 회신 대상에 대해 클라이언트에서 생성한 식별자가 모든 클라이언트에서 고유하며, 보안상의 이유로 예측하기 어려워야 함을 명확히 요구합니다.

관리 프로토콜 및 동일한 패턴을 사용하는 다른 모든 프로토콜에 사용되는 메시지 교환은 애플리케이션 수준에서 발생합니다. 또한 새로운 AMQP 프로토콜 수준 제스처를 정의하지 않습니다. 이러한 방식은 애플리케이션이 호환 AMQP 1.0 스택에서 이러한 확장을 즉시 활용할 수 있도록 하기 위해 의도된 것입니다.

Service Bus는 현재 관리 사양의 어떤 핵심 기능도 구현하지 않지만 관리 사양이 정의하는 요청/응답 패턴은 클레임 기반 보안 기능 및 다음 섹션에서 다룰 고급 기능 대부분의 토대가 됩니다.

### <a name="claims-based-authorization"></a>클레임 기반 권한 부여

AMQP CBS(클레임 기반 인증) 사양 초안은 관리 사양의 요청/응답 패턴을 기반으로 구축되며, AMQP와 페더레이션된 보안 토큰을 사용하는 방법에 대한 일반화된 모델을 설명합니다.

소개 부분에서 나오는 AMQP의 기본 보안 모델은 SASL을 기준으로 하며, AMQP 연결 핸드셰이크와 통합됩니다. SASL을 사용하면 이전에 SASL에 의존하던 프로토콜이 정의될 수 있는 메커니즘 집합의 확장 가능한 모델을 제공한다는 장점이 있습니다. 이러한 메커니즘 중에는 사용자 이름 및 암호 전송을 위한 “PLAIN”, TLS 수준 보안에 바인딩하기 위한 “EXTERNAL”, 명시적 인증/권한 부여가 없음을 나타내기 위한 “ANONYMOUS”가 있으며, 인증 및/또는 권한 부여 자격 증명이나 토큰을 전달할 수 있도록 하는 다양한 추가 메커니즘도 있습니다.

AMQP의 SASL 통합에는 다음과 같은 두 가지 단점이 있습니다.

* 모든 자격 증명 및 토큰 범위가 해당 연결로 지정됩니다. 메시지 인프라에서 엔터티 기준으로 차별화된 액세스 제어를 제공하려고 할 수 있습니다. 예를 들어 토큰의 전달자가 큐 A로 전송하는 것은 허용하지만 큐 B로는 전송하지 못하게 할 수 있습니다. 연결에 권한 부여 컨텍스트가 고정되면 단일 연결을 사용할 수 없지만 큐 A 및 큐 B에 대해 다른 액세스 토큰을 사용할 수 있습니다.
* 액세스 토큰은 일반적으로 제한된 시간 동안만 유효합니다. 이 유효성 때문에 사용자는 주기적으로 토큰을 다시 획득해야 하며, 사용자 권한이 변경될 경우 토큰 발급자가 새 토큰 발행을 거부할 기회가 부여됩니다. AMQP 연결은 매우 긴 시간 동안 지속될 수 있습니다. SASL 모델은 연결 시에만 토큰을 설정할 기회를 제공합니다. 즉, 토큰이 만료되거나 액세스 권한이 임시로 취소되었을 수 있는 클라이언트와의 통신을 지속하는 것이 위험할 수 있는 경우 메시징 인프라는 클라이언트와의 연결을 끊어야 합니다.

Service Bus에 의해 구현되는 AMQP CBS 사양은 이러한 문제에 대한 적합한 해결 방법을 활성화합니다. 클라이언트는 각 노드로 액세스 토큰을 연결하고, 메시지 흐름을 방해하지 않고 만료되기 전에 해당 토큰을 업데이트할 수 있습니다.

CBS는 *$cbs*라는 가상 관리 노드가 메시징 인프라에 의해 제공되도록 정의합니다. 관리 노드는 메시징 인프라의 다른 노드를 대신하여 토큰을 수락합니다.

프로토콜 제스처는 관리 사양에 정의되는 요청/회신 교환입니다. 즉, 클라이언트는 *$cbs* 노드와의 링크 쌍을 설정한 다음, 아웃바운드 링크에 요청을 전달하고 인바운드 링크에서 응답을 기다립니다.

요청 메시지에는 다음과 같은 애플리케이션 속성이 적용됩니다.

| 키 | 옵션 | 값 형식 | 값 내용 |
| --- | --- | --- | --- |
| operation |아닙니다. |문자열 |**put-token** |
| 형식 |아닙니다. |문자열 |배치되는 토큰의 형식입니다. |
| 이름 |아닙니다. |문자열 |토큰이 적용되는 "대상"입니다. |
| expiration |예 |timestamp |토큰의 만료 시간입니다. |

*name* 속성은 토큰이 연결되어야 하는 엔터티를 식별합니다. Service Bus에서 큐 또는 토픽/구독에 대한 경로에 해당합니다. *type* 속성은 토큰 형식을 식별합니다.

| 토큰 형식 | 토큰 설명 | 본문 형식 | 메모 |
| --- | --- | --- | --- |
| amqp:jwt |JWT(JSON 웹 토큰) |AMQP 값(문자열) |아직 사용할 수 없습니다. |
| amqp:swt |SWT(단순 웹 토큰) |AMQP 값(문자열) |AAD/ACS에서 발급한 SWT 토큰에 대해서만 지원됩니다. |
| servicebus.windows.net:sastoken |Service Bus SAS 토큰 |AMQP 값(문자열) |- |

토큰은 권한을 부여합니다. Service Bus는 다음 세 가지 기본 권한을 알고 있습니다. "전송"은 전송을 가능하게 하고, "수신"은 수신을 가능하게 하고, "메시지"는 엔터티 조작을 가능하게 합니다. 명시적으로 AAD/ACS에서 발급한 SWT 토큰에는 이러한 권한이 클레임으로 포함되어 있습니다. Service Bus SAS 토큰은 네임스페이스 또는 엔터티에 구성된 규칙을 참조하며, 이러한 규칙은 권한으로 구성됩니다. 해당 규칙과 연결된 키를 사용하여 토큰에 서명하면 토큰이 해당 권한을 나타냅니다. *put-token*을 사용하여 엔터티와 연결된 토큰은 연결된 클라이언트가 토큰 권한에 따라 엔터티와 상호 작용하는 것을 허용합니다. 클라이언트가 *발신자* 역할을 맡고 있는 링크에는 "전송" 권한이 필요하고, *수신자* 역할을 맡고 있는 링크에는 "수신" 권한이 필요합니다.

회신 메시지는 다음과 같은 *애플리케이션 속성* 값을 갖습니다.

| 키 | 옵션 | 값 형식 | 값 내용 |
| --- | --- | --- | --- |
| status-code |아닙니다. |int |HTTP 응답 코드 **[RFC2616]** |
| status-description |예 |문자열 |상태에 대한 설명입니다. |

클라이언트는 메시징 인프라의 모든 엔터티에 대해 반복적으로 *put-token*을 호출할 수 있습니다. 토큰은 현재 클라이언트로 범위가 지정되며 현재 연결에 고정됩니다. 즉, 연결이 삭제되면 서버는 보유된 토큰을 모두 삭제합니다.

현재 Service Bus 구현에서는 SASL 메서드 “ANONYMOUS”와 함께 사용되는 경우에만 CBS를 허용합니다. SSL/TLS 연결은 항상 SASL 핸드셰이크보다 먼저 존재해야 합니다.

따라서 선택한 AMQP 1.0 클라이언트에서 익명 메커니즘을 지원해야 합니다. 익명 액세스는 Service Bus에서 연결을 만드는 대상이 누구인지 알지 못하는 상태에서 초기 세션 생성을 비롯한 초기 연결 핸드셰이크가 발생하는 것을 의미합니다.

연결 및 세션이 설정되고 나면 링크가 *$cbs* 노드에 연결되고 *put-token* 요청이 전송될 수만 있습니다. 연결이 설정되고 20초 이내에 일부 엔터티 노드에 대해 *put-token* 요청을 사용하여 유효한 토큰을 설정해야 합니다. 그러지 않으면 Service Bus에서 일방적으로 연결을 삭제합니다.

클라이언트는 이후에 토큰 만료를 계속 추적해야 합니다. 토큰이 만료되면 Service Bus는 각 엔터티에 대한 연결에서 모든 링크를 즉시 삭제합니다. 문제 발생을 방지하기 위해 클라이언트는 다른 링크에서 흐르는 페이로드 트래픽을 방해하지 않으면서 가상 *$cbs* 관리 노드에서 *put-token* 제스처를 사용하여 언제든지 노드에 대한 토큰을 새 토큰으로 바꿀 수 있습니다.

### <a name="send-via-functionality"></a>경유 전송 기능

[경유 전송/전송자 전송](service-bus-transactions.md#transfers-and-send-via)은 서비스 버스가 다른 엔터티를 통해 특정 메시지를 대상 엔터티로 전달할 수 있게 해주는 기능입니다. 이 기능은 단일 트랜잭션 내에서 엔터티 간 작업을 수행하는 데 사용됩니다.

이 기능을 통해 전송자를 만들고 `via-entity`에 대한 링크를 설정할 수 있습니다. 링크를 설정하는 동안 이 링크의 메시지/전송 대상을 설정하기 위한 추가 정보가 전달됩니다. 연결에 성공하면 이 링크에 전송된 모든 메시지가 *via-entity*를 통해 *destination-entity*로 자동 전달됩니다. 

> 참고: 이 링크를 설정하기 전에 *via-entity* 및 *destination-entity* 모두에 대해 인증이 수행되어야 합니다.

| 클라이언트 | | Service Bus |
| --- | --- | --- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={client link ID},<br/>target=**{via-entity}**,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | attach(<br/>name={link name},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>다음 단계

AMQP에 대한 자세한 내용을 알아보려면 다음 링크를 방문하세요.

* [Service Bus AMQP 개요]
* [Service Bus 분할 큐 및 토픽에 대한 AMQP 1.0 지원]
* [Windows Server용 Service Bus의 AMQP]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP 개요]: service-bus-amqp-overview.md
[Service Bus 분할 큐 및 토픽에 대한 AMQP 1.0 지원]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[Windows Server용 Service Bus의 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
