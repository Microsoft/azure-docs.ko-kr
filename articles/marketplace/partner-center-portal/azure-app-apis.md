---
title: 상업적 Marketplace에서 Azure 앱에 대 한 온 보 딩 API
description: Microsoft 파트너 센터의 상용 marketplace에서 Azure 앱에 대 한 API 필수 조건입니다.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: c14d8c6f27e4b0f4a4a75fa14b83455ff30ee35a
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933658"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>파트너 센터에서 Azure 앱을 온 보 딩 하기 위한 API

*파트너 센터 제출 API* 를 사용 하 여 프로그래밍 방식으로 쿼리 하 고 Azure 제품에 대 한 제출을 만들며 게시 합니다.  이 API는 계정이 많은 제품을 관리 하 고 이러한 제품에 대 한 제출 프로세스를 자동화 하 고 최적화 하려는 경우에 유용 합니다.

## <a name="api-prerequisites"></a>API 필수 조건

Azure 제품용 파트너 센터 API를 사용 하기 위해 필요한 몇 가지 프로그래밍 자산이 있습니다. 

- Azure Active Directory 응용 프로그램입니다.
- Azure Active Directory (Azure AD) 액세스 토큰입니다.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1 단계: 파트너 센터 제출 API를 사용 하기 위한 필수 구성 요소 완료

파트너 센터 제출 API를 호출 하는 코드 작성을 시작 하기 전에 다음 필수 구성 요소를 완료 했는지 확인 합니다.

- 사용자(또는 조직)에게 Azure AD 디렉터리와 해당 디렉터리에 대한 [전역 관리자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 권한이 있어야 합니다. 이미 Office 365 또는 Microsoft의 다른 비즈니스 서비스를 사용하는 경우 이미 Azure AD 디렉터리가 있습니다. 그렇지 않으면 추가 비용 없이 [파트너 센터에서 새 AZURE AD를 만들](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) 수 있습니다.

- [AZURE AD 응용 프로그램을 파트너 센터 계정에 연결](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) 하 고 테넌트 id, 클라이언트 id 및 키를 가져와야 합니다. 이러한 값은 Microsoft Store 제출 API에 대한 호출에 사용하는 Azure AD 액세스 토큰을 가져오는 데 필요합니다.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>파트너 센터 계정에 Azure AD 응용 프로그램을 연결 하는 방법

Microsoft Store 제출 API를 사용 하려면 Azure AD 응용 프로그램을 파트너 센터 계정에 연결 하 고, 응용 프로그램에 대 한 테넌트 ID와 클라이언트 ID를 검색 하 고, 키를 생성 해야 합니다. Azure AD 응용 프로그램은 파트너 센터 제출 API를 호출 하려는 앱 또는 서비스를 나타냅니다. API에 전달하는 Azure AD 액세스 토큰을 가져오려면 테넌트 ID, 클라이언트 ID 및 키가 필요합니다.

>[!Note]
>이 작업은 한 번만 수행 하면 됩니다. 테넌트 ID, 클라이언트 ID 및 키는 Azure AD 액세스 토큰을 새로 만들 때마다 다시 사용할 수 있습니다.

1. 파트너 센터에서 조직의 [파트너 센터 계정을 조직의 AZURE AD 디렉터리와 연결](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)합니다.
1. 그런 다음 파트너 센터의 **계정 설정** 섹션에 있는 **사용자** 페이지에서 파트너 센터 계정에 대 한 전송에 액세스 하는 데 사용할 앱 또는 서비스를 나타내는 [Azure AD 응용 프로그램을 추가](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) 합니다. 이 응용 프로그램에 **관리자** 역할을 할당하도록 합니다. 응용 프로그램이 아직 Azure AD 디렉터리에 존재 하지 않는 경우 [파트너 센터에서 새 AZURE ad 응용 프로그램을 만들](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)수 있습니다.
1. **사용자** 페이지로 돌아가서 Azure AD 응용 프로그램의 이름을 클릭하여 응용 프로그램 설정으로 이동하고 **테넌트 ID** 및 **클라이언트 ID** 값을 복사합니다.
1. **새 키 추가**를 클릭합니다. 다음 화면에서 **키** 값을 복사합니다. 이 페이지를 벗어난 후에는 이 정보에 다시 액세스할 수 없습니다. 자세한 내용은 [AD 응용 프로그램 키 관리](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)를 참조하세요.

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 가져오기

파트너 센터 제출 API의 메서드를 호출 하기 전에 먼저 API의 각 메서드에 대 한 **인증** 헤더에 전달 하는 Azure AD 액세스 토큰을 가져와야 합니다. 액세스 토큰을 얻은 후 만료되기 전에 60분 동안 사용할 수 있습니다. 토큰이 만료 된 후에는 API에 대 한 후속 호출에서 토큰을 계속 사용할 수 있도록 토큰을 새로 고칠 수 있습니다.

액세스 토큰을 가져오려면 [클라이언트 자격 증명을 사용 하 여 서비스 호출](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) 에 대 한 지침에 따라 `HTTP POST`를 `https://login.microsoftonline.com/<tenant_id>/oauth2/token` 엔드포인트으로 보냅니다. 다음은 샘플 요청입니다.

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

`POST URI` 및 *client_id* 및 *client_secret* 매개 변수의 *tenant_id* 값에 대해 이전 섹션에서 파트너 센터에서 검색 한 응용 프로그램의 테넌트 id, 클라이언트 id 및 키를 지정 합니다. *resource* 매개 변수에는 `https://api.partner.microsoft.com`을 지정해야 합니다.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>3단계: Microsoft Store 제출 API 사용

Azure AD 액세스 토큰을 만든 후 파트너 센터 제출 API에서 메서드를 호출할 수 있습니다. 제출을 만들거나 업데이트 하려면 일반적으로 파트너 센터 제출 API에서 특정 순서로 여러 메서드를 호출 합니다. 각 시나리오와 각 방법의 구문에 대 한 자세한 내용은 수집 API swagger를 참조 하세요.

https://apidocs.microsoft.com/services/partneringestion/
