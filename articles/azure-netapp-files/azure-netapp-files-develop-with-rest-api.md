---
title: REST API를 사용한 Azure NetApp Files용 개발 | Microsoft Docs
description: Azure NetApp Files REST API 사용을 시작하는 방법을 설명합니다.
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
ms.date: 02/06/2019
ms.author: b-juche
ms.openlocfilehash: 56667b9a47411b2abae30ff159fa6bc555fec070
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087253"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>REST API를 사용한 Azure NetApp Files용 개발 

Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅숏과 같은 리소스에 대한 HTTP 작업을 정의합니다. 이 문서는 Azure NetApp Files REST API 사용을 시작하는 데 도움이 됩니다.

## <a name="access-the-azure-netapp-files-rest-api"></a>Azure NetApp Files REST API 액세스  

1. 아직 설치하지 않은 경우 [Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)합니다.
2. Azure AD(Azure Active Directory)에 서비스 주체를 만듭니다.
   1. [충분한 권한](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)이 있는지 확인합니다.

   1. Azure CLI에서 다음 명령을 입력합니다.  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      명령 출력은 다음 예제와 유사합니다.  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      명령 출력을 보관합니다.  `appId`, `password` 및 `tenant` 값이 필요합니다. 

3. OAuth 액세스 토큰을 요청합니다.

    이 문서의 예제에서는 cURL을 사용합니다.  [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) 및 [Paw](https://paw.cloud/)와 같은 다양한 API 도구를 사용할 수도 있습니다.  

    다음 예제의 변수를 위 2단계의 명령 출력으로 바꿉니다. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    출력에서 다음 예제와 유사한 액세스 토큰을 제공합니다.

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    표시된 토큰은 3600초간 유효합니다. 그 후에는 새 토큰을 요청해야 합니다. 
    텍스트 편집기에 토큰을 저장합니다.  이 토큰은 다음 단계에서 필요합니다.

4. 테스트 호출을 보내고 REST API에 대한 액세스 권한을 확인할 토큰을 포함합니다.

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>API 사용 예제  

이 문서에서는 요청 기준선으로 다음 URL을 사용합니다. 이 URL은 Azure NetApp Files 네임스페이스의 루트를 가리킵니다. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

다음 예제의 `subID` 및 `resourceGroups` 값을 사용자 고유의 값으로 바꾸어야 합니다. 

### <a name="get-request-examples"></a>GET 요청 예제

다음 예제와 같이 GET 요청을 사용하여 구독에서 Azure NetApp Files의 개체를 쿼리합니다. 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>PUT 요청 예제

다음 예제와 같이 PUT 요청을 사용하여 Azure NetApp Files에 새 개체를 만듭니다. PUT 요청의 본문은 변경 내용에 대한 JSON 형식 데이터를 포함하거나, 읽어올 파일을 지정할 수 있습니다. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>JSON 예

다음 예제에서는 NetApp 계정을 만드는 방법을 보여 줍니다.

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

다음 예제에서는 용량 풀을 만드는 방법을 보여 줍니다. 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

다음 예제에서는 새 볼륨을 만드는 방법을 보여 줍니다. 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

다음 예제에서는 볼륨의 스냅숏을 만드는 방법을 보여 줍니다. 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> 스냅숏을 만들려면 `fileSystemId`를 지정해야 합니다.  볼륨에 대한 GET 요청을 사용하여 `fileSystemId` 값을 얻을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files REST API 참조 문서 참조](https://docs.microsoft.com/rest/api/netapp/)
