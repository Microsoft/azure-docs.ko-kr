---
title: HYPR를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 진정한 암호 없는 강력한 고객 인증을 위해 Hypr를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 4f6b09061a4aa98824e176af55efcedfab3df48c
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051919"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 HYPR 구성에 대 한 자습서

이 샘플 자습서에서는 [HYPR](https://get.hypr.com)를 사용 하 여 Azure AD B2C를 구성 하는 방법에 대 한 지침을 제공 합니다. Azure AD B2C을 id 공급자로 사용 하 여 HYPR를 고객 응용 프로그램과 통합 하 여 사용자에 게 진정한 암호 없는 인증을 제공할 수 있습니다. HYPR는 암호를 공개 키 암호화로 바꿔서 사기 행위, 피싱 및 자격 증명 재사용을 제거 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)입니다. 테 넌 트는 Azure 구독에 연결 됩니다.

- HYPR cloud 테 넌 트, 무료 [평가판 계정](https://get.hypr.com/free-trial)받기.

- HYPR Rest Api 또는 HYPR Device Manager를 사용 하 여 등록 한 사용자의 모바일 장치가 HYPR 테 넌 트에 있습니다. 예를 들어 [HYPR JAVA SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) 를 사용 하 여이 작업을 수행할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

HYRP 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C – 사용자의 자격 증명을 확인 하 고 id 공급자 라고도 하는 권한 부여 서버입니다.

- 웹 및 모바일 응용 프로그램-HYPR 및 Azure AD B2C를 사용 하 여 보호 하도록 선택 하는 모바일 또는 웹 응용 프로그램입니다. HYPR는 iOS 및 Android 플랫폼에서 진정한 암호 없는 인증을 수행 하는 데 사용할 수 있는 강력한 모바일 SDK도 제공 합니다.

- HYPR 모바일 앱-사용자의 모바일 응용 프로그램에서 모바일 Sdk를 사용 하지 않으려는 경우 HYPR mobile 앱을 사용 하 여이 샘플을 실행할 수 있습니다.

- HYPR Rest Api-HYPR Api를 사용 하 여 사용자 장치 등록과 인증을 모두 수행할 수 있습니다. 이러한 Api는 [여기](https://apidocs.hypr.com)에서 찾을 수 있습니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![Hypr에 대 한 스크린샷-다이어그램](media/partner-hypr/hypr-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도착 합니다. 사용자가 로그인/등록을 선택 하 고 페이지에 사용자 이름을 입력 합니다.
| 2. | 응용 프로그램은 식별 확인을 위해 Azure AD B2C 사용자 특성을 보냅니다.
| 3. | Azure AD B2C 사용자 특성을 수집 하 고 HYPR mobile 앱을 통해 사용자를 인증 하기 위해 특성을 HYPR에 보냅니다.
| 4. | HYPR는 FIDO (Fast Identity Online) 인증 인증을 위해 등록 된 사용자 모바일 장치에 푸시 알림을 보냅니다. 사용자 핑거 인쇄, 생체 인식 또는 분산 된 pin이 될 수 있습니다.  
| 5. | 사용자가 푸시 알림을 승인한 후 확인 결과에 따라 사용자에 게 고객 응용 프로그램에 대 한 액세스 권한이 부여 되거나 거부 됩니다.

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C 정책 구성

1. 정책 폴더에서 [AZURE AD B2C HYPR 정책](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) 으로 이동 합니다.

2. 이 [문서](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 에 따라 [localaccounts 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) 을 다운로드 합니다.

3. Azure AD B2C 테 넌 트에 대 한 정책을 구성 합니다.

>[!NOTE]
>제공 된 정책을 업데이트 하 여 특정 테 넌 트와 관련 된 정책을 업데이트 합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 정책에서 **Id 경험 프레임 워크**를 선택 합니다.

2. 이전에 만든 **Signupsignin**을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   a. **응용 프로그램**: 등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. **회신 url**: **리디렉션 url** 을 선택 합니다.

   다. **사용자 흐름 실행**을 선택합니다.

4. 등록 흐름으로 이동 하 여 계정 만들기

5. 사용자 특성을 만든 후 흐름 중에 HYPR가 호출 됩니다. 흐름이 불완전 한 경우 사용자가 디렉터리에 저장 되지 않았는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
