---
title: Azure Dev Spaces를 사용 하 여 원격으로 코드를 디버깅 하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces 사용 하 여 Azure Kubernetes Service에서 원격 디버깅을 위한 프로세스를 설명 합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: fd984ff6a8ebe336f76643406c0957769dbfd3da
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213396"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Azure Dev Spaces를 사용 하 여 원격으로 코드를 디버깅 하는 방법

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. 프로젝트가 개발 공간에서 실행 되 면 Azure Dev Spaces는 AKS에서 실행 중인 응용 프로그램에 연결 하 고 디버그 하는 방법을 제공 합니다.

이 문서에서는 Dev 공간으로 원격 디버깅이 작동 하는 방법을 설명 합니다.

## <a name="debug-your-code"></a>코드 디버그

Java, .NET Core 및 Node.js 응용 프로그램의 경우 Visual Studio Code 또는 Visual Studio를 사용 하 여 개발 공간에서 직접 실행 되는 응용 프로그램을 디버그할 수 있습니다. Visual Studio Code 및 Visual Studio는 개발자 공간에 연결 하 고, 응용 프로그램을 시작 하 고, 디버거를 연결 하는 도구를 제공 합니다. 을 실행 한 후 `azds prep` Visual Studio Code 또는 Visual Studio에서 프로젝트를 열 수 있습니다. Visual Studio Code 또는 Visual Studio는를 실행 하는 것과 별개의 연결에 대 한 자체 구성 파일을 생성 `azds prep` 합니다. Visual Studio Code 또는 Visual Studio 내에서 중단점을 설정 하 고 개발 공간에 응용 프로그램을 시작할 수 있습니다.

![코드 디버그](media/get-started-node/debug-configuration-nodejs2.png)

디버깅을 위해 Visual Studio Code 또는 Visual Studio를 사용 하 여 응용 프로그램을 시작 하는 경우 실행 하는 것과 같은 방식으로 개발 공간에 대 한 시작 및 연결을 처리 `azds up` 합니다. 또한 Visual Studio Code 및 Visual Studio의 클라이언트 쪽 도구는 각각 디버깅을 위해 특정 정보가 포함 된 추가 매개 변수를 제공 합니다. 매개 변수에는 디버거 이미지의 이름, 디버거의 이미지 내에 있는 디버거의 위치 및 디버거 폴더를 탑재 하기 위한 응용 프로그램 컨테이너 내의 대상 위치가 포함 됩니다.

디버거 이미지는 클라이언트 쪽 도구에 의해 자동으로 결정 됩니다. Dockerfile 중에 사용 된 것과 유사한 메서드를 사용 하 고 실행 시에 투구 차트를 생성 `azds prep` 합니다. 디버거는 응용 프로그램의 이미지에 탑재 된 후를 사용 하 여 실행 됩니다 `azds exec` .

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](how-dev-spaces-works.md)
