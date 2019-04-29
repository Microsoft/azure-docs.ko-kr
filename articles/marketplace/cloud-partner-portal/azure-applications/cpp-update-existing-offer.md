---
title: 기존 Azure 애플리케이션 제품 업데이트 | Microsoft Docs
description: Azure Marketplace에서 기존 Azure 애플리케이션 제품을 업데이트하는 방법입니다.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1019c06afbdab0a6b0e172133da316aea01a5504
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744621"
---
# <a name="update-an-existing-azure-application-offer"></a>기존 Azure 애플리케이션 제품 업데이트

제품이 게시되고 활성화된 후 제품을 대상으로 다양한 종류의 업데이트를 수행할 수 있습니다. 새 버전의 제품에 적용할 변경 내용을 저장하고 Marketplace에 반영되도록 다시 게시해야 합니다. 이 문서에서는 [Cloud 파트너 포털](https://cloudpartner.azure.com/)에서 관리되는 애플리케이션 제품을 업데이트하는 다양한 측면을 단계별로 안내합니다.

다음과 같이 제품을 업데이트하는 여러 가지 이유가 있습니다.

- 기존 SKU에 새 이미지 버전 추가
- 새 SKU를 추가합니다.
- 제품 또는 개별 SKU에 대한 마켓플레이스 메타데이터 업데이트

이러한 수정 작업을 지원하기 위해 포털에서 **비교** 및 **기록** 기능을 제공합니다.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Azure 애플리케이션 제품 또는 SKU에 대한 허용되지 않는 변경 내용

제품이 Azure Marketplace에서 라이브가 되면 변경할 수 없는 컨테이너 제품 또는 SKU의 특성이 있습니다. 다음 설정을 변경할 수 없습니다.

- 해당 제품의 제품 ID 및 게시자 ID
- 기존 SKU의 SKU ID
- 버전 태그(예: `1.0.1`)
- 기존 SKU에 대한 청구/라이선스 모델 변경

## <a name="common-update-operations"></a>일반적인 업데이트 작업

다음 업데이트 작업이 빈번하게 이루어집니다.

### <a name="update-image-version-for-a-sku"></a>SKU에 대한 이미지 버전 업데이트

일반적으로 이미지는 보안 패치, 추가 기능 등으로 정기적으로 업데이트됩니다. 이 시나리오에서는 다음 단계를 사용하여 SKU에서 참조하는 이미지를 업데이트하려고 합니다.

1. [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. **모든 제품** 아래에서 업데이트하려는 제품을 찾습니다.
3. **SKU** 탭에서 업데이트할 이미지와 연결된 SKU를 선택합니다.
4. **+ 새 이미지 버전**을 선택하여 새 이미지를 추가합니다.
5. 새 이미지 버전을 제공합니다. 이미지 버전은 이전 버전과 동일한 태그 지침을 따라야 합니다. 버전 태그는 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다. 제공하는 새 버전이 모든 이전 버전보다 큰지 확인합니다.
6. **게시**를 선택하여 새 컨테이너 이미지 버전을 Azure Marketplace에 게시하는 워크플로를 시작합니다.

### <a name="add-a-new-sku"></a>새 SKU 추가

다음 단계를 사용하여 제품에 새 SKU를 사용할 수 있게 합니다.

1. [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. **모든 제품** 아래에서 업데이트하려는 제품을 찾습니다.
3. **SKU** 탭 아래에서 **새 SKU 추가**를 선택하고 팝업 창에서 **SKU ID**를 제공합니다.
4. [Azure 애플리케이션 제품 게시](./cpp-publish-offer.md)에 설명된 단계를 사용하여 제품을 다시 게시합니다.
5. **게시**를 선택하여 새 SKU를 게시하는 워크플로를 시작합니다.

### <a name="update-offer-marketplace-metadata"></a>제품 마켓플레이스 메타데이터 업데이트

다음 단계를 사용하여 제품과 연결된 마켓플레이스 메타데이터를 업데이트합니다. (예: 회사 이름, 로고 등)

1. [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. **모든 제품** 아래에서 업데이트할 제품을 찾습니다.
3. **Marketplace** 탭으로 이동합니다. [Azure 애플리케이션 제품 게시](./cpp-publish-offer.md)의 지침에 따라 메타데이터를 변경합니다.
4. **게시**를 선택하여 변경 내용을 게시하는 워크플로를 시작합니다.
 
>[!Note]
>클라우드 솔루션 공급자 (CSP) 파트너 채널 옵트인를 출시 되었습니다.  참조 하세요 [클라우드 솔루션 공급자](../../cloud-solution-providers.md) Microsoft CSP를 통해 제품을 마케팅 하는 방법은 채널 파트너입니다.

## <a name="deleting-an-existing-offer"></a>기존 제품 삭제

Marketplace에서 제품을 제거할 수 있습니다. 제품을 삭제해도 현재 제품의 구매에는 영향을 주지 않습니다. 이러한 고객 구매는 이전처럼 진행됩니다. 그러나 삭제가 완료된 후에는 해당 제품을 새로 구매할 수 없습니다.

제품 종료는 귀하와 기존 고객 간의 서비스 및/또는 사용권 계약이 종료되는 프로세스입니다.
제품 제거 및 종료와 관련된 지침 및 정책은 Microsoft Marketplace 게시자 계약(섹션 7 참조) 및 참여 정책(섹션 6.2 참조)을 따릅니다.

자세한 내용은 [Azure Marketplace에서 제품/SKU 삭제](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete)를 참조하세요.

## <a name="compare-feature"></a>기능 비교

게시된 제품을 변경하는 경우 비교 기능을 사용하여 수행한 변경을 감사할 수 있습니다.

비교 기능을 사용하려면:

1. 편집 프로세스 중에 언제든지 제품에 대해 비교를 선택합니다.
2. 마케팅 자산 및 메타데이터 버전을 함께 비교합니다.

## <a name="history-of-publishing-actions"></a>게시 작업의 기록

이전의 모든 게시 작업을 보려면 Cloud 파트너 포털의 왼쪽 탐색 메뉴 모음에서 **기록** 탭을 선택합니다. Azure Marketplace 제품의 수명 동안 수행된 타임스탬프 지정 작업을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 애플리케이션 제품](./cpp-azure-app-offer.md)