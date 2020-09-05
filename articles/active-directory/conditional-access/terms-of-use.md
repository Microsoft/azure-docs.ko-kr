---
title: 사용 약관-Azure Active Directory | Microsoft Docs
description: 액세스 권한을 얻기 전에 직원 또는 게스트에 게 정보를 제공 하기 위해 Azure Active Directory 사용 약관을 사용 하 여 시작 하세요.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cead917c6c98d10d3f9e5ebe2f75bd5513574c5
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948919"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 사용 약관

Azure AD 사용 약관은 조직에서 최종 사용자에 게 정보를 제공 하는 데 사용할 수 있는 간단한 방법을 제공 합니다. 이 프레젠테이션은 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있게 해줍니다. 이 문서에서는 사용 약관을 시작 하는 방법을 설명 합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>개요 비디오

다음 비디오에서는 사용 약관에 대 한 간략 한 개요를 제공 합니다.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

추가 비디오를 보려면 다음을 참조하세요.
- [Azure Active Directory에서 사용 약관을 배포 하는 방법](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Azure Active Directory에서 사용 약관을 배포 하는 방법](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>사용 약관으로 무엇을 할 수 있나요?

Azure AD 사용 약관에는 다음과 같은 기능이 있습니다.

- 액세스 권한을 얻기 전에 직원 또는 게스트가 사용 약관에 동의 하도록 요구 합니다.
- 액세스 권한을 얻기 전에 모든 장치에서 사용 약관에 동의 하려면 직원 또는 게스트가 필요 합니다.
- 직원 또는 게스트가 되풀이 일정에 대 한 사용 약관에 동의 하도록 요구 합니다.
- MFA (Azure Multi-Factor Authentication)에 보안 정보를 등록 하기 전에 직원 또는 게스트가 사용 약관에 동의 해야 합니다.
- Azure AD 셀프 서비스 암호 재설정 (SSPR)에 보안 정보를 등록 하기 전에 직원이 사용 약관에 동의 해야 합니다.
- 조직의 모든 사용자에 게 일반적인 사용 약관을 제공 합니다.
- 사용자 특성을 기반으로 특정 사용 약관을 표시 합니다 (예: [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)을 사용하여 의사와 간호사, 국내 직원과 해외 직원에게 맞는 사용 약관을 각각 제공).
- Salesforce와 같은 높은 비즈니스 영향 응용 프로그램에 액세스할 때 특정 사용 약관을 제공 합니다.
- 다른 언어로 사용 약관을 제공 합니다.
- 사용 약관에 동의 하지 않은 사용자를 나열 합니다.
- 개인 정보 보호 규정을 준수하도록 지원
- 준수 및 감사에 대 한 사용 약관 작업 로그를 표시 합니다.
- [Microsoft Graph api](/graph/api/resources/agreement?view=graph-rest-beta) (현재 미리 보기 상태)를 사용 하 여 사용 약관을 만들고 관리 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD 사용 약관을 사용 하 고 구성 하려면 다음이 있어야 합니다.

- Azure AD Premium P1, P2, EMS E3 또는 EMS E5 구독.
   - 이러한 구독 중 하나가 없는 경우 [Azure AD Premium을 받거나](../fundamentals/active-directory-get-started-premium.md)[Azure AD Premium 평가판을 사용](https://azure.microsoft.com/trial/get-started-active-directory/)할 수 있습니다.
- 구성하려는 디렉터리에 대한 다음 관리자 계정 중 하나:
   - 전역 관리자
   - 보안 관리자
   - 조건부 액세스 관리자

## <a name="terms-of-use-document"></a>사용 약관 문서

Azure AD 사용 약관에서는 PDF 형식을 사용 하 여 콘텐츠를 제공 합니다. PDF 파일은 기존 계약서와 같이 사용자가 로그인하는 동안 최종 사용자 계약을 수집할 수 있는 모든 콘텐츠가 될 수 있습니다. 모바일 디바이스에서 사용자를 지원하기 위한 PDF의 권장 글꼴 크기는 24pt입니다.

## <a name="add-terms-of-use"></a>사용 약관 추가

사용 약관 문서를 완료 했으면 다음 절차를 사용 하 여 추가 합니다.

1. Azure에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. [https://aka.ms/catou](https://aka.ms/catou)에서 **사용 약관**으로 이동합니다.

   ![조건부 액세스-사용 약관 블레이드](./media/terms-of-use/tou-blade.png)

1. **새 사용 약관**을 클릭합니다.

   ![사용 약관 설정을 지정 하는 새로운 사용 약관 창](./media/terms-of-use/new-tou.png)

1. **이름** 상자에 Azure Portal에 사용 되는 사용 약관의 이름을 입력 합니다.
1. **표시 이름** 상자에 사용자가 로그인할 때 표시되는 제목을 입력합니다.
1. **사용 약관 문서**에 대해 완성 된 사용 약관 PDF로 이동 하 여 선택 합니다.
1. 사용 약관 문서에 대 한 언어를 선택 합니다. 언어 옵션을 사용하면 사용 약관을 다양한 언어로 여러 개 업로드할 수 있습니다. 최종 사용자에게 표시되는 사용 약관의 버전은 브라우저 기본 설정에 기반합니다.
1. 최종 사용자가 사용 약관에 동의 하기 전에 사용 약관을 볼 수 있도록 하려면 **사용자에 게 사용 약관을 확장 하도록 요구** 를 설정 **합니다.**
1. 최종 사용자가 액세스 하는 모든 장치에서 사용 약관에 동의 하도록 요구 하려면 **모든 장치에서 사용자에 게 동의 해야** 함을 설정 **합니다.** 이 옵션을 사용 하도록 설정 하면 사용자가 추가 응용 프로그램을 설치 해야 할 수 있습니다. 자세한 내용은 [장치 단위 사용 조건](#per-device-terms-of-use)을 참조 하세요.
1. 일정에 따라 사용 약관 동의을 만료 하려면 **만료 동의** 를 **켜기**로 설정 합니다. 켜기로 설정되면 두 개의 추가 일정 설정이 표시됩니다.

   ![시작 날짜, 빈도 및 기간을 설정 하는 동의 설정 만료](./media/terms-of-use/expire-consents.png)

1. **시작** 날짜 및 **빈도** 설정을 사용 하 여 사용 약관에 대 한 일정을 지정 합니다. 다음 표는 몇 가지 예제 설정의 결과를 보여 줍니다.

   | 만료 시작 날짜 | 빈도 | 결과 |
   | --- | --- | --- |
   | 오늘 날짜  | 매월 | 오늘부터 사용자가 사용 약관에 동의한 다음 매월 다시 수락 해야 합니다. |
   | 미래 날짜  | 매월 | 오늘부터 사용자가 사용 약관에 동의 해야 합니다. 미래 날짜가 발생하면 동의가 만료되고 이후 사용자는 매월 다시 동의해야 합니다.  |

   예를 들어 만료 시작 날짜를 **1월 1일**로 설정하고 빈도를 **매월**로 설정하는 경우 사용자 두 명에 대한 만료는 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 2월 1일 | 3월 1일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 1일 | 3월 1일 | 4월 1일 |

1. 사용자가 사용 약관에 다시 동의 해야 하는 기간 (일)을 지정 하려면 **다시 승인 해야 하는 기간 (일)** 을 지정 하는 기간을 사용 합니다. 이를 통해 사용자는 자신의 일정을 따를 수 있습니다. 예를 들어 기간을 **30**일로 설정하면 두 사용자에 대한 만료가 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 1월 31일 | 3월 2일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 14일 | 3월 16일 | 4월 15일 |

   **동의 만료** 및 **다시 동의가 필요하기 전 기간(일)** 설정을 함께 사용할 수 있지만, 일반적으로 두 설정 중 하나만 사용합니다.

1. **조건부 액세스**에서 **조건부 액세스 정책 템플릿을** 사용 하 여 적용 목록을 사용 하 여 사용 약관을 적용할 템플릿을 선택 합니다.

   ![정책 템플릿을 선택 하는 조건부 액세스 드롭다운 목록](./media/terms-of-use/conditional-access-templates.png)

   | 템플릿 | 설명 |
   | --- | --- |
   | **모든 게스트에 대한 클라우드 앱 액세스** | 모든 게스트 및 모든 클라우드 앱에 대 한 조건부 액세스 정책이 생성 됩니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **모든 사용자에 대한 클라우드 앱 액세스** | 모든 사용자 및 모든 클라우드 앱에 대 한 조건부 액세스 정책이 생성 됩니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **사용자 지정 정책** | 이 사용 약관이 적용 될 사용자, 그룹 및 앱을 선택 합니다. |
   | **나중에 조건부 액세스 정책 만들기** | 이 사용 약관은 조건부 액세스 정책을 만들 때 권한 부여 컨트롤 목록에 표시 됩니다. |

   >[!IMPORTANT]
   >조건부 액세스 정책 제어 (사용 약관 포함)는 서비스 계정에 대 한 적용을 지원 하지 않습니다. 조건부 액세스 정책에서 모든 서비스 계정을 제외 하는 것이 좋습니다.

    사용자 지정 조건부 액세스 정책을 사용 하면 특정 클라우드 응용 프로그램 또는 사용자 그룹에 대해 세부적인 사용 약관을 사용할 수 있습니다. 자세한 내용은 [빠른 시작: 클라우드 앱에 액세스 하기 전에 동의 해야 하는 사용 약관](require-tou.md)을 참조 하세요.

1. **만들기**를 클릭합니다.

   사용자 지정 조건부 액세스 템플릿을 선택한 경우 사용자 지정 조건부 액세스 정책을 만들 수 있는 새 화면이 표시 됩니다.

   ![사용자 지정 조건부 액세스 정책 템플릿을 선택한 경우의 새 조건부 액세스 창](./media/terms-of-use/custom-policy.png)

   이제 새 사용 약관이 표시 됩니다.

   ![사용 약관 블레이드에 새로운 사용 약관 나열](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>사용 약관에 동의/거부한 사람에 대한 보고서 보기

[사용 약관] 블레이드에는 사용 약관에 동의한 사용자 및 거부한 사용자 수가 표시됩니다. 이러한 개수와 승인/거부 된 사람은 사용 약관의 수명 동안 저장 됩니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.

   ![수락 하 고 거부 한 사용자 표시 수를 나열 하는 사용 약관 블레이드](./media/terms-of-use/view-tou.png)

1. 사용 약관에 대해 **허용** 또는 **거부** 아래에 있는 숫자를 클릭 하 여 사용자의 현재 상태를 확인 합니다.

   ![수락 된 사용자를 나열 하는 사용 약관 동의 창](./media/terms-of-use/accepted-tou.png)

1. 개별 사용자의 기록을 보려면 줄임표(**...**)를 클릭한 후 **기록 보기**를 클릭합니다.

   ![사용자에 대 한 기록 상황에 맞는 메뉴 보기](./media/terms-of-use/view-history-menu.png)

   [기록 보기] 창에서 모든 승인, 거부 및 만료 기록을 확인합니다.

   ![기록 보기 창에는 사용자에 대 한 승인, 거부 및 만료 기록이 나열 됩니다.](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD 감사 로그 보기

추가 활동을 보려는 경우 Azure AD 사용 약관에 감사 로그가 포함 됩니다. 각 사용자 동의는 **30일** 동안 저장된 감사 로그에서 이벤트를 트리거합니다. 포털에서 이러한 로그를 살펴보거나 .csv 파일로 다운로드할 수 있습니다.

Azure AD 감사 로그를 시작하려면 다음 절차를 따릅니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 사용 약관을 선택 합니다.
1. **감사 로그 보기**를 클릭합니다.

   ![감사 로그 보기 옵션이 강조 표시 된 사용 약관 블레이드](./media/terms-of-use/audit-tou.png)

1. Azure AD 감사 로그 화면에서 제공된 목록을 사용하여 특정 감사 로그 정보를 대상으로 하도록 정보를 필터링할 수 있습니다.

   **다운로드**를 클릭하여 로컬에서 사용하도록 정보를 .csv 파일로 다운로드할 수도 있습니다.

   ![Azure AD 감사 로그 화면 목록, 대상 정책, 시작 날짜, 작업](./media/terms-of-use/audit-logs-tou.png)

   로그를 클릭하면 추가 활동 세부 정보가 포함된 창이 나타납니다.

   ![활동, 활동 상태, 시작한 사람, 대상 정책을 보여 주는 로그의 활동 세부 정보](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>사용자에 대 한 사용 약관은 다음과 같습니다.

사용 약관을 만들고 적용 한 후에는 로그인 하는 동안 범위 내에 있는 사용자에 게 다음 화면이 표시 됩니다.

![사용자가 로그인 할 때 표시 되는 사용 약관 예](./media/terms-of-use/user-tou.png)

사용자는 사용 약관을 확인 하 고 필요한 경우 단추를 사용 하 여 확대 및 축소할 수 있습니다.

![확대/축소 단추 사용 약관 보기](./media/terms-of-use/zoom-buttons.png)

다음 화면은 모바일 장치에서 사용 약관을 표시 하는 방법을 보여 줍니다.

![사용자가 모바일 장치에 로그인 할 때 표시 되는 사용 약관 예](./media/terms-of-use/mobile-tou.png)

사용자는 한 번만 사용 약관에 동의 하면 되며 후속 로그인 시 사용 약관은 다시 표시 되지 않습니다.

### <a name="how-users-can-review-their-terms-of-use"></a>사용자가 사용 약관을 검토 하는 방법

사용자는 다음 절차를 사용 하 여 수락한 사용 약관을 검토 하 고 확인할 수 있습니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.
1. 오른쪽 위 모서리에서 자신의 이름을 클릭하고 **프로필**을 선택합니다.

   ![사용자 창이 열려 있는 MyApps 사이트](./media/terms-of-use/tou14.png)

1. 프로필 페이지에서 **사용 약관 검토**를 클릭합니다.

   ![사용 약관 검토 링크를 표시 하는 사용자의 프로필 페이지](./media/terms-of-use/tou13a.png)

1. 여기에서 사용자가 동의한 사용 약관을 검토할 수 있습니다.

## <a name="edit-terms-of-use-details"></a>사용 약관 세부 정보 편집

사용 약관에 대 한 세부 정보를 편집할 수 있지만 기존 문서를 수정할 수는 없습니다. 다음 절차에서는 세부 정보를 편집하는 방법에 대해 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 편집 하려는 사용 약관을 선택 합니다.
1. **사용 약관 편집**을 클릭합니다.
1. [사용 약관 편집] 창에서 이름을 변경하거나, 이름을 표시하거나, 사용자가 값을 확장하도록 요구합니다.

   PDF 문서와 같이 변경 하려는 다른 설정이 있는 경우 사용자가 모든 장치에 동의 해야 함, 동의 만료, 다시 승인 전 기간 또는 조건부 액세스 정책을 사용 하 여 새 사용 약관을 만들어야 합니다.

   ![이름 및 확장 옵션을 보여 주는 사용 약관 패널 편집](./media/terms-of-use/edit-tou.png)

1. **저장**을 클릭하여 변경 내용을 저장합니다.

   변경 내용이 저장된 후 사용자가 이러한 편집 내용에 다시 동의할 필요가 없습니다.

## <a name="add-a-terms-of-use-language"></a>사용 약관 언어 추가

다음 절차에서는 사용 약관 언어를 추가 하는 방법에 대해 설명 합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 편집 하려는 사용 약관을 선택 합니다.
1. 세부 정보 창에서 **언어** 탭을 클릭합니다.

   ![사용 약관 선택 하 고 세부 정보 창에서 언어 탭을 표시 합니다.](./media/terms-of-use/languages-tou.png)

1. **언어 추가**를 클릭합니다.
1. [사용 약관 언어 추가] 창에서 지역화된 PDF를 업로드하고 언어를 선택합니다.

   ![지역화 된 Pdf 업로드 옵션과 함께 사용 약관 언어 창 추가](./media/terms-of-use/language-add-tou.png)

1. **추가**를 클릭하여 언어를 추가합니다.

## <a name="per-device-terms-of-use"></a>장치 단위 사용 약관

**사용자가 모든 장치에 동의 해야** 함 설정을 사용 하면 최종 사용자가 액세스 하는 모든 장치에서 사용 약관에 동의 해야 합니다. 최종 사용자가 Azure AD에 장치를 등록 해야 합니다. 장치를 등록 하면 장치 ID를 사용 하 여 각 장치에서 사용 약관을 적용 합니다.

지원되는 플랫폼 및 소프트웨어 목록은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 기타 |
> | --- | --- | --- | --- | --- |
> | **네이티브 앱** | 예 | 예 | 예 |  |
> | **Microsoft Edge** | 예 | 예 | 예 |  |
> | **Internet Explorer** | 예 | 예 | 예 |  |
> | **Chrome(확장 포함)** | 예 | 예 | 예 |  |

장치당 사용 약관에는 다음과 같은 제약 조건이 있습니다.

- 디바이스는 하나의 테넌트에만 연결될 수 있습니다.
- 사용자는 해당 디바이스에 연결할 권한이 있어야 합니다.
- Intune 등록 앱이 지원되지 않습니다.
- Azure AD B2B 사용자는 지원 되지 않습니다.

사용자의 디바이스가 연결되지 않으면 해당 디바이스에 연결해야 한다는 메시지가 표시됩니다. 해당 환경은 플랫폼 및 소프트웨어에 따라 다릅니다.

### <a name="join-a-windows-10-device"></a>Windows 10 디바이스 연결

사용자가 Windows 10 및 Microsoft Edge를 사용하는 경우 [해당 디바이스에 연결](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)하기 위해 다음과 같은 메시지가 표시됩니다.

![장치를 등록 해야 함을 나타내는 Windows 10 및 Microsoft Edge 메시지](./media/terms-of-use/per-device-win10-edge.png)

Chrome을 사용하는 경우 [Windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치할지 묻는 메시지가 표시됩니다.

### <a name="register-an-ios-device"></a>IOS 장치 등록

사용자가 iOS 장치를 사용 하는 경우 [Microsoft Authenticator 앱](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)을 설치 하 라는 메시지가 표시 됩니다.

### <a name="register-an-android-device"></a>Android 장치 등록

사용자가 Android 장치를 사용 하는 경우 [Microsoft Authenticator 앱](https://play.google.com/store/apps/details?id=com.azure.authenticator)을 설치 하 라는 메시지가 표시 됩니다.

### <a name="browsers"></a>브라우저

사용자가 지원되지 않는 브라우저를 사용하는 경우에는 다른 브라우저를 사용하도록 요청하는 메시지가 표시됩니다.

![장치를 등록 해야 하지만 브라우저가 지원 되지 않음을 나타내는 메시지입니다.](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>사용 약관 삭제

다음 절차를 사용 하 여 이전 사용 약관을 삭제할 수 있습니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관**으로 이동합니다.
1. 제거할 사용 약관을 선택합니다.
1. **사용 약관 삭제**를 클릭합니다.
1. 계속 진행할 것인지 묻는 메시지가 표시되면 **예**를 클릭합니다.

   ![사용 약관 삭제에 대 한 확인을 요청 하는 메시지](./media/terms-of-use/delete-tou.png)

   사용 약관이 더 이상 표시 되지 않습니다.

## <a name="deleted-users-and-active-terms-of-use"></a>삭제 된 사용자 및 활성 사용 약관

기본적으로 삭제된 사용자는 30일 동안 Azure AD에 삭제된 상태로 남아 있지만, 이 기간 동안 관리자는 필요한 경우 삭제된 사용자를 복원할 수 있습니다. 30일 후 해당 사용자가 영구적으로 삭제됩니다. 또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다. 사용자 한 명에 게 영구적으로 삭제 되 면 해당 사용자에 대 한 후속 데이터가 활성 사용 약관에서 제거 됩니다. 삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.

## <a name="policy-changes"></a>정책 변경 내용

조건부 액세스 정책은 즉시 적용 됩니다. 이 경우 관리자에게 "sad 클라우드" 또는 "Azure AD 토큰 문제"가 표시되기 시작합니다. 관리자는 새 정책을 충족하기 위해 로그아웃했다가 다시 로그인해야 합니다.

> [!IMPORTANT]
> 범위에 있는 사용자는 다음과 같은 경우 새 정책을 충족하기 위해 로그아웃 및 로그인해야 합니다.
>
> - 조건부 액세스 정책은 사용 약관에서 사용 하도록 설정 됩니다.
> - 또는 두 번째 사용 약관이 작성된 경우

## <a name="b2b-guests-preview"></a>B2B 게스트(미리 보기)

대부분의 조직에는 직원이 조직의 사용 약관 및 개인 정보 취급 방침에 동의 하는 프로세스가 있습니다. 그러나 SharePoint 또는 Teams를 통해 추가될 경우 Azure AD B2B 게스트에게 동일한 동의를 어떻게 적용할 수 있나요? 조건부 액세스 및 사용 약관을 사용 하 여 B2B 게스트 사용자에 게 직접 정책을 적용할 수 있습니다. 초대 상환 흐름 중에 사용자에 게 사용 약관이 표시 됩니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

사용 약관은 사용자에게 Azure AD의 게스트 계정이 있는 경우에만 표시됩니다. SharePoint Online에는 현재 사용자에 게 게스트 계정이 없어도 되는 문서 또는 폴더를 공유 하는 [임시 외부 공유 받는 사람 환경이](/sharepoint/what-s-new-in-sharing-in-targeted-release) 있습니다. 이 경우 사용 약관은 표시 되지 않습니다.

![사용자 및 그룹 창-모든 게스트 사용자 옵션을 선택한 상태로 포함 탭](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>클라우드 앱 지원(미리 보기)

사용 약관은 Azure Information Protection 및 Microsoft Intune과 같은 다른 클라우드 앱에 사용할 수 있습니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

### <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection 앱에 대 한 조건부 액세스 정책을 구성 하 고 사용자가 보호 된 문서에 액세스할 때 사용 약관을 요구할 수 있습니다. 이렇게 하면 사용자가 처음으로 보호 된 문서에 액세스 하기 전에 사용 약관을 트리거합니다.

![Microsoft Azure Information Protection 앱이 선택 된 클라우드 앱 창](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 등록

Intune에서 장치를 등록 하기 전에 Microsoft Intune 등록 앱에 대 한 조건부 액세스 정책을 구성 하 고 사용 약관을 요구할 수 있습니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) 읽기를 참조하세요.

![Microsoft Intune 앱이 선택 된 클라우드 앱 창](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune 등록 앱은 [장치 단위 사용 약관](#per-device-terms-of-use)에 대해 지원 되지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 사용자가 사용 약관에 동의 하는 경우/어떻게 할까요?를 참조 하세요.**<br />
A: 사용 약관 블레이드에서 **수락** 아래에 있는 숫자를 클릭합니다. Azure AD 감사 로그에서 동의 작업을 보거나 검색할 수도 있습니다. 자세한 내용은 사용 약관에 동의/거부한 사람에 대한 보고서 보기 및 [Azure AD 감사 로그 보기](#view-azure-ad-audit-logs)를 참조하세요.

**Q: 정보가 얼마나 오래 저장되나요?**<br />
A: 사용 약관 보고서에서 사용자 수가 계산 되 고 승인/거부 된 사용자는 사용 약관의 수명 동안 저장 됩니다. Azure AD 감사 로그는 30일 동안 저장됩니다.

**Q: 사용 약관 보고서와 Azure AD 감사 로그에 다른 수의 동의가 표시 되는 이유는 무엇 인가요?**<br />
A: 사용 약관 보고서는 해당 사용 약관의 수명 동안 저장 되며 Azure AD 감사 로그는 30 일 동안 저장 됩니다. 또한 사용 약관 보고서에는 사용자의 현재 동의 상태만 표시 됩니다. 예를 들어 사용자가 동의 하 고 수락 하면 사용 약관 보고서에 해당 사용자의 수락만 표시 됩니다. 기록을 확인해야 하는 경우, Azure AD 감사 로그를 사용할 수 있습니다.

**Q: 사용 약관에 대 한 세부 정보를 편집 하는 경우 사용자가 다시 동의 해야 하나요?**<br />
A: 아니요, 관리자가 사용 약관에 대 한 세부 정보 (이름, 표시 이름, 사용자를 확장 하거나 언어를 추가 해야 함)를 편집 하는 경우 사용자가 새 약관에 다시 동의 하지 않아도 됩니다.

**Q: 기존 사용 약관 문서를 업데이트할 수 있나요?**<br />
A: 현재는 기존 사용 약관 문서를 업데이트할 수 없습니다. 사용 약관 문서를 변경 하려면 새 사용 약관 인스턴스를 만들어야 합니다.

**Q: 하이퍼링크가 PDF 문서 사용 약관에 있으면 최종 사용자가 해당 문서를 클릭할 수 있나요?**<br />
A: 예, 최종 사용자는 추가 페이지에 대 한 하이퍼링크를 선택할 수 있지만 문서 내의 섹션에 대 한 링크는 지원 되지 않습니다. 또한 Azure AD MyApps/MyAccount 포털에서 액세스할 때 Pdf 사용 측면에서 하이퍼링크가 작동 하지 않습니다.

**Q: 사용 약관에서 여러 언어를 지원할 수 있나요?**<br />
A: 예. 현재 관리자가 단일 사용 약관에 대해 구성할 수 있는 108 다른 언어가 있습니다. 관리자는 여러 PDF 문서를 업로드하고 해당 언어를 사용하는 문서에 태그를 지정할 수 있습니다(최대 108개). 최종 사용자가 로그인할 때 브라우저 언어 기본 설정이 확인되고 일치하는 문서가 표시됩니다. 일치 항목이 없으면 업로드된 첫 번째 문서인 기본 문서가 표시됩니다.

**Q: 사용 약관은 언제 트리거됨?**<br />
A: 사용 약관은 로그인 환경 중에 트리거됩니다.

**Q: 사용 약관을 대상으로 지정할 수 있는 애플리케이션은 무엇인가요?**<br />
A: 최신 인증을 사용 하 여 엔터프라이즈 응용 프로그램에 대 한 조건부 액세스 정책을 만들 수 있습니다. 자세한 내용은 [엔터프라이즈 애플리케이션](./../manage-apps/view-applications-portal.md)을 참조하세요.

**Q: 지정 된 사용자 또는 앱에 여러 사용 약관을 추가할 수 있나요?**<br />
A: 예, 이러한 그룹 또는 응용 프로그램을 대상으로 하는 여러 조건부 액세스 정책을 만듭니다. 사용자가 여러 사용 약관의 범위에 속하는 경우 한 번에 하나의 사용 약관에 동의 하는 것입니다.

**Q: 사용자가 사용 약관을 거부 하면 어떻게 되나요?**<br />
A: 사용자는 애플리케이션에 액세스할 수 없습니다. 액세스하려면 다시 로그인하여 약관에 동의해야 합니다.

**Q: 이전에 수락 된 사용 약관에 동의 하지 못할 수 있나요?**<br />
A: [이전에 동의한 사용 약관을 검토할](#how-users-can-review-their-terms-of-use)수 있지만 현재 수락 하지 않는 방법은 없습니다.

**Q: Intune 사용 약관도 사용하면 어떻게 되나요?**<br />
A: Azure AD 사용 약관 및 [Intune](/intune/terms-and-conditions-create)사용 약관을 모두 구성한 경우 사용자는 둘 다 수락 해야 합니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)을 참조하세요.

**Q: 사용 약관 서비스에서 인증에 사용 하는 끝점은 무엇 인가요?**<br />
A: 사용 약관는 인증을 위해 및의 끝점을 활용 합니다. https://tokenprovider.termsofuse.identitygovernance.azure.com https://account.activedirectory.windowsazure.com 조직에서 등록을 위한 Url의 허용 목록을 사용 하는 경우 로그인을 위해 Azure AD 끝점과 함께 이러한 끝점을 허용 목록에 추가 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관에 동의하도록 요구](require-tou.md)
- [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](best-practices.md)