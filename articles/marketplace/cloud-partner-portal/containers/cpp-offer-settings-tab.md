---
title: Azure 컨테이너 이미지에 대한 제품 설정 | Microsoft Docs
description: Azure 컨테이너에 대한 제품 설정을 구성합니다.
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
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a669ebad674f938d11c8e372e6f586ca3c67f03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472928"
---
# <a name="container-offer-settings-tab"></a>컨테이너 제품 설정 탭

**컨테이너 > 새 제품** 페이지는 **제품 설정** 탭에 초점을 맞춰 열립니다. 

![제안 ID](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>제품 ID 설정

**제품 ID** 아래에서 다음 표에서 설명하는 필드에 대한 정보를 제공해야 합니다. 필드 이름에 추가된 별표(*)는 필수를 나타냅니다. 

|  **필드**       |     **설명**                                                          |
|  ---------       |     ---------------                                                          |
| **제안 ID**       | 제품의 고유 식별자(게시자 프로필 내)입니다. 이 ID는 제품 URL 및 인사이트 보고서에서 볼 수 있습니다. 최대 길이가 50자이며, 소문자 영숫자 및 대시(-)를 사용할 수 있습니다. (식별자는 대시로 끝날 수 없습니다.) **참고:** 제품이 라이브 상태가 되면 이 필드를 변경할 수 없습니다. <br> 예를 들어 Contoso에서 제품 ID가 **sample-container**인 제품을 게시하면 Azure Marketplace URL인 `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`가 할당됩니다. |
| **게시자 ID**     | Azure Marketplace에서 사용되는 조직의 고유 식별자입니다. 모든 제품은 게시자 ID에 연결되어야 합니다. 제품이 저장된 후에 이 값을 변경할 수 없습니다. |
| **Name**          | 제품의 표시 이름입니다. 이 이름은 Azure Marketplace 및 Cloud 파트너 포털에 표시됩니다. 최대 50문자를 포함할 수 있습니다. 제품의 인식 가능한 브랜드 이름을 사용하는 것이 좋습니다. 제품의 마케팅 전략인 경우가 아니면 조직의 이름을 포함하지 마십시오. 다른 웹 사이트 및 게시에서 이 제품을 마케팅하는 경우 모든 게시에서 이름이 정확하게 동일한지 확인합니다. |
|  |  |

**저장**을 선택하여 제품 설정을 저장합니다.

## <a name="next-steps"></a>다음 단계

[SKU](./cpp-skus-tab.md) 탭을 사용하여 제품의 SKU를 구성합니다.
