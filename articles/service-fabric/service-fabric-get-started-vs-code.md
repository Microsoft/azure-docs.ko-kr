---
title: Azure Service Fabric VS Code 시작 하기
description: 이 문서에서는 Visual Studio Code를 사용하여 Service Fabric 애플리케이션을 만드는 과정을 대략적으로 설명합니다.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 67846293257661a4f7a907d76402d2e98c339225
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251637"
---
# <a name="service-fabric-for-visual-studio-code"></a>Visual Studio Code용 Service Fabric

[VS Code용 Service Fabric Reliable Services 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)은 Windows, Linux 및 macOS 운영 체제에서 Java Service Fabric 애플리케이션을 만들고 빌드하고 디버그하는 데 필요한 도구를 제공합니다.

또한 이 문서에서는 확장이 제공하는 다양한 명령의 사용법과 확장의 요구 사항 및 설치에 대해서도 간단히 설명합니다. 

> [!IMPORTANT]
> Service Fabric Java 애플리케이션은 Windows 컴퓨터에서 개발할 수 있지만 Azure Linux 클러스터에만 배포할 수 있습니다. Windows에서는 Java 애플리케이션을 디버그할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 필수 구성 요소를 모든 환경에 설치해야 합니다.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.JS](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman 생성기 -- 애플리케이션에 적절한 생성기 설치

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Java 개발을 위해서는 다음 필수 구성 요소를 설치해야 합니다.

* [Java SDK](https://aka.ms/azure-jdks)(버전 1.8)
* [Gradle](https://gradle.org/install/)
* [Java VS Code 확장용 디버거](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Java 서비스를 디버그하는 데 필요합니다. Java 서비스 디버깅은 Linux에서만 지원됩니다. VS Code의 **작업 막대**에서 확장 아이콘을 클릭하고 해당 확장을 검색하거나 VS Code Marketplace에서 설치할 수 있습니다.

.NET Core/C# 개발을 위해서는 다음 필수 구성 요소를 설치해야 합니다.

* [.NET Core](https://www.microsoft.com/net/learn/get-started)(버전 2.0.0 이상)
* [Visual Studio Code용 C#(OmniSharp 제공) VS Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) C# 서비스 디버그에 필요합니다. VS Code의 **작업 막대**에서 확장 아이콘을 클릭하고 해당 확장을 검색하거나 VS Code Marketplace에서 설치할 수 있습니다.

## <a name="setup"></a>설치 프로그램

1. VS Code를 엽니다.
2. VS Code 왼쪽의 **작업 막대**에서 확장 아이콘을 클릭합니다. "Service Fabric"을 검색합니다. Service Fabric Reliable Services 확장에 대해 **설치**를 클릭합니다.

## <a name="commands"></a>명령
VS Code용 Service Fabric Reliable Services 확장은 개발자가 Service Fabric 프로젝트를 만들고 배포하는 데 도움이 되는 많은 명령을 제공합니다. **을 누르거나, 입력 표시줄에 명령 이름을 입력하거나, 프롬프트 목록에서 원하는 명령을 선택하여** 명령 팔레트`(Ctrl + Shift + p)`에서 명령을 호출할 수 있습니다. 

* Service Fabric: Create Application 
* Service Fabric: Publish Application 
* Service Fabric: Deploy Application 
* Service Fabric: Remove Application  
* Service Fabric: Build Application 
* Service Fabric: Clean Application 

### <a name="service-fabric-create-application"></a>Service Fabric: Create Application

**Service Fabric: Create Application** 명령은 현재 작업 영역에서 새 Service Fabric 애플리케이션을 만듭니다. 개발 컴퓨터에 설치되는 yeoman 생성기에 따라, Java, C#, Container 및 Guest 프로젝트를 비롯한 여러 유형의 Service Fabric 애플리케이션을 만들 수 있습니다. 

1.  **Service Fabric: 응용 프로그램 만들기** 명령을 선택 합니다.
2.  새 Service Fabric 애플리케이션의 유형을 선택합니다. 
3.  만들려는 애플리케이션의 이름을 입력합니다.
3.  Service Fabric 애플리케이션에 추가하려는 서비스의 유형을 선택합니다. 
4.  지시에 따라 서비스 이름을 지정합니다. 
5.  새 Service Fabric 애플리케이션이 작업 영역에 표시됩니다.
6.  작업 영역의 루트 폴더로 사용할 새 애플리케이션 폴더를 엽니다. 여기에서 명령을 계속 실행할 수 있습니다.

### <a name="service-fabric-add-service"></a>Service Fabric: Add Service
**Service Fabric: Add Service** 명령은 기존 Service Fabric 애플리케이션에 새 서비스를 추가합니다. 서비스가 추가될 애플리케이션은 작업 영역의 루트 디렉터리여야 합니다. 

1.  **Service Fabric: Add Service** 명령을 선택합니다.
2.  현재 Service Fabric 애플리케이션의 유형을 선택합니다. 
3.  Service Fabric 애플리케이션에 추가하려는 서비스의 유형을 선택합니다. 
4.  지시에 따라 서비스 이름을 지정합니다. 
5.  새 서비스가 프로젝트 디렉터리에 표시됩니다. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publish Application
**Service Fabric: Publish Application** 명령은 원격 클러스터에서 Service Fabric 애플리케이션을 배포합니다. 대상 클러스터는 보안 또는 비보안 클러스터일 수 있습니다. Cloud.json에서 매개 변수를 설정하지 않은 경우, 애플리케이션은 로컬 클러스터에 배포됩니다.

1.  애플리케이션이 처음 빌드될 때 Cloud.json 파일이 프로젝트 디렉터리에 생성됩니다.
2.  연결하려는 클러스터에 대한 값을 Cloud.json 파일에 입력합니다.
3.  **Service Fabric: Publish Application** 명령을 선택합니다.
4.  Service Fabric Explorer를 사용하여 대상 클러스터에 애플리케이션이 설치되어 있는지 확인합니다. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Deploy Application(Localhost)
**Service Fabric: Deploy Application** 명령은 로컬 클러스터에 Service Fabric 애플리케이션을 배포합니다. 이 명령을 사용하기 전에 로컬 클러스터가 실행되고 있는지 확인합니다. 

1. **Service Fabric: Deploy Application** 명령을 선택합니다.
2. Service Fabric Explorer를 사용 하 여 로컬 클러스터를 확인 합니다 (http:\//sts: 19080/Explorer). 응용 프로그램이 설치 되었는지 확인 합니다. 다소 시간이 소요되니 기다려 주세요.
3. Cloud.json 파일에서 매개 변수를 설정하지 않은 상태로 **Service Fabric: Publish Application** 명령을 사용하여 로컬 클러스터에 배포할 수도 있습니다.

> [!NOTE]
> Windows 컴퓨터에서는 로컬 클러스터로 Java 애플리케이션을 배포할 수 없습니다.

### <a name="service-fabric-remove-application"></a>Service Fabric: Remove Application
**Service Fabric: Remove Application** 명령은 VS Code 확장을 사용하여 이전에 배포된 클러스터에서 Service Fabric 애플리케이션을 제거합니다. 

1.  **Service Fabric: Remove Application** 명령을 선택합니다.
2.  Service Fabric Explorer를 사용하여 클러스터에서 애플리케이션이 제거되었는지 확인합니다. 다소 시간이 소요되니 기다려 주세요.

### <a name="service-fabric-build-application"></a>Service Fabric: Build Application
**Service Fabric: Build Application** 명령은 Java 또는 C# Service Fabric 응용 프로그램을 빌드할 수 있습니다. 

1.  이 명령을 실행하기 전에 애플리케이션 루트 폴더에 있는지 확인합니다. 이 명령은 애플리케이션의 유형(C# 또는 Java)을 식별하고 그에 따라 애플리케이션을 빌드합니다.
2.  **Service Fabric: Build Application** 명령을 선택합니다.
3.  빌드 프로세스의 출력이 통합된 터미널에 기록됩니다.

### <a name="service-fabric-clean-application"></a>Service Fabric: Clean Application
**Service Fabric: Clean Application** 명령은 빌드를 통해 생성된 모든 jar 파일 및 네이티브 라이브러리를 삭제합니다. Java 애플리케이션의 경우에만 유효합니다. 

1.  이 명령을 실행하기 전에 애플리케이션 루트 폴더에 있는지 확인합니다. 
2.  **Service Fabric: Clean Application** 명령을 선택합니다.
3.  정리 프로세스의 출력이 통합된 터미널에 기록됩니다.

## <a name="next-steps"></a>다음 단계

* [VS Code를 통해 C# Service Fabric 애플리케이션 개발 및 디버그](./service-fabric-develop-csharp-applications-with-vs-code.md) 방법을 알아봅니다.
* [VS Code를 통해 Java Service Fabric 애플리케이션 개발 및 디버그](./service-fabric-develop-java-applications-with-vs-code.md) 방법을 알아봅니다.
