---
title: VM(클래식) 또는 Cloud Services 역할 인스턴스를 다른 서브넷으로 이동 - Azure PowerShell | Microsoft Docs
description: PowerShell을 사용하여 VM(클래식) 및 Cloud Services 역할 인스턴스를 다른 서브넷으로 이동하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 787a50a0cbf16089cd15f922b494cd12d680cb43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640398"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>PowerShell을 사용하여 VM(클래식) 또는 Cloud Services 역할 인스턴스를 다른 서브넷으로 이동
PowerShell을 사용하여 동일한 가상 네트워크(VNet)에 있는 한 서브넷에서 다른 서브넷으로 VM(클래식)을 이동할 수 있습니다. PowerShell을 사용하지 않고 CSCFG 파일을 편집하여 역할 인스턴스를 이동할 수 있습니다.

> [!NOTE]
> 이 문서에서는 클래식 배포 모델을 통해서만 배포된 VM을 이동하는 방법을 설명합니다.
> 
> 

다른 서브넷으로 VM을 이동하는 이유 서브넷 마이그레이션은 기존 서브넷이 너무 작고 해당 서브넷에서 실행 중인 기존 VM으로 인해 확장할 수 없는 경우에 유용합니다. 이 경우 새로운, 더 큰 서브넷을 만들고 새 서브넷으로 VM을 마이그레이션한 다음 마이그레이션이 완료된 후 이전의 빈 서브넷을 삭제할 수 있습니다.

## <a name="how-to-move-a-vm-to-another-subnet"></a>다른 서브넷으로 VM을 이동하는 방법
VM을 이동하려면 아래 예를 템플릿으로 사용하여 Set-AzureSubnet PowerShell cmdlet을 실행합니다. 아래 예제에서는 현재 서브넷에서 Subnet-2로 TestVM을 이동합니다. 사용 중인 환경에 맞게 예를 편집해야 합니다. 절차의 일부로 Update-AzureVM cmdlet을 실행할 때마다 VM이 업데이트 프로세스의 일환으로 다시 시작됩니다.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

VM에 대한 고정 내부 개인 IP를 지정한 경우 먼저 해당 설정을 제거해야 새 서브넷으로 VM을 이동할 수 있습니다. 이 경우 다음을 사용합니다.

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>다른 서브넷으로 역할 인스턴스를 이동하려면
역할 인스턴스를 이동하려면 CSCFG 파일을 편집합니다. 아래 예제에서는 가상 네트워크 *VNETName*의 "Role0"을 현재 서브넷에서 *Subnet-2*로 이동합니다. 역할 인스턴스가 이미 배포되었기 때문에 서브넷 이름 = Subnet-2를 변경하기만 합니다. 사용 중인 환경에 맞게 예를 편집해야 합니다.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
