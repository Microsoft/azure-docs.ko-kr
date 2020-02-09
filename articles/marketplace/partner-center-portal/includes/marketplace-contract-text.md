---
title: 포함 파일
description: Microsoft 상업적 marketplace 표준 계약에 대 한 텍스트 파일 포함
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: ChJenk
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: v-chjen
ms.custom: include file
ms.openlocfilehash: 76603ef30cefa14018a7c6b976eb8541b9b608f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76760902"
---
Microsoft는 상업적 marketplace에 대 한 표준 계약 템플릿을 제공 합니다.

- **Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하나요?**

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft는 marketplace에서 트랜잭션을 용이 하 게 하는 Microsoft 상업적 marketplace에 대 한 표준 계약을 제공 합니다. 상업적 marketplace 게시자는 사용자 지정 약관을 만드는 대신 표준 계약에 따라 소프트웨어를 제공 하도록 선택할 수 있으며,이는 고객이 한 번만 심사 하 고 수락 하면 됩니다. 표준 계약은 https://go.microsoft.com/fwlink/?linkid=2041178 에서 찾을 수 있습니다.

"상용 marketplace에 대 한 표준 계약 사용" 확인란을 선택 하 여 사용자 고유의 사용자 지정 약관을 제공 하는 대신 표준 계약을 사용 하도록 선택할 수 있습니다.

![표준 계약 확인란 사용](./media/use-standard-contract.png)

> [!NOTE]
> Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 하면 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. "Or" 시나리오입니다. 표준 계약이 **나** 사용자 고유의 사용 약관에 따라 솔루션을 제공 합니다. 표준 계약의 조건을 수정 하려면 표준 계약 개정을 통해이 작업을 수행할 수 있습니다.

**표준 계약 개정**

표준 계약 개정 let은 간소화를 위해 표준 계약 용어를 선택 하 고 제품 또는 비즈니스에 대 한 용어를 사용자 지정 합니다. 고객은 계약에 대 한 개정을 검토 하 여 Microsoft 표준 계약을 이미 검토 하 고 수락한 경우에만 해당 계약에 대 한 검토를 수행 해야 합니다.

상용 marketplace 게시자에 사용할 수 있는 개정에는 두 가지 종류가 있습니다.

- 유니버설 개정: 이러한 개정은 모든 고객에 대 한 표준 계약에 전체적으로 적용 됩니다. 유니버설 개정 구매 흐름에서 제품의 모든 고객에 게 표시 됩니다. 고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.
- Custom 개정: 이러한 개정는 Azure 테넌트 Id를 통해서만 특정 고객을 대상으로 하는 표준 계약에 대 한 특별 한 개정입니다. 게시자는 대상으로 지정할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 제안의 구매 흐름에 사용자 지정 수정 용어가 표시 됩니다.  고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.

>[!NOTE]
> 이러한 두 가지 유형의 개정 stack이 서로 위에 있습니다. 사용자 지정 개정를 대상으로 하는 고객은 구매 중에 표준 계약에 대 한 범용 수정도 받게 됩니다.

**Microsoft의 상용 marketplace에 대 한 표준 계약에 대 한 범용 수정 용어**:이 상자에 유니버설 수정 용어를 입력 합니다. 제품 당 단일 범용 수정 기능을 제공할 수 있습니다. 이 상자에는 개수에 제한 없이 문자를 입력할 수 있습니다. 이러한 용어는 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에 게 표시 됩니다.

**Microsoft의 상용 marketplace에 대 한 표준 계약에 대 한 사용자 지정 수정 용어**: **사용자 지정 수정 용어 추가**를 선택 하 여 시작 합니다. 제품 당 최대 10 개의 사용자 지정 수정 단어를 제공할 수 있습니다.

- **사용자 지정 수정 용어**: 사용자 지정 수정 약관 상자에 사용자 지정 수정 용어를 입력 합니다. 이 상자에는 개수에 제한 없이 문자를 입력할 수 있습니다. 이러한 사용자 지정 약관에 대해 지정 하는 테넌트 Id의 고객만 Azure Portal의 제품 구매 흐름에 사용자 지정 수정 용어가 표시 됩니다.  
- **테넌트 id** (필수): 각 사용자 지정 수정은 최대 20 개의 테넌트 id를 대상으로 지정할 수 있습니다. 사용자 지정 수정 항목을 추가 하는 경우 하나 이상의 테넌트 ID를 제공 해야 합니다. 테넌트 ID는 Azure에서 고객을 식별 합니다. Portal.azure.com > Azure Active Directory > 속성으로 이동 하 여이 ID를 고객에 게 요청할 수 있습니다. 디렉터리 ID 값은 테넌트 ID (예: 50c464d3-4930-494c-963c-1e951d15360e)입니다. 사용자의 도메인 이름 URL을 사용 하 여 고객의 테넌트 ID를 조회할 수도 있습니다. [Microsoft Azure 및 Office 365 테넌트 id?](https://www.whatismytenantid.com)
- **설명** (선택 사항): 필요에 따라 테넌트 ID에 대 한 친숙 한 설명을 제공 하 여, 수정으로 대상으로 하는 고객을 식별할 수 있습니다.

**사용 약관**

사용자 고유의 사용자 지정 사용 약관을 제공 하려는 경우 사용 약관 필드에 해당 조건을 입력 하도록 선택할 수 있습니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다. 사용 약관에 더 긴 설명이 필요한 경우 사용 약관을 찾을 수 있는이 필드에 단일 URL 링크를 입력 합니다. 고객이 활성 링크로 표시 됩니다.

고객은 이러한 약관에 동의 해야 제품을 사용해 볼 수 있습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.
다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.
