---
title: Azure NetApp Files에 등록 | Microsoft Docs
description: Waitlist 요청을 제출하고 Azure NetApp Files에 대한 Azure 리소스 공급자를 등록하여 Azure NetApp Files을 등록하는 방법을 알아봅니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: a61d6ba1f908a7dae56db066bfae329fc26f1c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696132"
---
# <a name="register-for-azure-netapp-files"></a>Azure NetApp Files에 등록

> [!IMPORTANT] 
> Azure NetApp Files 리소스 공급자를 등록하기 전에 Azure NetApp Files 팀에서 서비스에 대한 액세스 권한이 부여되었는지 확인하는 이메일을 받아야 합니다. 

이 문서에서는 서비스 사용을 시작할 수 있도록 Azure NetApp Files 등록하는 방법에 대해 알아봅니다.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>서비스에 액세스하기 위해 Waitlist 요청을 제출하세요

1. 다음 페이지로 이동하여 Azure NetApp Files 서비스에 액세스하기 위한 Waitlist 요청을 제출합니다.  
    [**Azure NetApp Files Waitlist 제출 페이지**](https://aka.ms/azurenetappfiles) 

    대기목록 등록은 즉각적인 서비스 액세스를 보장하지 않습니다. 

2. 다른 작업을 계속하려면 Azure NetApp Files 팀의 공식 확인 이메일을 기다리세요. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>NetApp 리소스 공급자 등록

서비스를 사용하려면 Azure NetApp Files에 Azure 리소스 공급자를 등록해야 합니다.

> [!NOTE] 
> 서비스에 대한 액세스 권한이 부여되지 않은 경우에도 NetApp 리소스 공급자를 성공적으로 등록할 수 있습니다. 그러나 액세스 권한 부여를 사용하지 않으면 NetApp 계정이나 기타 Azure NetApp Files 리소스를 만들기 위한 모든 Azure Portal 또는 API 요청이 다음의 에러와 함께 거부됩니다.  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Azure Portal의 오른쪽 위 모서리에 있는 Azure Cloud Shell 아이콘을 클릭합니다.

      ![Azure Cloud Shell 아이콘](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Azure 계정에 여러 구독이 있는 경우 Azure NetApp Files의 허용된 구독을 선택합니다.
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. Azure Cloud Shell 콘솔에서 구독이 허용되었는지 확인하려면 다음 명령을 입력합니다.
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   명령 출력은 다음과 같이 나타납니다.
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>`는 구독 ID입니다.

    기능 이름 `Microsoft.NetApp/ANFGA`이 표시되지 않는 경우 서비스에 대한 액세스 권한이 없는 것입니다. 이 단계에서 중지합니다. 계속하기 전에 서비스 액세스를 요청하기 위해 [서비스에 액세스하기 위한 waitlist 요청 제출](#waitlist)의 지침을 따르세요. 

4. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자를 등록합니다. 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   `--wait` 매개 변수는 등록이 완료될 때까지 기다리도록 콘솔에 지시합니다. 등록 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.

5. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자가 등록되었는지 확인합니다. 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   명령 출력은 다음과 같이 나타납니다.
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>`는 구독 ID입니다.  `state` 매개 변수 값은 `Registered`를 나타냅니다.

6. Azure Portal에서 **구독** 블레이드를 클릭합니다.
7. 구독 블레이드에서 구독 ID를 클릭합니다. 
8. 구독 설정에서 **리소스 공급자** 를 클릭하여 Microsoft.NetApp 공급자가 등록됨 상태를 나타내는지 확인합니다. 

      ![등록된 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>다음 단계

[NetApp 계정 만들기](azure-netapp-files-create-netapp-account.md)
