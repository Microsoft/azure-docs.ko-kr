---
title: Azure에서 개인 엔드포인트에 대 한 네트워크 정책 사용 안 함
description: 개인 엔드포인트에 대 한 네트워크 정책을 사용 하지 않도록 설정 하는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453013"
---
# <a name="disable-network-policies-for-private-endpoints"></a>전용 엔드포인트에 대 한 네트워크 정책 사용 안 함

NSG (네트워크 보안 그룹)와 같은 네트워크 정책은 전용 엔드포인트에 대해 지원 되지 않습니다. 지정 된 서브넷에 개인 엔드포인트을 배포 하기 위해 해당 서브넷에 명시적 사용 안 함 설정이 필요 합니다. 이 설정은 개인 엔드포인트에만 적용 됩니다. 서브넷의 다른 리소스에 대 한 액세스는 NSG (네트워크 보안 그룹) 보안 규칙 정의를 기반으로 제어 됩니다. 
 
포털을 사용 하 여 개인 엔드포인트을 만들 때이 설정은 만들기 프로세스의 일부로 자동으로 사용 하지 않도록 설정 됩니다. 다른 클라이언트를 사용 하는 배포에는이 설정을 변경 하는 추가 단계가 필요 합니다. Cloud shell을 사용 하 여 Azure PowerShell Azure CLI의 로컬 설치 또는 Azure Portal에서 설정을 사용 하지 않도록 설정 하거나 Azure Resource Manager 템플릿을 사용할 수 있습니다.  
 
다음 예에서는 *Myresourcegroup*이라는 리소스 그룹에서 호스트 되는 *기본* 서브넷을 사용 하 여 *myVirtualNetwork* 이라는 가상 네트워크에 대 한 `PrivateEndpointNetworkPolicies`를 사용 하지 않도록 설정 하는 방법을 설명 합니다.

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell를 사용 하 여 서브넷 개인 엔드포인트 정책을 사용 하지 않도록 설정 하는 방법을 설명 합니다.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI를 사용 하 여 서브넷 개인 엔드포인트 정책을 사용 하지 않도록 설정 하는 방법을 설명 합니다.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>다음 단계
- [Azure 개인 엔드포인트](private-endpoint-overview.md) 에 대 한 자세한 정보
 
