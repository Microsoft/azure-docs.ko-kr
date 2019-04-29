---
title: Azure 개발 공간에 피드 사용자 지정 NuGet을 사용 하는 방법
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: conceptual
description: Azure Dev Space에서 사용자 지정 NuGet 피드를 사용하여 NuGet 패키지에 액세스하고 이를 사용합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
manager: ghogen
ms.openlocfilehash: 1a000e378a9b8ecfb09d778fd6444e3f24b3df7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686460"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Azure Dev Space에서 사용자 지정 NuGet 피드 사용

NuGet 피드는 프로젝트에 패키지 원본을 포함하는 편리한 방법을 제공합니다. Azure Dev Spaces는 종속성이 Docker 컨테이너에 제대로 설치되기 위해 이 피드에 액세스할 수 있어야 합니다.

## <a name="set-up-a-nuget-feed"></a>NuGet 피드 설정

NuGet 피드를 설정하려면:
1. `PackageReference` 노드의 `*.csproj` 파일에 [패키지 참조](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)를 추가합니다.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. 프로젝트 폴더에 [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) 파일을 만듭니다.
     * `packageSources` 섹션을 사용하여 NuGet 피드 위치를 참조합니다. 중요: NuGet 피드는 공개적으로 액세스 가능해야 합니다.
     * `packageSourceCredentials` 섹션을 사용하여 사용자 이름 및 암호 자격 증명을 구성합니다. 

   ```xml
   <packageSources>
       <add key="Contoso" value="https://contoso.com/packages/" />
   </packageSources>

   <packageSourceCredentials>
       <Contoso>
           <add key="Username" value="user@contoso.com" />
           <add key="ClearTextPassword" value="33f!!lloppa" />
       </Contoso>
   </packageSourceCredentials>
   ```

3. 원본 코드 제어를 사용 중인 경우:
    - 자격 증명을 실수로 원본 리포지토리에 커밋하지 않도록 `.gitignore` 파일에서 `NuGet.Config`를 참조합니다.
    - 프로젝트에서 `azds.yaml` 파일을 열고, `build` 섹션을 찾고, 컨테이너 이미지 빌드 프로세스 중에 사용할 수 있도록 `NuGet.Config` 파일이 Azure로 동기화되도록 다음 코드 조각을 삽입합니다. (기본적으로 Azure Dev Spaces는 `.gitignore` 및 `.dockerignore` 규칙과 일치하는 파일을 동기화하지 않습니다.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>다음 단계

위의 단계를 수행한 후 다음 번에 `azds up`을 실행하면(또는 VSCode 또는 Visual Studio에서 `F5`를 누르면) Azure Dev Spaces는 `NuGet.Config` 파일을 Azure로 동기화하고, 이는 컨테이너에서 패키지 종속성을 설치하도록 `dotnet restore`에서 사용됩니다.

