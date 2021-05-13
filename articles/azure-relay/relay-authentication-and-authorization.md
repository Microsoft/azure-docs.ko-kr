---
title: Azure Relay 인증 및 권한 부여 | Microsoft Docs
description: 이 문서에서는 Azure Relay 서비스를 사용하는 SAS(공유 액세스 서명) 인증에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033380"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay 인증 및 권한 부여

애플리케이션은 SAS(공유 액세스 서명) 인증을 사용하여 Azure Relay에 인증할 수 있습니다. SAS 인증을 사용하면 애플리케이션이 Relay 네임스페이스에 구성된 액세스 키를 통해 Azure Relay 서비스에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 Relay 서비스를 인증하는 데 사용할 수 있는 공유 액세스 서명 토큰을 생성할 수 있습니다.

## <a name="shared-access-signature-authentication"></a>공유 액세스 서명 인증

[SAS 인증](../service-bus-messaging/service-bus-sas.md)을 사용하면 특정 권한으로 Azure Relay 리소스에 액세스하는 권한을 사용자에게 부여할 수 있습니다. SAS 인증은 리소스에 연결된 권한이 있는 암호화 키의 구성을 포함합니다. 클라이언트는 액세스된 리소스 URI 및 구성된 키로 서명된 만료로 구성된 SAS 토큰을 제공하여 해당 리소스에 대한 액세스를 얻을 수 있습니다.

Relay 네임스페이스에서 SAS에 대한 키를 구성할 수 있습니다. Service Bus 메시징과 달리 [Relay 하이브리드 연결](relay-hybrid-connections-protocol.md)은 무단 또는 익명 발신자를 지원합니다. 포털에서 다음 스크린샷에 표시된 것처럼 만들 때 엔터티에 대한 익명 액세스를 활성화할 수 있습니다.

![“하이브리드 연결 만들기” 대화 상자에 “이름” 텍스트 상자와 “클라이언트 인증 필요” 레이블이 지정되고 선택된 확인란이 있습니다.][0]

SAS를 사용하려면 다음을 구성하는 Relay 네임스페이스에서 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 개체를 구성할 수 있습니다.

* *KeyName* 입니다.
* *PrimaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *SecondaryKey* 는 SAS 토큰을 서명/확인하는 데 사용되는 암호화 키입니다.
* *권한* 입니다.

네임스페이스 수준에서 구성된 권한 부여 규칙은 해당 키를 사용하여 서명된 토큰으로 클라이언트에 대한 네임스페이스의 모든 relay 연결에 액세스를 부여할 수 있습니다. 권한 부여 규칙은 Relay 네임스페이스에서 최대 12개까지 구성할 수 있습니다. 기본적으로 모든 권한이 있는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 은 처음으로 프로비전될 때 모든 네임스페이스에 대해 구성됩니다.

엔터티에 액세스하려면 클라이언트는 특정 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)을 사용하여 생성된 SAS 토큰이 필요합니다. 액세스를 요청하는 리소스 URI로 구성된 리소스 문자열의 HMAC-SHA256 및 권한 부여 규칙에 연결된 암호화 키가 있는 만료를 사용하여 SAS 토큰을 생성합니다.

Azure Relay에 대한 SAS 인증 지원은 Azure.NET SDK 버전 2.0 이상에 포함됩니다. SAS는 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)에 대한 지원을 포함합니다. 연결 문자열을 매개 변수로 허용하는 모든 API는 SAS 연결 문자열에 대한 지원을 포함합니다.

## <a name="next-steps"></a>다음 단계

- SAS에 대한 자세한 내용은 [공유 액세스 서명을 사용한 Service Bus 인증](../service-bus-messaging/service-bus-sas.md)을 계속 읽으세요.
- 하이브리드 연결 기능에 대한 자세한 내용은 [Azure Relay 하이브리드 연결 프로토콜 가이드](relay-hybrid-connections-protocol.md)를 참조하세요.
- Service Bus 메시징 인증 및 권한 부여에 관한 해당 정보는 [Service Bus 인증 및 권한 부여](../service-bus-messaging/service-bus-authentication-and-authorization.md)를 참조하세요. 

[0]: ./media/relay-authentication-and-authorization/hcanon.png