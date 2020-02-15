---
title: 사용 하지 않도록 설정 된 ACS (Azure Access Control Service) 네임 스페이스 다시 활성화
description: ACS (Azure Access Control Service) 네임 스페이스를 찾아서 사용 하도록 설정 하 고 2019 년 2 월 4 일까 지 사용할 수 있도록 확장을 요청 합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 85696a5580c4ed6c03f257787e2693a61a6158de
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164618"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>방법: 비활성화된 Access Control Service 네임스페이스 다시 활성화

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

2017년 11월에 Microsoft Azure AD(Azure Active Directory) 서비스의 하나인 Azure ACS(Access Control Service)는 2018년 11월 7일부터 사용 중지된다고 발표했습니다.

이후 사용 중지일인 2018년 11월 7일로부터 12개월, 9개월, 6개월, 3개월, 1개월, 2주, 1주 및 1일 전에 ACS 사용 중지에 관한 ACS 구독 관리자 이메일로 여러 차례 이메일을 보냈습니다.

2018년 10월 3일에 (이메일과 [블로그 게시물](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)을 통해) 2018년 11월 7일까지 마이그레이션을 완료할 수 없는 고객에게 연장을 제공한다고 발표했습니다. 또한 이 공지에는 연장 요청을 위한 지침이 포함되었습니다.

## <a name="why-your-namespace-is-disabled"></a>네임스페이스를 사용할 수 없는 이유

연장을 신청하지 않은 경우 2018년 11월 7일에 ACS 네임스페이스를 사용하지 않도록 설정하기 시작했습니다. 2019년 2월 4일로 연장을 요청했어야 합니다. 그렇지 않으면 PowerShell을 통해 네임스페이스를 활성화할 수 없습니다.

> [!NOTE]
> PowerShell 명령을 실행하고 연장을 요청하려면 구독의 서비스 관리자 또는 공동 관리자여야 합니다.

## <a name="find-and-enable-your-acs-namespaces"></a>ACS 네임스페이스 찾기 및 사용하도록 설정

ACS 네임스페이스를 사용하여 모든 ACS 네임스페이스를 나열하고 사용하지 않게 설정된 항목을 비활성화할 수 있습니다.

1. ACS PowerShell 다운로드 및 설치:
    1. PowerShell 갤러리로 이동하여 [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)를 다운로드합니다.
    1. 모듈 설치:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 가능한 모든 명령 목록 가져오기:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        특정 명령에 대한 도움말을 보려면 다음을 실행합니다.

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        여기서 `[Command-Name]`은 ACS 명령의 이름입니다.
1. **Connect-AcsAccount** cmdlet을 사용하여 ACS에 연결합니다. 

    **Set-ExecutionPolicy**를 실행하여 실행 정책을 변경해야 명령을 실행할 수 있습니다.
1. **Get-AcsSubscription** cmdlet을 사용하여 사용 가능한 Azure 구독을 나열합니다.
1. **Get-AcsNamespace** cmdlet을 사용하여 ACS 네임스페이스를 나열합니다.
1. `State`가 `Disabled`인지 확인하여 네임스페이스가 사용하도록 설정되었는지 확인합니다.

    [![네임스페이스가 사용하도록 설정되었는지 확인](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    `nslookup {your-namespace}.accesscontrol.windows.net`을 사용하여 도메인이 여전히 활성화된 상태인지 확인할 수도 있습니다.

1. **Get-AcsNamespace** cmdlet을 사용하여 ACS 네임스페이스를 사용하도록 설정합니다.

    네임스페이스를 사용하도록 설정한 후에는 네임스페이스가 2019년 2월 4일 이전에는 다시 사용 중지되지 않도록 연장을 요청할 수 있습니다. 이 날짜가 지나면 모든 ACS 요청이 실패합니다.

## <a name="request-an-extension"></a>연장 요청

2019년 1월 21일에 시작하는 새 연장 요청을 진행하고 있습니다.

2019년 2월 4일로 연장을 요청한 고객에 대한 네임스페이스 비활성화를 시작합니다. PowerShell을 통해 여전히 네임스페이스 다시 활성화할 수 있지만 네임스페이스는 48시간 후 다시 비활성화됩니다.

2019년 3월 4일 이후 고객은 더 이상 PowerShell을 통해 모든 네임스페이스를 다시 활성화할 수 없습니다.

추가 연장은 더 이상 자동으로 승인되지 않습니다. 마이그레이션할 추가 시간이 필요할 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)에 문의하여 자세한 마이그레이션 타임라인을 제공하세요.

### <a name="to-request-an-extension"></a>연장을 요청하려면

1. Azure Portal에 로그인 하 고 [새 지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만듭니다.
1. 다음 예제와 같이 새 지원 요청 양식을 입력합니다.

    | 지원 요청 필드 | 값 |
    |-----------------------|--------------------|
    | **문제 유형** | `Technical` |
    | **구독** | 구독에 대한 설정 |
    | **서비스** | `All services` |
    | **리소스** | `General question/Resource not available` |
    | **문제 유형** | `ACS to SAS Migration` |
    | **Subject** | 문제 설명 |

   ![새 기술 지원 요청의 예를 보여 줍니다.](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>도움말 및 지원

- 이 방법을 수행한 후 문제가 발생하면 [Azure 지원 팀](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.
- ACS 사용 중지에 대한 질문이나 의견은 acsfeedback@microsoft.com에 문의하세요.

## <a name="next-steps"></a>다음 단계

- [방법: Azure Access Control Service에서 마이그레이션](active-directory-acs-migration.md)에서 ACS 사용 중지에 대한 정보를 검토합니다.
