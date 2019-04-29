---
title: Linux와 Windows 간의 Azure Service Fabric 차이점 | Microsoft Docs
description: Linux의 Azure Service Fabric과 Windows의 Azure Service Fabric 간의 차이점입니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 829a3ea6e8fafa24831f1731e402a3991c0258c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719994"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Linux와 Windows의 Service Fabric 간의 차이점

일부 기능 Windows에서는 지원되지만 Linux에서는 지원되지 않습니다. 결국 기능 집합은 동격이며 이러한 기능 차이는 각 릴리스에서 축소될 예정입니다. 사용 가능한 최신 릴리스 간에 다음과 같은 차이점이 있습니다.

* Envoy(Reverse Proxy)는 Linux에서 미리 보기 상태
* Linux용 독립 실행형 설치 관리자를 Linux에서 사용할 수 없음
* 콘솔 리디렉션(Linux나 Windows 프로덕션 클러스터에서 지원되지 않음)
* Linux의 FAS(오류 분석 서비스)
* Service Fabric 서비스에 대한 DNS 서비스(DNS 서비스는 Linux의 컨테이너에 지원됨)
* 특정 Powershell 명령에 해당하는 CLI 명령(아래 목록은 대부분 독립 실행형 클러스터에만 적용됨)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Linux Service Fabric 클러스터에서 작동하지 않는 PowerShell cmdlet

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>다음 단계
* [Linux에서 개발 환경 준비](service-fabric-get-started-linux.md)
* [OSX에서 개발 환경 준비](service-fabric-get-started-mac.md)
* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Linux에서 첫 번째 CSharp 애플리케이션 만들기](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Service Fabric CLI를 사용하여 애플리케이션 관리](service-fabric-application-lifecycle-sfctl.md)
