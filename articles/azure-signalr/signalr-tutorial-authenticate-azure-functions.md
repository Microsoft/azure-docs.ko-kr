---
title: '자습서: Azure Functions를 사용한 인증 - Azure SignalR'
description: 이 자습서에서는 Azure Functions 바인딩을 위해 Azure SignalR Service 클라이언트를 인증하는 방법을 알아봅니다.
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.custom: devx-track-javascript
ms.openlocfilehash: 45dc137141491938367fb57c6955e8e3145f8ff9
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050457"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>자습서: Azure Functions를 사용하여 Azure SignalR Service 인증

Azure Functions, App Service 인증 및 SignalR Service를 사용하여 인증 및 프라이빗 메시징이 포함된 대화방을 작성하는 단계별 자습서입니다.

## <a name="introduction"></a>소개

### <a name="technologies-used"></a>사용 기술

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) - 사용자를 인증하고 채팅 메시지를 보내기 위한 백 엔드 API입니다.
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) - 연결된 채팅 클라이언트에 새 메시지를 브로드캐스트합니다.
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) - 채팅 클라이언트 UI에 대한 정적 웹 사이트를 호스팅합니다.

### <a name="prerequisites"></a>사전 요구 사항

이 자습서를 작성하는 데 필요한 소프트웨어는 다음과 같습니다.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/)(버전 10.x)
* [.NET SDK](https://www.microsoft.com/net/download)(버전 2.x, Functions 확장에 필요)
* [Azure Functions 핵심 도구](https://github.com/Azure/azure-functions-core-tools)(버전 2)
* [VS Code](https://code.visualstudio.com/)(Visual Studio Code)(다음 확장 포함)
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) - VS Code에서 Azure Functions를 사용합니다.
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) - 테스트를 위해 웹 페이지를 로컬로 제공합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com/)로 이동하여 자격 증명으로 로그인합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service 인스턴스 만들기

Azure Functions 앱을 로컬로 빌드하고 테스트합니다. 이 앱은 미리 만들어야 하는 Azure의 SignalR Service 인스턴스에 액세스합니다.

1. 새 Azure 리소스를 만들려면 **리소스 만들기**( **+** ) 단추를 클릭합니다.

