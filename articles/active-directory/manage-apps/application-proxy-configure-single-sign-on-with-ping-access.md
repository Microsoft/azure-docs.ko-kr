---
title: Azure AD 애플리케이션 프록시용 PingAccess를 통한 헤더 기반 인증 | Microsoft Docs
description: PingAccess 및 앱 프록시를 사용하여 애플리케이션을 게시하여 헤더 기반 인증을 지원합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec115e0fa76e695809ba140202d5f13a319d33dd
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062731"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>애플리케이션 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증

Azure ad (Azure Active Directory) 응용 프로그램 프록시는 Azure AD 고객이 더 많은 응용 프로그램에 액세스할 수 있도록 Azure 액세스와 파트너 관계를 맺고 있습니다. PingAccess는 [기존 애플리케이션 프록시 제품](application-proxy.md)을 확장하여 인증에 헤더를 사용하는 애플리케이션에 대한 Single Sign-On 액세스를 포함하고 있습니다.

## <a name="whats-pingaccess-for-azure-ad"></a>Azure AD에 대 한 Azure 액세스는 무엇 인가요?

Azure AD에 대 한 액세스를 통해 사용자에 게 인증에 헤더를 사용 하는 응용 프로그램에 대 한 액세스 및 Single Sign-On (SSO)를 제공할 수 있습니다. 애플리케이션 프록시는 이러한 애플리케이션을 Azure AD를 사용하여 액세스를 인증한 다음, 커넥터 서비스를 통해 트래픽을 전달하는 방식으로 다른 앱과 유사하게 처리합니다. 액세스는 응용 프로그램 앞에 배치 되 고 액세스 토큰을 Azure AD에서 헤더로 변환 합니다. 그러면 응용 프로그램에서 읽을 수 있는 형식으로 인증을 받습니다.

사용자는 회사 애플리케이션을 사용하기 위해 로그인할 때 다른 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 디바이스에서 작업할 수 있습니다. 응용 프로그램 프록시 커넥터는 인증 유형에 관계 없이 모든 앱에 원격 트래픽을 전달 하므로 부하를 자동으로 분산 합니다.

## <a name="how-do-i-get-access"></a>액세스 권한은 어떻게 얻을 수 있나요?

이 시나리오는 Azure Active Directory 간의 파트너 관계에서 제공 되므로 두 서비스에 대 한 라이선스가 필요 합니다. 그러나 Azure Active Directory Premium 구독에는 최대 20개의 애플리케이션을 보장하는 기본 PingAccess 라이선스가 포함되어 있습니다. 헤더 기반 애플리케이션을 20개 이상 게시해야 하는 경우 PingAccess에서 라이선스를 추가로 구입할 수 있습니다.

