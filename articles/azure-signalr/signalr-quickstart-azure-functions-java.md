---
title: Java를 사용하여 Azure Functions와 SignalR Service로 대화방 만들기
description: Azure SignalR Service와 Azure Functions를 사용하여 대화방을 만들기 위한 빠른 시작입니다.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: 544f200e749b1b125e8077ee65f20a06779fb13d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050576"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>빠른 시작: Java를 사용하여 Azure Functions와 SignalR Service로 대화방 만들기

Azure SignalR Service를 사용하면 애플리케이션에 실시간 기능을 쉽게 추가할 수 있으며 Azure Functions는 인프라를 관리하지 않고도 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 빠른 시작에서는 Java를 통해 SignalR Serivces와 Functions를 사용하여 서버리스, 실시간 대화 애플리케이션을 빌드합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기
- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Azure 함수 앱을 로컬로 실행하는 데 사용됩니다.

   > [!NOTE]
   > Java의 필수 SignalR Service 바인딩은 Azure Function Core Tools 버전 2.4.419(호스트 버전 2.0.12332) 이상에서만 지원됩니다.

   > [!NOTE]
   > 확장을 설치하려면 Azure Functions Core Tools에 [.NET Core SDK](https://www.microsoft.com/net/download)가 설치되어 있어야 합니다. 그러나 JavaScript Azure 함수 앱을 빌드하는 데는 .NET에 대한 지식이 필요하지 않습니다.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), 버전 8
- [Apache Maven](https://maven.apache.org), 버전 3.0 이상

> [!NOTE]
> 이 빠른 시작은 macOS, Windows 또는 Linux에서 실행할 수 있습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 <https://portal.azure.com/>에서 Azure Portal에 로그인합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)

## <a name="configure-and-run-the-azure-function-app"></a>Azure 함수 앱을 구성하고 실행합니다.

1. Azure Portal이 열리는 브라우저에서, 포털의 맨 위에 있는 검색 상자에서 해당 이름을 검색하여 이전에 배포한 SignalR Service 인스턴스를 성공적으로 만들었는지 확인합니다. 인스턴스를 선택하여 엽니다.

    ![SignalR Service 인스턴스를 검색합니다.](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. SignalR Service 인스턴스의 연결 문자열을 보려면 **키**를 선택합니다.

1. 기본 연결 문자열을 선택하여 복사합니다.

    ![SignalR Service 만들기](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. 코드 편집기에서 복제된 리포지토리의 *src/chat/java* 폴더를 엽니다.

1. *local.settings.sample.json*의 이름을 *local.settings.json*으로 바꿉니다.

1. **local.settings.json** 에서 연결 문자열을 **AzureSignalRConnectionString** 설정 값에 붙여넣습니다. 파일을 저장합니다.

1. 함수가 포함된 주 파일은 *src/chat/java/src/main/java/com/function/Functions.java*에 있습니다.

    - **negotiate** - *SignalRConnectionInfo* 입력 바인딩을 사용하여 올바른 연결 정보를 생성하고 리턴합니다.
    - **sendMessage** - 요청 본문에서 대화 메시지를 수신하고 *SignalR* 출력 바인딩을 사용하여 모든 연결된 클라이언트 애플리케이션으로 메시지를 브로드캐스트합니다.

1. 터미널에서 *src/chat/java* 폴더에 있는지 확인합니다. 함수 앱을 빌드합니다.

    ```bash
    mvn clean package
    ```

1. 함수 앱을 로컬로 실행합니다.

    ```bash
    mvn azure-functions:run
    ```
[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Maven을 사용하여 실시간 서버리스 애플리케이션을 빌드하고 실행했습니다. 다음 과정으로, Java Azure Functions를 처음부터 새로 만드는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Java 및 Maven을 사용하여 첫 번째 함수 만들기](../azure-functions/functions-create-first-java-maven.md)

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsjava)
