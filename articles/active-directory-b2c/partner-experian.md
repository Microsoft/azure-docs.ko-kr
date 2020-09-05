---
title: Experian를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 사기 방지를 위해 사용자 특성을 기반으로 하 여 Id 확인 및 Experian 인증을 위해 Azure AD B2C 인증을 통합 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5c2fb330149d3e6530e7cb0fc3350d5db3fa24cf
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683881"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 Experian 구성에 대 한 자습서

이 샘플 자습서에서는 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)와 Azure AD B2C를 통합 하는 방법에 대 한 지침을 제공 합니다. Experian는 [여기](https://www.experian.com/)에서 찾을 수 있는 다양 한 솔루션을 제공 합니다.

이 샘플에서는 Experian의 통합 디지털 id 및 사기 위험 **CrossCore** 사용 됩니다. CrossCore은 사용자 id를 확인 하는 데 사용 되는 ID 확인 서비스입니다. 등록 흐름 중에 사용자가 제공 하는 여러 정보를 기반으로 하는 위험 분석을 수행 합니다. CrossCore은 사용자가 계속 로그인 할 수 있도록 허용할지 여부를 결정 하는 데 사용 됩니다. CrossCore 위험 분석에 사용할 수 있는 특성은 다음과 같습니다.

- 메일
- IP 주소
- 이름
- Middle Name
- Surname
- 주소
- City
- 시/도
- 우편 번호
- 국가/지역
- 전화 번호

## <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 입니다.

## <a name="scenario-description"></a>시나리오 설명

Experian 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C – 사용자의 자격 증명을 확인 하 고 id 공급자 라고도 하는 권한 부여 서버입니다.

- Experian – Experian 서비스는 사용자가 제공 하는 입력을 사용 하 여 사용자의 id를 확인 합니다.

- 사용자 지정 Rest API –이 API는 Azure AD B2C와 Experian 서비스 간의 통합을 구현 합니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![experian에 대 한 스크린샷-다이어그램](media/partner-experian/experian-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도착 합니다. 사용자가 등록을 선택 하 여 새 계정을 만들고 페이지에 정보를 입력 합니다. Azure AD B2C 사용자 특성을 수집 합니다.
| 2. | Azure AD B2C 중간 계층 API를 호출 하 고 사용자 특성을 전달 합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집 하 고 Experian API에서 사용할 수 있는 형식으로 변환 합니다. 그런 다음 Experian로 보냅니다.
| 4. | Experian는 정보를 사용 하 고이를 처리 하 여 위험 분석을 기반으로 사용자 id의 유효성을 검사 합니다. 그런 다음 중간 계층 API에 결과를 반환 합니다.
| 5. | 중간 계층 API는 정보를 처리 하 고 Azure AD B2C에 올바른 JSON 형식으로 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시 되 면 사용자에 게 오류 메시지가 표시 됩니다. 성공 응답이 표시 되 면 사용자가 인증 되 고 디렉터리에 기록 됩니다.

## <a name="onboard-with-experian"></a>Experian으로 등록

1. Experian 계정을 만들려면 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) 에 문의 하세요.

2. 계정이 만들어지면 API 구성에 필요한 정보를 받게 됩니다. 다음 섹션에서는이 프로세스에 대해 설명 합니다.

## <a name="configure-azure-ad-b2c-with-experian"></a>Experian를 사용 하 여 Azure AD B2C 구성

### <a name="part-1---deploy-the-api"></a>1 부-API 배포

제공 된 API 코드를 Azure 서비스에 배포 합니다. 이러한 [지침](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

>[!NOTE]
>필요한 설정을 사용 하 여 Azure AD를 구성 하려면 배포 된 서비스의 URL이 필요 합니다.

### <a name="part-2---deploy-the-client-certificate"></a>2 부-클라이언트 인증서 배포

Experian API 호출은 클라이언트 인증서로 보호 됩니다. 이 클라이언트 인증서는 Experian에서 제공 됩니다. 이 [문서](https://docs.microsoft.com/azure/app-service/environment/certificates#private-client-certificate)에 설명 된 지침에 따라 인증서를 Azure 앱 서비스에 업로드 해야 합니다. 이 샘플 정책은 프로세스의 다음 단계를 사용 합니다.

- 인증서 업로드

- `WEBSITE_LOAD_ROOT_CERTIFICATES`인증서의 지 문으로 키를 설정 합니다.

### <a name="part-3---configure-the-api"></a>3 부-API 구성

응용 프로그램 설정은 [Azure의 App service에서 구성할](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)수 있습니다. 이 방법을 사용 하면 설정을 리포지토리로 체크 인하지 않고도 안전 하 게 구성할 수 있습니다. Rest API에 대 한 다음 설정을 제공 해야 합니다.

| 애플리케이션 설정 | 원본 | 메모 |
| :-------- | :------------| :-----------|
|CrossCoreConfig: TenantId | Experian 계정 구성 |     |
|CrossCoreConfig:OrgCode | Experian 계정 구성 |     |
|CrossCore: ApiEndpoint |Experian 계정 구성|  |
|CrossCore: ClientReference | Experian 계정 구성 | |
| CrossCore: ModelCode |Experian 계정 구성|
| CrossCore:OrgCode | Experian 계정 구성 |
| CrossCore: 서명 키  | Experian 계정 구성 |
| CrossCore: TenantId  | Experian 계정 구성 |
| CrossCore: CertificateThumbprint | Experian certificate |
| BasicAuth: ApiUsername | API에 대 한 사용자 이름 정의 | ExtId 구성에서 사용 됨 |
| BasicAuth: ApiPassword | API에 대 한 암호를 정의 합니다. | ExtId 구성에서 사용 됨

### <a name="part-4---create-api-policy-keys"></a>4 부-API 정책 키 만들기

이 [문서](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) 를 참조 하 여 두 개의 정책 키를 만듭니다. 하나는 API 사용자 이름, 하나는 HTTP 기본 인증에 대해 위에서 정의한 api 암호입니다.

>[!NOTE]
>나중에 정책을 구성 하기 위한 키가 필요 합니다.

### <a name="part-5---replace-the-configuration-values"></a>5 부-구성 값 바꾸기

제공 된 사용자 지정 정책에서 다음 자리 표시자를 찾고를 인스턴스의 해당 값으로 바꿉니다.

|                      자리표시자                       |                                   대체할 값                                 |                   예제                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | 테 넌 트 약식 이름                                                           | yourtenant.onmicrosoft.com의 "모든 테 넌 트" |
| {your_trustframeworkbase_policy}                       | Trustframeworkbase.xml 정책의 Azure AD B2C 이름                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 테 넌 트에 구성 된 IdentityExperienceFramework 앱의 앱 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 테 넌 트에 구성 된 ProxyIdentityExperienceFramework 앱의 앱 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 테 넌 트 저장소 응용 프로그램의 앱 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 테 넌 트 저장소 응용 프로그램의 개체 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | [여기](#part-4---create-api-policy-keys) 에서 만든 사용자 이름 키의 이름입니다.             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | [여기](#part-4---create-api-policy-keys) 에서 만든 암호 키의 이름             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | 설정한 app service의 URL                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>6 부-Azure AD B2C 정책 구성

Azure AD B2C 테 넌 트를 설정 하 고 정책을 구성 하는 방법에 대 한 지침은이 [문서](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 를 참조 하세요.

>[!NOTE]
>이 샘플 정책은 [로컬 계정 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 기반으로 합니다.

>[!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가 하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보를 보내도록 사용자에 게 알립니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 정책 아래에서 **사용자 흐름**을 선택 합니다.

2. 이전에 만든 **사용자 흐름**을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   a. **응용 프로그램**: 등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. **회신 url**: **리디렉션 url** 을 선택 합니다.

   다. **사용자 흐름 실행**을 선택합니다.

4. 등록 흐름으로 이동 하 여 계정 만들기

5. 로그 아웃

6. 로그인 흐름을 통해 이동  

7. **Continue**를 입력 하면 CrossCore 퍼즐이 팝업 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
