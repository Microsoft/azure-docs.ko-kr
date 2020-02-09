---
title: Azure에서 개인 엔드포인트 연결 관리
description: Azure에서 개인 엔드포인트 연결을 관리 하는 방법 알아보기
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452974"
---
# <a name="manage-a-private-endpoint-connection"></a>개인 엔드포인트 연결 관리
Azure 개인 링크는 개인 링크 서비스 소비자가 서비스를 사용 하기 위해 서비스 공급자에 대 한 연결을 요청할 수 있는 승인 호출 흐름 모델에서 작동 합니다. 그러면 서비스 공급자가 소비자의 연결을 허용할지 여부를 결정할 수 있습니다. Azure 개인 링크를 통해 서비스 공급자는 해당 리소스에 대 한 개인 엔드포인트 연결을 관리할 수 있습니다. 이 문서에서는 개인 엔드포인트 연결을 관리 하는 방법에 대 한 지침을 제공 합니다.

![개인 엔드포인트 관리](media/manage-private-endpoint/manage-private-endpoint.png)

개인 링크 서비스 소비자는 다음과 같은 두 가지 연결 승인 방법을 선택할 수 있습니다.
- **자동**: 서비스 소비자에 게 서비스 공급자 리소스에 대 한 RBAC 권한이 있는 경우 소비자가 자동 승인 방법을 선택할 수 있습니다. 이 경우 요청이 서비스 공급자 리소스에 도달 하면 서비스 공급자의 작업이 필요 하지 않으며 연결이 자동으로 승인 됩니다. 
- **수동**: 반대로 서비스 소비자에 게 서비스 공급자 리소스에 대 한 RBAC 권한이 없는 경우 소비자는 수동 승인 방법을 선택할 수 있습니다. 이 경우 연결 요청이 **보류 중**으로 서비스 리소스에 나타납니다. 서비스 공급자는 연결을 설정할 수 있으려면 먼저 요청을 수동으로 승인 해야 합니다. 수동 사례에서 서비스 소비자는 서비스 공급자에 게 더 많은 컨텍스트를 제공 하는 요청을 포함 하 여 메시지를 지정할 수도 있습니다. 서비스 공급자는 모든 개인 엔드포인트 연결 ( **승인 됨**, **거부**, **제거**)에 대해 다음 옵션을 선택할 수 있습니다.

아래 표에서는 다양 한 서비스 공급자 작업 및 개인 엔드포인트에 대 한 결과 연결 상태를 보여 줍니다.  서비스 공급자는 나중에 소비자 개입 없이 개인 엔드포인트 연결의 연결 상태를 변경할 수도 있습니다. 작업은 소비자 쪽에서 엔드포인트의 상태를 업데이트 합니다. 


|서비스 공급자 작업   |서비스 소비자 전용 엔드포인트 상태   |Description   |
|---------|---------|---------|
|없음    |    Pending     |    연결이 수동으로 만들어지고 개인 링크 리소스 소유자의 승인 보류 중입니다.       |
|승인    |  승인됨       |  연결이 자동 또는 수동으로 승인 되었고 사용할 준비가 되었습니다.     |
|거부     | 거부됨        | 개인 링크 리소스 소유자가 연결을 거부 했습니다.        |
|제거    |  연결 끊김       | 개인 링크 리소스 소유자가 연결을 제거 했습니다. 개인 엔드포인트은 정보를 제공 하 고 정리를 위해 삭제 해야 합니다.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Azure PaaS 리소스에서 개인 엔드포인트 연결 관리
포털은 Azure PaaS 리소스에서 개인 엔드포인트 연결을 관리 하는 데 선호 되는 방법입니다. 현재 Azure PaaS 리소스에 대 한 연결을 관리 하기 위해 PowerShell/CLI를 지원 하지 않습니다.
1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
2. 개인 링크 센터로 이동 합니다.
3. **리소스**에서 개인 엔드포인트 연결을 관리 하려는 리소스 종류를 선택 합니다.
4. 각 리소스 유형에 대해 연결 된 개인 엔드포인트 연결의 수를 볼 수 있습니다. 필요에 따라 리소스를 필터링 할 수 있습니다.
5. 개인 엔드포인트 연결을 선택 합니다.  나열 된 연결 아래에서 관리 하려는 연결을 선택 합니다. 
6. 위쪽의 옵션 중에서 선택 하 여 연결 상태를 변경할 수 있습니다.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>고객/파트너 소유 개인 링크 서비스에서 개인 엔드포인트 연결 관리

Azure PowerShell 및 Azure CLI은 Microsoft 파트너 서비스나 고객 소유 서비스에서 개인 엔드포인트 연결을 관리 하는 데 선호 되는 방법입니다. 현재 개인 링크 서비스에 대 한 연결 관리에 대 한 포털 지원이 없습니다.  
 
### <a name="powershell"></a>PowerShell 
  
다음 PowerShell 명령을 사용 하 여 개인 엔드포인트 연결을 관리 합니다.  
#### <a name="get-private-link-connection-states"></a>개인 링크 연결 상태 가져오기 
`Get-AzPrivateLinkService` cmdlet을 사용 하 여 개인 엔드포인트 연결 및 해당 상태를 가져옵니다.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>개인 엔드포인트 연결 승인 
 
`Approve-AzPrivateEndpointConnection` cmdlet을 사용 하 여 개인 엔드포인트 연결을 승인 합니다. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>개인 엔드포인트 연결 거부 
 
`Deny-AzPrivateEndpointConnection` cmdlet을 사용 하 여 개인 엔드포인트 연결을 거부 합니다. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>개인 엔드포인트 연결 제거 
 
`Remove-AzPrivateEndpointConnection` cmdlet을 사용 하 여 개인 엔드포인트 연결을 제거 합니다. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
개인 엔드포인트 연결을 관리 하는 데 `az network private-link-service update`를 사용 합니다. 연결 상태는 ```azurecli connection-status``` 매개 변수에 지정 됩니다. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>다음 단계
- [개인 엔드포인트에 대 한 자세한 정보](private-endpoint-overview.md)
 
