---
title: Visual Studio Code를 사용한 .NET Core 애플리케이션 개발
description: 이 문서에서는 Visual Studio Code를 사용하여 .NET Core Service Fabric 애플리케이션을 빌드, 배포 및 디버그하는 방법을 보여 줍니다.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 5fbd523a38b3c4860316e45b8b7c03a17de19499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92678331"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Visual Studio Code를 사용하여 C# Service Fabric 애플리케이션 개발

[VS Code용 Service Fabric Reliable Services 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)을 사용하면 Windows, Linux 및 macOS 운영 체제에서 .NET Core Service Fabric 애플리케이션을 쉽게 빌드할 수 있습니다.

이 문서에서는 Visual Studio Code를 사용하여 .NET Core Service Fabric 애플리케이션을 빌드, 배포 및 디버그하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 VS Code, VS Code용 Service Fabric Reliable Services 확장 및 개발 환경에 필요한 모든 종속성을 설치했다고 가정합니다. 자세한 내용은 [시작](./service-fabric-get-started-vs-code.md#prerequisites)을 참조하세요.

## <a name="download-the-sample"></a>샘플 다운로드
이 문서에서는 [Service Fabric .NET Core 시작 샘플 GitHub 리포지토리](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)의 CounterService 애플리케이션을 사용합니다. 

개발 컴퓨터에 리포지토리를 복제하려면 터미널 창(Windows의 명령 창)에서 다음 명령을 실행합니다.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>VS Code에서 애플리케이션 열기

### <a name="windows"></a>Windows
시작 메뉴에서 VS Code 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행** 을 선택합니다. 디버거를 서비스에 연결하려면 관리자 권한으로 VS Code를 실행해야 합니다.

### <a name="linux"></a>Linux
터미널을 사용하여 애플리케이션이 로컬로 복제되는 디렉터리에서 /service-fabric-dotnet-core-getting-started/Services/CounterService 경로로 이동합니다.

디버거가 서비스에 연결할 수 있도록 다음 명령을 실행하여 루트 사용자로 VS Code를 엽니다.
```
sudo code . --user-data-dir='.'
```

이제 애플리케이션이 VS Code 작업 영역에 나타납니다.

![작업 영역의 Counter Service 애플리케이션](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>애플리케이션 빌드
1. VS Code에서 (Ctrl + Shift + p)를 눌러 **명령 팔레트** 를 엽니다.
2. **Service Fabric: Build Application** 명령을 검색한 후 선택합니다. 빌드 출력이 통합된 터미널로 전송됩니다.

   ![VS Code의 Build Application 명령](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>로컬 클러스터에 애플리케이션 배포
애플리케이션이 빌드되면 로컬 클러스터에 배포할 수 있습니다. 

1. **명령 팔레트** 에서 **Service Fabric: Deploy Application (Localhost) 명령** 을 선택합니다. 설치 프로세스의 출력이 통합된 터미널로 전송됩니다.

   ![VS Code의 Deploy Application 명령](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. 배포가 완료되면 브라우저를 시작하고 Service Fabric Explorer(http:\//localhost:19080/Explorer)를 엽니다. 애플리케이션이 실행되고 있는 것을 확인할 수 있습니다. 다소 시간이 소요되니 기다려 주세요. 

   ![Service Fabric Explorer의 Counter Service 애플리케이션](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. 애플리케이션이 실행되고 있는지 확인한 후 브라우저를 시작하고 http:\//localhost:31002 페이지를 엽니다. 애플리케이션의 웹 프런트 엔드입니다. 증가하는 카운터의 현재 값을 확인하려면 페이지를 새로 고칩니다.

   ![브라우저의 Counter Service 애플리케이션](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Azure Service Fabric 클러스터에 애플리케이션 게시
로컬 클러스터에 애플리케이션을 배포하는 것과 함께 원격 Azure Service Fabric 클러스터에 애플리케이션을 게시할 수도 있습니다. 

1. 위의 지침을 사용하여 애플리케이션을 빌드했는지 확인합니다. 게시하려는 원격 클러스터의 세부 정보를 사용하여 생성된 구성 파일 `Cloud.json`을 업데이트합니다.

2. **명령 팔레트** 에서 **Service Fabric: Publish Application 명령** 을 선택합니다. 설치 프로세스의 출력이 통합된 터미널로 전송됩니다.

   ![VS Code의 Publish Application 명령](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. 배포가 완료되면 브라우저를 시작하고 `https:<clusterurl>:19080/Explorer`의 Service Fabric Explorer를 엽니다. 애플리케이션이 실행되고 있는 것을 확인할 수 있습니다. 다소 시간이 소요되니 기다려 주세요. 

## <a name="debug-the-application"></a>애플리케이션 디버그
VS Code에서 애플리케이션을 디버그할 때 애플리케이션은 로컬 클러스터에서 실행되고 있어야 합니다. 그래야 코드에 중단점을 추가할 수 있습니다.

중단점을 설정하고 디버그하려면 다음 단계를 수행합니다.
1. Explorer에서 */src/CounterServiceApplication/CounterService/CounterService.cs* 파일을 열고 `RunAsync` 메서드 내에서 줄 62에 중단점을 설정합니다.
3. **작업 막대** 에서 디버그 아이콘을 클릭하여 VS Code에서 디버거 보기를 엽니다. 디버거 보기의 맨 위에 있는 톱니바퀴 아이콘을 클릭하고 드롭다운 메뉴에서 **.NET Core** 를 선택합니다. launch.json 파일이 열립니다. 이 파일을 닫을 수 있습니다. 이제 실행 단추(녹색 화살표) 옆에 있는 디버그 구성 메뉴에 구성 옵션이 표시됩니다.

   ![VS Code 작업 영역의 디버그 아이콘](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. 디버그 구성 메뉴에서 **.NET Core 연결** 을 선택합니다.

   ![디버그 구성 메뉴에서 선택한 .NET Core Attach를 보여 주는 스크린샷](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. 브라우저에서 http: \/ /shosts: 19080/Explorer를 입력하여 Service Fabric Explorer를 엽니다. **애플리케이션** 을 클릭하고 드릴다운하여 CounterService가 실행되고 있는 주 노드를 확인합니다. 아래 이미지에서 CounterService의 주 노드는 노드 0입니다.

   ![CounterService의 주 노드](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. VS Code에서 **.NET Core 연결** 디버그 구성 옆에 있는 실행 아이콘(녹색 화살표)을 클릭합니다. 프로세스 선택 대화 상자로 가서 4단계에서 식별된 주 노드에서 실행되는 CounterService 프로세스를 선택합니다.

   ![기본 프로세스](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. *CounterService.cs* 파일의 중단점은 매우 빠르게 적중됩니다. 그런 후에는 로컬 변수의 값을 탐색할 수 있습니다. VS Code 맨 위에 있는 디버그 도구 모음을 사용하여 실행을 계속하거나, 줄을 건너뛰거나, 메서드를 한 단계씩 실행하거나, 현재 메서드에서 나갑니다. 

   ![값 디버그](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. 디버깅 세션을 종료하려면 VS Code 맨 위에 있는 디버그 도구 모음에서 플러그 아이콘을 클릭합니다.
   
   ![디버거에서 연결 끊기](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. 디버깅을 완료한 경우 **Service Fabric: Remove Application** 명령을 사용하여 로컬 클러스터에서 CounterService 애플리케이션이 제거할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [VS Code를 통해 Java Service Fabric 애플리케이션 개발 및 디버그](./service-fabric-develop-java-applications-with-vs-code.md) 방법을 알아봅니다.



