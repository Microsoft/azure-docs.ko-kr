---
title: Azure Advisor와 Windows 가상 데스크톱 통합-Azure
description: Windows 가상 데스크톱 배포와 함께 Azure Advisor를 사용 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147536"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Windows 가상 데스크톱과 Azure Advisor 사용

Azure Advisor를 사용 하면 파일 지원 사례 없이 사용자가 자신의 일반적인 문제를 해결 하는 데 도움이 될 수 있습니다. 권장 사항을 통해 지원 요청을 제출 하 여 시간과 비용을 절감할 수 있습니다.

이 문서에서는 사용자에 게 도움이 되도록 Windows 가상 데스크톱 배포에서 Azure Advisor를 설정 하는 방법을 설명 합니다.

## <a name="what-is-azure-advisor"></a>Azure Advisor란?

Azure Advisor는 구성 및 원격 분석을 분석 하 여 일반적인 문제를 해결 하는 맞춤형 권장 사항을 제공 합니다. 이러한 권장 사항을 사용 하 여 안정성, 보안, 운영, 성능 및 비용에 대 한 Azure 리소스를 최적화할 수 있습니다. [Azure Advisor 웹 사이트](https://azure.microsoft.com/services/advisor/)에서 자세히 알아보세요.

## <a name="how-to-start-using-azure-advisor"></a>Azure Advisor 사용을 시작 하는 방법

Azure Portal에서 Azure 계정으로 시작 해야 합니다. 먼저 <https://portal.azure.com/#home> 다음 그림에 표시 된 것 처럼에서 Azure Portal를 연 다음 **Azure 서비스**에서 **Advisor** 를 선택 합니다. Azure Portal의 검색 표시줄에 "Azure Advisor"을 입력할 수도 있습니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal의 스크린샷 사용자가 Azure Advisor 링크 위로 마우스 커서를 가져가면 드롭다운 메뉴가 나타납니다.](media/azure-advisor.png)

Azure Advisor 열면 다음 5 개의 범주가 표시 됩니다.

- 비용
- 보안
- 안정성
- 뛰어난 운영
- 성능

> [!div class="mx-imgBorder"]
> ![각 범주에 대 한 5 개의 메뉴를 보여 주는 Azure Advisor의 스크린샷 자신의 상자에 표시 되는 5 개 항목은 비용, 보안, 안정성, 운영 및 성능입니다.](media/advisor-categories.png)

범주를 선택 하면 활성 권장 사항 페이지로 이동 합니다. 이 페이지에서 다음 이미지와 같이 사용자에 게 Azure Advisor 있는 권장 사항을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![운영 하는 데 필요한 활성 권장 사항 목록의 스크린샷 이 목록에는 다양 한 우선 순위 수준으로 일곱 가지 권장 사항이 표시 됩니다.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Azure Advisor에 대 한 추가 팁

- 권장 사항은 일주일에 두 번 이상 자주 확인 해야 합니다. Azure Advisor은 하루에 여러 번 활성 권장 사항을 업데이트 합니다. 새 권장 사항을 확인 하면 더 작은 문제를 파악 하 고 해결 하는 데 도움이 되므로 큰 문제를 방지할 수 있습니다.

- 항상 Azure Advisor에서 가장 높은 우선 순위 수준으로 문제를 해결 해 보십시오. 우선 순위가 높은 문제는 빨강으로 표시 됩니다. 우선 순위가 높은 권장 사항을 확인 되지 않은 상태로 두면 줄에서 문제가 발생할 수 있습니다.

- 권장 사항이 그다지 중요 하지 않은 것으로 보이면이를 해제 하거나 연기할 수 있습니다. 권장 사항을 해제 하거나 연기 하려면 **작업** 열로 이동 하 여 항목의 상태를 변경 합니다.

- 표시 되는 이유를 확인 하 고 사용자 또는 사용자에 게 부정적인 영향을 주지 않을 때까지 권장 사항을 해제 하지 마십시오. 항상 **자세한 정보** 를 선택 하 여 문제를 확인 합니다. Azure Advisor의 지침에 따라 문제를 해결 하는 경우 목록에서 자동으로 사라집니다. 반복 해 서 연기 하는 것 보다 문제를 해결 하는 것이 더 좋습니다.

- Windows 가상 데스크톱에서 문제가 발생할 때마다 항상 먼저 Azure Advisor 확인 합니다. Azure Advisor는 문제를 해결 하는 방법에 대 한 지침을 제공 하거나 최소한 도움이 될 수 있는 리소스를 제공 합니다.

## <a name="next-steps"></a>다음 단계

권장 사항을 해결 하는 방법에 대 한 자세한 내용은 [Azure Advisor 권장 사항을 해결 하는 방법](azure-advisor-recommendations.md)을 참조 하세요.

새 권장 사항에 대 한 제안이 있는 경우 [Azure Advisor 사용자 의견 포럼](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations)에 게시 하세요.
