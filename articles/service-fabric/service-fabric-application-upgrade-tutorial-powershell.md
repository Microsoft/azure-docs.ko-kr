---
title: PowerShell을 사용하여 Service Fabric 앱 업그레이드 | Microsoft Docs
description: 이 문서는 PowerShell을 사용하여 서비스 패브릭 애플리케이션의 배포, 코드 변경, 업그레이드 롤아웃 환경을 안내합니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: c8f56897380bc3108cb979d9d15e7dbd0a329064
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614385"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>PowerShell을 사용하여 서비스 패브릭 애플리케이션 업그레이드
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

가장 자주 사용되며 바람직한 업그레이드 방법은 모니터링 되는 롤링 업그레이드입니다.  Azure 서비스 패브릭은 상태 정책 세트에 기반하여 업그레이드되는 애플리케이션의 상태를 모니터링합니다. UD(업데이트 도메인)이 업그레이드된 경우, 서비스 패브릭은 애플리케이션의 상태를 평가하고 다음 업데이트 도메인으로 진행할지 또는 업그레이드를 실패할지 여부를 상태 정책에 기반하여 결정합니다.

모니터링되는 애플리케이션 업그레이드는 관리 또는 네이티브 API, PowerShell, Azure CLI, Java 또는 REST를 사용하여 수행할 수 있습니다. Visual Studio를 사용하여 업그레이드를 수행하는 지침은 [Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)를 참조하세요.

서비스 패브릭 모니터링되는 롤링 업그레이드는 애플리케이션 관리자가 서비스 패브릭이 사용하여 애플리케이션이 정상인지 결정하는 상태 평가 정책을 구성할 수 있게 합니다. 또한 관리자는 상태 평가가 실패했을 때 자동 롤백과 같은 수행할 작업을 구성할 수 있습니다. 이 섹션에서는 PowerShell을 사용하는 SDK 샘플 중 하나에 대해 모니터링되는 업그레이드를 연습합니다. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>1단계: 시각적 개체 샘플 빌드 및 배포
애플리케이션 프로젝트, **VisualObjectsApplication**을 마우스 오른쪽 단추로 클릭하고 **게시** 명령을 선택하여 애플리케이션을 빌드 및 게시합니다.  자세한 내용은 [서비스 패브릭 애플리케이션 업그레이드 자습서](service-fabric-application-upgrade-tutorial.md)를 참조하세요.  또는 PowerShell을 사용하여 애플리케이션을 배포할 수 있습니다.

> [!NOTE]
> 어떤 서비스 패브릭 명령이 PowerShell에서 사용되기 전에 `Connect-ServiceFabricCluster` cmdlet을 사용하여 클러스터에 처음 연결해야 합니다. 마찬가지로, 클러스터는 로컬 컴퓨터에 이미 설치된 것으로 간주됩니다. [서비스 패브릭 개발 환경 설정](service-fabric-get-started.md)문서를 참조하십시오.
> 
> 

