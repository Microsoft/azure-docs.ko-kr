---
title: 고정 내부 개인 IP - Azure VM - 클래식
description: 고정 내부 개인 IP(DIP) 및 관리 방법 이해
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b83a6e2c81eac9993c481561e3cebbed681d2c4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640330"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>PowerShell을 사용하여 고정 내부 개인 IP를 설정하는 방법(기본)
대부분의 경우 가상 머신에 고정 내부 IP 주소를 지정할 필요가 없습니다. 가상 네트워크의 VM은 사용자가 지정한 범위의 내부 IP 주소를 자동으로 받습니다. 그러나 특정한 상황에서는 특정 VM에 고정 IP 주소를 지정하는 것이 적합한 경우도 있습니다. 예를 들어 VM에서 DNS를 실행하거나 VM을 도메인 컨트롤러로 구성하는 경우입니다. 고정 내부 IP 주소는 중지 상태 및 프로비전 해제 상태에서도 VM에 유지됩니다. 

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한  [Resource Manager 및 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 [Resource Manager 배포 모델](virtual-networks-static-private-ip-arm-ps.md)을 사용하는 것이 좋습니다.
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Azure PowerShell Service Management 모듈 설치

다음 명령을 실행하기 전에 머신에 [Azure PowerShell Service Management 모듈](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
)이 설치되어 있는지 확인합니다. Azure PowerShell Service Management 모듈의 버전 기록은 [PowerShell 갤러리에서 Azure 모듈](https://www.powershellgallery.com/packages/Azure/5.3.0)을 참조하세요.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>특정 IP 주소를 사용할 수 있는지 확인하는 방법
IP 주소 *10.0.0.7*을 *TestVnet*이라는 이름의 VNet에서 사용할 수 있는지 확인하려면 다음 PowerShell 명령을 실행하고 *IsAvailable* 값을 확인합니다.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> 안전한 환경에서 위 명령을 테스트하려는 경우 [가상 네트워크 만들기(클래식)](virtual-networks-create-vnet-classic-pportal.md)의 지침에 따라 *TestVnet*이라는 이름의 VNet을 만들어 *10.0.0.0/8* 주소 공간을 사용하도록 합니다.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>VM을 만들 때 고정 내부 IP를 지정하는 방법
아래의 PowerShell 스크립트는 *TestService*라는 새 클라우드 서비스를 만들고 Azure에서 이미지를 검색합니다. 그다음에 이 이미지를 사용하여 새 클라우드 서비스에 *TestVM*이라는 VM을 만들고 이 VM을 *Subnet-1*이라는 서브넷에 속하도록 설정하고 VM의 고정 내부 IP로 *10.0.0.7*을 설정합니다.

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>VM의 고정 내부 IP 정보를 검색하는 방법
위의 스크립트를 사용하여 만든 VM의 고정 내부 IP 정보를 보려면 다음 PowerShell 명령을 실행하고 *IpAddress*의 값을 확인합니다.

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>VM에서 고정 내부 IP를 제거하는 방법
위의 스크립트에서 VM에 추가된 고정 내부 IP를 제거하려면 다음 PowerShell 명령을 실행합니다.

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>기존 VM에 고정 내부 IP를 추가하는 방법
위의 스크립트를 사용하여 만든 VM에 고정 내부 IP를 추가하려면 다음 명령을 실행합니다.

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>다음 단계
[예약된 IP](virtual-networks-reserved-public-ip.md)

[인스턴스 수준 공용 IP(ILPIP)](virtual-networks-instance-level-public-ip.md)

[예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)

