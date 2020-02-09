---
title: 원격 Linux 클러스터에 a.Net Core 앱 만들기 및 게시
description: Visual Studio에서 원격 Linux 클러스터를 대상으로 하는 .NET Core 앱 만들기 및 게시
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614352"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Visual Studio를 사용 하 여 원격 Linux Service Fabric 클러스터를 대상으로 하는 .NET Core 응용 프로그램 만들기 및 게시
Visual Studio 도구를 사용 하면 Linux Service Fabric 클러스터를 대상으로 하는 Service Fabric .NET Core 응용 프로그램을 개발 하 고 게시할 수 있습니다. Visual Studio에서 Linux Service Fabric 클러스터를 대상으로 하는 .NET Core 응용 프로그램을 배포 하려면 SDK 버전은 3.4 이상 이어야 합니다.

> [!Note]
> Visual Studio에서는 Linux를 대상으로 하는 Service Fabric 응용 프로그램 디버깅을 지원 하지 않습니다.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>.NET Core를 대상으로 하는 Service Fabric 응용 프로그램 만들기
1. **관리자** 권한으로 Visual Studio를 시작합니다.
2. **파일 > 새 > 프로젝트**를 사용 하 여 프로젝트를 만듭니다.
3. **새 프로젝트** 대화 상자에서 **클라우드 > Service Fabric 응용 프로그램**을 선택 합니다.
![create-application]
4. 응용 프로그램 이름을로 확인 하 고 **확인**을 클릭 합니다.
5. **새 Service Fabric 서비스** 페이지의 **.NET Core 섹션**에서 만들려는 서비스 유형을 선택 합니다.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>원격 Linux 클러스터에 배포
1. 솔루션 탐색기에서 응용 프로그램을 마우스 오른쪽 단추로 클릭 하 고 **빌드**를 선택 합니다.
![build-application]
2. 응용 프로그램에 대 한 빌드 프로세스가 완료 되 면 해당 서비스를 마우스 오른쪽 단추로 클릭 하 고 **.csproj 파일**편집을 선택 합니다.
![edit-csproj]
3. 서비스가 **행위자 프로젝트 형식인**경우 UpdateServiceFabricManifestEnabled 속성을 True에서 **False** 로 편집 합니다. 응용 프로그램에 행위자 서비스가 없는 경우 4 단계로 건너뜁니다.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> UpdateServiceFabricManifestEnabled를 false로 설정 하면 빌드 중에 Servicemanifest.xml에 대 한 업데이트를 사용 하지 않도록 설정 합니다. 서비스에 대 한 추가, 제거 또는 이름 바꾸기와 같은 변경 내용은 Servicemanifest.xml에 반영 되지 않습니다. 변경을 수행 하는 경우 Servicemanifest.xml을 수동으로 업데이트 하거나 UpdateServiceFabricManifestEnabled를 true로 설정 하 고 Servicemanifest.xml를 업데이트 하는 서비스를 빌드한 다음 다시 false로 되돌려야 합니다.
>

4. RuntimeIndetifier를 win7-x64에서 서비스 프로젝트의 대상 플랫폼으로 업데이트 합니다.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Servicemanifest.xml에서 entrypoint 프로그램을 업데이트 하 여 .exe를 제거 합니다. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 솔루션 탐색기에서 응용 프로그램을 마우스 오른쪽 단추로 클릭 하 고 **게시**를 선택 합니다. **게시** 대화 상자가 나타납니다.
7. **연결 엔드포인트**에서 대상으로 하려는 원격 Service Fabric Linux 클러스터에 대 한 엔드포인트을 선택 합니다.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>다음 단계
* [.NET Core로 Service Fabric를 시작](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/) 하는 방법을 알아봅니다.
