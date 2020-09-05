---
title: 내 로그인 페이지에서 최근 로그인 활동 보기 및 검색-Azure Active Directory | Microsoft Docs
description: 내 계정 포털의 내 로그인 페이지에서 최근 로그인 활동을 보고 검색하는 방법을 자세히 설명합니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/03/2020
ms.author: curtand
ms.openlocfilehash: d9023579b6627e9dab9feac8dfaccd94dc9f5c12
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798128"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>내 로그인 페이지에서 최근 로그인 활동 보기 및 검색

**내 계정** 포털의 **내 로그인** 페이지에서 모든 최근 회사 또는 학교 계정 로그인 활동을 볼 수 있습니다. 로그인 기록을 검토하면 다음을 확인하여 비정상적인 활동이 있는지 확인할 수 있습니다.

- 누군가가 암호를 추측하려고 하고 있는지 여부
- 공격자가 계정에 성공적으로 로그인했는지 여부 및 해당 위치
- 공격자가 액세스하려고 시도한 앱

## <a name="view-your-recent-sign-in-activity"></a>최근 로그인 활동 보기

1. 회사 또는 학교 계정에 로그인한 다음, https://myaccount.microsoft.com/ 페이지로 이동합니다.

2. 왼쪽 탐색 창에서 **내 로그인** 을 선택 하거나 **내 로그인** 블록에서 **최근 작업 검토** 링크를 선택 합니다.

    ![강조 표시된 최근 활동 링크가 표시된 내 계정 페이지](media/my-account-portal/my-account-portal-sign-ins.png)

3. 각 로그인 항목을 확장하고 검토하여 각 항목을 인식할 수 있는지 확인합니다. 친숙 하지 않은 로그인 항목이 발견 되 면 암호를 변경 하 여 손상 된 경우 계정을 보호 합니다.

    ![확장 로그인 세부 정보가 포함된 최근 활동 페이지](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>성공한 로그인이 표시 되는 경우

경우에 따라 일반적인 로그인 활동을 검토 하는 동안 익숙하지 않은 위치, 브라우저 또는 운영 체제에서 성공적으로 로그인 한 것을 볼 수 있습니다. 익숙하지 않은 로그인은 공격자가 계정에 액세스할 수 있음을 의미할 수 있습니다. 권한이 없는 활동이 표시 되 면 암호를 즉시 변경한 다음 [보안 정보](https://mysignins.microsoft.com/security-info) 로 이동 하 여 보안 설정을 업데이트 하는 것이 좋습니다.

잘못된 항목을 확인하기 전에 거짓 긍정이 표시되지 않는지 확인해야 합니다. 이 경우 항목에 문제가 있어 보이지만 괜찮습니다. 예를 들어 IP 주소를 기준으로 대략적인 위치와 지도를 확인 합니다. 모바일 네트워크는 때때로 먼 위치를 거쳐 트래픽을 라우팅하기 때문에 정확한 위치를 알아내는 것이 특히 어렵습니다. 워싱턴 주에 있는 모바일 장치를 사용 하 여 로그인 하는 경우에도 해당 위치에 캘리포니아에서 들어오는 로그인이 표시 될 수 있습니다. 단지 단지 위치 외에도 세부 정보를 확인 하는 것이 좋습니다. 운영 체제, 브라우저 및 앱이 모두 적합 한지 확인 합니다.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>실패 한 로그인이 표시 되는 경우

실패 한 로그인이 표시 되 면 자격 증명을 잘못 입력 한 것일 수 있습니다. 공격자가 암호를 추측 하려고 시도 했을 수도 있습니다. 이 위험에 대처 하기 위해 암호를 변경 하지 않아도 되지만 MFA (Azure Multi-Factor Authentication)에 등록 하는 것이 좋습니다. Multi-factor authentication을 사용 하는 경우 해커가 암호를 추측 하더라도 계정에 액세스 하는 데 충분 하지 않습니다.

![실패 한 로그인 타일](media/my-account-portal-sign-ins-page/unsuccessful.png)

실패 한 로그인이 표시 되는 경우 (즉, 이라는 **세션 활동** 아래에 참고), `Additional verification failed, invalid code` 기본 인증 자격 증명이 성공 했지만 multi-factor authentication이 실패 했음을 의미 합니다. 이 문제는 공격자가 암호를 정확 하 게 추측 했지만 multi-factor authentication 챌린지를 전달할 수 없음을 의미할 수 있습니다. 그래도 암호를 변경 하는 것이 좋지만 [보안 정보](https://mysignins.microsoft.com/security-info) 페이지로 이동 하 여 보안 설정을 업데이트할 수 있습니다.

## <a name="search-for-specific-sign-in-activity"></a>특정 로그인 작업 검색

사용 가능한 정보 중 하나를 사용하여 최근 로그인 작업을 검색할 수 있습니다. 예를 들어, 운영 체제, 위치, 앱 등을 기준으로 최근 로그인 작업을 검색할 수 있습니다.

1. **최근 작업 검토** 페이지에서 **검색** 표시줄에 검색할 정보를 입력합니다. 예를 들어,를 입력 `Unsuccessful` 하 여 내 계정 앱에서 수집 된 실패 한 모든 로그인 활동을 검색 합니다.

2. **검색** 단추를 선택하여 검색을 시작합니다.

    ![강조 표시된 검색 표시줄, 검색 단추 및 결과를 표시하는 최근 작업 페이지](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>비정상적인 활동 확인

비정상적인 활동으로 플래그가 지정 된 로그인은 **내 로그인** 페이지의 해당 활동에 대 한 타일에서 확인할 수 있습니다.

![의 비정상적인 로그인 타일은 로그인을 시도 했는지 여부를 확인 합니다.](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>다음 단계

최근 로그인 작업을 확인한 후에 다음을 수행할 수 있습니다.

- [보안 정보](./security-info-setup-signin.md)를 보거나 관리합니다.

- 연결된 [디바이스](my-account-portal-devices-page.md)를 보거나 관리합니다.

- [조직](my-account-portal-organizations-page.md)을 보거나 관리합니다.

- 조직에서 [개인 정보 관련 데이터를 사용](my-account-portal-privacy-page.md)하는 방법을 확인합니다.

- [내 계정 포털 설정](my-account-portal-settings.md) 변경