Visual Studio에서 프로젝트를 빌드한 후, PowerShell 명령 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) 를 사용하여 애플리케이션 패키지를 ImageStore에 복사합니다. 앱 패키지를 로컬로 확인하려는 경우 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) cmdlet을 사용합니다. 다음 단계는 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) cmdlet을 사용하여 서비스 패브릭 런타임에 애플리케이션을 등록하는 것입니다. 다음 단계로, [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet을 사용하여 애플리케이션의 인스턴스를 시작합니다.  이 세 단계는 Visual Studio의 **배포** 메뉴를 사용하는 것과 유사합니다.  프로비전이 완료되면 리소스 소비를 줄이기 위해 이미지 저장소에서 복사된 애플리케이션 패키지를 정리해야 합니다.  같은 이유로, 애플리케이션 형식이 더 이상 필요하지 않은 경우 등록 해제해야 합니다. 자세한 내용은 [PowerShell을 사용하여 애플리케이션 배포 및 제거](service-fabric-application-upgrade-tutorial-powershell.md)를 참조하세요.

이제 [클러스터 및 애플리케이션을 보는 서비스 패브릭 탐색기](service-fabric-visualizing-your-cluster.md)를 사용할 수 있습니다. 애플리케이션에는 웹 서비스가 있으며, Internet Explorer 주소 표시줄에 [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects)를 입력하여 웹 서비스로 이동할 수 있습니다.  화면에서 일부 부동 시각적 개체가 움직이는 것을 볼 수 있을 것입니다.  또한 [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)을 사용하여 애플리케이션 상태를 확인할 수 있습니다.

## <a name="step-2-update-the-visual-objects-sample"></a>2단계: 시각적 개체 샘플 업데이트
1단계에서에서 배포된 버전에서 알 수 있듯이 시각적 개체는 회전하지 않습니다. 이 애플리케이션을 시각적 개체도 회전하도록 업그레이드하겠습니다.

VisualObjects 솔루션에서 VisualObjects.ActorService 프로젝트를 선택하고, StatefulVisualObjectActor.cs 파일을 엽니다. 해당 파일 내에서 `MoveObject` 메서드로 이동하고 `this.State.Move()`를 주석 처리하고 `this.State.Move(true)`의 주석 처리를 제거합니다. 이렇게 변경하면 서비스가 업그레이드된 후 개체가 회전됩니다.

프로젝트 *VisualObjects.ActorService* 의 **ServiceManifest.xml**파일(PackageRoot 아래)도 업데이트해야 합니다. *CodePackage* 및 서비스 버전을 2.0으로 업데이트하고 *ServiceManifest.xml* 파일의 해당 줄을 업데이트합니다.
솔루션을 마우스 오른쪽 단추로 클릭한 다음 Visual Studio *Edit Manifest Files* (매니페스트 파일 편집) 옵션을 사용하여 매니패스트 파일을 변경할 수 있습니다.

변경 후 매니페스트 버전은 다음과 같이 설정됩니다(강조 표시된 글씨가 변경된 부분임).

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

이제 *ApplicationManifest.xml* 파일(**VisualObjects** 솔루션 내 **VisualObjects** 프로젝트 내에서 찾을 수 있음)이 **VisualObjects.ActorService** 프로젝트의 2.0 버전으로 업데이트됩니다. 또한 애플리케이션 버전이 1.0.0.0에서 2.0.0.0으로 업데이트됩니다. *ApplicationManifest.xml*은 다음 코드 조각과 비슷합니다.

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

이제 **ActorService** 프로젝트를 선택하고 마우스 오른쪽 단추를 클릭한 후 Visual Studio에서 **Build** 옵션을 선택하여 프로젝트를 빌드합니다. **모두 다시 빌드**를 선택한 경우 코드가 변경되었으므로 모든 프로젝트의 버전을 업데이트해야 합니다. 다음으로, ***VisualObjectsApplication***을 마우스 오른쪽 단추로 클릭하고, 서비스 패브릭 메뉴를 선택하고, **패키지**를 선택하여 업데이트된 애플리케이션을 패키지해 봅니다. 이 작업을 수행하면 배포 가능한 애플리케이션 패키지가 만들어집니다.  업데이트된 애플리케이션의 배포 준비가 되었습니다.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>3단계:  상태 정책 결정 및 매개 변수 업그레이드
[애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 및 [업그레이드 프로세스](service-fabric-application-upgrade.md)를 파악하여 다양한 업그레이드 매개 변수, 제한 시간 및 적용되는 상태 조건을 잘 이해하세요. 이 연습에서는 서비스 상태 평가 조건이 모든 서비스 및 인스턴스가 업그레이드 후에 *정상* 이 되어야 함을 의미하는 기본값(및 권장값)으로 설정됩니다.  

그러나 *HealthCheckStableDuration*을 180초로 증가시켜 보겠습니다(그러면 서비스는 다음 업데이트 도메인으로 업그레이드를 진행하기 전에 적어도 120초간 정상이 됩니다).  또한 *UpgradeDomainTimeout*을 1200초로, *UpgradeTimeout*을 3000초로 설정해 보겠습니다.

마지막으로 *UpgradeFailureAction* 이 롤백되도록 설정해 보겠습니다. 이 옵션에서는 업그레이드 중에 문제가 발생할 경우 서비스 패브릭이 애플리케이션을 이전 버전으로 롤백해야 합니다. 따라서 업그레이드를 시작할 때(4단계) 다음 매개 변수가 지정됩니다.

FailureAction = Rollback

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>4단계: 업그레이드를 위한 애플리케이션 준비
이제 애플리케이션이 빌드되고 업그레이드 준비되었습니다. 관리자로 PowerShell 창을 열고 [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)을 입력하면 **VisualObjects**의 애플리케이션 형식 1.0.0.0이 배포되었음을 알려줍니다.  

이 애플리케이션 패키지는 Service Fabric SDK의 압축을 풀고 상대 경로 아래 저장됩니다. *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug* 디렉터리에서 애플리케이션 패키지가 저장된 "Package" 폴더를 찾을 수 있습니다. 타임스탬프를 확인하여 최신 빌드인지 확인합니다(경로를 적절하게 수정해야 할 수 있음).

이제 업데이트된 애플리케이션 패키지를 서비스 패브릭 ImageStore(서비스 패브릭에 의해 애플리케이션 패키지가 저장된 곳)에 복사합니다. 매개 변수 *ApplicationPackagePathInImageStore*는 Service Fabric에 애플리케이션 패키지를 찾을 수 있는 위치를 알립니다. 다음 명령으로 업데이트된 애플리케이션을 "VisualObjects\_V2"에 저장하였습니다(경로를 다시 적절하게 수정해야 할 수 있습니다).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

다음 단계에서는 Service Fabric으로 이 애플리케이션을 등록하는 것이며, [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 명령을 사용하여 수행할 수 있습니다.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

위 명령이 실패하면 모든 서비스를 다시 빌드해야 할 가능성이 높습니다. 2단계에서 언급했듯이 WebService 버전도 업데이트해야 합니다.

애플리케이션이 성공적으로 등록된 후에는 애플리케이션 패키지를 제거하는 것이 좋습니다.  이미지 저장소에서 애플리케이션 패키지를 삭제하면 시스템 리소스가 해제됩니다.  사용되지 않는 애플리케이션 패키지를 그대로 두면 디스크 스토리지를 소비하고 애플리케이션 성능 문제로 이어집니다.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>5단계: 애플리케이션 업그레이드 시작
이제 [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) 명령을 사용하여 애플리케이션 업그레이드를 시작할 수 있습니다.

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


애플리케이션 이름은 *ApplicationManifest.xml* 파일에 기재된 것과 같습니다. 서비스 패브릭은 이 이름을 사용하여 업그레이드할 애플리케이션을 식별합니다. 제한 시간을 너무 짧게 설정하면 문제 발생을 알리는 오류 메시지가 발생할 수 있습니다. 문제 해결 섹션을 참조하거나 제한 시간을 늘리십시오.

이제 애플리케이션 업그레이드가 진행되면 Service Fabric Explorer 또는 [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell 명령을 사용하여 이 업그레이드를 모니터링할 수 있습니다. 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

몇 분 후, 앞의 PowerShell 명령을 사용하여 얻은 상태는 모든 업데이트 도메인이 업그레이드되었음(완료)을 알립니다. 브라우저 창에서 시각적 개체가 이제 회전되는 것을 볼 수 있을 것입니다.

연습을 위해 버전 2에서 버전 3으로 또는 버전 2에서 버전 1로 업그레이드를 시도할 수 있습니다. 버전 2에서 버전 1로 이동하는 것도 업그레이드로 간주됩니다. 제한 시간과 상태 정책을 변경해 보면서 익숙해지십시오. Azure 클러스터에 배포하는 경우 매개 변수를 적절히 설정해야 합니다. 제한 시간을 신중하게 설정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md) 사용 방법을 익혀 애플리케이션 업그레이드와 호환되도록 만듭니다.

[고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 애플리케이션을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.

[애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.

