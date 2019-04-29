---
title: Azure SignalR Service에 대 한 액세스 키를 회전 하는 방법
description: 고객이 정기적으로 액세스 키를 회전해야 하는 이유 및 Azure Portal GUI와 Azure CLI를 사용하여 액세스 키를 회전하는 방법에 대한 개요입니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 15b0ff0bbb96e5fa96d81cfa265e83abf749cf85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688902"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Azure SignalR Service에 대 한 액세스 키를 회전 하는 방법

각 Azure SignalR Service 인스턴스는 기본 키와 보조 키라고 하는 액세스 키 쌍을 갖고 있습니다. 두 키는 서비스에 요청을 만들 때 SignalR 클라이언트를 인증하는 데 사용됩니다. 키는 인스턴스 엔드포인트 URL과 연결됩니다. 키를 안전하게 보호하고 정기적으로 회전해야 합니다. 두 개의 액세스 키가 제공되므로 한 키를 다시 생성하는 동안 다른 키를 사용하여 연결을 유지할 수 있습니다.

## <a name="why-rotate-access-keys"></a>액세스 키를 회전하는 이유는?

보안 및 규정 준수 요구 사항을 준수하도록 액세스 키를 정기적으로 회전해야 합니다.

## <a name="regenerate-access-keys"></a>액세스 키 다시 생성

1. [Azure Portal](https://portal.azure.com/)로 이동하여 자격 증명으로 로그인합니다.

1. 다시 생성하려는 키가 있는 Azure SignalR Service 인스턴스의 **키** 섹션을 찾습니다.

1. 탐색 메뉴에서 **키**를 선택합니다.

1. **기본 키 다시 생성** 또는 **보조 키 다시 생성**을 선택합니다.

   새 키와 해당 연결 문자열이 생성되어 표시됩니다.

   ![키 다시 생성](media/signalr-howto-key-rotation/regenerate-keys.png)

[Azure CLI](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew)를 사용하여 키를 다시 생성할 수도 있습니다.

## <a name="update-configurations-with-new-connection-strings"></a>새 연결 문자열을 사용하여 구성 업데이트

1. 새로 생성된 연결 문자열을 복사합니다.

1. 새 연결 문자열을 사용하도록 모든 구성을 업데이트합니다.

1. 필요에 따라 애플리케이션을 다시 시작합니다.

## <a name="forced-access-key-regeneration"></a>강제 액세스 키 다시 생성

Azure SignalR Service는 특정 상황에서 필수 액세스 키를 강제로 다시 생성할 수 있습니다. 이 서비스는 이메일 및 포털 알림을 통해 고객에게 알림을 제공합니다. 이 통신을 받거나 액세스 키로 인해 서비스 오류가 발생하는 경우 이 가이드의 지침에 따라 키를 회전하세요.

## <a name="next-steps"></a>다음 단계

보안을 유지하기 위해 정기적으로 액세스 키를 회전합니다.

이 가이드에서는 액세스 키를 다시 생성하는 방법에 대해 알아보았습니다. Azure Functions 또는 OAuth를 사용한 인증에 관한 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [ASP.NET Core ID와 통합](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [인증을 사용하는 서버리스 실시간 앱 빌드](./signalr-tutorial-authenticate-azure-functions.md)