1. **SignalR Service**를 검색하여 선택합니다. **만들기**를 클릭합니다.

    ![새 SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. 다음 정보를 입력합니다.

    | Name | 값 |
    |---|---|
    | 리소스 이름 | SignalR Service 인스턴스에 대한 고유한 이름입니다. |
    | 리소스 그룹 | 고유한 이름으로 새 리소스 그룹을 만듭니다. |
    | 위치 | 가까운 위치를 선택합니다. |
    | 가격 책정 계층 | Free |

1. **만들기**를 클릭합니다.

1. 인스턴스가 배포되면 포털에서 열고 해당 설정 페이지를 찾습니다. 서비스 모드 설정을 *서버리스*로 변경합니다.

    ![SignalR Service 모드](media/signalr-concept-azure-functions/signalr-service-mode.png)
    
[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="initialize-the-function-app"></a>함수 앱 초기화

### <a name="create-a-new-azure-functions-project"></a>새 Azure Functions 프로젝트 만들기

1. 새 VS Code 창의 메뉴에서 `File > Open Folder`를 사용하여 적절한 위치에 빈 폴더를 만들고 엽니다. 이는 빌드할 애플리케이션에 대한 주 프로젝트 폴더입니다.

1. VS Code에서 Azure Functions 확장을 사용하여 주 프로젝트 폴더에서 함수 앱을 초기화합니다.
   1. 메뉴에서 **보기 > 명령 팔레트**를 차례로 선택하여 VS Code에서 명령 팔레트를 엽니다(바로 가기: `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
   1. **Azure Functions: 새 프로젝트 만들기** 명령을 검색하여 선택합니다.
   1. 주 프로젝트 폴더가 표시됩니다. 이 폴더를 선택하거나 "찾아보기"를 사용하여 찾습니다.
   1. 언어를 선택하라는 프롬프트에서 **JavaScript**를 선택합니다.

      ![함수 앱 만들기](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>함수 앱 확장 설치

이 자습서에서는 Azure Functions 바인딩을 사용하여 Azure SignalR Service와 상호 작용합니다. 대부분의 다른 바인딩과 마찬가지로, SignalR Service 바인딩은 사용하기 전에 먼저 Azure Functions 핵심 도구 CLI를 설치해야 하는 확장으로 사용할 수 있습니다.

1. 메뉴(Ctrl-\`)에서 **보기 > 터미널**을 차례로 선택하여 VS Code에서 터미널을 엽니다.

1. 주 프로젝트 폴더가 현재 디렉터리인지 확인합니다.

1. SignalR Service 함수 앱 확장을 설치합니다.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>애플리케이션 설정 구성

Azure Functions 런타임을 로컬로 실행하고 디버그하는 경우 **local.settings.json**에서 애플리케이션 설정을 읽습니다. 이 파일을 앞에서 만든 SignalR Service 인스턴스의 연결 문자열로 업데이트합니다.

1. VS Code의 [탐색기] 창에서 **local.settings.json**을 선택하여 엽니다.

1. 파일의 내용을 다음으로 바꿉니다.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * `AzureSignalRConnectionString`이라는 설정에 Azure SignalR Service 연결 문자열을 입력합니다. Azure Portal의 Azure SignalR Service 리소스에 있는 **키** 페이지에서 값을 가져옵니다. 기본 또는 보조 연결 문자열을 사용할 수 있습니다.
   * `WEBSITE_NODE_DEFAULT_VERSION` 설정은 로컬로 사용되지 않지만 Azure에 배포할 때 필요합니다.
   * `Host` 섹션은 로컬 Functions 호스트에 대한 포트 및 CORS 설정을 구성합니다(Azure에서 실행하는 경우 이 설정은 효과가 없음).

       > [!NOTE]
       > 라이브 서버는 일반적으로 `http://127.0.0.1:5500`의 콘텐츠를 제공하도록 구성됩니다. 다른 URL이 사용되고 있거나 다른 HTTP 서버를 사용하고 있는 경우, 올바른 원본을 반영하도록 `CORS` 설정을 변경합니다.

     ![SignalR Service 키 가져오기](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. 파일을 저장합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>사용자를 SignalR Service에 인증하는 함수 만들기

브라우저에서 채팅 앱을 처음 열 때 Azure SignalR Service에 연결하려면 유효한 연결 자격 증명이 필요합니다. 함수 앱에서 *negotiate*라는 HTTP 트리거 함수를 만들어서 이 연결 정보를 반환합니다.

> [!NOTE]
> SignalR 클라이언트에서 `/negotiate`로 끝나는 엔드포인트가 필요하기 때문에 이 함수의 이름을 *negotiate*로 지정해야 합니다.

1. VS Code 명령 팔레트를 엽니다(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure Functions: Create Function** 명령을 찾아 선택합니다.

1. 메시지가 표시되면 다음 정보를 제공합니다.

    | 이름 | 값 |
    |---|---|
    | 함수 앱 폴더 | 주 프로젝트 폴더를 선택합니다. |
    | 템플릿 | HTTP 트리거 |
    | 이름 | negotiate |
    | 권한 부여 수준 | 익명 |

    새 함수가 포함된 **negotiate**라는 폴더가 만들어집니다.

1. **negotiate/function.json**을 열어서 함수에 대한 바인딩을 구성합니다. 파일의 내용을 다음과 같이 수정합니다. 이렇게 하면 클라이언트에서 `chat`이라는 Azure SignalR Service 허브에 연결할 수 있는 유효한 자격 증명을 생성하는 입력 바인딩이 추가됩니다.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    `signalRConnectionInfo` 바인딩의 `userId` 속성은 인증된 SignalR Service 연결을 만드는 데 사용됩니다. 이 속성은 로컬 개발을 위해 비워 둡니다. 함수 앱이 Azure에 배포될 때 이를 사용하게 됩니다.

1. **negotiate/index.js**를 열어서 함수의 본문을 볼 수 있습니다. 파일의 내용을 다음과 같이 수정합니다.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    이 함수는 입력 바인딩의 SignalR 연결 정보를 가져와서 HTTP 응답 본문의 클라이언트에 반환합니다. SignalR 클라이언트는 이 정보를 사용하여 SignalR Service 인스턴스에 연결합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-send-chat-messages"></a>채팅 메시지를 보내는 함수 만들기

웹앱에는 채팅 메시지를 보내는 HTTP API가 필요합니다. SignalR Service를 통해 연결된 모든 클라이언트에 메시지를 보내는 *SendMessage*라는 HTTP 트리거 함수를 만듭니다.

1. VS Code 명령 팔레트를 엽니다(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure Functions: Create Function** 명령을 찾아 선택합니다.

1. 메시지가 표시되면 다음 정보를 제공합니다.

    | 이름 | 값 |
    |---|---|
    | 함수 앱 폴더 | 주 프로젝트 폴더를 선택합니다. |
    | 템플릿 | HTTP 트리거 |
    | 이름 | SendMessage |
    | 권한 부여 수준 | 익명 |

    새 함수가 포함된 **SendMessage**라는 폴더가 만들어집니다.

1. **SendMessage/function.json**을 열어 함수에 대한 바인딩을 구성합니다. 파일의 내용을 다음과 같이 수정합니다.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    이렇게 하면 원래 함수에 다음 두 가지 변경 내용이 적용됩니다.
    * 경로를 `messages`로 변경하고, HTTP 트리거를 **POST** HTTP 메서드로 제한합니다.
    * 함수에 의해 반환된 메시지를 `chat`이라는 SignalR Service 허브에 연결된 모든 클라이언트로 보내는 SignalR Service 출력 바인딩을 추가합니다.

1. 파일을 저장합니다.

1. **SendMessage/index.js**를 열어 함수 본문을 봅니다. 파일의 내용을 다음과 같이 수정합니다.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    이 함수는 HTTP 요청의 본문을 가져와서 SignalR Service에 연결된 클라이언트에 보냅니다. 그러면 각 클라이언트에서 `newMessage`라는 함수를 호출합니다.

    이 함수는 보낸 사람의 ID를 읽을 수 있으며, 메시지 본문의 *recipient(받는 사람)* 값을 수락하여 단일 사용자에게 메시지를 개인적으로 보낼 수 있습니다. 이러한 기능은 이 자습서 뒷부분에서 사용됩니다.

1. 파일을 저장합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="create-and-run-the-chat-client-web-user-interface"></a>채팅 클라이언트 웹 사용자 인터페이스 만들기 및 실행

채팅 애플리케이션의 UI는 Vue JavaScript 프레임워크를 사용하여 만든 간단한 SPA(단일 페이지 애플리케이션)입니다. 함수 앱과 별도로 호스팅됩니다. 로컬에서는 Live Server VS Code 확장을 사용하여 웹 인터페이스를 실행합니다.

1. VS Code에서 주 프로젝트 폴더의 루트에 **content**라는 새 폴더를 만듭니다.

1. **content** 폴더에서 **index.html**이라는 새 파일을 만듭니다.

1. **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)** 의 내용을 복사하여 붙여넣습니다.

1. 파일을 저장합니다.

1. **F5** 키를 눌러 함수 앱을 로컬로 실행하고 디버거를 연결합니다.

1. **index.html**이 열린 상태에서 VS Code 명령 팔레트를 열고(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`), **Live Server: Live Server로 열기**를 선택하여 Live Server를 시작합니다. Live Server는 브라우저에서 애플리케이션을 엽니다.

1. 애플리케이션이 열립니다. 채팅 상자에서 메시지를 입력하고 Enter 키를 누릅니다. 새 메시지를 보려면 애플리케이션을 새로 고칩니다. 인증이 구성되지 않았으므로 모든 메시지는 "익명"으로 보내집니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="deploy-to-azure-and-enable-authentication"></a>Azure에 배포 및 인증을 사용하도록 설정

함수 앱 및 채팅 애플리케이션을 로컬로 실행했습니다. 이제 이러한 앱을 Azure에 배포하고 애플리케이션에서 인증 및 프라이빗 메시징을 사용하도록 설정합니다.

### <a name="log-into-azure-with-vs-code"></a>VS Code를 사용하여 Azure에 로그인

1. VS Code 명령 팔레트를 엽니다(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure: 로그인** 명령을 검색하고 선택합니다.

1. 지침에 따라 브라우저에서 로그인 프로세스를 수행합니다.

### <a name="create-a-storage-account"></a>Storage 계정 만들기

Azure에서 실행되는 함수 앱에는 Azure Storage 계정이 필요합니다. Azure Storage의 정적 웹 사이트 기능을 사용하여 채팅 UI용 웹 페이지도 호스팅합니다.

1. Azure Portal에서 새 Azure 리소스를 만들려면 **리소스 만들기**( **+** ) 단추를 클릭합니다.

1. **스토리지** 범주를 선택한 다음, **스토리지 계정**을 선택합니다.

1. 다음 정보를 입력합니다.

    | Name | 값 |
    |---|---|
    | Subscription | SignalR Service 인스턴스가 포함된 구독 선택 |
    | 리소스 그룹 | 동일한 리소스 그룹 선택 |
    | 리소스 이름 | 스토리지 계정에 대한 고유한 이름 |
    | 위치 | 다른 리소스와 동일한 위치 선택 |
    | 성능 | 표준 |
    | 계정 종류 | StorageV2(범용 V2) |
    | 복제 | LRS(로컬 중복 스토리지) |
    | 액세스 계층 | 핫 |

1. **검토 + 만들기**와 **만들기**를 차례로 클릭합니다.

### <a name="configure-static-websites"></a>정적 웹 사이트 구성

1. 스토리지 계정이 만들어지면 Azure Portal에서 엽니다.

1. **정적 웹 사이트**를 선택합니다.

1. **사용**을 선택하여 정적 웹 사이트 기능을 사용하도록 설정합니다.

1. **인덱스 문서 이름**에 *index.html*을 입력합니다.

1. **Save**을 클릭합니다.

1. **기본 엔드포인트**가 나타납니다. 이 값을 적어 둡니다. 함수 앱을 구성하는 데 필요합니다.

### <a name="configure-function-app-for-authentication"></a>인증을 위한 함수 앱 구성

지금까지 채팅 앱은 익명으로 작동합니다. Azure에서는 [App Service 인증](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)을 사용하여 사용자를 인증합니다. 인증된 사용자의 사용자 ID 또는 사용자 이름을 *SignalRConnectionInfo* 바인딩에 전달하여 사용자로 인증된 연결 정보를 생성할 수 있습니다.

메시지를 보내는 경우 연결된 모든 클라이언트에 보낼지, 아니면 지정한 사용자에게 인증된 클라이언트에만 보낼지 여부를 결정할 수 있습니다.

1. VS Code에서 **negotiate/function.json**을 엽니다.

1. *SignalRConnectionInfo* 바인딩의 *userId* 속성에 [바인딩 식](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)(`{headers.x-ms-client-principal-name}`)을 삽입합니다. 이렇게 하면 값이 인증된 사용자의 사용자 이름으로 설정됩니다. 특성은 이제 다음과 같습니다.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. 파일을 저장합니다.


### <a name="deploy-function-app-to-azure"></a>Azure에 함수 앱 배포

1. VS Code 명령 팔레트를 열고(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`), **Azure Functions: 함수 앱에 배포**를 선택합니다.

1. 메시지가 표시되면 다음 정보를 제공합니다.

    | 이름 | 값 |
    |---|---|
    | 배포할 폴더 | 주 프로젝트 폴더를 선택합니다. |
    | 구독 | 구독 선택 |
    | 함수 앱 | **새 함수 앱 만들기**를 선택합니다. |
    | 함수 앱 이름 | 고유한 이름을 입력합니다. |
    | 리소스 그룹 | SignalR Service 인스턴스와 동일한 리소스 그룹을 선택합니다. |
    | 스토리지 계정 | 이전에 만든 스토리지 계정을 선택합니다. |

    새 함수 앱이 Azure에 만들어지고 배포가 시작됩니다. 배포가 완료될 때가지 기다립니다.

### <a name="upload-function-app-local-settings"></a>함수 앱 로컬 설정 업로드

1. VS Code 명령 팔레트를 엽니다(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure Functions: 로컬 설정 업로드** 명령을 검색하여 선택합니다.

1. 메시지가 표시되면 다음 정보를 제공합니다.

    | 이름 | 값 |
    |---|---|
    | 로컬 설정 파일 | local.settings.json |
    | 구독 | 구독 선택 |
    | 함수 앱 | 이전에 배포한 함수 앱을 선택합니다. |

로컬 설정이 Azure의 함수 앱에 업로드됩니다. 기존 설정을 덮어쓸지 묻는 메시지가 표시되면 **모두 예**를 선택합니다.


### <a name="enable-app-service-authentication"></a>App Service 인증을 사용하도록 설정

App Service 인증은 Azure Active Directory, Facebook, Twitter, Microsoft 계정 및 Google을 통해 인증을 지원합니다.

1. VS Code 명령 팔레트를 엽니다(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure Functions: 포털에서 열기** 명령을 검색하여 선택합니다.

1. 구독 및 함수 앱 이름을 선택하여 Azure Portal에서 함수 앱을 엽니다.

1. 포털에 열린 함수 앱에서, **플랫폼 기능** 탭을 찾아서 **인증/권한 부여**를 선택합니다.

1. App Service 인증을 **켜기**로 설정합니다.

1. **요청이 인증되지 않은 경우 수행할 작업**에서 "{이전에 선택한 인증 공급자}를 사용하여 로그인"을 선택합니다.

1. **허용되는 외부 리디렉션 URL**에서 앞에서 적어 둔 스토리지 계정 기본 웹 엔드포인트의 URL을 입력합니다.

1. 선택한 로그인 공급자에 대한 설명서에 따라 구성을 완료합니다.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Microsoft 계정](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="update-the-web-app"></a>웹앱 업데이트

1. Azure Portal에서 함수 앱의 개요 페이지로 이동합니다.

1. 함수 앱의 URL을 복사합니다.

    ![URL 가져오기](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. VS Code에서 **index.html**을 열고 `apiBaseUrl`의 값을 함수 앱의 URL로 바꿉니다.

1. 애플리케이션은 Azure Active Directory, Facebook, Twitter, Microsoft 계정 또는 Google을 사용하여 인증을 통해 구성할 수 있습니다. `authProvider` 값을 설정하여 사용할 인증 공급자를 선택합니다.

1. 파일을 저장합니다.

### <a name="deploy-the-web-application-to-blob-storage"></a>Blob Storage에 웹 애플리케이션 배포

웹 애플리케이션은 Azure Blob Storage의 정적 웹 사이트 기능을 사용하여 호스팅됩니다.

1. VS Code 명령 팔레트를 엽니다(`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. **Azure Storage: 정적 웹 사이트에 배포** 명령을 검색하여 선택합니다.

1. 다음 값을 입력합니다.

    | Name | 값 |
    |---|---|
    | 구독 | 구독 선택 |
    | 스토리지 계정 | 이전에 만든 스토리지 계정을 선택합니다. |
    | 배포할 폴더 | **찾아보기**를 선택하고 *content* 폴더를 선택합니다. |

*content* 폴더의 파일이 정적 웹 사이트에 배포됩니다.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>함수 앱 CORS(원본 간 리소스 공유)를 사용하도록 설정

CORS 설정은 **local.settings.json**에 있지만 Azure의 함수 앱에 전파되지 않습니다. 따라서 개별적으로 설정해야 합니다.

1. Azure Portal에서 함수 앱을 엽니다.

1. **플랫폼 기능** 탭에서 **CORS**를 선택합니다.

    ![CORS 찾기](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. 허용된 원본(*Allowed origins*) 섹션에서 정적 웹 사이트 기본 엔드포인트(*primary endpoint*)가 있는 항목을 값으로 추가합니다(후행 */* 제거).

1. SignalR JavaScript SDK가 브라우저에서 함수 앱을 호출하려면 CORS의 자격 증명에 지원을 사용하도록 설정되어 있어야 합니다. "Access-Control-Allow-Credentials 사용" 확인란을 선택합니다.

    ![Access-Control-Allow-Credentials 사용](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. **저장**을 클릭하여 CORS 설정을 유지합니다.

### <a name="try-the-application"></a>애플리케이션 사용해 보기

1. 브라우저에서 스토리지 계정의 기본 웹 엔드포인트로 이동합니다.

1. **로그인**을 선택하여 선택한 인증 공급자를 통해 인증합니다.

1. 기본 채팅 상자에 공개 메시지를 입력하여 보냅니다.

1. 채팅 기록에서 사용자 이름을 클릭하여 프라이빗 메시지를 보냅니다. 선택한 받는 사람만 이러한 메시지를 받습니다.

축하합니다! 실시간 서버리스 채팅 앱을 배포했습니다!

![데모](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 Azure Portal을 사용하여 해당 리소스 그룹을 삭제합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure SignalR Service에서 Azure Functions를 사용하는 방법을 알아보았습니다. Azure Functions에 대한 SignalR Service 바인딩을 사용하여 실시간 서버리스 애플리케이션을 빌드하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Functions를 사용하여 실시간 앱 빌드](signalr-concept-azure-functions.md)

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsauth)

