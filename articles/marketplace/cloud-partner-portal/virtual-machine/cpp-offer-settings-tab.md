---
title: Cloud 파트너 포털의 Azure Marketplace에 대한 Virtual Machine 제안 설정 탭 | Microsoft Docs
description: Azure Marketplace VM 제안을 만드는 데 사용되는 제안 설정 탭에 대해 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844364"
---
# <a name="virtual-machine-offer-settings-tab"></a>가상 머신 제안 설정 탭

가상 머신에 대한 **새 제안** 페이지는 **제안 설정**이라는 첫 번째 탭에서 열립니다.  필드 이름에 별표(*)가 추가되면 필수 항목임을 나타냅니다. 

![가상 머신에 대한 새 제안 페이지](./media/publishvm_004.png)

**제안 설정** 탭에서 제공해야 하는 필수 필드는 다음과 같습니다.

|  **필드**       |     **설명**                                                          |
|  ---------       |     ---------------                                                          |
| **제안 ID**       | 게시자 프로필 내에 있는 제안에 대한 고유 식별자입니다. 이 식별자는 제품 URL, Azure Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 최대 길이는 50자이며, 소문자 영숫자와 대시(-)로만 구성할 수 있지만 대시로 끝날 수 없습니다. 제안이 라이브 상태가 되면 이 필드를 변경할 수 없습니다. <br> 예를 들어 Contoso에서 제안 ID가 **sample-vm**인 제안을 게시하면 Azure Marketplace URL인 `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`가 할당됩니다. |
| **게시자**     | Azure Marketplace에서 사용되는 조직의 고유 식별자입니다. 모든 제안은 게시자 ID와 연결되어야 합니다. 제안이 저장되면 이 값을 수정할 수 없습니다. |
| **Name**          | 제안에 대한 표시 이름입니다. 이 이름은 Azure Marketplace 및 Cloud 파트너 포털에 표시됩니다. 최대 50문자를 포함할 수 있습니다. 여기서 제공하는 지침은 제품의 인식 가능한 브랜드 이름을 포함하는 것입니다. 마케팅 전략이 아닌 경우 조직 이름을 여기에 포함시키지 않습니다. 다른 웹 사이트 및 게시에서 이 제안을 마케팅하는 경우 모든 게시에서 이름이 정확히 동일한지 확인합니다. |
|  |  |
 
**저장**을 선택하여 진행 상황을 저장합니다. 다음 탭에서는 [SKU](./cpp-skus-tab.md)를 제안에 추가합니다.
