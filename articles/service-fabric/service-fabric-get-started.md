---
title: Azure 마이크로 서비스에 대한 Windows 개발 환경 설정 | Microsoft Docs
description: 런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 완료하면 Windows에서 애플리케이션을 빌드할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/02/2019
ms.author: aljo
ms.openlocfilehash: 45d5d1b4e050d01b003b6321e1af44c0da762e19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946536"
---
# <a name="prepare-your-development-environment-on-windows"></a>Windows에서 개발 환경 준비
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Windows 개발 컴퓨터에서 [Azure Service Fabric 애플리케이션][1]을 빌드 및 실행하려면 Service Fabric 런타임, SDK, 도구를 설치합니다. 또한 SDK에 포함된 [Windows PowerShell 스크립트의 실행을 사용하도록 설정](#enable-powershell-script-execution)해야 합니다.

## <a name="prerequisites"></a>필수 조건
### <a name="supported-operating-system-versions"></a>지원되는 운영 체제 버전
개발을 위해 다음 운영 체제 버전이 지원됩니다.

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* 윈도우 10

> [!NOTE]
> Windows 7 지원:
> - Windows 7은 기본적으로 Windows PowerShell 2.0만을 포함합니다. 서비스 패브릭 PowerShell cmdlet에는 PowerShell 3.0 이상이 필요합니다. Microsoft 다운로드 센터에서 [Windows PowerShell 5.0을 다운로드][powershell5-download]할 수 있습니다.
> - Service Fabric 역방향 프록시는 Windows 7에서 사용할 수 없습니다.
>

## <a name="install-the-sdk-and-tools"></a>SDK 및 도구 설치
SDK와 도구를 설치하려면 WebPI(웹 플랫폼 설치 관리자)를 사용하는 것이 좋습니다. WebPI 사용 시에 런타임 오류가 발생하는 경우 특정 Service Fabric 릴리스의 릴리스 정보에서 설치 관리자의 직접 링크를 확인할 수도 있습니다. [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)의 여러 릴리스 공지에서 릴리스 정보를 확인할 수 있습니다.

> [!NOTE]
> 로컬 Service Fabric 개발 클러스터 업그레이드는 지원되지 않습니다.

### <a name="to-use-visual-studio-2017"></a>Visual Studio 2017을 사용하려면 다음을 수행합니다.
Service Fabric 도구는 Visual Studio 2017의 Azure 개발 워크로드의 일부입니다. 이 워크로드를 Visual Studio 설치의 일부로 사용하도록 설정해야 합니다.
또한 웹 플랫폼 설치 관리자를 사용하여 Microsoft Azure Service Fabric SDK 및 런타임을 설치해야 합니다.

* [Microsoft Azure Service Fabric SDK 설치][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Visual Studio 2015 사용(Visual Studio 2015 업데이트 2 이상 필요)
Visual Studio 2015의 경우 Service Fabric 도구는 웹 플랫폼 설치 관리자를 사용하여 SDK 및 런타임과 함께 설치됩니다.

* [Microsoft Azure Service Fabric SDK 및 도구 설치][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK 설치만
SDK만 필요한 경우 다음 패키지를 설치할 수 있습니다.
* [Microsoft Azure Service Fabric SDK 설치][core-sdk]

현재 버전은 다음과 같습니다.
* Service Fabric SDK 및 도구 3.3.654
* Service Fabric 런타임 6.4.654
* Visual Studio 2015 2.4.11116.1용 Service Fabric 도구
* Visual Studio 2017 15.9에는 Visual Studio 2.4.11024.1용 Service Fabric 도구가 포함되어 있습니다. 

지원되는 버전 목록은 [Service Fabric 버전](service-fabric-versions.md)을 참조하세요.

> [!NOTE]
> 단일 컴퓨터 클러스터(OneBox)는 애플리케이션 또는 클러스터 업그레이드용으로 지원되지 않습니다. 클러스터 업그레이드를 수행해야 하거나 애플리케이션 업그레이드 수행 시에 문제가 발생하는 경우에는 OneBox 클러스터를 삭제한 후에 다시 만드세요. 

## <a name="enable-powershell-script-execution"></a>PowerShell 스크립트 실행 활성화
서비스 패브릭은 로컬 개발 클러스터를 만들고 Visual Studio에서 애플리케이션을 배포하기 위해 Windows PowerShell 스크립트를 사용합니다. 기본적으로 Windows에서는 이러한 스크립트의 실행을 차단합니다. 따라서 이러한 스크립트를 사용하려면 PowerShell 실행 정책을 수정해야 합니다. 관리자로 PowerShell을 열고 다음 명령을 입력합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Docker 설치(선택 사항)
Service Fabric은 머신 클러스터에 마이크로 서비스를 배포하기 위한 [컨테이너 오케스트레이터](service-fabric-containers-overview.md)입니다. 로컬 개발 클러스터에서 Windows 컨테이너 애플리케이션을 실행하려면 먼저 Windows용 Docker를 설치해야 합니다. [Windows용 Docker CE(안정화)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)를 가져옵니다. Docker를 설치하고 시작한 후에 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환**을 선택합니다. 이 단계는 Windows 기반의 Docker 이미지를 실행하는 데 필요합니다.

## <a name="next-steps"></a>다음 단계
개발 환경의 설정을 마쳤으므로 앱을 빌드하고 실행하기 시작할 수 있습니다.

* [애플리케이션 만들기, 배포 및 관리하는 방법 알아보기](service-fabric-tutorial-create-dotnet-app.md)
* [프로그래밍 모델에 대해 알아보기: Reliable Services 및 Reliable Actors](service-fabric-choose-framework.md)
* [GitHub의 서비스 패브릭 코드 샘플 확인](https://aka.ms/servicefabricsamples)
* [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 캠페인 페이지"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 링크"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 링크"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 링크"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
