---
title: Azure 엔드포인트 액세스 제어 목록 관리 | PowerShell | 클래식 | Microsoft Docs
description: PowerShell을 사용하여 ACL을 관리하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1f6c14c15d4930902ced642bd02d1d1833e0b361
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032540"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>클래식 배포 모델에서 PowerShell을 사용하여 엔드포인트 액세스 제어 목록 관리
Azure PowerShell을 사용하거나 관리 포털에서 엔드포인트에 대한 네트워크 ACL(Access Control 목록)을 생성 및 관리할 수 있습니다. 이 항목에서는 PowerShell을 사용하여 완료할 수 있는 ACL 공통 작업의 절차에 대해 알아봅니다. Azure PowerShell cmdlet 목록은 [Azure 관리 Cmdlet](https://go.microsoft.com/fwlink/?LinkId=317721)을 참조하세요. ACL에 대한 자세한 내용은 [네트워크 ACL(Access Control 목록)이란?](virtual-networks-acl.md)을 참조하세요. 관리 포털을 사용하여 ACL을 관리하려는 경우 [Virtual Machine에 엔드포인트를 설정하는 방법](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

## <a name="manage-network-acls-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 네트워크 ACL 관리
Azure PowerShell cmdlet을 사용하여 네트워크 ACL(Access Control 목록)을 생성, 제거 및 구성(설정)할 수 있습니다. 여기에는 PowerShell을 사용하여 ACL을 구성하는 일부 방법의 몇 가지 예가 포함되어 있습니다.

다음 중 하나를 사용하면 ACL PowerShell cmdlet의 전체 목록을 검색할 수 있습니다.

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>원격 서브넷의 액세스를 허용하는 규칙이 있는 네트워크 ACL 만들기
아래 예제는 규칙이 포함된 새 ACL을 만드는 방법을 설명합니다. 그런 다음 이 ACL을 가상 머신 엔드포인트에 적용합니다. 아래 예제에서 ACL 규칙은 원격 서브넷에서의 액세스를 허용합니다. 원격 서브넷에 대한 허용 규칙을 포함한 새로운 네트워크 ACL을 생성하려면 Azure PowerShell ISE를 엽니다. 아래 스크립트를 복사하고 붙여넣어 원하는 값으로 스크립트를 구성한 후 스크립트를 실행합니다.

1. 새 네트워크 ACL 개체를 만듭니다.
   
        $acl1 = New-AzureAclConfig
2. 원격 서브넷에서의 액세스를 허용하는 규칙을 설정합니다. 아래 예제에서는 규칙 *100*(규칙 200보다 우선 순위가 높음)을 설정하여 가상 머신 엔드포인트에 대한 원격 서브넷(*10.0.0.0/8*) 액세스를 허용합니다. 값은 고유한 구성 요구 사항으로 바꿉니다. "SharePoint ACL config"라는 이름은 이 규칙에 붙이고 싶은 식별 이름으로 바꿔야 합니다.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. 추가 규칙의 경우 cmdlet을 반복하여 값을 고유한 구성 요구 사항으로 바꿉니다. 규칙을 적용할 순서를 반영하려면 규칙 번호 순서를 변경해야 합니다. 낮은 규칙 번호가 높은 번호보다 우선합니다.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. 다음으로 새 엔드포인트(추가)를 생성하거나 기존 엔드포인트(설정)에 대한 ACL을 설정할 수 있습니다. 이 예제에서는 "web"이라는 새 가상 머신 엔드포인트를 추가하고 ACL 설정이 있는 가상 머신 엔드포인트를 업데이트합니다.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. 이제, cmdlet을 결합하고 스크립트를 실행합니다. 이 예제의 경우 결합한 cmdlet은 다음과 같습니다.
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>원격 서브넷의 액세스를 허용하는 네트워크 ACL 규칙 제거
아래 예제는 네트워크 ACL 규칙을 제거하는 방법을 설명합니다.  원격 서브넷에 대한 허용 규칙이 있는 네트워크 ACL 규칙을 제거하려면 Azure PowerShell ISE를 엽니다. 아래 스크립트를 복사하고 붙여넣어 원하는 값으로 스크립트를 구성한 후 스크립트를 실행합니다.

1. 첫 번째 단계는 가상 머신 엔드포인트에 대한 네트워크 ACL 개체를 가져오는 것입니다. 그런 다음 ACL 규칙을 제거합니다. 이 경우 규칙 ID를 기준으로 규칙을 제거합니다. 여기서는 규칙 ID 0만 ACL에서 제거할 뿐, 가상 머신 엔드포인트에서 ACL 개체를 제거하지는 않습니다.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. 이제, 가상 머신 엔드포인트에 네트워크 ACL 개체를 적용하고 가상 머신을 업데이트해야 합니다.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>가상 머신 엔드포인트에서 네트워크 ACL 제거
특정 시나리오에서는 가상 머신 엔드포인트에서 네트워크 ACL 개체를 제거할 수도 있습니다. 이 작업을 수행하려면 Azure PowerShell ISE를 엽니다. 아래 스크립트를 복사하고 붙여넣어 원하는 값으로 스크립트를 구성한 후 스크립트를 실행합니다.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>다음 단계
[네트워크 ACL(Access Control 목록)이란?](virtual-networks-acl.md)