자세한 내용은 [Azure Active Directory Edition](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="publish-your-application-in-azure"></a>Azure에 애플리케이션 게시

이 문서는이 시나리오에서 처음으로 응용 프로그램을 게시할 수 있는 사용자를 위한 것입니다. 게시 단계를 자세히 설명 하는 것 외에도 응용 프로그램 프록시와 사용자 액세스를 모두 시작 하는 과정을 안내 합니다. 두 서비스를 이미 구성 했지만 게시 단계에서 리프레셔를 원하는 경우 응용 프로그램 프록시를 사용 하 [여 AZURE AD에 응용 프로그램 추가](#add-your-application-to-azure-ad-with-application-proxy) 섹션으로 건너뜁니다.

> [!NOTE]
> 이 시나리오는 Azure AD와 PingAccess 간의 파트너 관계이므로 일부 지침은 Ping ID 사이트에 존재합니다.

### <a name="install-an-application-proxy-connector"></a>애플리케이션 프록시 커넥터 설치

응용 프로그램 프록시를 사용 하도록 설정 하 고 커넥터를 이미 설치한 경우이 섹션을 건너뛰고 [응용 프로그램 프록시를 사용 하 여 AZURE AD에 응용 프로그램 추가](#add-your-application-to-azure-ad-with-application-proxy)로 이동할 수 있습니다.

응용 프로그램 프록시 커넥터는 원격 직원의 트래픽을 게시 된 응용 프로그램으로 전달 하는 Windows Server 서비스입니다. 자세한 설치 지침은 [자습서: 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가 Azure Active Directory](application-proxy-add-on-premises-application.md)를 참조 하세요.

1. 응용 프로그램 관리자로 [Azure Active Directory 포털](https://aad.portal.azure.com/) 에 로그인 합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. **커넥터 서비스 > 다운로드** **Azure Active Directory** > **응용 프로그램 프록시** 를 선택 합니다. **응용 프로그램 프록시 커넥터 다운로드** 페이지가 나타납니다.

   ![응용 프로그램 프록시 커넥터 다운로드](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 설치 지침을 따릅니다.

커넥터를 다운로드 하면 디렉터리에 응용 프로그램 프록시가 자동으로 사용 하도록 설정 되지만 그렇지 않은 경우 **응용 프로그램 프록시 사용**을 선택할 수 있습니다.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 여 Azure AD에 응용 프로그램 추가

Azure Portal에서 수행해야 하는 두 가지 작업이 있습니다. 먼저 애플리케이션 프록시를 사용하여 애플리케이션을 게시해야 합니다. 그런 다음 응용 프로그램에 대 한 일부 정보를 수집 해야 합니다.

#### <a name="publish-your-application"></a>애플리케이션 게시

먼저 응용 프로그램을 게시 해야 합니다. 이 작업에는 다음이 포함 됩니다.

- Azure AD에 온-프레미스 응용 프로그램 추가
- 응용 프로그램을 테스트 하 고 헤더 기반 SSO를 선택 하기 위한 사용자 할당
- 응용 프로그램의 리디렉션 URL 설정
- 사용자 및 다른 응용 프로그램에서 온-프레미스 응용 프로그램을 사용할 수 있는 권한 부여

자신의 온-프레미스 응용 프로그램을 게시 하려면:

1. 마지막 섹션에 없는 경우 응용 프로그램 관리자로 [Azure Active Directory 포털](https://aad.portal.azure.com/) 에 로그인 합니다.
1. **새 응용** 프로그램 > **엔터프라이즈 응용** 프로그램 **을 선택 > 온-프레미스 응용 프로그램을 추가**합니다. **사용자 고유의 온-프레미스 응용 프로그램 추가** 페이지가 나타납니다.

   ![자신의 온-프레미스 응용 프로그램 추가](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 새 응용 프로그램에 대 한 정보를 사용 하 여 필수 필드를 작성 합니다. 설정에 대 한 아래 지침을 사용 합니다.

   > [!NOTE]
   > 이 단계에 대 한 자세한 연습은 [AZURE AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조 하세요.

   1. **내부 url**: 회사 네트워크에 있을 때 일반적으로 앱의 로그인 페이지로 이동 하는 URL을 제공 합니다. 이 시나리오의 경우 커넥터는 응용 프로그램의 front 페이지로 연결 액세스 프록시를 처리 해야 합니다. `https://<host name of your PingAccess server>:<port>` 형식을 사용합니다. 포트는 기본적으로 3000이지만 PingAccess에서 구성할 수 있습니다.

      > [!WARNING]
      > 이 유형의 Single Sign-On에서 내부 URL은 `https`를 사용 해야 하며 `http`를 사용할 수 없습니다.

   1. **사전 인증 방법**: **Azure Active Directory**를 선택 합니다.
   1. **헤더에서 URL 변환**: **아니요**를 선택 합니다.

   > [!NOTE]
   > 첫 번째 애플리케이션인 경우 3000 포트를 사용하여 시작하고 PingAccess 구성을 변경하면 다시 돌아와서 이 설정을 업데이트합니다. 후속 응용 프로그램의 경우 포트는 사용자가 액세스 하도록 구성한 수신기와 일치 해야 합니다. [PingAccess의 수신기](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)에 대해 자세히 알아봅니다.

1. **추가**를 선택합니다. 새 응용 프로그램에 대 한 개요 페이지가 나타납니다.

이제 응용 프로그램 테스트를 위해 사용자를 할당 하 고 헤더 기반 Single Sign-On를 선택 합니다.

1. 응용 프로그램 사이드바에서 사용자 **및 그룹** 을 선택 하 > 사용자 > 사용자 **및 그룹 (\<번호 > 선택)** 을 **추가** 합니다. 사용자가 선택할 수 있는 사용자 및 그룹의 목록이 표시 됩니다.

   ![사용자 및 그룹의 목록을 표시 합니다.](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 응용 프로그램 테스트를 위한 사용자를 선택 하 고 **선택**을 선택 합니다. 이 테스트 계정에 온-프레미스 애플리케이션에 대한 액세스 권한이 있는지 확인합니다.
1. **할당**을 선택합니다.
1. 응용 프로그램 사이드바에서 **Single sign-on** > **헤더 기반**을 선택 합니다.

   > [!TIP]
   > 헤더 기반 Single Sign-On을 처음 사용하는 경우 PingAccess를 설치해야 합니다. Azure 구독이 PingAccess 설치와 자동으로 연결되도록 하려면 이 Single Sign-On 페이지의 링크를 사용하여 PingAccess를 다운로드합니다. 지금 다운로드 사이트를 열거나 나중에 이 페이지로 다시 돌아올 수 있습니다.

   ![헤더 기반 로그온 화면 및 액세스 권한 표시](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. **저장**을 선택합니다.

그런 다음 리디렉션 URL이 외부 URL로 설정 되었는지 확인 합니다.

1. **Azure Active Directory 관리 센터** 사이드바에서 **Azure Active Directory** > **앱 등록**를 선택 합니다. 응용 프로그램 목록이 표시 됩니다.
1. 애플리케이션을 선택합니다.
1. 웹 및 공용 클라이언트에 대해 설정 된 리디렉션 Uri 수를 표시 하는 **리디렉션 uri**옆의 링크를 선택 합니다. **\<응용 프로그램 이름 > 인증** 페이지가 표시 됩니다.
1. 이전에 응용 프로그램에 할당 한 외부 URL이 **리디렉션 uri** 목록에 있는지 확인 합니다. 그렇지 않은 경우에는 **웹**의 리디렉션 URI 형식을 사용 하 여 외부 URL을 지금 추가 하 고 **저장**을 선택 합니다.

마지막으로, 사용자가 읽기 액세스 권한을 가지 며 다른 응용 프로그램에 읽기/쓰기 액세스 권한이 있도록 온-프레미스 응용 프로그램을 설정 합니다.

1. 응용 프로그램에 대 한 **앱 등록** 사이드바에서 **API 사용 권한** > **Microsoft api > Microsoft api** > **Microsoft Graph** **추가** 를 선택 합니다. **Microsoft Graph** 에 대 한 **api 권한 요청** 페이지가 표시 되 고 Windows Azure Active Directory에 대 한 api가 포함 되어 있습니다.

   ![API 권한 요청 페이지를 표시 합니다.](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. **사용자** > 사용자 > **위임 된 권한** 을 선택 **합니다. 읽기**.
1. **응용 프로그램 > 응용** 프로그램 > 응용 프로그램 **사용 권한** **을 선택**합니다.
1. **권한 추가**를 선택 합니다.
1. **API 사용 권한** 페이지에서 **\<디렉터리 이름 >에 대 한 관리자 동의 부여**를 선택 합니다.

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess 단계에 대한 정보 수집

다음 세 가지 정보 (모든 Guid)를 수집 하 여 응용 프로그램에 대 한 액세스 권한을 설정 해야 합니다.

| Azure AD 필드의 이름 | 인터넷 액세스 필드의 이름입니다. | 데이터 형식 |
| --- | --- | --- |
| **애플리케이션(클라이언트) ID** | **클라이언트 ID** | GUID |
| **디렉터리(테넌트) ID** | **발행** | GUID |
| `PingAccess key` | **클라이언트 암호** | 임의의 문자열 |

이 정보를 수집 하려면:

1. **Azure Active Directory 관리 센터** 사이드바에서 **Azure Active Directory** > **앱 등록**를 선택 합니다. 응용 프로그램 목록이 표시 됩니다.
1. 애플리케이션을 선택합니다. 응용 프로그램에 대 한 **앱 등록** 페이지가 나타납니다.

   ![응용 프로그램에 대 한 등록 개요](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. **응용 프로그램 (클라이언트) ID** 값 옆에서 **클립보드에 복사** 아이콘을 선택 하 고 복사 하 여 저장 합니다. 나중에이 값을 사용자의 클라이언트 ID로 지정 합니다.
1. 그런 다음 **디렉터리 (테넌트) ID** 값은 **클립보드에 복사**를 선택 하 고 복사 하 여 저장 합니다. 나중에이 값을 지정 하 여 액세스의 발급자로 지정 합니다.
1. 응용 프로그램에 대 한 **앱 등록** 의 사이드바에서 **인증서 및 암호** > **새 클라이언트 암호**를 선택 합니다. **클라이언트 암호 추가** 페이지가 나타납니다.

   ![클라이언트 암호 추가 페이지를 표시 합니다.](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. **설명**에 `PingAccess key`을 입력 합니다.
1. **만료**에서, **1 년**, **2 년**또는 **안 함**중에서 원하는 액세스 키를 설정 하는 방법을 선택 합니다.
1. **추가**를 선택합니다. 클라이언트 암호의 테이블에는 **값** 필드에 autofills 임의의 문자열이 포함 된 액세스 키가 표시 됩니다.
1. 키의 **값** 필드 옆에 **클립보드로 복사** 아이콘을 선택 하 고 복사 하 여 저장 합니다. 나중에이 값을 지정 합니다.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>사용자 지정 필드를 보내도록 GraphAPI 업데이트 (옵션)

Access_token에서 사용 하는 다른 토큰을 전송 하는 사용자 지정 클레임이 필요한 경우 `acceptMappedClaims` 응용 프로그램 필드를 `True`으로 설정 합니다. 그래프 탐색기 또는 Azure AD 포털의 응용 프로그램 매니페스트를 사용 하 여 이러한 변경을 수행할 수 있습니다.

**이 예제에서는 그래프 탐색기를 사용 합니다.**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**이 예에서는 [Azure Active Directory 포털](https://aad.portal.azure.com/) 을 사용 하 여 `acceptMappedClaims` 필드를 업데이트 합니다.**

1. 응용 프로그램 관리자로 [Azure Active Directory 포털](https://aad.portal.azure.com/) 에 로그인 합니다.
1. **Azure Active Directory** > **앱 등록**을 선택합니다. 응용 프로그램 목록이 표시 됩니다.
1. 애플리케이션을 선택합니다.
1. 응용 프로그램에 대 한 **앱 등록** 페이지의 사이드바에서 **매니페스트**를 선택 합니다. 응용 프로그램의 등록에 대 한 매니페스트 JSON 코드가 표시 됩니다.
1. `acceptMappedClaims` 필드를 검색 하 고 값을 `True`로 변경 합니다.
1. **저장**을 선택합니다.

### <a name="use-of-optional-claims-optional"></a>선택적 클레임 사용 (선택 사항)

선택적 클레임을 사용 하 여 모든 사용자와 테넌트에 포함 된 기본 제공 클레임을 추가할 수 있습니다. 응용 프로그램 매니페스트를 수정 하 여 응용 프로그램에 대 한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [AZURE AD 응용 프로그램 매니페스트 이해 문서](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/) 를 참조 하세요.

Access_token에 대 한 전자 메일 주소를 포함 하는 예는 다음과 같습니다.
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>클레임 매핑 정책 사용 (선택 사항)

AzureAD에 없는 특성에 대 한 [클레임 매핑 정책 (미리 보기)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) 입니다. 클레임 매핑을 사용 하면 ADFS 또는 사용자 개체에 의해 지원 되는 사용자 지정 클레임을 더 추가 하 여 오래 된 온-프레미스 앱을 클라우드로 마이그레이션할 수 있습니다.

응용 프로그램에서 사용자 지정 클레임을 사용 하 고 추가 필드를 포함 하려면 [사용자 지정 클레임 매핑 정책도 만들고 응용 프로그램에 할당](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)했는지 확인 합니다.

> [!NOTE]
> 사용자 지정 클레임을 사용하려면 사용자 지정 정책도 정의하고 애플리케이션에 할당해야 합니다. 이 정책은 필요한 모든 사용자 지정 특성을 포함해야 합니다.
>
> PowerShell, Azure AD Graph Explorer 또는 Microsoft Graph를 통해 정책 정의 및 할당을 수행할 수 있습니다. PowerShell에서이 작업을 수행 하는 경우 먼저 `New-AzureADPolicy`를 사용 하 고 `Add-AzureADServicePrincipalPolicy`를 사용 하 여 응용 프로그램에 할당 해야 할 수 있습니다. 자세한 내용은 [클레임 매핑 정책 할당](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)을 참조 하세요.

예제:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>사용자 지정 클레임 사용에 대 한 액세스 권한 설정

사용자 지정 클레임을 사용 하는 데 필요한 액세스를 사용 하도록 설정 하는 것은 선택 사항 이지만 응용 프로그램에서 추가 클레임을 사용 해야 하는 경우

다음 단계에서 특성 액세스를 구성 하는 경우 만들 웹 세션 (설정-> 액세스-> 웹 세션)에서 **요청 프로필** 선택을 **취소 하 고** **사용자 특성을 새로 고쳐야** 합니다.

## <a name="download-pingaccess-and-configure-your-application"></a>응용 프로그램 액세스 및 구성 다운로드

이제 Azure Active Directory 설정 단계를 모두 완료했으므로 PingAccess 구성으로 이동할 수 있습니다.

이 시나리오의 연결에 대 한 자세한 단계는 Ping Id 설명서를 참조 하세요. Ping Id 웹 사이트에서 [Microsoft Azure AD 응용 프로그램 프록시를 사용 하 여 게시 된 응용 프로그램을 보호 하려면 AZURE AD에 대 한 연결 액세스 구성](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) 의 지침을 따르세요.

이러한 단계를 통해 액세스를 설치 하 고, 연결 액세스 계정 (아직 없는 경우)을 설정할 수 있습니다. 그런 다음 Azure AD Openid connect Connect (OIDC) 연결을 만들려면 Azure AD 포털에서 복사한 **디렉터리 (테넌트) ID** 값을 사용 하 여 토큰 공급자를 설정 합니다. 그런 다음, **응용 프로그램 (클라이언트) ID** 및 `PingAccess key` 값을 사용 하 여 웹 세션을 만듭니다. 그런 다음 ID 매핑을 설정하고 가상 호스트, 사이트 및 애플리케이션을 만들 수 있습니다.

### <a name="test-your-application"></a>애플리케이션 테스트

이러한 모든 단계를 완료 하면 응용 프로그램이 실행 중 이어야 합니다. 테스트 하려면 브라우저를 열고 Azure에서 응용 프로그램을 게시할 때 만든 외부 URL로 이동 합니다. 응용 프로그램에 할당 한 테스트 계정으로 로그인 합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure AD 응용 프로그램 프록시를 사용 하 여 게시 된 응용 프로그램을 보호 하기 위해 Azure AD에 대 한 연결 액세스](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)
