---
title: Azure Active Directory Access Control Service에서 공유 액세스 서명 권한 부여로 마이그레이션 | Microsoft Docs
description: Access Control Service에서 SAS로 애플리케이션 마이그레이션
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 746b19062c3014caa37c6668e6c41df054a47e25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868165"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Active Directory Access Control Service에서 공유 액세스 서명 권한 부여로 마이그레이션

기존에는 Service Bus 애플리케이션에서 두 가지 권한 부여 모델 중 하나를 선택하여 사용할 수 있었습니다. 그 중 하나는 Service Bus에서 직접 제공되는 [SAS(공유 액세스 서명)](service-bus-sas.md) 토큰 모델이고, 다른 하나는 페더레이션 모델입니다. 페더레이션 모델에서는 권한 부여 규칙 관리 작업이 [Azure Active Directory](/azure/active-directory/) ACS(Access Control Service)를 통해 내부에서 관리되며, 원하는 기능에 대한 액세스 권한을 부여하기 위해 ACS에서 가져온 토큰이 Service Bus로 전달됩니다.

ACS 권한 부여 모델은 [SAS 권한 부여](service-bus-authentication-and-authorization.md)가 기본 모델로 사용되면서 이미 오래전에 교체되었으며 현재는 모든 설명서, 지침 및 샘플에서 SAS만 사용되고 있습니다. 또한 ACS와 쌍으로 지정된 새 Service Bus 네임스페이스도 더 이상 만들 수 없습니다.

SAS는 다른 서비스를 직접적으로 사용하지 않으며, SAS 규칙 이름과 규칙 키 액세스 권한을 클라이언트에 제공하는 방식으로 별도의 매개체 없이도 클라이언트에서 바로 사용할 수 있다는 장점이 있습니다. 또한 SAS는 클라이언트가 다른 서비스에 대한 권한 부여 확인을 통과해야 토큰이 발급되는 방식과도 쉽게 통합할 수 있습니다. 두 번째 방식은 ACS 사용 패턴과 비슷하지만, ACS에서는 표현하기가 어려운 애플리케이션 관련 조건에 따라 액세스 토큰을 발급할 수 있습니다.

고객은 ACS를 사용하는 모든 기존 애플리케이션이 SAS를 대신 사용하도록 마이그레이션하는 것이 좋습니다.

## <a name="migration-scenarios"></a>마이그레이션 시나리오

*서명 키*를 공유하는 방식으로 ACS와 Service Bus를 통합합니다. 서명 키는 ACS 네임스페이스에서는 권한 부여 토큰에 서명을 하는 데 사용되며, Service Bus에서는 쌍으로 지정된 ACS 네임스페이스에서 토큰을 발급했는지를 확인하는 데 사용됩니다. ACS 네임스페이스에는 서비스 ID 및 권한 부여 규칙이 포함됩니다. 권한 부여 규칙은 외부 ID 공급자가 발급한 각 토큰이나 서비스 ID가 Service Bus 네임스페이스 그래프의 특정 부분에 대해 소유하는 액세스 권한의 유형을 가장 긴 접두사 일치 형식으로 정의합니다.

ACS 규칙이 경로 접두사 `/`에 대한 **보내기** 클레임을 서비스 ID에 부여하는 경우를 예로 들어 보겠습니다. 이 경우 해당 규칙을 기준으로 하여 ACS에서 발급하는 토큰은 네임스페이스의 모든 엔터티에 대한 보내기 권한을 클라이언트에 부여합니다. 경로 접두사가 `/abc`라면 해당 ID는 이름이 `abc`이거나 해당 접두사 하위의 경로에 구성된 엔터티에 대해서만 보내기를 수행할 수 있습니다. 이 문서에서는 이 마이그레이션 지침의 독자가 이러한 개념에 대해 이미 잘 알고 있다고 가정합니다.

마이그레이션 시나리오는 대략적으로 다음의 세 가지 범주에 속합니다.

