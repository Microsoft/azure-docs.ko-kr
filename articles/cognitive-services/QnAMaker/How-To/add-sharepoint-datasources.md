---
title: SharePoint 파일 - QnA Maker
description: 기술 자료에 보안 SharePoint 데이터 원본을 추가하여 Active Directory로 보호할 수 있는 질문과 대답으로 기술 자료를 보강합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c15bac8417fdba5f87551dc13311b5272c0926ee
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108743852"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>기술 자료에 보안 SharePoint 데이터 원본 추가

기술 자료에 보안 클라우드 기반 SharePoint 데이터 원본을 추가하여 Active Directory로 보호할 수 있는 질문과 대답으로 기술 자료를 보강합니다.

기술 자료에 보안 SharePoint 문서를 추가하는 경우 QnA Maker 관리자로서 QnA Maker에 대한 Active Directory 권한을 요청해야 합니다. SharePoint에 접속하기 위해 이 권한이 Active Directory 관리자에서 QnA Maker로 제공되는 경우 다시 제공하지 않아도 됩니다. 동일한 SharePoint 리소스에 있는 경우 기술 자료에 대한 각 후속 문서 추가는 권한 부여가 필요하지 않습니다.

QnA Maker 기술 자료 관리자가 Active Directory 관리자가 아닌 경우에는 Active Directory 관리자와 통신하여 이 프로세스를 완료해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 클라우드 기반 SharePoint - QnA Maker는 권한에 대해 Microsoft Graph를 사용합니다. SharePoint가 온-프레미스인 경우 Microsoft Graph에서 권한을 확인할 수 없기 때문에 SharePoint에서 추출할 수 없습니다.
* URL 형식 - QnA Maker는 공유를 위해 생성되고 `https://\*.sharepoint.com` 형식인 SharePoint URL만 지원합니다.

## <a name="add-supported-file-types-to-knowledge-base"></a>기술 자료에 지원되는 파일 형식 추가

SharePoint 사이트에서 지원되는 모든 QnA Maker [파일 형식](../index.yml)을 기술 자료에 추가할 수 있습니다. 파일 리소스가 보호되는 경우 [사용 권한](#permissions)을 부여해야 할 수 있습니다.

1. SharePoint 사이트를 사용하는 라이브러리에서 파일의 줄임표 메뉴 `...`를 선택합니다.
1. 파일 URL을 복사합니다.

   ![파일의 줄임표 메뉴를 선택하고 URL을 복사하여 SharePoint 파일 URL을 가져옵니다.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. QnA Maker 포털의 **설정** 페이지에서 기술 자료에 URL을 추가합니다.

### <a name="images-with-sharepoint-files"></a>SharePoint 파일이 있는 이미지

파일이 이미지를 포함하는 경우 추출되지 않습니다. 파일이 QnA 쌍으로 추출된 후, QnA Maker 포털에서 이미지를 추가할 수 있습니다.

다음 Markdown 구문을 사용하여 이미지를 추가합니다.

```markdown
![Explanation or description of image](URL of public image)
```

대괄호 `[]` 안의 텍스트는 이미지에 대해 설명합니다. 괄호 `()` 안의 URL은 이미지에 대한 직접 링크입니다.

대화형 테스트 패널에서 QnA 쌍을 테스트하면 QnA Maker 포털에서 Markdown 텍스트가 아닌 이미지가 표시됩니다. 이렇게 하면 클라이언트-애플리케이션에서 이미지를 공개적으로 검색할 수 있는지 유효성을 검사합니다.

## <a name="permissions"></a>사용 권한

권한 부여는 SharePoint를 실행하는 서버의 보안 파일이 기술 자료에 추가된 경우에 발생합니다. SharePoint를 설정하는 방법 및 파일을 추가하는 사용자의 권한에 따라 다음과 같은 권한 부여 단계가 필요할 수 있습니다.

* 필요한 추가 단계가 없습니다. 파일을 추가하는 사용자에게 필요한 모든 권한이 있습니다.
* [기술 자료 관리자](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)와 [Active Directory 관리자](#active-directory-manager-grant-file-read-access-to-qna-maker) 모두에게 권한 부여 단계가 필요합니다.

아래 나열된 단계를 참조하세요.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>기술 자료 관리자: QnA Maker 포털에서 SharePoint 데이터 원본 추가

**QnA Maker 관리자** 가 기술 자료에 보안 SharePoint 문서를 추가하면 기술 자료 관리자는 Active Directory 관리자가 완료해야 하는 사용 권한에 대한 요청을 시작합니다.

요청은 Active Directory 계정에 인증하기 위한 팝업으로 시작합니다.

![사용자 계정 인증](../media/add-sharepoint-datasources/authenticate-user-account.png)

QnA Maker 관리자가 계정을 선택하면 Azure Active Directory 관리자는 QnA Maker 앱(QnA Maker 관리자가 아님)이 SharePoint 리소스에 액세스할 수 있도록 허용해야 한다는 알림을 받게 됩니다. Azure Active Directory 관리자는 모든 SharePoint 리소스에 대해 이 작업을 수행해야 하지만 해당 리소스의 모든 문서는 그렇지 않습니다.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory 관리자: QnA Maker에 파일 읽기 액세스 권한을 부여합니다.

Active Directory 관리자(QnA Maker 관리자가 아님)는 [이 링크](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68)를 선택하여 QnA Maker Portal SharePoint 엔터프라이즈 앱에 파일 읽기 권한을 부여함으로, SharePoint 리소스에 액세스 할 수 있는 액세스 권한을 QnA Maker에 부여해야 합니다.

![Azure Active Directory 관리자는 대화형으로 사용 권한을 부여합니다.](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에서 액세스 권한 부여

1. Active Directory 관리자는 Azure Portal에 로그인하고 **[엔터프라이즈 애플리케이션](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** 을 엽니다.

1. `QnAMakerPortalSharePoint`를 검색하고 QnA Maker 앱을 선택합니다.

    [![Enterprise 앱 목록에서 QnAMakerPortalSharePoint를 검색합니다.](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. **보안** 에서 **사용 권한** 으로 이동합니다. **조직에 대한 관리자 동의 부여** 를 선택합니다.

    [![Active Directory 관리자에 대해 인증된 사용자를 선택합니다.](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 권한이 있는 로그온 계정을 선택하여 Active Directory에 대한 권한을 부여합니다.




## <a name="add-sharepoint-data-source-with-apis"></a>API를 사용하여 SharePoint 데이터 원본 추가

Azure Blob Storage를 사용하여 API를 통해 최신 SharePoint 콘텐츠를 추가하는 방법은 다음과 같습니다. 
1.  SharePoint 파일을 로컬로 다운로드합니다. API를 호출하는 사용자는 SharePoint에 대한 액세스 권한이 있어야 합니다. 
1.  Azure Blob Storage에 업로드합니다. 이렇게 하면 [SAS 토큰을 사용](../../../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works)하여 보안 공유 액세스가 생성됩니다. 
1. SAS 토큰을 사용하여 생성된 Blob URL을 QnA Maker API로 전달합니다. 파일에서 질문 및 답변 추출을 허용하려면 URL의 끝에 접미사 파일 유형을 '&ext=pdf' 또는 '&ext=doc'로 추가하여 QnA Maker API로 전달해야 합니다.


<!--
## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) or [updating a knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에 대한 공동 작업](../index.yml)
