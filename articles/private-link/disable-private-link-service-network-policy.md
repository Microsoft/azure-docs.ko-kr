---
title: 'Azure 개인 링크 서비스 원본 IP 주소에 대 한 네트워크 정책 사용 안 함 '
description: Azure 개인 링크에 대 한 네트워크 정책을 사용 하지 않도록 설정 하는 방법 알아보기
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453001"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>개인 링크 서비스 원본 IP에 대해 네트워크 정책 사용 안 함

개인 링크 서비스에 대 한 원본 IP 주소를 선택 하기 위해 서브넷에 `privateLinkServiceNetworkPolicies` 명시적 사용 안 함 설정이 필요 합니다. 이 설정은 개인 링크 서비스의 원본 IP로 선택한 특정 개인 IP 주소에만 적용 됩니다. 서브넷의 다른 리소스에 대 한 액세스는 NSG (네트워크 보안 그룹) 보안 규칙 정의를 기반으로 제어 됩니다. 
 
Azure 클라이언트 (PowerShell, CLI 또는 템플릿)를 사용 하는 경우이 속성을 변경 하려면 추가 단계가 필요 합니다. Azure Portal 또는 Azure PowerShell의 로컬 설치 Azure CLI에서 cloud shell을 사용 하 여 정책을 사용 하지 않도록 설정 하거나 Azure Resource Manager 템플릿을 사용할 수 있습니다.  
 
다음 단계를 수행 하 여 *Myresourcegroup*이라는 리소스 그룹에서 호스트 되는 *기본* 서브넷이 있는 *myVirtualNetwork* 이라는 가상 네트워크에 대 한 개인 링크 서비스 네트워크 정책을 사용 하지 않도록 설정 합니다. 

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell를 사용 하 여 서브넷 개인 엔드포인트 정책을 사용 하지 않도록 설정 하는 방법을 설명 합니다.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI를 사용 하 여 서브넷 개인 엔드포인트 정책을 사용 하지 않도록 설정 하는 방법을 설명 합니다.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>템플릿 사용
이 섹션에서는 Azure Resource Manager 템플릿을 사용 하 여 서브넷 개인 엔드포인트 정책을 사용 하지 않도록 설정 하는 방법을 설명 합니다.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>다음 단계
- [Azure 개인 엔드포인트](private-endpoint-overview.md) 에 대 한 자세한 정보
 
