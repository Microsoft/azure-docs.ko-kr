---
title: REST를 사용하여 Azure VM에서 관리 ID 구성 - Azure AD
description: CURL을 통해 REST API를 호출하여 Azure VM에서 시스템 및 사용자 할당 관리 ID를 구성하기 위한 단계별 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59f60a0167e7ac09b1fdfee87cc8412cdbe19053
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255973"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>REST API 호출을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 시스템 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 CURL을 통해 Azure Resource Manager REST 엔드포인트를 호출하여 Azure VM에서 다음과 같은 Azure 리소스 관리 ID 작업을 수행하는 방법을 알아봅니다.

- Azure VM에서 시스템 할당 관리 ID를 사용 및 사용하지 않도록 설정
- Azure VM에서 사용자 할당 관리 ID 추가 및 제거

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- Windows를 사용하는 경우, [Linux용 Windows 하위 시스템](/windows/wsl/about)을 설치하거나 Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.
- [Linux용 Windows 하위 시스템](/windows/wsl/about) 또는 [Linux 배포 OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)를 사용하는 경우, [Azure CLI 로컬 콘솔](/cli/azure/install-azure-cli)을 설치합니다.
- Azure CLI 로컬 콘솔을 사용하는 경우, 시스템 또는 사용자 할당 관리 ID를 관리하려는 Azure 구독과 연결된 계정으로 `az login`을 사용하여 Azure에 로그인합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 CURL을 통해 Azure Resource Manager REST 엔드포인트를 호출하여 Azure VM에서 시스템 할당 관리 ID를 사용하거나 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 시스템 할당 관리 ID를 사용하도록 설정

