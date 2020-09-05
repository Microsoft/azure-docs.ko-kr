---
title: Microsoft Graph Api를 사용 하 여 프로 비전 구성-Azure Active Directory | Microsoft Docs
description: 응용 프로그램의 여러 인스턴스에 대해 프로 비전을 설정 해야 하나요? 자동 프로 비전 구성을 자동화 하기 위해 Microsoft Graph Api를 사용 하 여 시간을 절약 하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 17660df34c8039ae96440c417aef051d51a5c91c
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234756"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Microsoft Graph Api를 사용 하 여 프로 비전 구성

Azure Portal은 한 번에 하나씩 개별 앱에 대 한 프로 비전을 구성 하는 편리한 방법입니다. 하지만 응용 프로그램의 인스턴스를 여러 개 또는 수백 개 만들 경우 Microsoft Graph Api를 사용 하 여 앱 만들기 및 구성을 쉽게 자동화할 수 있습니다. 이 문서에서는 Api를 통해 프로 비전 구성을 자동화 하는 방법을 설명 합니다. 이 메서드는 [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso)같은 응용 프로그램에 일반적으로 사용 됩니다.

**Microsoft Graph Api를 사용 하 여 프로 비전 구성을 자동화 하는 단계 개요**


|단계  |세부 정보  |
|---------|---------|
|[1 단계. 갤러리 응용 프로그램 만들기](#step-1-create-the-gallery-application)     |API 클라이언트에 로그인 <br> 갤러리 응용 프로그램 템플릿을 검색 합니다. <br> 갤러리 애플리케이션 만들기         |
|[2 단계. 템플릿을 기반으로 프로 비전 작업 만들기](#step-2-create-the-provisioning-job-based-on-the-template)     |프로 비전 커넥터용 템플릿 검색 <br> 프로 비전 작업 만들기         |
|[3 단계. 액세스 권한 부여](#step-3-authorize-access)     |응용 프로그램에 대 한 연결 테스트 <br> 자격 증명 저장         |
|[4 단계. 프로 비전 작업 시작](#step-4-start-the-provisioning-job)     |작업 시작         |
|[5 단계. 프로 비전 모니터링](#step-5-monitor-provisioning)     |프로 비전 작업의 상태를 확인 합니다. <br> 프로 비전 로그를 검색 합니다.         |

> [!NOTE]
> 이 문서에서 제시하는 응답 개체는 가독성을 위해 짧게 표시될 수 있습니다. 모든 속성은 실제 호출에서 반환됩니다.

## <a name="step-1-create-the-gallery-application"></a>1단계: 갤러리 애플리케이션 만들기

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Microsoft Graph Explorer(권장), Postman 또는 그 밖의 API 클라이언트에 로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 시작합니다.
1. "Microsoft 로그인" 단추를 선택 하 고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용 하 여 로그인 합니다.

    ![Graph 로그인](./media/application-provisioning-configure-api/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

### <a name="retrieve-the-gallery-application-template-identifier"></a>갤러리 애플리케이션 템플릿 식별자 검색
Azure AD 애플리케이션 갤러리의 각 애플리케이션에는 해당 애플리케이션의 메타데이터를 설명하는 [애플리케이션 템플릿](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta)이 있습니다. 이 템플릿을 사용하여 테넌트에서 관리를 위해 애플리케이션 및 서비스 주체의 인스턴스를 만들 수 있습니다.

#### <a name="request"></a>*요청*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*응답*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>갤러리 애플리케이션 만들기

마지막 단계에서 응용 프로그램에 대해 검색 된 템플릿 ID를 사용 하 여 테 넌 트에 응용 프로그램 및 서비스 사용자의 [인스턴스를 만듭니다](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta) .

#### <a name="request"></a>*요청*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*응답*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>2 단계: 템플릿을 기반으로 프로 비전 작업 만들기

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>프로 비전 커넥터용 템플릿 검색

프로 비전을 사용 하도록 설정 된 갤러리의 응용 프로그램에는 구성을 간소화 하는 템플릿이 있습니다. 아래 요청을 사용 하 여 [프로 비전 구성에 대 한 템플릿을 검색](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta)합니다. ID를 제공 해야 합니다. ID는 위의 리소스를 참조 합니다 .이 경우에는 ServicePrincipal입니다. 

#### <a name="request"></a>*요청*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*응답*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>프로 비전 작업 만들기
프로 비전을 사용 하도록 설정 하려면 먼저 [작업을 만들어야](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta)합니다. 아래 요청을 사용 하 여 프로 비전 작업을 만듭니다. 작업에 사용할 템플릿을 지정할 때 이전 단계의 templateId을 사용 합니다.

#### <a name="request"></a>*요청*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*응답*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>3 단계: 액세스 권한 부여

### <a name="test-the-connection-to-the-application"></a>응용 프로그램에 대 한 연결 테스트

타사 응용 프로그램에 대 한 연결을 테스트 합니다. 아래 예제는 clientSecret 및 secretToken가 필요한 응용 프로그램에 대 한 것입니다. 각 응용 프로그램에는 요구 사항이 있습니다. 응용 프로그램에서는 ClientSecret 대신 BaseAddress를 사용 하는 경우가 많습니다. 앱에 필요한 자격 증명을 확인 하려면 응용 프로그램의 프로 비전 구성 페이지로 이동 하 고 개발자 모드에서 연결 테스트를 클릭 합니다. 네트워크 트래픽은 자격 증명에 사용 되는 매개 변수를 표시 합니다. 자격 증명의 전체 목록은 [여기](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta)에서 찾을 수 있습니다. 

#### <a name="request"></a>*요청*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*응답*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>자격 증명 저장

프로 비전을 구성 하려면 Azure AD와 응용 프로그램 간에 트러스트를 설정 해야 합니다. 타사 응용 프로그램에 대 한 액세스 권한을 부여 합니다. 아래 예제는 clientSecret 및 secretToken가 필요한 응용 프로그램에 대 한 것입니다. 각 응용 프로그램에는 요구 사항이 있습니다. [API 설명서](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta) 를 검토 하 여 사용 가능한 옵션을 확인 합니다. 

#### <a name="request"></a>*요청*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*응답*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>4 단계: 프로 비전 작업 시작
프로 비전 작업이 구성 되었으므로 다음 명령을 사용 하 여 [작업을 시작](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta)합니다. 


#### <a name="request"></a>*요청*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*응답*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>5 단계: 프로 비전 모니터링

### <a name="monitor-the-provisioning-job-status"></a>프로 비전 작업 상태 모니터링

프로 비전 작업이 실행 되 고 있으므로 다음 명령을 사용 하 여 대상 시스템에 생성 된 사용자 및 그룹의 수와 같은 통계 뿐만 아니라 현재 프로 비전 주기의 진행 상황을 추적할 수 있습니다. 

#### <a name="request"></a>*요청*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*응답*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>프로 비전 로그를 사용 하 여 프로 비전 이벤트 모니터링
프로 비전 작업의 상태를 모니터링 하는 것 외에도 [프로 비전 로그](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta) 를 사용 하 여 발생 하는 모든 이벤트를 쿼리할 수 있습니다. 예를 들어 특정 사용자에 대 한 쿼리를 수행 하 여 해당 사용자가 성공적으로 프로 비전 되었는지 여부를 확인할 수 있습니다.

#### <a name="request"></a>*요청*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*응답*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>관련 문서

- [동기화 Microsoft Graph 설명서 검토](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [사용자 지정 SCIM 앱을 Azure AD와 통합](use-scim-to-provision-users-and-groups.md)