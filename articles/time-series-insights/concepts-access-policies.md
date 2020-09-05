---
title: 데이터 액세스를 허용 하도록 보안 구성-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 환경에서 보안, 권한 및 데이터 액세스 정책 관리를 구성 하는 방법에 대해 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 35a2be5c9f418bc423ae56c456db0062677a33c2
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856122"
---
# <a name="grant-data-access-to-an-environment"></a>환경에 대한 데이터 액세스 권한 부여

이 문서에서는 두 가지 유형의 Azure Time Series Insights 액세스 정책에 대해 설명 합니다.

## <a name="sign-in-to-azure-time-series-insights"></a>Azure Time Series Insights에 로그인

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. `Time Series Insights environments` **검색** 상자에를 입력 하 여 Azure Time Series Insights 환경을 찾습니다. `Time Series Insights environments`검색 결과에서을 선택 합니다.
1. 목록에서 Azure Time Series Insights 환경을 선택 합니다.

## <a name="grant-data-access"></a>데이터 액세스 권한 부여

다음 단계에 따라 사용자 계정에 대해 데이터 액세스 권한을 부여합니다.

1. **데이터 액세스 정책**을 선택한 다음, **+ 추가**를 선택합니다.

    [![데이터 액세스 정책 선택 및 추가](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. **사용자 선택**을 선택합니다. 사용자 이름 또는 메일 주소를 검색하여 추가할 사용자를 찾습니다. 선택 **을 선택 하 여** 선택 내용을 확인 합니다.

    [![추가할 사용자 선택](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. **역할 선택**을 선택합니다. 사용자에 대한 적절한 액세스 역할을 선택합니다.

    * 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있도록 하려면 **참가자**를 선택합니다.

    * 또는 사용자가 환경의 데이터를 쿼리하고 공유되지 않는 개인 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

   **확인**을 선택하여 역할 선택을 확인합니다.

    [![선택한 역할을 확인 합니다.](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

    [![사용자 역할 선택 페이지에서 확인을 선택 합니다.](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. **데이터 액세스 정책** 페이지에 사용자 및 각 사용자의 역할이 표시되는지 확인합니다.

    [![올바른 사용자 및 역할 확인](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>다른 Azure AD 테 넌 트에서 게스트 액세스 제공

`Guest`역할은 관리 역할이 아닙니다. 한 테 넌 트에서 다른 테 넌 트에서 초대 된 계정에 사용 되는 용어입니다. 게스트 계정이 테 넌 트의 디렉터리에 초대 된 후 다른 계정 처럼 동일한 액세스 제어를 적용할 수 있습니다. Access Control (IAM) 블레이드를 사용 하 여 Azure Time Series Insights 환경에 대 한 관리 액세스 권한을 부여할 수 있습니다. 또는 데이터 액세스 정책 블레이드를 통해 환경의 데이터에 대한 액세스 권한을 부여할 수 있습니다. Azure AD(Azure Active Directory) 테넌트 게스트 액세스에 대한 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)를 참조하세요.

다른 테 넌 트에서 Azure AD 사용자에 게 Azure Time Series Insights 환경에 대 한 게스트 액세스 권한을 부여 하려면 다음 단계를 수행 합니다.

1. Azure Portal로 이동 하 여  **Azure Active Directory**를 클릭 하 고 **개요** 탭에서 아래로 스크롤한 다음 **게스트 사용자**를 선택 합니다.

    [![데이터 액세스 정책을 선택 하 고 + 초대를 선택 합니다.](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. 초대할 사용자의 메일 주소를 입력합니다. 이 메일 주소는 Azure AD와 연결되어야 합니다. 필요한 경우 초대에 개인 메시지를 포함할 수 있습니다.

    [![선택한 사용자를 찾을 전자 메일 주소를 입력 하세요.](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. 화면에 표시되는 확인 거품을 찾습니다. **알림을** 클릭 하 여 게스트 사용자가 추가 되었는지 확인할 수도 있습니다.

    [![표시 되는 확인 거품을 찾습니다.](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Time Series Insights 환경으로 돌아가서 새로 만든 게스트 사용자를 추가 합니다. **데이터 액세스 권한 부여**에 설명 된 대로 **데이터 액세스 정책** 을 클릭 합니다. **사용자를 선택**합니다. 초대한 게스트 사용자의 메일 주소를 검색하여 추가할 사용자를 찾습니다. 그런 다음 **선택** 항목을 확인 하려면 선택 합니다.

    [![사용자를 선택 하 고 선택 확인](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. **역할 선택**을 선택합니다. 게스트 사용자의 적절한 액세스 역할을 선택합니다.

    * 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있도록 하려면 **참가자**를 선택합니다.

    * 또는 사용자가 환경의 데이터를 쿼리하고 공유되지 않는 개인 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

   **확인**을 선택하여 역할 선택을 확인합니다.

    [![역할 선택 확인](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

1. **데이터 액세스 정책** 페이지에 게스트 사용자 및 각 게스트 사용자의 역할이 표시되는지 확인합니다.

    [![사용자 및 역할이 올바르게 할당 되었는지 확인](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. 이제 게스트 사용자가 위에서 지정 된 전자 메일 주소로 초대 전자 메일을 받게 됩니다. 게스트 사용자가 **시작** 을 선택 하 여 동의를 확인 하 고 Azure 클라우드에 연결 합니다.

    [![허용 시작을 선택 하는 게스트](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. **시작**을 선택한 후에는 게스트 사용자에 게 관리자 조직과 연결 된 사용 권한 상자가 표시 됩니다. **수락**을 선택 하 여 권한을 부여 하면 로그인 됩니다.

    [![게스트 검토 권한 및 수락](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. 관리자는 해당 게스트와 [환경 URL을 공유](time-series-insights-parameterized-urls.md) 합니다.

1. 게스트 사용자가 초대 하는 데 사용한 전자 메일 주소에 로그인 한 후 초대를 수락 하면 Azure Portal 전송 됩니다.

1. 이제 게스트가 관리자가 제공한 환경 URL을 사용 하 여 공유 환경에 액세스할 수 있습니다. 이러한 URL을 웹 브라우저에 입력 하 여 즉시 액세스할 수 있습니다.

1. 시계열 탐색기의 오른쪽 위 모서리에서 프로필 아이콘을 선택한 후 관리자의 테 넌 트가 게스트 사용자에 게 표시 됩니다.

    [![Insights.azure.com에서 아바타 선택](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    게스트 사용자가 관리자의 테 넌 트를 선택 하면 공유 Azure Time Series Insights 환경을 선택할 수 있습니다.

    이제 **5 단계**에서 제공 하는 역할과 관련 된 모든 기능을 제공 합니다.

    [![게스트 사용자가 드롭다운에서 Azure 테 넌 트를 선택 합니다.](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>다음 단계

* Azure Active Directory 앱 등록 단계에 대 한 [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md) 를 읽습니다.

* [Azure Time Series Insights TSI 탐색기에서 환경을](./time-series-insights-update-explorer.md)확인 합니다.