시스템 할당 관리 ID를 사용하도록 설정된 Azure VM을 만들려면 계정에 [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. [az group create](/cli/azure/group/#az-group-create)를 사용하여 VM 및 관련 리소스를 포함하고 배포하는 데 사용할 [리소스 그룹](../../azure-resource-manager/management/overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. VM에 대한 [네트워크 인터페이스](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)를 만듭니다.

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. 인증 헤더의 다음 단계에서 시스템 할당 관리 ID로 VM을 만드는 데 사용할 전달자 액세스 토큰을 검색합니다.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. CURL을 사용하여 Azure Resource Manager REST 엔드포인트를 호출하는 VM을 만듭니다. 다음 예제에서는 요청 본문에서 `"identity":{"type":"SystemAssigned"}` 값으로 식별된 시스템 할당 관리 ID를 사용하여 *myVM*이라는 VM을 만듭니다. 전달자 액세스 토큰을 요청한 이전 단계에서 받은 값 및 사용자 환경에 적절한 `<SUBSCRIPTION ID>` 값으로 `<ACCESS TOKEN>`을 바꿉니다.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **요청 헤더**
   
   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 
   
   **요청 본문**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>기존 Azure VM에서 시스템 할당 ID를 사용하도록 설정

원래 시스템 할당 관리 ID 없이 프로비전된 VM에서 해당 ID를 사용하도록 설정하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. 인증 헤더의 다음 단계에서 시스템 할당 관리 ID로 VM을 만드는 데 사용할 전달자 액세스 토큰을 검색합니다.

   ```azurecli-interactive
   az account get-access-token
   ```

2. 다음 CURL 명령을 사용하여 Azure Resource Manager REST 엔드포인트를 호출하면 요청 본문에서 *myVM*이라는 VM의 `{"identity":{"type":"SystemAssigned"}` 값으로 식별된 시스템 할당 관리 ID를 VM에서 사용할 수 있습니다.  전달자 액세스 토큰을 요청한 이전 단계에서 받은 값 및 사용자 환경에 적절한 `<SUBSCRIPTION ID>` 값으로 `<ACCESS TOKEN>`을 바꿉니다.
   
   > [!IMPORTANT]
   > VM에 할당된 기존 사용자 할당 관리 ID를 삭제하지 않는지 확인하려면 다음 CURL 명령을 사용하여 사용자 할당 관리 ID를 나열해야 합니다. `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` 응답에서 `identity` 값으로 식별된 사용자 할당 관리 ID가 VM에 할당되어 있는 경우에는 VM에서 시스템 할당 관리 ID를 사용하면서 사용자 할당 관리 ID를 유지하는 방법을 보여주는 3단계로 건너뜁니다.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 
   
   **요청 본문**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. 기존 사용자 할당 관리 ID와 함께 시스템 할당 관리 ID를 VM에서 사용하려면 `SystemAssigned`를 `type` 값에 추가해야 합니다.  
   
   예를 들어, VM에 사용자 할당 관리 ID `ID1` 및 `ID2`가 할당되어 있고 시스템 할당 관리 ID를 VM에 추가하려면 다음 CURL 호출을 사용합니다. `<ACCESS TOKEN>` 및 `<SUBSCRIPTION ID>`를 사용자 환경에 적절한 값으로 바꿉니다.

   API 버전 `2018-06-01`은 API 버전 `2017-12-01`에서 사용된 배열 형식의 `identityIds` 값과 달리 사전 형식의 `userAssignedIdentities` 값에 사용자 할당 관리 ID를 저장합니다.
   
   **API 버전 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API 버전 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM에서 시스템 할당 관리 ID를 사용하지 않도록 설정

VM에서 시스템 할당 관리 ID를 사용하지 않도록 설정하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. 인증 헤더의 다음 단계에서 시스템 할당 관리 ID로 VM을 만드는 데 사용할 전달자 액세스 토큰을 검색합니다.

   ```azurecli-interactive
   az account get-access-token
   ```

2. CURL을 사용하여 Azure Resource Manager REST 엔드포인트를 호출하도록 VM을 업데이트하여 시스템 할당 관리 ID를 사용하지 않도록 설정합니다.  다음 예제에서는 요청 본문에서 *myVM*이라는 VM의 `{"identity":{"type":"None"}}` 값으로 식별된 시스템 할당 관리 ID를 사용하지 않도록 설정합니다.  전달자 액세스 토큰을 요청한 이전 단계에서 받은 값 및 사용자 환경에 적절한 `<SUBSCRIPTION ID>` 값으로 `<ACCESS TOKEN>`을 바꿉니다.

   > [!IMPORTANT]
   > VM에 할당된 기존 사용자 할당 관리 ID를 삭제하지 않는지 확인하려면 다음 CURL 명령을 사용하여 사용자 할당 관리 ID를 나열해야 합니다. `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` 응답에서 `identity` 값으로 식별된 사용자 할당 관리 ID가 VM에 할당되어 있는 경우에는 VM에서 시스템 할당 관리 ID를 사용하지 않도록 설정하면서 사용자 할당 관리 ID를 유지하는 방법을 보여주는 3단계로 건너뜁니다.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   사용자 할당 관리 ID가 있는 가상 머신에서 시스템 할당 관리 ID를 제거하려면 **API 버전 2018-06-01**을 사용 중인 경우 `UserAssigned` 값 및 `userAssignedIdentities` 사전 값을 유지하면서 `{"identity":{"type:" "}}` 값에서 `SystemAssigned`를 제거합니다. **API 버전 2017-12-01** 이전 버전을 사용 중인 경우 `identityIds` 배열을 유지합니다.

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 CURL을 통해 Azure Resource Manager REST 엔드포인트를 호출하여 Azure VM에서 사용자 할당 관리 ID를 추가 및 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Azure VM 생성 중에 사용자 할당 관리 ID 할당

VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 및 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. 인증 헤더의 다음 단계에서 시스템 할당 관리 ID로 VM을 만드는 데 사용할 전달자 액세스 토큰을 검색합니다.

   ```azurecli-interactive
   az account get-access-token
   ```

2. VM에 대한 [네트워크 인터페이스](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)를 만듭니다.

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. 인증 헤더의 다음 단계에서 시스템 할당 관리 ID로 VM을 만드는 데 사용할 전달자 액세스 토큰을 검색합니다.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 사용자 할당 관리 ID는 [사용자 할당 관리 ID 만들기](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)의 지침에 따라 만듭니다.

5. CURL을 사용하여 Azure Resource Manager REST 엔드포인트를 호출하는 VM을 만듭니다. 다음 예제에서는 요청 본문에서 `"identity":{"type":"UserAssigned"}` 값으로 식별된 사용자 할당 관리 ID `ID1`을 사용하여 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 만듭니다. 전달자 액세스 토큰을 요청한 이전 단계에서 받은 값 및 사용자 환경에 적절한 `<SUBSCRIPTION ID>` 값으로 `<ACCESS TOKEN>`을 바꿉니다.
 
   **API 버전 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API 버전 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>기존 Azure VM에 사용자 할당 관리 ID 할당

VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 및 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. 인증 헤더의 다음 단계에서 시스템 할당 관리 ID로 VM을 만드는 데 사용할 전달자 액세스 토큰을 검색합니다.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  [사용자 할당 관리 ID 만들기](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)의 지침에 따라 사용자 할당 관리 ID를 만듭니다.

3. VM에 할당된 기존 사용자 또는 시스템 할당 관리 ID를 삭제하지 않는지 확인하려면 다음 CURL 명령을 사용하여 VM에 할당된 ID 유형을 나열해야 합니다. 가상 머신 확장 집합에 할당된 관리 ID가 있는 경우, 해당 ID가 `identity` 값 아래에 나열됩니다.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.

    응답에서 `identity` 값으로 식별된 사용자 또는 시스템 할당 관리 ID가 VM에 할당되어 있는 경우에는 VM에서 사용자 할당 관리 ID를 추가하면서 시스템 할당 관리 ID를 유지하는 방법을 보여주는 5단계로 건너뜁니다.

4. VM에 할당된 사용자 할당 관리 ID가 없는 경우에는 다음 CURL 명령을 통해 Azure Resource Manager REST 엔드포인트를 호출하여 첫 번째 사용자 할당 관리 ID를 VM에 할당합니다.

   다음 예제에서는 리소스 그룹 *myResourceGroup*의 *myVM*이라는 VM에 사용자 할당 관리 ID `ID1`을 할당합니다.  전달자 액세스 토큰을 요청한 이전 단계에서 받은 값 및 사용자 환경에 적절한 `<SUBSCRIPTION ID>` 값으로 `<ACCESS TOKEN>`을 바꿉니다.

   **API 버전 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        |
 
   **요청 본문**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API 버전 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. 기존 사용자 할당 또는 시스템 할당 관리 ID가 VM에 할당된 경우,
   
   **API 버전 2018-06-01**

   사용자 할당 관리 ID를 `userAssignedIdentities` 사전 값에 추가합니다.
    
   예를 들어, 현재 VM에 시스템 할당 관리 ID 및 사용자 할당 관리 ID `ID1`가 할당되어 있고 사용자 할당 관리 ID `ID2`를 추가하려면 다음을 입력합니다.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API 버전 2017-12-01**

   새 사용자 할당 관리 ID를 추가하면서 `identityIds` 배열 값에 유지하려는 사용자 할당 관리 ID를 유지합니다.

   예를 들어, 현재 VM에 시스템 할당 관리 ID 및 사용자 할당 관리 ID `ID1`가 할당되어 있고 사용자 할당 관리 ID `ID2`를 추가하려면 다음을 입력합니다. 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM에서 사용자 할당 관리 ID 제거

VM의 사용자 할당 ID를 제거하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.

1. 인증 헤더의 다음 단계에서 시스템 할당 관리 ID로 VM을 만드는 데 사용할 전달자 액세스 토큰을 검색합니다.

   ```azurecli-interactive
   az account get-access-token
   ```

2. VM에 할당된 상태를 유지할 기존 사용자 할당 관리 ID를 삭제하거나 시스템 할당 관리 ID를 제거하지 않는지 확인하려면 다음 CURL 명령을 사용하여 관리 ID를 나열해야 합니다. 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.
 
   VM에 관리 ID가 할당되어 있는 경우, `identity` 값의 응답으로 해당 ID가 나열됩니다.

   예를 들어 사용자 할당 관리 ID `ID1` 및 `ID2`가 VM에 할당되고 `ID1`의 할당 상태 및 시스템 할당 ID를 유지하려는 경우,
   
   **API 버전 2018-06-01**

   제거하려는 사용자 할당 관리 ID에 `null`을 추가합니다.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API 버전 2017-12-01**

   `identityIds` 배열에 유지하려는 사용자 할당 관리 ID만 유지합니다.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **요청 헤더**

   |요청 헤더  |Description  |
   |---------|---------|
   |*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
   |*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        | 

   **요청 본문**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

VM에 시스템 할당 및 사용자 할당 관리 ID가 모두 있는 경우 다음 명령을 사용하여 시스템 할당 관리 ID만 사용하도록 전환함으로써 모든 사용자 할당 관리 ID를 제거할 수 있습니다.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**요청 헤더**

|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다. | 

**요청 본문**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
VM에 사용자 할당 관리 ID만 있고 이를 모두 제거하려면 다음 명령을 사용합니다.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**요청 헤더**

|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.| 

**요청 본문**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>다음 단계

REST를 사용하여 사용자 할당 관리 ID를 만들거나, 나열하거나, 삭제하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [REST API 호출을 사용하여 사용자 할당 관리 ID 만들기, 나열 또는 삭제](how-to-manage-ua-identity-rest.md)