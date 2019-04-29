---
title: Azure Virtual Network(클래식) 구성 - 네트워크 구성 파일 | Microsoft Docs
description: 네트워크 구성 파일을 내보내고, 변경하며, 가져와서 가상 네트워크(클래식)를 만들고 수정하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: e26ec4d268b9bd8852ef8cd2c522995902e15923
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108014"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>네트워크 구성 파일을 사용하여 가상 네트워크(클래식) 구성
> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 Resource Manager 배포 모델을 사용하는 것이 좋습니다.

Azure 클래식 CLI(명령줄 인터페이스) 또는 Azure PowerShell을 사용하여 네트워크 구성 파일이 있는 가상 네트워크(클래식)를 만들고 구성할 수 있습니다. 네트워크 구성 파일을 사용하여 Azure Resource Manager 배포 모델을 통해 가상 네트워크를 만들거나 수정할 수는 없습니다. 네트워크 구성 파일을 사용하여 가상 네트워크(클래식)를 만들거나 수정하는 데는 Azure Portal을 사용할 수 없지만 네트워크 구성 파일을 사용하지 않고 가상 네트워크(클래식)를 만드는 데는 Azure Portal을 사용할 수 있습니다.

네트워크 구성 파일을 사용하여 가상 네트워크(클래식)을 만들고 구성하려면 파일을 내보내기, 변경 및 가져오기 해야 합니다.

## <a name="export"></a>네트워크 구성 파일 내보내기

PowerShell 또는 Azure 클래식 CLI를 사용하여 네트워크 구성 파일을 내보낼 수 있습니다. PowerShell은 XML 파일을 내보내는 반면 Azure 클래식 CLI는 json 파일을 내보냅니다.

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell 설치 및 Azure에 로그인](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. 다음 명령에서 디렉터리(있는지 확인) 및 파일 이름을 원하는 대로 변경한 후 명령을 실행하여 네트워크 구성 파일을 내보냅니다.

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure 클래식 CLI

1. [Azure 클래식 CLI를 설치합니다](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 클래식 CLI 명령 프롬프트에서 나머지 단계를 완료합니다.
2. `azure login` 명령을 입력하여 Azure에 로그인합니다.
3. `azure config mode asm` 명령을 입력하여 asm 모드에 있는지 확인합니다.
4. 다음 명령에서 디렉터리(있는지 확인) 및 파일 이름을 원하는 대로 변경한 후 명령을 실행하여 네트워크 구성 파일을 내보냅니다.
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>네트워크 구성 파일 만들기 또는 수정

네트워크 구성 파일은 XML 파일(PowerShell 사용) 또는 json 파일(클래식 CLI 사용)입니다. 모든 텍스트 또는 XML/json 편집기에서 파일을 편집할 수 있습니다. [네트워크 구성 파일 스키마 설정](https://msdn.microsoft.com/library/azure/jj157100.aspx) 문서에는 모든 설정에 대한 세부 정보가 포함됩니다. 설정에 대한 추가 설명은 [가상 네트워크 및 설정 보기](manage-virtual-network.md#view-virtual-networks-and-settings)를 참조하세요. 파일에 대한 변경 내용:

- 스키마를 준수해야 하며 그렇지 않은 경우 네트워크 구성 파일 가져오기가 실패합니다.
- 구독에 대한 모든 기존 네트워크 설정을 덮어쓰므로 수정할 때는 특히 주의하세요. 예를 들어, 다음에 나오는 네트워크 구성 파일 예를 참조하세요. 원본 파일에 두 **VirtualNetworkSite** 인스턴스가 포함되어 있고 예제와 같이 변경했다고 가정하겠습니다. 파일을 가져올 때 Azure는 파일에서 제거한 **VirtualNetworkSite** 인스턴스에 대한 가상 네트워크를 삭제합니다. 이 단순한 시나리오에서는 가상 네트워크에 리소스가 없다고 가정하고 가상 네트워크를 삭제할 수 없는 것처럼 가져오기가 실패합니다.

> [!IMPORTANT]
> Azure에서는 배포된 항목이 있는 서브넷을 **사용 중**인 것으로 간주합니다. 사용 중인 서브넷은 수정할 수 없습니다. 네트워크 구성 파일에서 서브넷 정보를 수정하기 전에 서브넷에 배포한 항목을 수정 중이지 않은 다른 서브넷으로 이동하세요. 자세한 내용은 [다른 서브넷으로 VM 또는 역할 인스턴스 이동](virtual-networks-move-vm-role-to-subnet.md)을 참조하세요.

### <a name="example-xml-for-use-with-powershell"></a>PowerShell과 함께 사용할 XML 예제

다음 네트워크 구성 파일 예제는 *미국 동부* Azure 지역에 주소 공간이 *10.0.0.0/16*이고 가상 네트워크 이름이 *myVirtualNetwork*인 가상 네트워크를 만듭니다. 가상 네트워크는 주소 접두사가 *10.0.0.0/24*인 *mySubnet*이라는 하나의 서브넷을 포함합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

내보낸 네트워크 구성 파일에 내용이 없는 경우 이전 예제에서 XML을 복사하여 새 파일에 붙여넣을 수 있습니다.

### <a name="example-json-for-use-with-the-classic-cli"></a>클래식 CLI와 함께 사용할 JSON 예제

다음 네트워크 구성 파일 예제는 *미국 동부* Azure 지역에 주소 공간이 *10.0.0.0/16*이고 가상 네트워크 이름이 *myVirtualNetwork*인 가상 네트워크를 만듭니다. 가상 네트워크는 주소 접두사가 *10.0.0.0/24*인 *mySubnet*이라는 하나의 서브넷을 포함합니다.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

내보낸 네트워크 구성 파일에 내용이 없는 경우 이전 예제에서 json을 복사하여 새 파일에 붙여넣을 수 있습니다.

## <a name="import"></a>네트워크 구성 파일 가져오기

PowerShell 또는 클래식 CLI를 사용하여 네트워크 구성 파일을 가져올 수 있습니다. PowerShell은 XML 파일을 가져오는 반면 클래식 CLI는 json 파일을 가져옵니다. 가져오기가 실패하는 경우 파일이 [네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 준수하는지 확인합니다. 

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell 설치 및 Azure에 로그인](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. 필요에 따라 다음 명령에서 디렉터리 및 파일 이름을 변경한 후 명령을 실행하여 네트워크 구성 파일을 가져옵니다.
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure 클래식 CLI

1. [Azure 클래식 CLI를 설치합니다](/cli/azure/install-classic-cli). 클래식 CLI 명령 프롬프트에서 나머지 단계를 완료합니다.
2. `azure login` 명령을 입력하여 Azure에 로그인합니다.
3. `azure config mode asm` 명령을 입력하여 asm 모드에 있는지 확인합니다.
4. 필요에 따라 다음 명령에서 디렉터리 및 파일 이름을 변경한 후 명령을 실행하여 네트워크 구성 파일을 가져옵니다.

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
