---
title: Powershell에서 클러스터에 애플리케이션 배포
description: Azure PowerShell 스크립트 샘플 - 클러스터에 애플리케이션 배포
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610304"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 애플리케이션 배포

이 샘플 스크립트는 클러스터 이미지 저장소에 애플리케이션 패키지를 복사하고, 애플리케이션 유형을 클러스터에 등록하고, 불필요한 애플리케이션 패키지를 제거하고, 해당 애플리케이션 유형에서 애플리케이션 인스턴스를 만듭니다.  기본 서비스가 대상 애플리케이션 형식의 애플리케이션 매니페스트에 정의된 경우 이때 이러한 서비스도 생성됩니다. 필요에 따라 매개 변수를 사용자 지정합니다. 

필요한 경우 [Service Fabric SDK](../service-fabric-get-started.md)를 사용하여 Service Fabric PowerShell 모듈을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플이 실행된 후에 [애플리케이션 제거](service-fabric-powershell-remove-application.md)의 스크립트를 사용하여 애플리케이션 인스턴스를 제거하고, 애플리케이션 유형을 등록 취소하고, 이미지 저장소에서 애플리케이션 패키지를 삭제할 수 있습니다.

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Service Fabric 클러스터에 대한 엔드포인트을 만듭니다. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | 애플리케이션 패키지를 클러스터 이미지 저장소에 복사합니다.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| 애플리케이션 유형 및 버전을 클러스터에 등록합니다. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| 등록된 애플리케이션 유형에서 애플리케이션을 만듭니다. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | 이미지 저장소에서 Service Fabric 애플리케이션 패키지를 제거합니다.|

## <a name="next-steps"></a>다음 단계

Service Fabric PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/service-fabric/?view=azureservicefabricps)를 참조하세요.

Azure Service Fabric에 대한 추가 PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
