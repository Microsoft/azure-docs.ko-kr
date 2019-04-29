---
title: 선호도 그룹에서 지역으로 Azure Virtual Network(클래식) 마이그레이션 | Microsoft Docs
description: 선호도 그룹에서 지역으로 가상 네트워크(클래식)를 마이그레이션하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648638"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>선호도 그룹에서 지역으로 가상 네트워크(클래식) 마이그레이션

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 Resource Manager 배포 모델을 사용하는 것이 좋습니다.

선호도 그룹을 사용하면 동일한 선호도 그룹 내에서 만든 리소스가 서로 가까이 있는 서버에서 물리적으로 호스트되도록 할 수 있으며, 이러한 리소스는 더욱 빠르게 통신할 수 있습니다. 과거에는 선호도 그룹이 가상 네트워크(클래식)를 만들기 위한 요구 사항이었습니다. 동시에 가상 네트워크(클래식)를 관리한 네트워크 관리자 서비스는 실제 서버 집합 또는 배율 단위 내에서만 작동할 수 있었습니다. 아키텍처 개선을 통해 네트워크 관리 범위가 하위 지역까지 증가했습니다.

이러한 아키텍처 개선의 결과, 선호도 그룹이 더 이상 권장 사항이 아니며 가상 네트워크(클래식)에 필수가 아닙니다. 가상 네트워크(클래식)에 대해 선호도 그룹을 사용하던 것이 지역으로 바뀝니다. 지역과 연결된 가상 네트워크(클래식)를 지역 가상 네트워크라고 합니다.

일반적으로 선호도 그룹을 사용하지 않는 것이 좋습니다. 가상 네트워크 요구 사항 외에도, 선호도 그룹은 계산(클래식) 및 스토리지(클래식)와 같은 리소스가 서로 가까이 있게 하려고 사용하는 것이 중요했습니다. 그러나 최신 Azure 네트워크 아키텍처를 통해 이러한 배치 요구 사항이 더 이상 필요하지 않습니다.

> [!IMPORTANT]
> 선호도 그룹과 연결된 가상 네트워크를 만드는 것이 여전히 기술적으로 가능하지만 그렇게 할 만큼 설득력 있는 이유가 없습니다. 네트워크 보안 그룹과 같은 다양한 가상 네트워크 기능은 지역 가상 네트워크를 사용하는 경우에만 사용할 수 있으며, 선호도 그룹과 연결된 가상 네트워크에서는 사용할 수 없습니다.
> 
> 

## <a name="edit-the-network-configuration-file"></a>네트워크 구성 파일 편집

1. 네트워크 구성 파일을 내보냅니다. PowerShell 또는 Azure CLI(명령줄 인터페이스) 1.0을 사용하여 네트워크 구성 파일을 내보내는 방법을 알아보려면 [네트워크 구성 파일을 사용하여 가상 네트워크 구성](virtual-networks-using-network-configuration-file.md#export)을 참조하세요.
2. 네트워크 구성 파일을 편집하여 **AffinityGroup**을 **Location**으로 바꿉니다. **Location**에 Azure [지역](https://azure.microsoft.com/regions)을 지정합니다.
   
   > [!NOTE]
   > **Location**은 가상 네트워크(클래식)와 연결된 선호도 그룹에 대해 지정한 지역입니다. 예를 들어 가상 네트워크가 미국 서부에 위치한 선호도 그룹과 연결된 경우 마이그레이션할 때 **Location**이 미국 서부를 가리켜야 합니다. 
   > 
   > 
   
    네트워크 구성 파일에서 값을 고유한 값으로 바꿔 다음 줄을 편집합니다. 
   
    **이전 값:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **새 값:** \<VirtualNetworkSitename = "VNetUSWest" 위치 = "미국 서 부"\>
3. 변경 내용을 저장하고 Azure에 네트워크 구성을 [가져옵니다](virtual-networks-using-network-configuration-file.md#import) .

> [!NOTE]
> 이 마이그레이션에서는 서비스 가동 중지 시간이 발생하지 않습니다.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>선호도 그룹에 VM(클래식)이 있는 경우 수행할 수 있는 작업
현재 선호도 그룹에 있는 VM(클래식)을 선호도 그룹에서 제거할 필요는 없습니다. VM을 배포하면 단일 배율 단위에 배포됩니다. 선호도 그룹이 새 VM 배포에 대해 사용 가능한 VM 크기의 집합을 제한할 수 있지만 배포된 모든 기존 VM은 VM이 배포되는 배율 단위에서 사용 가능한 VM 크기의 집합으로 이미 제한되어 있습니다. 배율 단위에 VM을 이미 배포했기 때문에, 선호도 그룹에서 VM을 제거해도 VM에 미치는 영향은 없습니다.
