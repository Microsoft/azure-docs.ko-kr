---
title: Service Fabric 앱 업그레이드 자습서 | Microsoft Docs
description: 이 문서는 Visual Studio를 사용하여 서비스 패브릭 애플리케이션의 배포, 코드 변경, 업그레이드 롤아웃 환경을 안내합니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8fe0bf9c8827b7248195f89377176fd834845e32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615236"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Visual Studio를 사용하여 서비스 패브릭 애플리케이션 업그레이드 자습서
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure 서비스 패브릭을 사용하면 변경된 서비스만 업그레이드하고, 업그레이드 프로세스를 통해 애플리케이션 상태를 모니터링하여 클라우드 애플리케이션 업그레이드 프로세스를 간소화할 수 있습니다. 문제가 발생할 경우 애플리케이션이 자동으로 이전 버전으로 롤백합니다. Service Fabric 애플리케이션 업그레이드는 *가동 중지 시간 없이*애플리케이션을 업그레이드합니다. 이 자습서에서는 Visual Studio를 사용하여 롤링 업그레이드를 완료하는 방법을 설명합니다.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>1단계: 빌드 및 시각적 개체 샘플을 게시
먼저 GitHub에서 [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) 애플리케이션을 다운로드합니다. 그런 후 애플리케이션 프로젝트 **VisualObjects**를 마우스 오른쪽 단추로 클릭하고 Service Fabric 메뉴 항목에서 **게시** 명령을 선택하여 애플리케이션을 구축 및 게시합니다.

![서비스 패브릭 애플리케이션의 상황에 맞는 메뉴][image1]

**게시**를 선택하면 팝업이 표시되며 **대상 프로필**을 **PublishProfiles\Local.xml**로 설정할 수 있습니다. **게시**를 클릭하기 전의 창 모양은 다음과 같습니다.

![서비스 패브릭 애플리케이션 게시][image2]

이제 대화 상자에서 **게시** 를 클릭할 수 있습니다. [클러스터 및 애플리케이션을 보는 Service Fabric 탐색기](service-fabric-visualizing-your-cluster.md)를 사용할 수 있습니다. Visual Objects 응용 프로그램에는 브라우저의 주소 표시줄에 [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/)를 입력해서 이동할 수 있는 웹 서비스가 있습니다.  화면에서 10개의 부동 시각적 개체가 움직이는 것을 볼 수 있을 것입니다.

**참고:** 에 배포 하는 경우 `Cloud.xml` 프로필 (Azure 서비스 패브릭)에 응용 프로그램에서 사용할 수 해야 **http://{ServiceFabricName}. { Region}.cloudapp.azure.com:8081/visualobjects/** 합니다. 부하 분산 장치에 `8081/TCP`가 구성되었는지 확인합니다(Serivce Fabric 인스턴스와 동일한 리소스 그룹에 부하 분산 장치 찾기).

## <a name="step-2-update-the-visual-objects-sample"></a>2단계: 시각적 개체 샘플 업데이트
1단계에서 배포된 버전에서 알 수 있듯이 시각적 개체는 회전하지 않습니다. 이 애플리케이션을 시각적 개체도 회전하도록 업그레이드하겠습니다.

VisualObjects 솔루션에서 VisualObjects.ActorService 프로젝트를 선택하고 **VisualObjectActor.cs** 파일을 엽니다. 해당 파일 내에서 `MoveObject` 메서드로 이동하고 `visualObject.Move(false)`를 주석 처리하고 `visualObject.Move(true)`의 주석 처리를 제거합니다. 이렇게 코드를 변경하면 서비스가 업그레이드된 후 개체가 회전됩니다.  **이제 수정된 프로젝트를 빌드할 수 있는 솔루션**을 빌드(다시 빌드 아님)할 수 있습니다. *모두 다시 빌드*를 선택하는 경우 모든 프로젝트의 버전을 업데이트해야 합니다.

또한 애플리케이션의 버전을 관리해야 합니다. **VisualObjects** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 버전을 변경하려면 Visual Studio **매니페스트 파일 편집** 옵션을 사용할 수 있습니다. 이 옵션을 선택하면 다음과 같이 버전에 대한 대화 상자가 나타납니다.

![버전 관리 대화 상자][image3]

수정된 프로젝트 및 해당 코드 패키지의 버전을 애플리케이션과 같이 2.0.0으로 업데이트합니다. 변경 후 매니페스트 버전은 다음과 같이 설정됩니다(굵은 글씨가 변경된 부분임).

![버전 업데이트][image4]

**애플리케이션 및 서비스 버전 자동 업데이트**를 선택하는 경우 Visual Studio 도구는 버전의 자동 롤업을 수행할 수 있습니다. [SemVer](http://www.semver.org)를 사용하는 경우 해당 옵션이 선택된 경우 코드 및/또는 구성 패키지 버전을 업데이트해야 합니다.

변경 내용을 저장하고 이제 **Upgrade the Application** (애플리케이션 업그레이드) 상자를 선택합니다.

## <a name="step-3--upgrade-your-application"></a>3단계:  애플리케이션 업그레이드
[애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 및 [업그레이드 프로세스](service-fabric-application-upgrade.md)를 파악하여 다양한 업그레이드 매개 변수, 제한 시간 및 적용될 수 있는 상태 조건을 잘 이해하세요. 이 연습에서는 서비스 상태 평가 조건을 기본값(모니터링되지 않음 모드)으로 설정됩니다. **업그레이드 설정 구성** 을 선택한 다음 매개 변수를 원하는 대로 수정하여 이러한 설정을 구성할 수 있습니다.

이제 애플리케이션 업그레이드를 시작하기 위한 모든 설정이 완료되었으므로 **게시**를 선택합니다. 이 옵션을 선택하면 애플리케이션 버전이 2.0.0으로 업그레이드되고 개체가 회전됩니다. 서비스 패브릭은 업데이트 도메인을 한 번에 하나씩 업그레이드하며(일부 개체가 먼저 업데이트되고 나머지는 다음에 업데이트됨) 업그레이드 중에 서비스에 액세스할 수 있습니다. 클라이언트(브라우저)를 통해 서비스에 대한 액세스를 확인할 수 있습니다.  

애플리케이션 업그레이드가 진행되는 동안 애플리케이션 아래의 **Upgrades in Progress** (진행 중인 업그레이드) 탭을 사용하여 Service Fabric Explorer로 모니터링할 수 있습니다.

잠시 후 모든 업데이트 도메인이 업그레이드(완료)되고 Visual Studio 출력 창에 업그레이드 완료를 알리는 메시지가 나타납니다. 브라우저 창에서 이제 *모든* 시각적 개체가 회전되는 것을 볼 수 있을 것입니다.

버전을 변경하고 연습처럼 버전 2.0.0에서 버전 3.0.0으로 바꾸거나 버전 2.0.0에서 버전 1.0.0으로 바꾸려고 할 수 있습니다. 제한 시간과 상태 정책을 변경해 보면서 익숙해지십시오. 로컬 클러스터가 아닌 Azure 클러스터에 배포하는 경우 다른 매개 변수를 사용해야 할 수 있습니다. 제한 시간을 신중하게 설정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
[Powershell을 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md) 사용 방법을 익혀 애플리케이션 업그레이드와 호환되도록 만듭니다.

[고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 애플리케이션을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.

[애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
