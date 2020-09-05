---
title: Powershell에서 Service Fabric 클러스터 만들기
description: Azure PowerShell 스크립트 샘플 - X.509 인증서로 보호되는 Service Fabric 클러스터를 만듭니다.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/19/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: bae42c9ec8643da1563fb6b566d8db4af6039225
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078954"
---
# <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기

이 샘플 스크립트는 X.509 인증서를 사용하여 보호되는 5노드 Service Fabric 클러스터로 만듭니다.  이 명령은 자체 서명된 인증서를 만들고 새로운 Key Vault에 업로드합니다. 인증서는 로컬 디렉터리에도 복사됩니다.  *-OS* 매개 변수를 설정하여 클러스터 노드에서 실행되는 Windows 또는 Linux 버전을 선택합니다.  필요에 따라 매개 변수를 사용자 지정합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 클러스터 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
$groupname="mysfclustergroup"
Remove-AzResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) | 새 Service Fabric 클러스터를 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
