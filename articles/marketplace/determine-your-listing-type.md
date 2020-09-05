---
title: 게시 옵션 결정 - Microsoft 상업용 Marketplace
description: 이 문서에서는 Microsoft AppSource 및 Azure Marketplace에 제품을 게시하기 위한 자격 조건 및 요구 사항을 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 4a27ace4e45d00a2fbb35b9e0e3e9b7c03383681
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705880"
---
# <a name="determine-your-publishing-option"></a>게시 옵션 확인

제품에 대해 선택한 게시 옵션은 적격성 요구 사항 및 상업용 Marketplace GTM 혜택 모두와 직접 관련이 있습니다. 무엇보다도 게시 옵션 및 제품 유형의 선택은 사용자가 귀하의 상업용 Marketplace 제품과 상호 작용하는 방법을 정의합니다.

제품을 구성 하려면 상업적 marketplace 온라인 상점에서 제품이 표시 되는 방법 및 위치를 제어 하는 게시 옵션, 제품 유형 및 구성 및 작업 호출에 대 한 다음과 같은 주요 상업적 marketplace 개념을 이해 해야 합니다.

이 문서에서는 다음에 대해 알아봅니다.

- 솔루션에 적합 한 온라인 저장소를 확인 하는 방법
- 각 온라인 저장소에서 사용할 수 있는 게시 옵션과 동작에 대 한 호출을 사용할 수 있습니다.
- 각 게시 옵션에 사용할 수 있는 제품 유형을 제공 합니다.

## <a name="commercial-marketplace-publishing-options"></a>상업용 Marketplace 게시 옵션

다음 표에서는 Microsoft AppSource 및 Azure Marketplace의 제품 유형에 대한 게시 옵션을 보여 줍니다.

