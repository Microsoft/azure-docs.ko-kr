---
title: Onfido를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 문서 ID 및 얼굴 생체 인식 확인을 위해 Azure AD B2C 인증을 Onfido와 통합 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5ff2fe74a0dd5064232fcef3178aec2967ef6812
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683864"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 Onfido 구성에 대 한 자습서

이 샘플 자습서에서는 [Onfido](https://onfido.com/)와 Azure AD B2C를 통합 하는 방법에 대 한 지침을 제공 합니다. Onfido은 문서 ID 및 얼굴 생체 인식 확인 앱입니다. 회사에서 고객 및 id 요구 사항을 실시간으로 *파악* 하는 데 사용할 수 있습니다. Onfido는 먼저 사진 ID를 확인 한 후 얼굴 생체 인식에 대해 일치 시키는 정교한 AI 기반 id 확인을 사용 합니다. 이 솔루션은 디지털 id를 실제 사용자에 게 연결 하 고 사기 행위를 줄이는 동시에 안전한 온 보 딩 환경을 제공 합니다.

이 샘플에서는 등록 또는 로그인 흐름에 Onfido의 서비스를 연결 하 여 id 확인을 수행 합니다. 사용자가 액세스할 수 있는 제품 및 서비스에 대 한 의사 결정은 Onfido의 결과에 따라 결정 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 입니다.

- Onfido [평가판 계정](https://onfido.com/signup/).

## <a name="scenario-description"></a>시나리오 설명

Onfido 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C 테 넌 트-테 넌 트에 정의 된 사용자 지정 정책을 기반으로 사용자의 자격 증명을 확인 하는 권한 부여 서버입니다. Id 공급자 라고도 합니다. Onfido client 앱을 호스팅하고 사용자 문서를 수집 하 여 Onfido API 서비스로 전송 합니다.

- Onfido client – 다른 웹 페이지 내에 배포 되는 구성 가능한 JavaScript 클라이언트 문서 컬렉션 유틸리티입니다. 문서를 수집 하 고 문서 크기 및 품질과 같은 예비 검사를 수행 합니다.

- 중간 Rest API – Onfido API 서비스와 통신 하 고, 데이터 처리를 처리 하 고, 두 작업의 보안 요구 사항을 준수 하는 Azure AD B2C 테 넌 트에 대 한 끝점을 제공 합니다.

- Onfido API service – Onfido에서 제공 하는 백 엔드 서비스로, 사용자가 제공 하는 문서를 저장 하 고 확인 합니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![onfido에 대 한 스크린샷-다이어그램](media/partner-onfido/onfido-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도착 합니다. 사용자 등록 새 계정을 만들고 페이지에 정보를 입력 합니다. Azure AD B2C 사용자 특성을 수집 합니다. Azure AD B2C에서 호스트 되는 Onfido 클라이언트 앱은 사용자 정보를 확인 합니다.
| 2. | Azure AD B2C 중간 계층 API를 호출 하 고 사용자 특성을 전달 합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집 하 고 Onfido API에서 사용할 수 있는 형식으로 변환 합니다. 그런 다음 Onfido로 보냅니다.
| 4. | Onfido는 정보를 사용 하 고이를 처리 하 여 사용자 id의 유효성을 검사 합니다. 그런 다음 중간 계층 API에 결과를 반환 합니다.
| 5. | 중간 계층 API는 정보를 처리 하 고 Azure AD B2C에 올바른 JSON 형식으로 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시 되 면 사용자에 게 오류 메시지가 표시 됩니다. 성공 응답이 표시 되 면 사용자가 인증 되 고 디렉터리에 기록 됩니다.

## <a name="onboard-with-onfido"></a>Onfido으로 등록

1. Onfido 계정을 만들려면 [Onfido](https://onfido.com/signup/)에 문의 하세요.

2. 계정이 만들어지면 [API 키](https://documentation.onfido.com/)를 만듭니다. 라이브 키는 청구 가능 하지만 솔루션을 테스트 하는 데 [sandbox 키](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) 를 사용할 수 있습니다. Sandbox 키는 라이브 키와 동일한 결과 구조를 생성 하지만 결과는 항상 미리 결정 됩니다. 문서를 처리 하거나 저장 하지 않습니다.

>[!NOTE]
> 나중에 키가 필요 합니다.

Onfido에 대 한 자세한 내용은 [ONFIDO API 설명서](https://documentation.onfido.com) 및 [Onfido Developer Hub](https://developers.onfido.com)를 참조 하세요.  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Onfido를 사용 하 여 Azure AD B2C 구성

### <a name="part-1---deploy-the-api"></a>1 부-API 배포

- 제공 된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) 를 Azure 서비스에 배포 합니다. 이러한 [지침](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.
- CORS를 설정 하 고 **허용 된 원본을** https://{your_tenant_name}. b2clogin로 추가 합니다.

>[!NOTE]
>필요한 설정을 사용 하 여 Azure AD를 구성 하려면 배포 된 서비스의 URL이 필요 합니다.

#### <a name="adding-sensitive-configuration-settings"></a>중요 한 구성 설정 추가

응용 프로그램 설정은 [Azure의 App service](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)에서 구성할 수 있습니다. App service를 사용 하면 설정을 리포지토리로 체크 인하지 않고도 안전 하 게 구성할 수 있습니다. Rest API에는 다음 설정이 필요 합니다.

| 응용 프로그램 설정 이름 | 원본 | 메모 |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Onfido 계정 |

### <a name="part-2---deploy-the-ui"></a>2 부-UI 배포

#### <a name="configure-your-storage-location"></a>저장소 위치 구성

1. [저장소 계정에 blob 저장소 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container) 설정

2. Ui 폴더의 UI 파일을 blob 컨테이너에 저장 합니다.

3. 다음 지침에 따라 만든 저장소 컨테이너에 대 한 CORS 액세스를 허용 합니다.

   a. **설정**허용 된 원본으로 이동 하  > **Allowed Origin**고를 입력 `https://{your_tenant_name}.b2clogin.com` 합니다. -테 넌 트 이름을 Azure AD B2C 테 넌 트의 이름으로 바꿉니다. 예를 들면 https://fabrikam.b2clogin.com 입니다. 모든 소문자를 사용 하 여 테 넌 트 이름을 입력 합니다.

   b. **허용 되는 메서드의**경우 `GET` 및를 선택 `PUT` 합니다.

   c. **저장**을 선택합니다.

#### <a name="update-ui-files"></a>UI 파일 업데이트

1. UI 파일에서 폴더로 이동 [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. 각 html 파일을 엽니다.

3. {Ui-blob-container-url **}을 찾고**ui **ocean_blue**, 배포 및 **자산** 폴더가 있는 url로 바꿉니다.

4. {중간 api url}을 찾아서 중간 API app service의 URL로 바꿉니다.

#### <a name="upload-your-files"></a>파일 업로드

1. Ui 폴더의 UI 파일을 blob 컨테이너에 저장 합니다.

2. [Azure Storage 탐색기](https://docs.microsoft.com/azure/virtual-machines/windows/disks-use-storage-explorer-managed-disks) 를 사용 하 여 파일 및 액세스 권한을 관리 합니다.

### <a name="part-3---configure-azure-ad-b2c"></a>3 부-Azure AD B2C 구성

#### <a name="replace-the-configuration-values"></a>구성 값 바꾸기

제공 된 사용자 지정 정책에서 다음 자리 표시자를 찾고를 인스턴스의 해당 값으로 바꿉니다.

| 자리표시자 | 대체할 값 | 예제  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | 테 넌 트 약식 이름 |  yourtenant.onmicrosoft.com의 "모든 테 넌 트" |
| {your_tenantID} | Azure AD B2C 테 넌 트의 TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 테 넌 트에 구성 된 IdentityExperienceFramework 앱의 앱 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 테 넌 트에 구성 된 ProxyIdentityExperienceFramework 앱의 앱 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 테 넌 트 저장소 응용 프로그램의 앱 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 테 넌 트 저장소 응용 프로그램의 개체 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | App insights 인스턴스의 계측 키 *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| UI **ocean_blue** **, 배포 및** **자산** 폴더가 위치한 위치의 URL | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | 설정한 app service의 URL                                             | `https://yourapp.azurewebsites.net`          |

* App insights는 다른 테 넌 트에 있을 수 있습니다. 이 단계는 선택 사항입니다. 필요 하지 않은 경우 해당 TechnicalProfiles 및 OrchestrationSteps를 제거 합니다.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>4 부-Azure AD B2C 정책 구성

Azure AD B2C 테 넌 트를 설정 하 고 정책을 구성 하는 방법에 대 한 지침은이 [문서](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 를 참조 하세요.

>[!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가 하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보를 보내도록 사용자에 게 알립니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 정책에서 **Id 경험 프레임 워크**를 선택 합니다.

2. 이전에 만든 **Signupsignin**을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   a. **응용 프로그램**: 등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. **회신 url**: **리디렉션 url** 을 선택 합니다.

   다. **사용자 흐름 실행**을 선택합니다.

4. 등록 흐름으로 이동 하 여 계정 만들기

5. 사용자 특성을 만든 후 흐름 중에 Onfido 서비스가 호출 됩니다. 흐름이 불완전 한 경우 사용자가 디렉터리에 저장 되지 않았는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
