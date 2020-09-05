---
title: Azure Dev Spaces에서 Visual Studio Code 작동 하는 방법
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Kubernetes 응용 프로그램을 디버그 하 고 신속 하 게 반복 하는 데 도움이 되는 Visual Studio Code 및 Azure Dev Spaces 방법을 알아보세요.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: decf61bd55ed15182f030bb02a2fe9c959749556
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209875"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Azure Dev Spaces에서 Visual Studio Code 작동 하는 방법

Visual Studio Code 및 [Azure Dev Spaces 확장][azds-extension] 을 사용 하 여 Azure Dev Spaces으로 서비스를 준비, 실행 및 디버그할 수 있습니다. Visual Studio Code 및 Azure Dev Spaces 확장을 사용 하면 다음을 수행할 수 있습니다.

* AKS에서 실행 중인 서비스 및 디버깅을 위한 자산 생성
* 개발 공간에서 Java, Node.js 및 .NET Core 서비스를 실행 합니다.
* 개발 공간에서 실행 되는 Java, Node.js 및 .NET Core 서비스를 직접 디버그

## <a name="generate-assets"></a>자산 생성

Visual Studio Code 및 Azure Dev Spaces 확장은 프로젝트에 대해 다음과 같은 자산을 생성 합니다.

* Maven, Node.js 응용 프로그램 및 .NET Core 응용 프로그램을 사용 하는 Java 응용 프로그램용 dockerfiles
* Dockerfile을 사용 하는 거의 모든 언어에 대 한 투구 차트
* `azds.yaml`프로젝트에 대 한 [Azure Dev Spaces 구성 파일인][azds-yaml] 파일
* `.vscode`Maven, Node.js 응용 프로그램 및 .Net Core 응용 프로그램을 사용 하 여 Java 응용 프로그램에 대 한 프로젝트의 Visual Studio Code 시작 구성이 포함 된 폴더

Dockerfile, 투구 차트 및 파일은를 `azds.yaml` 실행할 때 생성 되는 것과 동일한 자산 `azds prep` 입니다. 이러한 파일은 Visual Studio code 외부에서 AKS에서 프로젝트를 실행 하는 등의 방법으로도 사용할 수 있습니다 `azds up` . `.vscode`이 폴더는 Visual Studio code에서 VISUAL STUDIO CODE AKS의 프로젝트를 실행 하는 데만 사용 됩니다.

## <a name="run-your-service-in-aks"></a>AKS에서 서비스를 실행 합니다.

프로젝트에 대 한 자산을 생성 한 후에는 Visual Studio Code에서 기존 개발 공간에 Java, Node.js 및 .NET Core 서비스를 실행할 수 있습니다. Visual Studio Code의 *디버그* 페이지에서 디렉터리의 시작 구성을 호출 하 여 프로젝트를 실행할 수 있습니다 `.vscode` .

AKS 클러스터를 만들고 Visual Studio Code 외부에서 클러스터의 Azure Dev Spaces를 사용 하도록 설정 해야 합니다. 을 `azds.yaml` 실행 하 여 생성 된 자산과 같이 Visual Studio Code 외부에서 만든 기존 Dockerfiles, 투구 차트 및 파일을 다시 사용할 수 있습니다 `azds prep` . Visual Studio Code 외부로 생성 된 자산을 다시 사용 하는 경우에도 `.vscode` 디렉터리가 있어야 합니다. 이 `.vscode` 디렉터리는 Visual Studio code 및 Azure Dev Spaces 확장에 의해 다시 생성 될 수 있으며 기존 자산을 덮어쓰지 않습니다.

.NET Core 프로젝트의 경우 Visual Studio Code에서 .NET 서비스를 실행 하려면 [c # 확장이][csharp-extension] 설치 되어 있어야 합니다. 또한 Maven를 사용 하는 Java 프로젝트의 경우 Visual Studio Code에서 Java 서비스를 실행 하도록 설치 [및 구성][maven] 된 [Azure Dev Spaces 확장을 위한 java 디버거가][java-extension] 설치 되어 있어야 합니다.

## <a name="debug-your-service-in-aks"></a>AKS에서 서비스 디버그

프로젝트를 시작한 후에는 Visual Studio Code에서 직접 개발 공간에서 실행 되는 Java, Node.js 및 .NET Core 서비스를 디버그할 수 있습니다. 디렉터리의 시작 구성은 `.vscode` 개발 공간에서 디버깅을 사용 하도록 설정 하 여 서비스를 실행 하기 위한 추가 디버깅 정보를 제공 합니다. 또한 Visual Studio Code는 dev 공간의 실행 중인 컨테이너에 있는 디버그 프로세스에 연결 하 여 중단점을 설정 하 고, 변수를 검사 하 고, 다른 디버깅 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
