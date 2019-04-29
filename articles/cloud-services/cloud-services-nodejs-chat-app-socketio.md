---
title: Socket.io를 사용하는 Node.js 애플리케이션 - Azure
description: Azure에 호스트된 node.js 애플리케이션에서 socket.io를 사용하는 방법을 알아봅니다.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: cd0bceae770182e778410d8065d34dfeed055acc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433204"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Azure 클라우드 서비스에서 Socket.IO를 사용하여 Node.js 채팅 애플리케이션 빌드

Socket.IO는 node.js 서버와 클라이언트 간에 실시간 통신을 제공합니다. 이 자습서는 Azure에서 채팅 애플리케이션을 기반으로 하는 socket.IO 호스팅에 대해 안내합니다. Socket.IO에 대한 자세한 내용은 [socket.io](https://socket.io)를 참조하세요.

아래에는 완성된 애플리케이션의 스크린샷이 표시되어 있습니다.

![Azure에 호스트된 서비스를 표시하는 브라우저 창][completed-app]  

## <a name="prerequisites"></a>필수 조건
이 문서의 예제를 완료하려면 다음 제품 및 버전이 설치되어 있는지 확인합니다.

*  [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* [Node.js](https://nodejs.org/download/)
*  [Python 버전 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>클라우드 서비스 프로젝트 만들기
다음은 Socket.IO 애플리케이션을 호스트하는 클라우드 서비스 프로젝트를 만드는 단계입니다.

1. **시작 메뉴** 또는 **시작 화면**에서 **Windows PowerShell**을 검색합니다. 마지막으로, **Windows PowerShell**을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.
   
    ![Azure PowerShell 아이콘][powershell-menu]
2. **c:\\node**라는 디렉터리를 만듭니다. 
   
        PS C:\> md node
3. 디렉터리를 **c:\\node** 디렉터리로 변경합니다.
   
        PS C:\> cd node
4. 다음 명령을 입력하여 **chatapp**라는 이름의 새 솔루션과 **WorkerRole1**이라는 작업자 역할을 만듭니다.
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    You will see the following response:
   
    ![new-azureservice 및 add-azurenodeworkerrolecmdlets의 출력](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>채팅 예제 다운로드
이 프로젝트에서는 [Socket.IO GitHub 리포지토리](영문)의 채팅 예제를 사용합니다. 다음 단계에 따라 예제를 다운로드하여 이전에 만든 프로젝트에 추가하세요.

1. **복제** 단추를 눌러 리포지토리의 로컬 복사본을 만듭니다. **ZIP** 단추를 눌러 프로젝트를 다운로드할 수도 있습니다.
   
   ![ZIP 다운로드 아이콘이 강조 표시된 https://github.com/LearnBoost/socket.io/tree/master/examples/chat을 보는 브라우저 창](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. **examples\\chat** 디렉터리를 찾을 때까지 로컬 리포지토리의 디렉터리 구조를 탐색합니다. 이 디렉터리의 내용을 이전에 만든 **C:\\node\\chatapp\\WorkerRole1** 디렉터리로 복사합니다.
   
   ![보관 파일에서 압축을 푼 examples\\chat 디렉터리의 내용을 표시하는 탐색기][chat-contents]
   
   위 스크린샷에서 강조 표시된 항목은 **examples\\chat** 디렉터리에서 복사한 파일입니다.
3. **C:\\node\\chatapp\\WorkerRole1** 디렉터리에서 **server.js** 파일을 삭제한 다음 **app.js** 파일의 이름을 **server.js**로 변경합니다. 그러면 이전에 **Add-AzureNodeWorkerRole** cmdlet로 만든 기본 **server.js** 파일이 제거되고 채팅 예제의 애플리케이션 파일로 바뀝니다.

### <a name="modify-serverjs-and-install-modules"></a>Server.js 수정 및 모듈 설치
Azure 에뮬레이터에서 애플리케이션을 테스트하기 전에 몇 가지 항목을 수정해야 합니다. server.js 파일에 대해 다음 단계를 수행합니다.

1. Visual Studio 또는 임의의 텍스트 편집기에서 **server.js** 파일을 엽니다.
2. server.js의 시작 부분에서 **모듈 종속성** 섹션을 찾아 아래와 같이 **sio = require('..//..//lib//socket.io')** 가 포함된 줄을 **sio = require('socket.io')** 로 변경합니다.
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. 애플리케이션이 올바른 포트에서 수신하도록 메모장 또는 좋아하는 편집기에서 server.js를 연 후 아래와 같이 다음 줄에서 **3000**을 **process.env.port**로 변경합니다.
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

**server.js**에 변경 내용을 저장한 후 다음 단계에 따라 필요한 모듈을 설치하고 Azure 에뮬레이터에서 애플리케이션을 테스트합니다.

1. **Azure PowerShell**을 사용하여 디렉터리를 **C:\\node\\chatapp\\WorkerRole1** 디렉터리를 변경하고 다음 명령을 사용하여 이 애플리케이션에서 필요한 모듈을 설치합니다.
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   그러면 package.json 파일에 나열된 모듈이 설치됩니다. 명령을 완료한 후 다음과 유사한 출력이 표시됩니다.
   
   ![npm 설치 명령의 출력][The-output-of-the-npm-install-command]
2. 이 예제는 원래 Socket.IO GitHub 리포지토리의 일부이고, 상대 경로에서 Socket.IO 라이브러리를 직접 참조하며 package.json 파일에서 Socket.IO가 참조되지 않기 때문에 다음 명령을 실행하여 모듈을 설치해야 합니다.
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>테스트 및 배포
1. 다음 명령을 실행하여 에뮬레이터를 시작합니다.
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > 예를 들어 에뮬레이터 시작 문제가 발생 하는 경우.: Start-azureemulator: 예기치 않은 오류가 발생 했습니다.  세부 정보: 발생 한 예기치 않은 오류가 통신 개체 System.ServiceModel.Channels.ServiceChannel Faulted 상태에서 이기 때문에 통신에 사용할 수 없습니다.
   > 
   > AzureAuthoringTools v 2.7.1 및 AzureComputeEmulator v 2.7 다시 설치 하십시오-버전이 일치 하는지 확인 합니다.

2. 브라우저를 열고 **http://127.0.0.1**로 이동합니다.
3. 브라우저 창이 열리면 애칭을 입력하고 Enter 키를 누릅니다.
   이렇게 하면 특정 애칭으로 메시지를 게시할 수 있습니다. 다중 사용자 기능을 테스트하려면 같은 URL을 사용하여 브라우저 창을 추가로 열고 다른 애칭을 입력합니다.
   
   ![User1 및 User2의 채팅 메시지를 표시하는 두 브라우저 창](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. 애플리케이션을 테스트한 후 다음 명령을 실행하여 에뮬레이터를 중지합니다.
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Azure에 애플리케이션을 배포하려면 **Publish-AzureServiceProject** cmdlet을 사용합니다. 예를 들면 다음과 같습니다.
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > 고유한 이름을 사용해야 합니다. 그렇지 않으면 게시 프로세스가 실패합니다. 배포가 완료되면 브라우저가 열리고 배포된 서비스로 이동합니다.
   > 
   > 제공한 구독 이름이 가져온 게시 프로필에 존재하지 않는다는 내용의 오류를 받게 되는 경우, Azure를 배포하기 전에 구독에 대한 게시 프로필을 다운로드하고 가져와야 합니다. **Azure 클라우드 서비스에 Node.js 애플리케이션 빌드 및 배포** (영문)에서 [Azure에 애플리케이션 배포](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Azure에 호스트된 서비스를 표시하는 브라우저 창][completed-app]
   
   > [!NOTE]
   > 제공한 구독 이름이 가져온 게시 프로필에 존재하지 않는다는 내용의 오류를 받게 되는 경우, Azure를 배포하기 전에 구독에 대한 게시 프로필을 다운로드하고 가져와야 합니다. **Azure 클라우드 서비스에 Node.js 애플리케이션 빌드 및 배포** (영문)에서 [Azure에 애플리케이션 배포](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

현재 애플리케이션이 Azure에서 실행되고 있으며 Socket.IO를 사용하여 다른 클라이언트 간에 채팅 메시지를 릴레이할 수 있습니다.

> [!NOTE]
> 간단히 하기 위해 샘플은 같은 인스턴스에 연결된 사용자 간 채팅으로 제한됩니다. 즉 클라우드 서비스에서 작업자 역할 인스턴스를 두 개 만드는 경우 사용자는 같은 작업자 역할 인스턴스에 연결된 사람들과만 채팅할 수 있습니다. 애플리케이션을 여러 역할 인스턴스와 작업하도록 조정하려면 Service Bus 같은 기술을 사용하여 인스턴스 간에 Socket.IO 저장소 상태를 공유할 수 있습니다. 예를 들어, [Node.js에 대한 Azure SDK GitHub 리포지토리](https://github.com/WindowsAzure/azure-sdk-for-node)(영문)의 Service Bus 큐 및 토픽 사용 샘플을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure 클라우드 서비스에 호스팅된 기본 채팅 애플리케이션을 만드는 방법을 학습했습니다. 이 애플리케이션을 Azure 웹 사이트에 호스트하는 방법을 학습하려면 [Azure 웹 사이트에서 Socket.IO를 사용하여 Node.js 채팅 애플리케이션 빌드][chatwebsite]를 참조하세요.

자세한 내용은 [Node.js 개발자 센터](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest)도 참조하세요.

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub 리포지토리]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


