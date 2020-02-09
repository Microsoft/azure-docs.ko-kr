---
title: 표준 계약 | Microsoft
description: Azure Marketplace 및 AppSource에 대 한 표준 계약
services: Azure, Marketplace, Compute, Storage, Networking
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/19/2019
ms.author: v-chjen
ms.openlocfilehash: dc8edf2b6e4a1204e7edd515da9323896049eb13
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228185"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace에 대 한 표준 계약

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft는 marketplace에서 트랜잭션을 용이 하 게 하기 위해 Microsoft 상업적 marketplace에 대 한 표준 계약을 제공 합니다. 상업적 marketplace 게시자는 사용자 지정 약관을 만드는 대신 표준 계약에 따라 소프트웨어를 제공 하도록 선택할 수 있으며,이는 고객이 한 번만 심사 하 고 수락 하면 됩니다. 표준 계약은 [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)에서 찾을 수 있습니다.

제품에 대 한 사용 조건은 파트너 센터 또는 Cloud 파트너 포털에서 제품을 만들 때 정의 됩니다. 사용자 고유의 사용자 지정 사용 약관을 제공 하는 대신 Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하도록 선택할 수 있습니다.

>[!Note]
>Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 하면 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. "Or" 시나리오입니다. 표준 계약이 *나* 사용자 고유의 사용 약관에 따라 솔루션을 제공 합니다. 표준 계약의 조건을 수정 하려면 표준 계약 개정을 통해이 작업을 수행할 수 있습니다.

## <a name="standard-contract-amendments"></a>표준 계약 개정

표준 계약 개정을 사용 하면 게시자가 편의를 위해 표준 계약을 선택 하 고 해당 제품 또는 비즈니스에 맞게 사용자 지정 된 용어를 선택할 수 있습니다. 고객은 계약에 대 한 개정을 검토 하 여 Microsoft 표준 계약을 이미 검토 하 고 수락한 경우에만 해당 계약에 대 한 검토를 수행 해야 합니다.

상용 marketplace 게시자에 사용할 수 있는 개정에는 두 가지 종류가 있습니다.

* 유니버설 개정: 이러한 개정은 모든 고객에 대 한 표준 계약에 전체적으로 적용 됩니다. 유니버설 개정 구매 흐름에서 제품의 모든 고객에 게 표시 됩니다. 고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.

* Custom 개정: 이러한 개정는 Azure 테넌트 Id를 통해서만 특정 고객을 대상으로 하는 표준 계약에 대 한 특별 한 개정입니다. 게시자는 대상으로 지정할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 제안의 구매 흐름에 사용자 지정 수정 용어가 표시 됩니다.  고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.

>[!Note]
>이러한 두 가지 유형의 개정 stack이 서로 위에 있습니다. 사용자 지정 개정를 대상으로 하는 고객은 구매 중에 표준 계약에 대 한 범용 수정도 받게 됩니다.

Microsoft 상업적 marketplace에 대 한 표준 계약을 활용할 수 있습니다. Azure 응용 프로그램 (솔루션 템플릿 및 관리 되는 응용 프로그램), Virtual Machines, 컨테이너, 컨테이너 응용 프로그램, IoT Edge 모듈 및 SaaS는 다음과 같습니다. .

## <a name="customer-experience"></a>사용자 환경

Azure marketplace 또는 AppSource의 검색 환경을 사용 하는 동안 고객은 Microsoft 상용 marketplace 및 모든 universal 개정의 표준 계약으로 제품에 연결 된 용어를 볼 수 있습니다.

![Azure Portal 고객 검색 환경입니다.](media/marketplace-publishers-guide/azure-discovery-process.png)

Azure Portal 구매 프로세스가 진행 되는 동안 고객은 제품과 관련 된 용어를 Microsoft 상업적 marketplace 및 유니버설 및/또는 테넌트 별 개정에 대 한 표준 계약으로 볼 수 있습니다.

![Azure Portal 고객 구매 환경.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

고객은 AzureRmMarketplaceTerms를 사용 하 여 제품의 약관을 검색 하 고 동의할 수 있습니다. 표준 계약과 연결 된 개정 cmdlet의 출력으로 반환 됩니다.
