---
title: Azure EA Portal 액세스 문제 해결
description: 이 문서에서는 Azure EA Portal에서 Azure 기업계약(EA)에 발생할 수 있는 몇 가지 일반적인 문제에 대해 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: d3199eb67e5672cd6f4a6d99219f184e7fbbdf6f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687402"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Azure EA Portal 액세스 문제 해결

이 문서에서는 Azure 기업계약(EA)에 발생할 수 있는 몇 가지 일반적인 문제에 대해 설명합니다. Azure EA Portal은 기업계약 사용자 및 비용을 관리하는 데 사용됩니다. Azure EA Portal 액세스를 구성하거나 업데이트하는 경우 이러한 문제가 발생할 수 있습니다.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>등록에 관리자 추가와 관련된 문제

기업 등록에 대한 다양한 유형의 인증 수준이 있습니다. 인증 수준이 잘못 적용되면 Azure EA Portal에 로그인하려고 할 때 문제가 발생할 수 있습니다.

Azure EA Portal을 사용하여 인증 수준이 서로 다른 사용자에게 액세스 권한을 부여할 수 있습니다. 엔터프라이즈 관리자는 조직의 보안 요구 사항에 맞게 인증 수준을 업데이트할 수 있습니다.

### <a name="authentication-level-types"></a>인증 수준 유형

- Microsoft 계정만 - Microsoft 계정을 통해 사용하고, 사용자를 만들고 관리하려는 조직에 적합합니다.
- 회사 또는 학교 계정 - 클라우드에 대한 페더레이션을 사용하여 Active Directory를 설정하고 모든 계정이 단일 테넌트에 있는 조직에 적합합니다.
- 회사 또는 학교 계정 교차 테넌트 - 클라우드에 대한 페더레이션을 사용하여 Active Directory를 설정하고 여러 테넌트에 있는 여러 계정을 갖는 조직에 적합합니다.
- 혼합 계정 - Microsoft 계정과 회사 또는 학교 계정을 사용하여 사용자를 추가할 수 있습니다.

등록에 추가된 첫 번째 회사 또는 학교 계정에 따라 _기본_ 또는 _마스터_ 도메인이 결정됩니다. 다른 테넌트를 사용하여 회사 또는 학교 계정을 추가하려면 등록에서 인증 수준을 교차 테넌트로 변경해야 합니다.

인증 수준을 업데이트하려면

1. Azure EA Portal에 엔터프라이즈 관리자로 로그인합니다.
2. 왼쪽 탐색 패널에서 **관리**를 클릭합니다.
3. **등록** 탭을 클릭합니다.
4. **등록 세부 정보**에서 **인증 수준**을 선택합니다.
5. 연필 기호를 클릭합니다.
6. **저장**을 클릭합니다.

