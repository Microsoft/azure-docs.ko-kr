---
title: 마켓플레이스 제품의 상태 보기 - Azure Marketplace | Microsoft Docs
description: Cloud 파트너 포털을 사용하여 Azure 및 AppSource Marketplace에서 제품의 상태 보기
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: bdec2d699e8448c8e2303dfbabcb4d176a9ca389
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729790"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Azure Marketplace 및 AppSource 제품의 게시 상태 보기

제품을 만든 후, 특히 게시 프로세스 중에 Cloud 파트너 포털에서 제품 상태를 볼 수 있습니다.  전체 게시 상태는 포털의 [**모든 제품**](../portal-tour/cpp-all-offers-page.md) 및 [**승인**](../portal-tour/cpp-approvals-page.md) 페이지에서 확인할 수 있습니다.  각 제품에 대해 다음 상태 표시기 중 하나가 표시되어야 합니다.  

|            상태              |   설명                                                           |
|            ------              |   -----------                                                           |
| **-**                          | 제품이 생성되었지만 게시 프로세스가 시작되지 않았습니다.            |
| **게시 진행 중**        | 제품이 게시 프로세스의 단계를 진행 중입니다.   |
| **게시 실패**             | Microsoft에서 유효성 검사 또는 검토 중에 심각한 문제를 발견했습니다. |
| **게시가 취소됨**           | 게시자가 제품 게시 프로세스를 취소했습니다.  이 상태는 마켓플레이스의 목록에서 기존 제품을 제거하지 않습니다. | 
| **게시자 확인 대기 중** | 제품이 Microsoft에서 검토되었으며 현재, 게시자의 최종 확인을 기다리고 있습니다. |
| **목록에서 제거됨**                   | 마켓플레이스에 이전에 게시된 제품이 제거되었습니다.      | 
|  |  |


## <a name="publishing-status-details"></a>게시 상태 정보 

게시 프로세스를 진행 중인 제품의 상태에 대한 자세한 내용은 **새 제품** 페이지의 **상태** 탭에서 확인할 수 있습니다.  이 페이지에는 해당 제품 유형의 모든 게시 단계가 표시됩니다.  번호와 특정 단계는 제품 유형마다 다른 경우가 많습니다.  이 페이지에는 Microsoft 유효성 검사 및 검토 단계에서 발생한 미결 문제도 표시되며, 게시자가 작업을 수행해야 게시 프로세스를 진행할 수 있는 경우가 많습니다.  예를 들어 다음 이미지는 새 가상 머신 제품의 **상태** 탭을 보여 줍니다. 

![VM 제품의 상태 탭](./media/vm-offer-pub-steps1.png)

컨설팅 서비스의 다음 예제 **상태** 탭에는 잠재 고객 관리 설정에서 보고된 오류가 표시됩니다.  컨설팅 서비스에는 잠재 고객 관리가 필요하므로 이 오류를 수정해야 게시를 계속할 수 있습니다.

![오류를 표시하는 컨설팅 서비스의 상태 탭](./media/consulting-service-error.png)

Azure 애플리케이션의 최종 예제 상태는 심각한 Microsoft 검토 문제를 보여 줍니다.  이 검토 문제에 대한 자세한 정보를 포함하는 VSTS 항목에 대한 핫 링크가 포함되어 있습니다.  자세한 내용은 [Azure 애플리케이션 제품 게시](cpp-publish-offer.md)를 참조하세요.

![검토 문제를 보여 주는 Azure 앱의 상태 탭](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>다음 단계

미결 문제를 해결하거나 제품 설정을 업데이트하려면 [제품을 업데이트](./cpp-update-offer.md)해야 합니다. 
