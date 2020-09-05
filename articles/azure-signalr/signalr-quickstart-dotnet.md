---
title: ASP.NET으로 개발 - Azure SignalR Service
description: ASP.NET 프레임워크로 Azure SignalR Service를 사용하여 대화방을 만들기 위한 빠른 시작입니다.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: 8797d17ac439882415a9f5360fded28cb55484d5
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050542"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>빠른 시작: ASP.NET과 SignalR Service를 사용하여 대화방 만들기

Azure SignalR Service는 ASP.NET SignalR과 100% 호환되지 **않는**[ASP.NET Core 2.0용 SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction)을 기반으로 합니다. Azure SignalR Service는 최신 ASP.NET Core 기술을 기반으로 ASP.NET SignalR 데이터 프로토콜을 재구현했습니다. ASP.NET SignalR용 Azure SignalR Service 서비스를 사용하는 경우 일부 ASP.NET SignalR 기능은 더 이상 지원되지 않습니다. 예를 들어 Azure SignalR은 클라이언트가 다시 연결되어도 메시지를 회신하지 않습니다. 또한 Forever Frame 전송 및 JSONP가 지원되지 않습니다. ASP.NET SignalR 애플리케이션이 SignalR Service와 작동하려면 일부 코드 변경과 적절한 버전의 종속 라이브러리가 필요합니다. 

ASP.NET SignalR과 ASP.NET Core SignalR의 기능을 비교한 전체 목록을 보려면 [버전 차이 문서](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2)를 참조하세요.

이 빠른 시작에서는 유사한 [대화방 애플리케이션](./signalr-quickstart-dotnet-core.md)에 대해 ASP.NET과 Azure SignalR Service를 시작하는 방법을 알아봅니다.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>사전 요구 사항

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsnet)

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsnet)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

ASP.NET SignalR 애플리케이션에는 *서버리스* 모드가 지원되지 않습니다. Azure SignalR Service 인스턴스에는 항상 *기본*이나 *클래식*을 사용하세요.

[SignalR Service 스크립트 만들기](scripts/signalr-cli-create-service.md)를 통해 이 빠른 시작에 사용되는 Azure 리소스를 만들 수도 있습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsnet)

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

서비스가 배포되는 동안 코드로 작업을 전환해 보겠습니다. [GitHub의 샘플 앱](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)을 복제하고, SignalR Service 연결 문자열을 설정하고, 애플리케이션을 로컬로 실행합니다.

1. Git 터미널 창을 엽니다. 샘플 프로젝트를 복제할 폴더로 변경합니다.

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsnet)

## <a name="configure-and-run-chat-room-web-app"></a>대화방 웹앱 구성 및 실행

1. Visual Studio를 시작하고 복제된 리포지토리의 *aspnet-samples/ChatRoom/* 폴더에서 솔루션을 엽니다.

1. Azure Portal이 열려있는 브라우저에서, 직접 만든 인스턴스를 찾아서 선택합니다.

1. SignalR Service 인스턴스의 연결 문자열을 보려면 **키**를 선택합니다.

1. 기본 연결 문자열을 선택하여 복사합니다.

1. 이제 web.config 파일에서 연결 문자열을 설정합니다.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. 애플리케이션이 SignalR을 자체적으로 호스팅하지 않고 서비스에 연결되도록 하려면 *Startup.cs*에서 `MapSignalR()`을 호출하는 대신 `MapAzureSignalR({your_applicationName})`을 호출하여 연결 문자열을 전달해야 합니다. `{YourApplicationName}`을 애플리케이션 이름으로 바꿉니다. 이 이름은 이 애플리케이션을 다른 애플리케이션과 구분하는 고유한 이름입니다. `this.GetType().FullName`을 값으로 사용할 수 있습니다.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    이러한 API를 사용하려면 먼저 서비스 SDK도 참조해야 합니다. **도구 | NuGet 패키지 관리자 | 패키지 관리자 콘솔**을 열어서 다음 명령을 실행합니다.

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    이러한 변화 이외에, 다른 모든 사항은 동일하게 유지됩니다. 이미 익숙한 허브 인터페이스를 계속 사용하여 비즈니스 논리를 작성할 수 있습니다.

    > [!NOTE]
    > 이 구현에서 `/signalr/negotiate` 엔드포인트는 Azure SignalR Service SDK의 협상을 위해 노출됩니다. 클라이언트가 연결하려고 하면 특수한 협상 응답을 반환하고, 연결 문자열에 정의된 서비스 엔드포인트로 클라이언트를 리디렉션합니다.

1. **F5** 키를 눌러 디버그 모드로 프로젝트를 실행합니다. 애플리케이션이 로컬에서 실행되는 것을 볼 수 있습니다. 애플리케이션이 SignalR 런타임을 자체적으로 호스팅하는 대신 Azure SignalR Service에 연결합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsnet)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스트하기 위한 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.
> 
> 

[Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

**이름을 기준으로 필터링...** 텍스트 상자에 리소스 그룹의 이름을 입력합니다. 이 빠른 시작의 지침에서는 *SignalRTestResources*라는 리소스 그룹을 사용합니다. 결과 목록의 리소스 그룹에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

   
![DELETE](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsnet)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 Azure SignalR Service 리소스를 만들어서, ASP.NET 웹앱에서 사용했습니다. 다음에는 ASP.NET Core와 Azure SignalR Service를 사용하여 실시간 애플리케이션을 개발하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [ASP.NET Core와 Azure SignalR Service](./signalr-quickstart-dotnet-core.md)

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsnet)

