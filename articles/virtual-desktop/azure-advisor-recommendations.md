---
title: Azure Advisor Windows 가상 데스크톱 연습-Azure
description: Windows 가상 데스크톱에 대 한 Azure Advisor 권장 사항을 해결 하는 방법입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b14dd4aa44620f2b13891a18fc473acbcb0f3245
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086514"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Azure Advisor 권장 사항을 해결 하는 방법

이 문서에서는 Windows 가상 데스크톱에 대 한 Azure Advisor에 표시 되는 권장 사항을 해결할 수 있는 방법을 설명 합니다.

## <a name="no-validation-environment-enabled"></a>"유효성 검사 환경 사용 안 함"

>[!div class="mx-imgBorder"]
>![Azure Advisor 작동 가능 페이지의 스크린샷 "유효성 검사 환경 사용 안 함" 권장 사항은 빨간색으로 강조 표시 됩니다.](media/no-validation-environment.png)

이 권장 사항은 운영에 뛰어난 권장 사항 아래에 나타납니다. 권장 사항에 다음과 같은 경고 메시지가 표시 되어야 합니다.

"이 구독에서 유효성 검사 환경을 사용할 수 없습니다. 호스트 풀을 만들 때 속성 탭에서 "유효성 검사 환경"에 대해 **아니요** 를 선택 했습니다. Windows 가상 데스크톱 서비스 배포를 통해 비즈니스 연속성을 보장 하려면 잠재적인 문제를 테스트할 수 있는 유효성 검사 환경을 포함 하는 호스트 풀이 하나 이상 있는지 확인 하십시오. "

호스트 풀 중 하나에서 유효성 검사 환경을 사용 하도록 설정 하면이 경고 메시지가 표시 되지 않도록 할 수 있습니다.

유효성 검사 환경을 사용 하도록 설정 하려면:

1. Azure Portal 홈 페이지로 이동 하 여 변경할 호스트 풀을 선택 합니다.

2. 그런 다음 프로덕션 환경에서 유효성 검사 환경으로 변경 하려는 호스트 풀을 선택 합니다.

3. 호스트 풀에서 화면 오른쪽에 있는 열의 **속성** 탭을 선택 합니다. 그런 다음 "유효성 검사 환경"이 표시 될 때까지 아래로 스크롤합니다. **예**를 선택 하 고 **적용**을 선택 합니다.

>[!div class="mx-imgBorder"]
>![속성 메뉴의 스크린샷 "유효성 검사 환경"이 빨간색으로 강조 표시 되 고 "예" 거품이 선택 됩니다.](media/validation-yes.png)

이러한 변경으로 인해 경고가 즉시 나타나지는 않지만 결국에는 사라집니다. 하루에 두 번 업데이트를 Azure Advisor 합니다. 그때 까지는 권장 사항을 수동으로 연기 하거나 해제할 수 있습니다. 권장 사항을 자체적으로 제거 하는 것이 좋습니다. Azure Advisor 이렇게 하면 설정이 변경 될 때 문제가 발생 하는지 알 수 있습니다.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"충분 한 프로덕션 (유효성 검사 아님) 환경 사용"

이 권장 사항은 운영에 뛰어난 권장 사항 아래에 나타납니다.

이 권장 사항에 대 한 경고 메시지는 다음과 같은 이유 중 하나로 표시 됩니다.

- 유효성 검사 환경에 호스트 풀이 너무 많습니다.
- 프로덕션 호스트 풀이 없습니다.

유효성 검사 환경에서 사용자의 호스트 풀이 절반 미만인 것이 좋습니다.

이 경고를 해결 하려면:

1. Azure Portal 홈 페이지로 이동 합니다.

2. 유효성 검사에서 프로덕션으로 변경 하려는 호스트 풀을 선택 합니다.

3. 호스트 풀에서 화면 오른쪽에 있는 열의 **속성** 탭을 선택 합니다. 그런 다음 "유효성 검사 환경"이 표시 될 때까지 아래로 스크롤합니다. **아니요**를 선택 하 고 **적용**을 선택 합니다.

>[!div class="mx-imgBorder"]
>![속성 메뉴의 스크린샷 "유효성 검사 환경"이 빨간색으로 강조 표시 되 고 "아니요" 거품이 선택 됩니다.](media/validation-no.png)

이러한 변경으로 인해 경고가 즉시 나타나지는 않지만 결국에는 사라집니다. 하루에 두 번 업데이트를 Azure Advisor 합니다. 그때 까지는 권장 사항을 수동으로 연기 하거나 해제할 수 있습니다. 권장 사항을 자체적으로 제거 하는 것이 좋습니다. Azure Advisor 이렇게 하면 설정이 변경 될 때 문제가 발생 하는지 알 수 있습니다.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"충분 한 링크를 해제 하 여 VM을 성공적으로 구현할 수 없습니다."

이 권장 사항은 운영에 뛰어난 권장 사항 아래에 나타납니다.

특정 Url을 차단 해제 하 여 VM (가상 머신)이 제대로 작동 하는지 확인 해야 합니다. [안전 URL 목록](safe-url-list.md)에서 목록을 볼 수 있습니다. Url이 차단 해제 되지 않으면 VM이 제대로 작동 하지 않습니다.

이 권장 사항을 해결 하려면 [안전한 url 목록](safe-url-list.md)에서 모든 url의 차단을 해제 해야 합니다. 서비스 태그 또는 FQDN 태그를 사용 하 여 Url도 차단을 해제할 수 있습니다.

## <a name="propose-new-recommendations"></a>새 권장 사항 제안

권장 사항에 대 한 아이디어를 제출 하 여 Azure Advisor 개선 하는 데 도움을 주실 수 있습니다. 권장 사항을 통해 다른 사용자가 어려울 수 있습니다. 제안을 제출 하려면 [UserVoice 포럼](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) 으로 이동 하 여 제출 양식을 작성 하세요. 양식을 작성 하는 경우 가능한 한 많은 정보를 제공 해야 합니다.

## <a name="next-steps"></a>다음 단계

일반적인 문제를 해결 하는 방법에 대 한 자세한 가이드를 찾고 있다면 [Windows 가상 데스크톱에 대 한 문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.