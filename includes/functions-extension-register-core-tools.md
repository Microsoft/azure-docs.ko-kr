---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689565"
---
확장 번들을 사용할 수 없는 경우 Azure Functions Core Tools를 로컬로 사용 하 여 프로젝트에 필요한 특정 확장 패키지를 설치할 수 있습니다.

> [!IMPORTANT]
> 확장 번들을 사용 하는 함수 앱에는 확장을 명시적으로 설치할 수 없습니다. 확장을 `extensionBundle` 명시적으로 설치 하기 전에 *host.js* 에서 섹션을 제거 합니다.

다음 항목은 수동으로 확장을 설치 해야 할 수 있는 몇 가지 이유를 설명 합니다.

* 번들에서 사용할 수 없는 확장의 특정 버전에 액세스 해야 합니다.
* 번들에서 사용할 수 없는 사용자 지정 확장 프로그램에 액세스 해야 합니다.
* 단일 번들에서 사용할 수 없는 확장의 특정 조합에 액세스 해야 합니다.

> [!NOTE]
> 핵심 도구를 사용 하 여 확장을 수동으로 설치 하려면 [.Net core 2.X SDK](https://dotnet.microsoft.com/download) 가 설치 되어 있어야 합니다. .NET Core SDK는 Azure Functions Core Tools NuGet에서 확장을 설치 하는 데 사용 됩니다. Azure Functions 확장을 사용 하기 위해 .NET을 알 필요가 없습니다.

확장을 명시적으로 설치 하면 확장명이 .csproj 인 .NET 프로젝트 파일이 프로젝트의 루트에 추가 됩니다. 이 파일은 함수에 필요한 NuGet 패키지 집합을 정의 합니다. 이 파일에서 [NuGet 패키지 참조](/nuget/consume-packages/package-references-in-project-files) 를 사용할 수 있지만, 핵심 도구를 사용 하면 파일을 수동으로 편집 하지 않고도 확장을 설치할 수 있습니다.

핵심 도구를 사용 하 여 로컬 프로젝트에 필요한 확장을 설치 하는 방법에는 여러 가지가 있습니다. 

#### <a name="install-all-extensions"></a>모든 확장 설치 

다음 명령을 사용 하 여 로컬 프로젝트의 바인딩에서 사용 하는 모든 확장 패키지를 자동으로 추가 합니다.

```dotnetcli
func extensions install
```
이 명령은 파일 * 의function.js* 를 읽어 필요한 패키지를 확인 하 고, 설치 하 고, 확장 프로젝트 (.csproj)를 다시 빌드합니다. 현재 버전에서 새 바인딩을 추가하되, 기존 바인딩을 업데이트하지는 않습니다. 새 바인딩을 설치할 때 기존 바인딩을 최신 버전으로 업데이트하려면 `--force` 옵션을 사용합니다.

함수 앱에서 핵심 도구가 인식 하지 않는 바인딩을 사용 하는 경우 특정 확장을 수동으로 설치 해야 합니다.

#### <a name="install-a-specific-extension"></a>특정 확장 설치

다음 명령을 사용 하 여 특정 버전 (이 경우 저장소 확장)에 특정 확장 패키지를 설치 합니다.

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
