---
title: Azure Service Fabric 독립 실행형 클러스터의 버전 업그레이드 | Microsoft Docs
description: 독립 실행형 Service Fabric 클러스터를 실행하는 Azure Service Fabric 코드를 업그레이드합니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 29d034be5999d0bc3f0a244cfa7a5658a4ecce32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711386"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>클러스터에서 실행되는 Service Fabric 버전 업그레이드 

최신 시스템에서 업그레이드 기능은 제품의 장기적인 성공을 위한 핵심적 요소입니다. Azure 서비스 패브릭 클러스터는 사용자가 소유하는 리소스입니다. 이 문서에서는 독립 실행형 클러스터에서 실행되는 Service Fabric의 버전을 업그레이드하는 방법을 설명합니다.

> [!NOTE]
> 클러스터가 지원되는 Service Fabric 버전을 항상 실행하는지 확인합니다. Microsoft에서 새로운 버전의 Service Fabric 릴리스를 발표하면 이전 버전은 해당 발표일로부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 발표됩니다. 그러면 해당 시점에 새로운 릴리스를 선택할 수 있습니다.
>
>

각 Service Fabric 노드를 별도 물리적 컴퓨터 또는 가상 머신에 할당하는 프로덕션 스타일 노드 구성을 사용하는 경우에만 클러스터를 새 버전으로 업그레이드할 수 있습니다. 하나의 물리적 또는 가상 머신에 Service Fabric 노드가 여러 개 있는 개발 클러스터가 있는 경우 새 버전으로 클러스터를 다시 만들어야 합니다.

클러스터를 최신 버전 또는 지원되는 Service Fabric 버전으로 업그레이드하는 데는 두 가지 워크플로가 있습니다. 한 가지 워크플로는 최신 버전을 자동으로 다운로드하도록 연결된 클러스터에 사용됩니다. 또 다른 워크플로는 최신 Service Fabric 버전을 다운로드할 수 있는 연결이 없는 클러스터에 사용됩니다.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>클러스터의 Service Fabric 버전의 자동 업그레이드를 사용하도록 설정
Microsoft에서 새 버전을 출시할 때 Service Fabric 업데이트를 다운로드하도록 클러스터를 설정하려면 `fabricClusterAutoupgradeEnabled` 클러스터 구성을 *true*로 설정합니다. 클러스터를 사용할 지원되는 Service Fabric 버전을 수동으로 선택하려면 `fabricClusterAutoupgradeEnabled` 클러스터 구성을 *false*로 설정합니다.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>최신 코드와 구성을 다운로드하도록 연결된 클러스터 업그레이드
클러스터 노드가 인터넷을 통해 [Microsoft 다운로드 센터](https://download.microsoft.com)에 연결되어 있으면 이러한 단계를 사용하여 클러스터를 지원되는 버전으로 업그레이드합니다.

[Microsoft 다운로드 센터](https://download.microsoft.com)에 연결되는 클러스터의 경우 새 Service Fabric 버전이 있는지 주기적으로 확인합니다.

새 Service Fabric 버전을 사용할 수 있는 경우 패키지를 클러스터에 로컬로 다운로드하고 업그레이드를 위해 프로비전됩니다. 또한 이 새로운 버전을 고객에게 알리기 위해 시스템은 다음과 같이 명시적인 클러스터 상태 경고를 표시합니다.

"현재 클러스터 버전[버전 #]에 대한 지원은 [날짜]로 종료됩니다."

클러스터가 최신 버전을 실행한 후 경고는 사라집니다.

클러스터 상태 경고가 표시되면 클러스터를 업그레이드합니다.

1. 클러스터에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 클러스터에 연결합니다. 이 스크립트가 실행되는 컴퓨터는 클러스터에 속할 필요가 없습니다.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. 업그레이드할 수 있는 Service Fabric 버전의 목록을 가져옵니다.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    다음과 유사한 결과가 표시됩니다.

    ![Service Fabric 버전 가져오기][getfabversions]
3. [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell 명령을 사용하여 사용 가능한 버전으로 클러스터 업그레이드를 시작합니다.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   업그레이드 진행 상황을 모니터링하려면 Service Fabric Explorer를 사용하거나 다음 PowerShell 명령을 실행할 수 있습니다.

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    클러스터 상태 정책이 충족되지 않으면 업그레이드가 롤백됩니다. Start-ServiceFabricClusterUpgrade 명령에 대한 사용자 지정 상태 정책을 지정하려면 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade)에 대한 설명서를 참조하세요.

    롤백을 일으킨 문제를 수정한 후 이전과 동일한 단계에 따라 업그레이드를 다시 시작합니다.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>최신 코드와 구성을 다운로드하도록 *연결되지 않은* 클러스터 업그레이드
클러스터 노드가 인터넷을 통해 [Microsoft 다운로드 센터](https://download.microsoft.com)에 연결되어 있지 않으면 이러한 단계를 사용하여 클러스터를 지원되는 버전으로 업그레이드합니다.

> [!NOTE]
> 인터넷에 연결되어 있지 않은 클러스터를 실행하는 경우 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)를 모니터링하여 새 릴리스를 확인해야 합니다. 시스템에는 새 릴리스를 알리는 클러스터 상태 경고가 표시되지 않습니다.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>자동 프로비전 및 수동 프로비전
최신 코드 버전에 대한 자동 다운로드 및 등록을 사용하도록 설정하려면 Service Fabric 업데이트 서비스를 설정합니다. 지침은 [독립 실행형 패키지](service-fabric-cluster-standalone-package-contents.md)에 있는 *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt*를 참조하세요.

수동 프로세스의 경우 다음 지침을 따릅니다.

구성 업그레이드를 시작하기 전에 클러스터 구성을 수정하여 다음 속성을 *false*로 설정합니다.

```json
"fabricClusterAutoupgradeEnabled": false,
```

자세한 사용 방법은 [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell 명령을 참조하세요. 구성 업그레이드를 시작하기 전에 JSON에서 'clusterConfigurationVersion'을 업데이트해야 합니다.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>클러스터 업그레이드 워크플로

1. 클러스터의 노드 중 하나에서 [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade)를 실행하고 *TargetCodeVersion*을 적어둡니다.

2. 인터넷에 연결된 컴퓨터에서 다음을 실행하여 현재 버전과 호환 가능한 모든 업그레이드 버전을 나열하고, 연결된 다운로드 링크에서 해당 패키지를 다운로드합니다.

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. 클러스터에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 클러스터에 연결합니다. 이 스크립트가 실행되는 컴퓨터는 클러스터에 속할 필요가 없습니다.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. 다운로드 한 패키지를 클러스터 이미지 저장소에 복사합니다.

5. 복사된 패키지를 등록합니다.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. 사용 가능한 버전으로의 클러스터 업그레이드를 시작합니다.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Service Fabric Explorer에서 업그레이드 진행 상황을 모니터링하거나 다음 PowerShell 명령을 실행할 수 있습니다.

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    클러스터 상태 정책이 충족되지 않으면 업그레이드가 롤백됩니다. Start-ServiceFabricClusterUpgrade 명령에 대한 사용자 지정 상태 정책을 지정하려면 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade)에 대한 설명서를 참조하세요.

    롤백을 일으킨 문제를 수정한 후 이전과 동일한 단계에 따라 업그레이드를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계
* [독립 실행형 클러스터의 구성 업그레이드](service-fabric-cluster-config-upgrade-windows-server.md)
* 일부 [Service Fabric 클러스터 설정](service-fabric-cluster-fabric-settings.md)을 사용자 지정합니다.
* [클러스터를 확장 및 축소합니다](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
