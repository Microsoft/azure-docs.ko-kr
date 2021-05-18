---
title: Azure Service Fabric Mesh 샘플 찾기
description: 다음은 사용 가능한 Service Fabric 메시 샘플 애플리케이션의 인덱스입니다. 이 예제의 소스 코드에서는 Service Fabric 리소스 모델을 사용하여 특정 시나리오를 수행하는 방법을 보여줍니다.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 2b38cd6a6c0f3aaab4ff5b77be82aee242afef23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627031"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric Mesh 샘플 찾기

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

이 표에서는 사용 가능한 Service Fabric Mesh 샘플 애플리케이션을 간단히 설명합니다. 이 예제의 소스 코드에서는 Service Fabric 리소스 모델을 사용하여 특정 시나리오를 수행하는 방법을 보여줍니다.

Azure에 직접 템플릿을 배포하는 방법에 대한 자세한 내용은 [샘플 템플릿 GitHub 페이지](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)를 참조하세요.

|샘플 템플릿|시나리오 설명|소스 코드|개발자 도구|
|------------|--------------------|----------|----------------------|
| [Hello World 앱](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | 컨테이너에서 호스트되는 정적 웹 페이지입니다. nginx를 사용하는 Linux의 경우, Windows IIS의 경우 | [소스 코드](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | 요구 사항 없음 |
| [Azure 파일 볼륨에 대한 카운터 앱](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | 컨테이너 내에 Azure Files 기반 볼륨을 탑재하여 상태를 저장합니다. <br><br> **참고:** 이 템플릿에서는 Azure Files 파일 공유가 이미 프로비전된 [지침](../storage/files/storage-how-to-create-file-share.md)이어야 합니다. | [소스 코드](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh 도구 |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | DNS 기반 해결을 사용하는 프런트 엔드 및 백 엔드 서비스를 사용하여 애플리케이션을 만듭니다. [여기](./service-fabric-mesh-tutorial-create-dotnetcore.md)에서 자습서로 사용됩니다. | [소스 코드](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh 도구 |
| [시각적 개체 앱](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | 애플리케이션 내에서 마이크로 서비스의 크기를 조정하고 업그레이드합니다. | [소스 코드](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh 도구 |
| [Voting 앱](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | DNS 기반 해결을 사용하는 프런트 엔드 및 백 엔드 서비스를 사용하여 애플리케이션을 만듭니다. | [소스 코드](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Windows 버전용 Visual Studio Mesh 도구, VS Code/dotnet CLI는 Linux 버전에서 사용할 수 있습니다. |
| [Service Fabric 신뢰할 수 있는 볼륨에 대한 카운터 앱](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| 컨테이너 내에서 Service Fabric 신뢰할 수 있는 디스크 기반 볼륨을 탑재하여 상태를 저장합니다.| [소스 코드](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh 도구 |
