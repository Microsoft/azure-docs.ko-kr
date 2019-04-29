---
title: VM(클래식)에 대한 개인 IP 주소 구성 - Azure PowerShell | Microsoft Docs
description: PowerShell을 사용하여 가상 머신(클래식)에 대한 개인 IP 주소를 구성하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fa82c2397b4c2601cdd943158054e1c8efb6b57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596330"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>PowerShell을 사용하여 가상 머신(클래식)에 대한 개인 IP 주소 구성

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 정적 개인 IP 주소를 관리](virtual-networks-static-private-ip-arm-ps.md)할 수도 있습니다.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

아래 샘플 PowerShell 명령에는 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [VNet 만들기](virtual-networks-create-vnet-classic-netcfg-ps.md)에 설명된 테스트 환경을 빌드합니다.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>특정 IP 주소를 사용할 수 있는지 확인하는 방법
IP 주소 *192.168.1.101*을 *TestVnet*이라는 이름의 VNet에서 사용할 수 있는지 확인하려면 다음 PowerShell 명령을 실행하고 *IsAvailable* 값을 확인합니다.

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

예상 출력:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM을 만들 때 정적 개인 IP 주소를 지정하는 방법
아래의 PowerShell 스크립트는 *TestService*라는 새 클라우드 서비스를 만들고 Azure에서 이미지를 검색합니다. 그 다음에 이 이미지를 사용하여 새 클라우드 서비스에 *DNS01*이라는 VM을 만들고 이 VM을 *FrontEnd*라는 서브넷에 속하도록 설정하고 VM의 정적 내부 IP로 *192.168.1.7*을 설정합니다.

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

예상 출력:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM의 정적 개인 IP 주소 정보를 검색하는 방법
위의 스크립트를 사용하여 만든 VM의 고정 개인 IP 주소 정보를 보려면 다음 PowerShell 명령을 실행하고 *IpAddress*의 값을 확인합니다.

    Get-AzureVM -Name DNS01 -ServiceName TestService

예상 출력:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
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

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소를 제거하는 방법
위의 스크립트에서 VM에 추가된 정적 개인 IP 주소를 제거하려면 다음 PowerShell 명령을 실행합니다.

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

예상 출력:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>기존 VM에 정적 개인 IP 주소를 추가하는 방법
위의 스크립트를 사용하여 만든 VM에 정적 개인 IP 주소를 추가하려면 다음 명령을 실행합니다.

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

예상 출력:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure VM에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. 가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다.

## <a name="next-steps"></a>다음 단계
* [예약된 공용 IP](virtual-networks-reserved-public-ip.md) 주소에 대해 알아봅니다.
* [ILPIP(인스턴스 수준 공용 IP)](virtual-networks-instance-level-public-ip.md) 주소에 대해 알아봅니다.
* [예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)를 참조합니다.