1.  **기본값 변경 안 됨**. [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) 개체를 사용하여 자동 생성된 **소유자** 서비스 ID와 해당 비밀 키를 Service Bus 네임스페이스와 쌍으로 설정된 ACS 네임스페이스용으로 전달하고 새 규칙은 추가하지 않는 일부 고객이 사용하는 시나리오입니다.

2.  **단순 규칙이 포함된 사용자 지정 서비스 ID**. 새 서비스 ID를 추가하고 특정 단일 엔터티에 대한 **보내기**, **수신 대기** 및 **관리** 권한을 각각의 새 서비스 ID에 부여하는 일부 고객이 사용하는 시나리오입니다.

3.  **복잡한 규칙이 포함된 사용자 지정 서비스 ID**. 매우 드물지만 외부에서 발급된 토큰을 Relay의 권한에 매핑하거나, 여러 규칙을 통해 단일 서비스 ID에 여러 네임스페이스 경로에 대한 차등 권한을 할당하는 복잡한 규칙 집합을 사용하는 고객도 있습니다.

복잡한 규칙 집합 마이그레이션과 관련하여 지원을 받으려는 경우 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의할 수 있습니다. 나머지 두 시나리오의 경우 손쉽게 마이그레이션할 수 있습니다.

### <a name="unchanged-defaults"></a>기본값 변경 안 됨

애플리케이션에서 ACS 기본값을 변경하지 않은 경우 사용되는 모든 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)를 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 개체로 바꾸고 ACS **소유자** 계정 대신 네임스페이스의 미리 구성된 **RootManageSharedAccessKey**를 사용할 수 있습니다. 그러나 ACS **소유자** 계정을 사용하더라도 이 구성은 이전에도 대체로 권장되지 않았으며 현재도 사용하지 않는 것이 좋습니다. 이 계정/규칙은 엔터티 삭제 권한을 비롯하여 네임스페이스에 대한 모든 권리 권한을 제공하기 때문입니다.

### <a name="simple-rules"></a>단순 규칙

애플리케이션이 단순 규칙을 포함하는 사용자 지정 서비스 ID를 사용하는 경우, 특정 큐에 대한 액세스 제어 기능을 제공하기 위해 ACS 서비스 ID를 만들었다면 마이그레이션을 손쉽게 수행할 수 있습니다. 이 시나리오는 대개 각 큐를 테넌트 사이트나 지점에 대한 브리지로 사용하며 해당 특정 사이트용으로 서비스 ID를 만드는 SaaS 스타일 솔루션에서 사용되는 경우가 많습니다. 이 경우에는 개별 서비스 ID를 큐에서 직접 공유 액세스 서명 규칙으로 마이그레이션할 수 있습니다. 서비스 ID 이름은 SAS 규칙 이름으로, 서비스 ID 키는 SAS 규칙 키로 설정될 수 있습니다. 그리고 나면 SAS 규칙의 권한이 엔터티에 해당하는 각 ACS 규칙과 동일하게 구성됩니다.

ACS와 페더레이션된 기존 네임스페이스 대신 이 새로운 SAS 구성을 추가로 만들 수 있으며, 그런 후에 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) 대신 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider)를 사용하여 ACS에서 SAS로의 마이그레이션을 수행할 수 있습니다. 이때 ACS에서 네임스페이스의 연결을 끊지 않아도 됩니다.

### <a name="complex-rules"></a>복잡한 규칙

SAS 규칙은 계정이 아니라 권한과 연결되어 있는 명명된 서명 키입니다. 따라서 애플리케이션이 서비스 ID를 여러 개 만들고 여러 엔터티나 전체 네임스페이스에 대한 액세스 권한을 부여하는 시나리오에서는 여전히 토큰 발급 매개체가 필요합니다. [Azure 지원에 문의](https://azure.microsoft.com/support/options/)하면 이러한 매개체에 대한 지침을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 인증에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Service Bus 인증 및 권한 부여](service-bus-authentication-and-authorization.md)
* [공유 액세스 서명을 사용한 Service Bus 인증](service-bus-sas.md)

