---
title: 마켓플레이스 제품 삭제 - Azure Marketplace | Microsoft Docs
description: Cloud 파트너 포털을 사용하여 Azure 및 AppSource Marketplace에서 제품 삭제
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
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 1d5d02d65dd3dcf5978639818fba4ebe36ffaaff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825152"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Azure Marketplace 및 AppSource 제품 또는 SKU 삭제

다양한 이유로 인해, 다음 두 가지 형태로 Microsoft 마켓플레이스에서 제품을 철회할 수 있습니다.

- ‘제품 제거’는 새 고객이 더 이상 제품을 구매하거나 배포할 수 없게 하지만, 사용권 계약 및 관련 법률에 따라 지원해야 하는 기존 고객에게는 영향을 주지 않습니다. 
- ‘제품 종료’는 귀하와 기존 고객 간의 서비스 및/또는 사용권 계약이 종료되는 프로세스입니다. 

지침 및 제품 제거 및 종료와 관련 된 정책을 받습니다 [Microsoft Marketplace 판매자 계약](https://go.microsoft.com/fwlink/?LinkID=699560) 하며 [참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (섹션 [제공 일시 중단 및 제거](https://docs.microsoft.com/en-us/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

이 문서에서는 지원되는 다양한 삭제 시나리오 및 각 시나리오를 수행하는 데 필요한 단계를 설명합니다.  

> [!NOTE]
> **편집기** 탭의 도구 모음에서 **삭제** 단추를 선택하면 게시되지 않은 제품을 삭제할 수 있습니다.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Azure Marketplace에서 게시된 SKU 삭제

다음 단계를 사용하여 Azure Marketplace에서 게시된 SKU를 삭제할 수 있습니다.

1.  [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2.  **모든 제품** 페이지에서 제품을 선택합니다.  **편집기** 탭에 제품이 표시됩니다.
3.  왼쪽 도구 모음에서 **SKU** 탭을 선택합니다. 
4.  삭제할 SKU를 선택하고 **삭제** 단추를 클릭합니다.
5.  제품을 Azure Marketplace에 [다시 게시](./cpp-publish-offer.md)합니다.

수정된 제품을 Azure Marketplace에 게시한 후에는 선택한 SKU가 Azure Marketplace 및 Azure Portal에 더 이상 나열되지 않습니다.


## <a name="roll-back-to-a-previous-sku-version"></a>이전 SKU 버전으로 롤백

다음 단계를 수행하여 Azure Marketplace에서 게시된 SKU의 현재 버전을 삭제할 수 있습니다. 이 프로세스가 완료되면 SKU가 이전 버전으로 롤백됩니다.

1. [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. **모든 제품** 페이지에서 제품을 선택합니다.  **편집기** 탭에 제품이 표시됩니다.
3. 왼쪽 도구 모음에서 **SKU** 탭을 선택합니다. 
4. 디스크 버전 목록에서 연관된 솔루션 자산의 최신 버전을 삭제합니다.  제품 유형에 따라 이 필드는 **디스크 버전**, **패키지 버전** 또는 유사한 자산일 수 있습니다. 
5. 제품을 Azure Marketplace에 [다시 게시](./cpp-publish-offer.md)합니다.

수정된 제품을 Azure Marketplace에 게시한 후에는 나열된 SKU의 현재 버전이 Azure Marketplace와 Azure Portal에 더 이상 나열되지 않습니다.  SKU가 이전 버전으로 롤백됩니다.


## <a name="delete-a-live-offer"></a>라이브 제품 삭제

라이브 제품 제거와 관련된 다양한 절차, 비즈니스 및 법적 측면이 있습니다. Azure Marketplace에서 라이브 제품을 제거하려면 다음 단계를 수행하여 지원 팀의 안내를 받으세요.

1.  [인시던트 만들기](https://go.microsoft.com/fwlink/?linkid=844975) 페이지를 사용하거나 [Cloud 파트너 포털](https://cloudpartner.azure.com/)의 오른쪽 위에 있는 **지원**을 클릭하여 지원 티켓을 제기합니다.

2.  **문제 유형** 목록에서 특정 제품 유형을 선택하고 **범주** 목록에서 **게시된 제품 제거**를 선택합니다.

3.  요청을 제출합니다.

지원 팀이 제품 삭제 프로세스를 안내합니다.

> [!NOTE]
> 제품(또는 SKU)을 삭제해도 해당 제품(또는 SKU)의 현재 구매에는 영향을 주지 않습니다. 이러한 구매는 이전처럼 진행됩니다. 그러나 삭제된 쿠폰이나 SKU는 향후 구매 시 사용할 수 없습니다.


## <a name="next-steps"></a>다음 단계

제품 관리에 사용되는 기본 작업을 확인했으면, Microsoft [마켓플레이스 제품](../cpp-marketplace-offers.md) 인스턴스를 만들 준비가 된 것입니다.
