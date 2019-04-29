---
title: Azure 애플리케이션 제품 게시 - Azure Marketplace | Microsoft Docs
description: Azure 애플리케이션 제품을 Azure Marketplace에 게시하는 프로세스 및 단계를 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: fdca47877d4cb2192eef0a26448cd21e8afe4b77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740259"
---
# <a name="publish-azure-application-offer"></a>Azure 애플리케이션 제품 게시

**새 제품** 페이지에 정보를 제공하여 제품을 만든 후 제품을 게시할 수 있습니다. **게시**를 선택하여 게시 프로세스를 시작합니다.

다음 다이어그램은 제안을 "라이브 상태"로 만들기 위한 게시 프로세스의 주요 단계를 보여줍니다.

![제품 게시 단계](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>게시 단계에 대한 자세한 설명

다음 표에는 각 게시 단계가 나열 및 설명되어 있으며, 각 단계를 완료하는 데 걸리는 예상 시간이 제공됩니다.  “일” 단위의 예상 시간은 업무일로 정의되며 주말과 휴일은 제외됩니다.

|  **게시 단계**           | **Time**    | **설명**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 필수 구성 요소 유효성 검사         | 15분 미만    | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                        |
| 영향을 받는 매출 설정의 유효성 검사 | 15분 미만  | 제안의 Azure 리소스 사용량 특성이 확인됩니다.             |
| 인증                  | 1일 미만     | Azure 인증 팀에서 제안을 분석합니다. 이 제품은 바이러스, 맬웨어, 안전 규정 준수 및 보안 문제에 대해 검사됩니다. 모든 자격 조건을 충족하는지 제품을 확인합니다. 자세한 내용은 [필수 구성 요소](./cpp-prerequisites.md)를 참조하세요. 문제가 발견되면 피드백이 제공됩니다. |
| 시험 사용 유효성 검사          | 2시간 미만   | (선택 사항) 시험 사용이 있는 경우, Microsoft는 시험 사용을 배포하고 복제할 수 있는지 확인합니다.  |
| 패키징 및 잠재 고객 생성 등록 | 1시간 미만  | 제품의 기술 자산이 고객의 사용에 맞게 패키지되고, 잠재 고객 시스템이 구성 및 배포됩니다. |
|  게시자 승인             |  수동    | 제안이 라이브 상태가 되기 전에 최종 게시자가 검토 및 확인합니다. 이제 제품 미리 보기를 사용할 수 있습니다.  선택한 구독에 제안을 배포하여(제안 정보 단계에서) 모든 요구 사항을 충족하는지 확인할 수 있습니다.  제품을 확인한 후 제품이 다음 단계로 넘어갈 수 있도록 **라이브로 전환**을 선택합니다. |
| Microsoft 검토                | 7-14일 | Microsoft는 Azure 애플리케이션을 전반적으로 검토하고, 문제가 발견되면 메일을 보냅니다.  이 단계의 기간은 애플리케이션의 복잡성, 발견된 문제, 문제에 대응하는 신속성에 따라 달라집니다.  |
| 라이브                           | 1일 미만 | 제품이 릴리스되고, 지정한 지역에 복제되고, 공개적으로 사용할 수 있게 됩니다. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Cloud 파트너 포털의 제품 **상태** 탭에서 게시 프로세스를 모니터링할 수 있습니다.

![Azure 앱 제품의 상태 탭](./media/offer-status-tab.png)

게시 프로세스가 완료되면 제품이 [Microsoft Azure Marketplace 애플리케이션 범주](https://azuremarketplace.microsoft.com/marketplace/apps/)에 나열됩니다.

>[!Note]
>클라우드 솔루션 공급자 (CSP) 파트너 채널 옵트인를 출시 되었습니다.  참조 하세요 [클라우드 솔루션 공급자](../../cloud-solution-providers.md) Microsoft CSP를 통해 제품을 마케팅 하는 방법은 채널 파트너입니다.

## <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

**상태** 탭에는 제안의 게시 상태를 표시하는 것 외에도 문제가 발생한 게시 단계의 오류 메시지 및 피드백이 표시됩니다.  중요한 문제이면 게시가 취소됩니다.  그러면 보고된 문제를 수정하고 제안을 다시 게시해야 합니다.  **Microsoft 검토** 단계는 제안 및 관련 기술 자산(특히 Azure Resource Manager 템플릿)에 대한 광범위한 검토를 나타내므로 문제는 일반적으로 PR(끌어오기 요청) 링크로 표시됩니다.  이러한 PR을 보고 응답하는 방법에 대한 설명은 [검토 피드백 처리](./cpp-handling-review-feedback.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

하나 이상의 게시 단계에서 오류가 발생하면 해당 오류를 수정하고 제안을 다시 게시해야 합니다.  **Microsoft 검토** 단계에서 중요한 문제가 발생하면 Microsoft 검토 팀의 Azure DevOps 리포지토리에 액세스하여 [검토 피드백을 처리](./cpp-handling-review-feedback.md)해야 합니다.

Azure 앱이 성공적으로 게시되면 변화하는 비즈니스 또는 기술 요구 사항을 반영하기 위해 [기존 제안을 업데이트](./cpp-update-existing-offer.md)할 수 있습니다. 
