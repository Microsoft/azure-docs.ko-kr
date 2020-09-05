---
title: Azure AD 스키마 및 사용자 지정 식 이해
description: 이 문서에서는 Azure AD 스키마, 프로 비전 에이전트가 흐르는 특성 및 사용자 지정 식을 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fff90777c63820b54a1cb37156021c894de19c8
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226933"
---
# <a name="understand-the-azure-ad-schema"></a>Azure AD 스키마 이해
모든 디렉터리와 같이 Azure Active Directory (Azure AD)의 개체는 사용자, 그룹 및 연락처와 같은 작업을 나타내는 프로그래밍 방식의 고급 데이터 구문입니다. Azure AD에서 새 사용자 또는 연락처를 만들 때 해당 개체의 새 인스턴스를 만듭니다. 이러한 인스턴스는 속성을 기반으로 구분할 수 있습니다.

Azure AD의 속성은 Azure AD에서 개체의 인스턴스에 대 한 정보를 저장 하는 요소입니다.

Azure AD 스키마는 항목에 사용 될 수 있는 속성, 해당 속성에 포함 될 수 있는 값의 종류 및 사용자가 이러한 값으로 상호 작용 하는 방법에 대 한 규칙을 정의 합니다. 

Azure AD에는 다음과 같은 두 가지 유형의 속성이 있습니다.
- **기본 제공 속성**: Azure AD 스키마에 의해 미리 정의 된 속성입니다. 이러한 속성은 다양 한 용도를 제공 하며 액세스할 수 있거나 액세스할 수 없는 경우도 있습니다.
- **디렉터리 확장**: 사용자가 직접 사용 하기 위해 Azure AD를 사용자 지정할 수 있도록 제공 되는 속성입니다. 예를 들어 특정 특성을 사용 하 여 온-프레미스 Active Directory을 확장 하 고 해당 특성을 전달 하려는 경우 제공 되는 사용자 지정 속성 중 하나를 사용할 수 있습니다. 

## <a name="attributes-and-expressions"></a>특성 및 식
사용자와 같은 개체가 Azure AD에 프로 비전 되 면 사용자 개체의 새 인스턴스가 만들어집니다. 여기에는 해당 개체의 속성 (특성)이 포함 됩니다. 처음에는 새로 만든 개체의 특성이 동기화 규칙에 따라 결정 되는 값으로 설정 됩니다. 이러한 특성은 클라우드 프로 비전 에이전트를 통해 최신 상태로 유지 됩니다.

![개체 프로 비전](media/concept-attributes/attribute1.png)

예를 들어 사용자가 마케팅 부서의 일부일 수 있습니다. 처음에는 해당 Azure AD 부서 특성이 프로 비전 될 때 생성 되 고 값이 Marketing으로 설정 됩니다. 6 개월 후에 판매로 변경 되 면 해당 온-프레미스 Active Directory 부서 특성이 Sales로 변경 됩니다. 이 변경 내용은 Azure AD와 동기화 되며 해당 Azure AD 사용자 개체에 반영 됩니다.

특성 동기화는 직접적인 일 수 있습니다. 여기서 Azure AD의 값은 온-프레미스 특성의 값으로 직접 설정 됩니다. 또는 프로그래밍 식이 동기화를 처리할 수도 있습니다. 값을 채우도록 일부 논리 또는 결정을 수행 해야 하는 경우 프로그래밍 식이 필요 합니다.

예를 들어 메일 특성 ""을 사용 하 여 " john.smith@contoso.com " 부분을 제거 하 @contoso.com 고 "john. smith" 값만 전달 해야 하는 경우 다음과 같은 항목을 사용 합니다.

`Replace([mail], "@contoso.com", , ,"", ,)`  

**샘플 입/출력:** <br>

* **입력**(메일): “john.smith@contoso.com”
* **출력**: "john. smith"

사용자 지정 식 및 구문을 작성 하는 방법에 대 한 자세한 내용은 [Azure Active Directory에서 특성 매핑에 대 한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)을 참조 하세요.

다음 표에서는 일반적인 특성 및 Azure AD에 동기화 되는 방법을 보여 줍니다.


|온-프레미스 Active Directory|매핑 유형|Azure AD|
|-----|-----|-----|
|cn|직접|commonName
|countryCode|직접|countryCode|
|displayName|직접|displayName|
|givenName|식|givenName|
|objectGUID|직접|sourceAnchorBinary|  
|userprincipalName|직접|userPrincipalName|
|ProxyAdress|직접|ProxyAddress|

## <a name="view-the-schema"></a>스키마 보기
> [!WARNING]
> 클라우드 프로 비전 구성에서는 서비스 주체를 만듭니다. 서비스 사용자는 Azure Portal에서 볼 수 있습니다. Azure Portal의 서비스 사용자 환경을 사용 하 여 특성 매핑을 수정 하면 안 됩니다.  이는 지원되지 않습니다.

스키마를 확인 하 고 확인 하려면 다음 단계를 수행 합니다.

1.  [그래프 탐색기](https://developer.microsoft.com/graph/graph-explorer)로 이동 합니다.
1.  전역 관리자 계정으로 로그인 합니다.
1.  왼쪽에서 **권한 수정** 을 선택 하 고 **디렉터리. ReadWrite** 가 *동의한*인지 확인 합니다.
1.  쿼리를 실행 `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')` 합니다. 이 쿼리는 서비스 사용자의 필터링 된 목록을 반환 합니다.
1.  `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"`의 값을 찾아 확인 `"id"` 합니다.
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. `{Service Principal id}`값으로 대체 하 고 쿼리를 실행 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/` 합니다.
1. `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"`의 값을 찾아 확인 `"id"` 합니다.
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. 이제 쿼리를 실행 `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` 합니다.
 
    예: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   `{Service Principal Id}` 및 `{AD2ADD Provisioning Id}` 를 값으로 바꿉니다.

1. 이 쿼리는 스키마를 반환 합니다.

   ![반환 된 스키마](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>다음 단계

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)