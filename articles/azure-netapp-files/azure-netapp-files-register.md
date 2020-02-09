---
title: Azure NetApp Files에 등록 | Microsoft Docs
description: Azure NetApp 파일을 사용 하도록 등록 하는 방법을 설명 합니다.
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
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299223"
---
# <a name="register-for-azure-netapp-files"></a>Azure NetApp Files에 등록

> [!IMPORTANT] 
> Azure NetApp 파일 리소스 공급자를 등록 하기 전에 해야 전자 메일에서에서 받은 Azure NetApp Files 팀은 부여 된 서비스에 대한 액세스를 확인 합니다. 

이 문서에서는 서비스를 사용 하기 시작할 수 있도록 Azure NetApp 파일에 대한 등록 하는 방법에 알아봅니다.

## <a name="waitlist"></a>서비스에 액세스 하기 위한 대기 요청 제출

1. 통해 Azure NetApp 파일 서비스에 액세스 하기 위한 대기 요청을 제출 합니다 [Azure NetApp 파일 대기 제출 페이지](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)합니다. 

    대기 등록 즉시 서비스 액세스를 보장 하지 않습니다. 

2. 다른 작업을 진행 하기 전에 Azure NetApp Files 팀의 공식 확인 전자 메일을 기다립니다. 

## <a name="resource-provider"></a>NetApp 리소스 공급자 등록

서비스를 사용하려면 Azure NetApp Files에 Azure 리소스 공급자를 등록해야 합니다.

> [!NOTE] 
> 서비스에 대한 액세스를 부여할 수 없이 NetApp 리소스 공급자를 성공적으로 등록 할 수 있습니다. 그러나 액세스 권한 부여 없이 모든 Azure portal 또는 NetApp 계정 또는 다른 Azure NetApp 파일 리소스를 만들려면 API 요청 거부 됩니다 다음 오류로 인해:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Azure Portal의 오른쪽 위 모서리에 있는 Azure Cloud Shell 아이콘을 클릭합니다.

      ![Azure Cloud Shell 아이콘](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Azure 계정에 여러 구독이 있는 경우 Azure NetApp Files의 허용 목록에 지정된 구독을 선택합니다.
    
        az account set --subscription <subscriptionId>

3. Azure Cloud Shell 콘솔에서 구독이 허용 목록에 지정되었는지 확인하려면 다음 명령을 입력합니다.
    
        az feature list | grep NetApp

   명령 출력은 다음과 같이 나타납니다.
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>`는 구독 ID입니다.

    기능 이름이 표시 되지 않으면 `Microsoft.NetApp/ANFGA`, 서비스에 대한 액세스 권한이 없습니다. 이 단계에서 중지 합니다. 지침에 따라 [서비스에 액세스 하기 위한 대기 요청을 제출](#waitlist) 계속 하기 전에 서비스 액세스를 요청 합니다. 

4. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자를 등록합니다. 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` 매개 변수는 등록이 완료될 때까지 기다리도록 콘솔에 지시합니다. 등록 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.

5. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자가 등록되었는지 확인합니다. 
    
        az provider show --namespace Microsoft.NetApp

   명령 출력은 다음과 같이 나타납니다.
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>`는 구독 ID입니다.  `state` 매개 변수 값은 `Registered`를 나타냅니다.

6. Azure Portal에서 **구독** 블레이드를 클릭합니다.
7. 구독 블레이드에서 구독 ID를 클릭합니다. 
8. 구독 설정에서 **리소스 공급자**를 클릭하여 Microsoft.NetApp 공급자가 등록됨 상태를 나타내는지 확인합니다. 

      ![등록된 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>다음 단계

[NetApp 계정 만들기](azure-netapp-files-create-netapp-account.md)