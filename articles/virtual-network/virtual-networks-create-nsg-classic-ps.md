---
title: PowerShell을 사용하여 네트워크 보안 그룹(클래식) 만들기 | Microsoft Docs
description: PowerShell을 사용하여 네트워크 보안 그룹(클래식)을 만들고 배포하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: d685f395aa25580c009fe3be660a8afc42dc79d9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125766"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>PowerShell을 사용하여 네트워크 보안 그룹(클래식) 만들기
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 NSG를 만들](tutorial-filter-network-traffic.md)수도 있습니다.

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

아래 샘플 PowerShell 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [VNet을 만들어](virtual-networks-create-vnet-classic-netcfg-ps.md)테스트 환경을 구축합니다.

## <a name="create-an-nsg-for-the-front-end-subnet"></a>프런트 엔드 서브넷에 대한 NSG 만들기

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

2. *NSG-FrontEnd*라는 네트워크 보안 그룹을 만듭니다.

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. 인터넷에서 포트 3389에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. 인터넷에서 포트 80에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>백 엔드 서브넷에 대한 NSG 만들기

1. *NSG-BackEnd*라는 네트워크 보안 그룹을 만듭니다.
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. 프런트 엔드 서브넷에서 포트 1433(SQL Server에서 사용되는 기본 포트)에 액세스할 수 있도록 허용하는 보안 규칙을 만듭니다.
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. 서브넷에서 인터넷에 액세스할 수 없도록 차단하는 보안 규칙을 만듭니다.
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```