|   | **목록(연락처)**  | **목록(평가판)**  | **Free** | **BYOL** | **거래**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtual Machine** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure 앱(다중 VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **컨테이너 이미지** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge 모듈** |  |  | Azure Marketplace | Azure Marketplace |   |
| **관리되는 서비스** |  |  |  | Azure Marketplace |   |
| **컨설팅 서비스** | 온라인 상점 모두 |  |  |  |   |
| **SaaS 앱** | 온라인 상점 모두 | 온라인 상점 모두 | 온라인 상점 모두 |  | 온라인 매장 모두 * |
| **Microsoft 365 앱** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 추가 기능** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Microsoft AppSource의 SaaS 앱 거래 제품은 현재 신용 카드로만 제공합니다.

&#42;&#42; Microsoft 365 제품은 무료로 설치할 수 있으며 라이선스 서비스로 SaaS 제품을 통해 수익화할 수 있습니다. 자세한 내용은 [Microsoft 상업용 Marketplace를 통해 Office 365 추가 기능으로 수익 창출](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)을 참조하세요.

## <a name="choose-a-listing-option"></a>목록 옵션 선택

사용 가능한 목록 옵션은 잠재 고객 참여를 제공 하는 동시에 잠재 고객의 공유 및 [상업적 marketplace 혜택](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)에 대 한 액세스를 제공 합니다. 게시 옵션에 해당 하는 나열 옵션을 확인 합니다.

| **게시 옵션**    | **설명**  |
| :------------------- | :-------------------|
| **목록** | 상업용 Marketplace 사용자가 **연락처** CTA를 통해 고객과 연결하도록 요청할 수 있는 애플리케이션 또는 서비스의 단순한 목록입니다. |
| **평가판** | 상업용 Marketplace를 사용하여 솔루션의 평가판 환경의 자동 프로비저닝 및 검색 기능을 향상시키면서 잠재 사용자가 구입 전에 제한된 시간 동안 무료로 SaaS, IaaS 또는 Microsoft 앱 내 환경을 사용하게 할 수 있습니다. 평가판 게시 옵션에 사용된 CTA(calls–to–action)는 **평가판** 또는 **시험 사용** 중 하나입니다. |
| **BYOL** | 상업용 Marketplace를 사용하여 검색 기능을 개선하고 솔루션의 프로비저닝을 자동화하며 재무 트랜잭션은 별도로 완료합니다. BYOL 제품 형식은 온-프레미스에서 클라우드로의 마이그레이션에 적합합니다. CTA(Call-To-Action)는 **지금 사용**입니다.
| **거래** | 거래 제품은 상업용 Marketplace를 통해 판매됩니다. Microsoft가 청구 및 수금을 담당합니다. CTA(Call-To-Action)는 **지금 사용**입니다.|

> [!Note]
> Transact 게시 옵션을 사용할 때는 제품 유형을 선택하고 제품을 만들기 전에 가격, 청구, 송장 처리 및 지급 고려 사항을 이해하는 것이 중요합니다. 자세히 알아보려면 [상업용 Marketplace의 거래 기능](./marketplace-commercial-transaction-capabilities-and-considerations.md) 문서를 참조하세요.

## <a name="selecting-an-online-store"></a>온라인 스토어 선택

각 온라인 저장소는 고유한 고객 요구 사항 및 대상 관련 대상을 제공 합니다. 제품 유형, 거래 기능 및 범주에 따라 제품이 게시 되는 위치가 결정 됩니다. 범주 및 하위 범주는 대상 그룹에 따라 각 온라인 저장소에 매핑됩니다.

**Microsoft AppSource** 는 Dynamics 365, Microsoft 365 및 Power Platform에 대 한 lob (기간 업무) 또는 업계 솔루션 및 컨설팅 서비스를 검색 하는 비즈니스 사용자를 대상으로 합니다.

**Azure Marketplace** 는 azure 용으로 작성 된 솔루션 및 azure 사용을 가속화 하는 컨설팅 서비스를 찾는 IT 전문가 및 개발자를 대상으로 합니다.

대상 사용자에 게 가장 적합 한 범주 및 하위 범주를 선택 합니다. 예를 들어, 웹 응용 프로그램 방화벽은 계획 된 대상이 IT 전문가 이므로 보안 범주 아래 Azure Marketplace에 게시 해야 합니다. 대상 사용자가 비즈니스 사용자 이기 때문에 계약 관리 앱을 판매 범주 아래의 AppSource에 게시 해야 합니다. 잘못 된 범주 또는 하위 범주를 선택 하면 제품이 잘못 된 온라인 저장소에 게시 될 수 있습니다.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>두 온라인 저장소에 게시 (SaaS 제품에만 해당)

SaaS 제품은 Azure Marketplace 또는 AppSource에 게시할 수 있습니다. SaaS 제품이 기술 사용자 (Azure Marketplace)와 비즈니스 대상 (AppSource)을 *모두* 사용 하는 경우 각 온라인 저장소에 적용 되는 범주 및/또는 하위 범주를 선택 합니다. 두 온라인 저장소에 게시 된 제품에는 IT 전문가 *및* 비즈니스 사용자로 확장 되는 가치 제안이 있어야 합니다.

> [!IMPORTANT]
> 요금제를 사용 하는 SaaS 제품은 Azure Marketplace 및 Azure Portal를 통해 제공 됩니다. 개인 계획만 포함 된 SaaS 제품은 Azure Portal를 통해 제공 됩니다.

| 요금제 청구 | 공개 계획 | 비공개 계획 | 사용 가능한 위치: |
|---|---|---|---|
| 예             | 예         | 아니요           | Azure Marketplace 및 Azure Portal |
| 예             | 예         | 예          | Azure Marketplace 및 Azure Portal * |
| 예             | 아니요          | 예          | Azure Portal만 |
| 아니요              | 아니요          | 예          | Azure Portal만 |

&#42; 제품의 비공개 요금제를 통해서만 사용할 수 있습니다 Azure Portal

예를 들어, 요금제를 사용 하는 제품 및 비공개 요금제 (공개 요금제 없음)는 Azure Portal 고객이 구매할 예정입니다. [Microsoft 상업적 marketplace의 개인 제품](private-offers.md)에 대해 자세히 알아보세요.

### <a name="categories"></a>범주

범주와 하위 범주는 대상 그룹에 따라 각 온라인 저장소에 매핑됩니다. 제품 및 의도 한 대상에 가장 적합 한 범주와 하위 범주를 선택 합니다. 선택 옵션:

- 하나 이상의 범주 (최대 2 개) 기본 및 보조 범주를 선택할 수 있는 옵션이 있습니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 하위 범주를 선택 하지 않은 경우에도 선택한 범주에서 제품을 검색할 수 있습니다.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>다음 단계

- [게시 옵션을 결정](./publisher-guide-by-offer-type.md)했으면 제품을 제공하는 데 사용할 제품 유형을 선택할 준비가 된 것입니다.
- 제품의 선택 및 구성을 완료하려면 제품 유형 섹션별 게시 옵션에서 자격 요구 사항을 검토합니다.
- 솔루션이 제품 유형 및 구성에 매핑되는 방법에 대 한 예제는 온라인 스토어에서 게시 패턴을 검토 합니다.