![인증 수준을 보여 주는 예 ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft 계정에는 [https://signup.live.com](https://signup.live.com/)에서 만든 연결된 ID가 있어야 합니다.

회사 또는 학교 계정은 페더레이션을 사용하여 Active Directory를 설정하고 모든 계정이 단일 테넌트에 있는 조직에서 사용할 수 있습니다. 회사의 내부 Active Directory가 페더레이션된 경우 회사 또는 학교 페더레이션 사용자 인증을 사용하여 사용자를 추가할 수 있습니다.

조직에서 Active Directory 페더레이션을 사용하지 않는 경우에는 회사 또는 학교 이메일 주소를 사용할 수 없습니다. 대신, 새 이메일 주소를 등록하거나 만들고 Microsoft 계정으로 등록하세요.

## <a name="unable-to-access-the-azure-ea-portal"></a>Azure EA Portal에 액세스할 수 없음

Azure EA Portal에 로그인하려고 시도할 때 오류 메시지가 표시되면 다음 문제 해결 단계를 사용합니다.

- 올바른 Azure EA Portal URL(https://ea.azure.com )을 사용하고 있는지 확인합니다.
- Azure EA Portal에 대한 액세스 권한이 회사 또는 학교 계정 또는 Microsoft 계정으로 추가되었는지 확인합니다.
  - 회사 계정을 사용하는 경우 업무용 이메일 및 암호를 입력합니다. 업무용 암호는 조직에서 제공됩니다. 문제가 있는 경우 IT 부서에서 암호를 다시 설정하는 방법에 대한 정보를 확인할 수 있습니다.
  - Microsoft 계정을 사용하는 경우 Microsoft 계정 이메일 주소와 암호를 입력합니다. Microsoft 계정 암호를 잊은 경우에는 [https://account.live.com/password/reset](https://account.live.com/password/reset)에서 다시 설정할 수 있습니다.
- 이전 또는 기존 세션에서 쿠키 또는 캐시된 정보가 유지되지 않도록 프라이빗 또는 incognito 브라우저 세션을 사용하여 로그인합니다. 브라우저의 캐시를 지우고 프라이빗 또는 incognito 창을 사용하여 https://ea.azure.com 을 엽니다.
- Microsoft 계정을 사용할 때 _잘못된 사용자_ 오류가 발생하는 경우 Microsoft 계정이 여러 개 있기 때문일 수 있습니다. 로그인하기 위해 사용하려고 하는 계정이 기본 이메일 주소가 아닙니다.
또는 사용자를 등록에 추가할 때 잘못된 계정 유형을 사용했기 때문에 _잘못된 사용자_ 오류가 발생할 수 있습니다. Microsoft 계정 대신 회사 또는 학교 계정을 사용하는 경우를 예로 들 수 있습니다. 이 예에서는 다른 EA 관리자가 올바른 계정을 추가하거나 [지원](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c) 담당자에게 문의해야 합니다.
  - 기본 별칭을 확인해야 하는 경우 [https://account.live.com](https://account.live.com)으로 이동합니다. 그런 다음, **사용자 정보**, **Microsoft에 로그인하는 방법 관리**를 차례로 클릭합니다. 프롬프트에 따라 암호 확인용 메일을 확인하고 중요한 정보에 액세스하기 위한 코드를 얻습니다. 보안 코드를 입력합니다. 2단계 인증을 설정하지 않으려면 **나중에 설정**을 선택합니다.
  - 계정 별칭을 볼 수 있는 **Microsoft에 로그인하는 방법 관리** 페이지가 표시됩니다. Azure EA Portal에 로그인하는 데 기본 별칭을 사용하고 있는지 확인합니다. 그렇지 않으면 기본 별칭으로 만들 수 있습니다. 또는 Azure EA Portal에 대한 기본 별칭을 대신 사용할 수 있습니다.

## <a name="azure-ea-activation-faq"></a>Azure EA 활성화 FAQ

이 문서 섹션에서는 Azure EA 활성화와 관련된 일반적인 문제에 대한 해결 방법을 간략하게 설명합니다.

### <a name="i-didnt-receive-an-activation-email"></a>활성화 이메일을 받지 못했습니다.

Azure EA Portal의 활성화 이메일은 *waep@microsoft.com* 에서 전송됩니다. 활성화 이메일을 받지 못한 경우 이메일에 대해 스팸 또는 정크 메일 폴더를 확인하세요. _Invitation to View/Manage the Microsoft Azure service_subject_ 로 전송됩니다. 새로 추가된 모든 EA 관리자에게 전송됩니다.

이미 EA 관리자로 설정된 경우에는 Azure EA Portal에 로그인하기 위해 활성화 이메일을 받을 때까지 기다릴 필요가 없습니다. [https://ea.azure.com](https://ea.azure.com )으로 이동하여 이메일 주소(회사, 학교 또는 Microsoft 계정)와 암호를 사용하여 로그인할 수 있습니다.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>새 EA 관리자를 내 등록에 추가하려고 합니다.

기존 엔터프라이즈 관리자는 새 엔터프라이즈 관리자를 추가할 수 있습니다. EA 관리자인 경우 EA Portal에 로그인하고, **관리**를 클릭하고, 오른쪽 위 모서리에서 **+ 관리자 추가**를 클릭하여 새 EA 관리자를 추가하세요. 사용자의 이메일 주소와 선호하는 로그인 방법(예: 회사/학교 인증 또는 Microsoft Live ID 사용)을 사용하여 사용자를 추가할 수 있는지 확인합니다.

EA 관리자가 아닌 경우 회사의 EA 관리자에게 연락하여 등록에 추가되도록 요청하세요. 사용자가 등록에 추가되면 해당 사용자는 활성화 이메일을 받게 됩니다.

그러나 EA 관리자가 도움을 줄 수 없는 경우 사용자가 다음을 제공할 수 있으면 해당 사용자를 대신하여 추가할 수 있습니다.
- 등록 번호
- 추가할 이메일 주소 및 인증 유형(회사/학교/MS)
- EA 관리자의 이메일 승인

필요한 정보가 모두 있으면 요청을 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)에 제출하세요.

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>등록에서 첫 번째 EA 관리자를 업데이트하려고 합니다.

첫 번째 EA 관리자는 포털에서 알림 연락처 및 온라인 관리자를 업데이트하여 볼륨 라이선싱 서비스 센터에서 업데이트할 수 있습니다. EA Portal에서 업데이트하는 데 약 24시간이 걸립니다. 업데이트되면 새 EA 관리자가 활성화 이메일을 받게 됩니다.

VLSC 포털 액세스 권한이 없거나 초기 EA 관리자가 더 이상 등록을 관리할 수 없고 EA Portal에 액세스할 수 없는 경우 요청을 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)에 제출하여 업데이트를 요청하고 다음 정보를 제공하세요.
- 등록 번호
- 추가할 이메일 주소 및 인증 유형(회사/학교/MS)
- 초기 EA 관리자를 변경하는 이유
- 초기 EA 관리자의 이메일 승인

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>현재 EA 관리자가 더 이상 회사에 있지 않습니다.

EA 등록에는 여러 EA 관리자가 있을 수 있으며, 다른 EA 관리자에게 연락하여 새 EA 관리자/계정 소유자/부서 관리자를 추가할 수 있습니다. 그러나 회사의 EA 관리자가 누구인지 확실하지 않거나 사용할 수 있는 다른 EA 관리자가 등록에 없는 경우 다음 정보를 사용하여 Microsoft에 문의하세요.
- 등록 번호
- 추가할 이메일 주소 및 인증 유형(회사/학교/MS)
- 현재 EA 관리자가 더 이상 회사에 있다는 정보 제공

다른 EA 관리자가 등록에 있는 경우 EA 관리자에게 연락하여 등록에 대한 관리 변경을 승인하도록 요청하세요.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>내 등록이 보류 중 상태로 표시됩니다. 이 등록을 활성화하려면 어떻게 해야 하나요?

초기 EA 관리자가 이전에 등록에 로그온하지 않은 경우 등록은 보류 중 상태가 됩니다. EA 관리자인 경우 Azure EA Portal에 로그인하세요. 모든 등록 번호가 포함된 방문 페이지에서 보류 중인 등록이 표시되지 않을 수 있습니다. EA Portal의 오른쪽 위 모서리에 있는 "활성" 상자를 선택 취소하세요. 그러면 보류 중인 등록이 표시됩니다. 정보에 액세스하려면 등록을 클릭하세요. 등록의 [관리] 페이지에 도달하면 상태가 [보류 중]에서 [활성]으로 업데이트됩니다.

### <a name="why-is-my-account-stuck-in-pending-status"></a>내 계정이 보류 중 상태로 유지되는 이유는 무엇인가요?

새 AO(계정 소유자)가 처음으로 등록에 추가되면 상태가 항상 "보류 중"으로 표시됩니다. 활성화 환영 이메일을 받으면 AO가 로그인하여 계정을 활성화할 수 있습니다. 로그인하면 계정 상태가 "보류 중"에서 "활성"으로 업데이트됩니다.

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Azure EA Portal에 로그인할 때 오류 메시지를 받았습니다.

로그인하는 동안 Azure EA Portal에서 오류 메시지가 발생할 수 있는 몇 가지 이유가 있습니다. 다음 문제 해결 단계를 수행하세요.

 1. [https://ea.azure.com](https://ea.azure.com)에서 올바른 EA Portal URL을 사용하고 있는지 확인합니다.
 1. Azure EA Portal에 대한 액세스 권한이 회사 계정, 학교 계정 또는 Microsoft Live ID로 추가되었는지 확인합니다. 회사 계정을 사용하는 경우 회사 이메일과 회사 암호를 입력하세요. Microsoft Live ID를 사용하는 경우 Live ID 이메일과 Microsoft Live ID 암호를 입력하세요. Microsoft Live ID 암호를 잊어버린 경우 [https://account.live.com/password/reset](https://account.live.com/password/reset)에서 다시 설정하세요.
 1. 이전/기존 세션의 쿠키 또는 캐시가 유지되지 않도록 프라이빗 브라우저를 사용하여 로그인하는 것이 좋습니다. 캐시를 지우고 프라이빗 검색 모드/시크릿 창을 사용하여 [https://ea.azure.com](https://ea.azure.com)을 엽니다.
 1. Microsoft 계정을 사용할 때 [잘못된 사용자] 오류가 발생하는 경우 여러 Microsoft 계정이 있고 로그인하려는 계정이 기본 별칭이 아니기 때문일 수 있습니다. 기본 별칭을 확인하려면 account.live.com으로 이동합니다.
    - "사용자 정보" > "로그인 이메일 또는 전화 번호 관리"로 이동합니다.
    - 화면의 프롬프트 지시에 따라 대체 이메일 주소를 확인하고 중요한 정보에 액세스하는 코드를 얻습니다.
    - 보안 코드를 입력합니다.
    - 나중에 2단계 인증을 설정하려면 "나중에 설정"을 선택합니다.
    - 그러면 보유한 계정 별칭이 표시되는 "계정 별칭 관리" 페이지로 이동합니다. 기본 별칭이 Azure EA Portal에 로그인하는 데 사용하는 별칭인지 다시 확인합니다. 그렇지 않으면 기본 별칭으로 설정하거나 EA Portal에 대한 기본 별칭을 대신 사용할 수 있습니다.

위의 문제 해결 단계가 실패한 경우 다음과 같은 정보가 포함된 요청을 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)에 제출하세요.
- 사용된 브라우저 및 버전
- 오류 메시지의 스크린샷
- 오류를 표시하는 페이지의 URL  
- 오류가 발생한 날짜, 시간 및 표준 시간대
- 로그 파일을 가져오는 경우에도 도움이 됩니다. 아래 정보를 사용하여 네트워크 추적을 캡처하는 단계는 다음과 같습니다.
  1. Internet Explorer를 엽니다.
  1. F12 키를 누릅니다. 그러면 IE의 아래쪽에 상자가 열립니다.
  1. **네트워크** 탭을 선택합니다.
  1. **캡처 시작**을 클릭합니다.
  1. 오류가 발생하는 작업을 수행합니다.
  1. 오류가 발생하면 **캡처 중지**를 클릭합니다.
  1. 파일을 저장하고 지원 요청에 정보를 포함시킵니다.
  1. 등록 번호와 이메일 주소를 지원 요청에 제공해야 합니다.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>회사/학교 계정과 Microsoft 계정의 차이점은 무엇인가요?

**Microsoft 계정:** [https://signup.live.com](https://signup.live.com)의 Live ID에 연결된 계정입니다.

**회사/학교 계정:** 클라우드에 페더레이션된 활성 디렉터리를 설정하고 모든 계정이 단일 테넌트에 있는 회사에서만 사용할 수 있습니다. 회사의 내부 활성 디렉터리가 클라우드에 페더레이션되면 사용자를 회사/학교 권한 부여 형식으로 추가할 수 있습니다.

  2016년 9월부터 Microsoft는 더 이상 주소가 지정된 회사 또는 학교 이메일을 Microsoft 계정으로 등록할 수 없습니다. 자세한 내용은 [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/) 자료를 참조하세요.

  조직이 클라우드에 페더레이션되지 않으면 회사 또는 학교 이메일 주소를 사용할 수 없습니다. 새 이메일 주소를 등록하거나 만들고, 이를 Microsoft 계정으로 대신 등록하세요.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Azure EA Portal에 대한 암호를 잊어버렸습니다.

Microsoft Live ID 암호를 잊어버린 경우 [https://account.live.com/password/reset](https://account.live.com/password/reset)에서 다시 설정하세요.

회사 암호를 잊어버린 경우 회사의 IT 관리자에게 문의하세요.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>유효한 회사 또는 학교 계정이 있지만 이를 EA Portal에 추가할 수 없습니다.

다른 테넌트의 회사 또는 학교 계정이 있는 경우 등록 세부 정보 페이지의 권한 부여 수준을 "회사 또는 학교 계정 교차 테넌트"로 변경하면 계정을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure EA Portal 관리자는 [Azure EA Portal 관리](ea-portal-administration.md)를 읽고 일반적인 관리 작업에 대해 알아보아야 합니다.
