---
title: 자습서 - Azure의 Cloudyn을 사용하여 소비 예측
description: 이 자습서에서는 기존 사용량 및 소비 데이터를 사용하여 소비를 예측하는 방법을 알아봅니다.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 309582464d85923823b3779338f288bfb5519c6f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687844"
---
# <a name="tutorial-forecast-future-spending"></a>자습서: 향후 소비 예측

Cloudyn은 기존 사용량 및 소비 데이터를 사용하여 향후 소비를 예측하는 데 도움이 됩니다. Cloudyn 보고서를 사용하여 모든 비용 예상 데이터를 볼 수 있습니다. 이 자습서의 예제는 보고서를 사용하여 비용 예상의 검토 과정을 안내합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 향후 소비 예측

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정이 있어야 합니다.
- Cloudyn에 대한 평가판 등록 또는 유료 구독이 있어야 합니다.

## <a name="forecast-future-spending"></a>향후 소비 예측

Cloudyn은 시간에 따른 사용량을 기반으로 소비를 예측하는 데 도움이 되는 비용 예상 보고서를 포함합니다. 기본 목적은 비용 추세가 조직의 기대치를 초과하지 않도록 하는 데 도움을 주는 것입니다. 사용하는 보고서는 이번 달 예상 비용 및 연간 예상 비용입니다. 사용량이 지난 30일 동안의 사용량과 비교적 일치하는 경우 두 보고서는 모두 예상되는 향후 소비를 보여 줍니다.

이번 달 예상 비용 보고서에는 서비스의 비용이 표시됩니다. 월 초반 및 이전 달의 비용을 사용하여 예상되는 비용을 보여 줍니다. 포털의 위쪽에 있는 보고서 메뉴에서 **비용** > **예상 및 예산** > **이번 달 예상 비용**을 클릭합니다. 다음 이미지에 예가 나와 있습니다.

![이번 달 예상 비용 보고서에 표시된 예제 정보](./media/tutorial-forecast-spending/project-month01.png)

예제에서 가장 많이 소비된 서비스를 볼 수 있습니다. Azure 비용은 AWS 비용에 비해 더 낮습니다. Azure VM에 대한 비용 예상 세부 정보를 확인하려는 경우 **필터** 목록에서 **Azure/VM**을 선택합니다.

![Azure VM 이번 달 예상 비용을 보여주는 예제](./media/tutorial-forecast-spending/project-month02.png)

위의 동일한 기본 단계를 수행하여 원하는 다른 서비스에 대한 월별 비용 예상을 살펴볼 수 있습니다.

연간 예상 비용 보고서는 향후 12개월 간 서비스의 추정 비용을 보여 줍니다.

포털의 위쪽에 있는 보고서 메뉴에서 **비용** > **예상 및 예산** > **연간 예상 비용**을 클릭합니다. 다음 이미지에 예가 나와 있습니다.

![연간 예상 비용 보고서를 보여주는 예제](./media/tutorial-forecast-spending/project-annual01.png)

예제에서 가장 많이 소비된 서비스를 볼 수 있습니다. 월별 예제와 같이 Azure 비용은 AWS 비용에 비해 더 낮습니다. Azure VM에 대한 비용 예상 세부 정보를 확인하려는 경우 **필터** 목록에서 **Azure/VM**을 선택합니다.

![연간 예상 VM 비용을 보여주는 예제](./media/tutorial-forecast-spending/project-annual02.png)

위의 이미지에서 Azure VM의 연간 예상 비용은 $28,374입니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 향후 소비 예측


다음 자습서를 진행하여 비용 할당 및 쇼백 보고서를 사용하여 비용을 관리하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [비용 할당 및 쇼백 보고서를 사용하여 비용 관리](../../cost-management/tutorial-manage-costs.md)
