---
title: Azure 컨테이너 이미지 제품 게시 | Microsoft Docs
description: Azure 컨테이너 제품을 게시하는 방법입니다.
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
ms.openlocfilehash: 7533d1a133c9c474bc39f0f64c5f1a8183ab30f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472894"
---
# <a name="publish-container-offer"></a>컨테이너 제품 게시

 **새 제품** 페이지를 사용하여 새 제품을 만든 후 제품을 게시할 수 있습니다. **게시**를 선택하여 게시 프로세스를 시작합니다.

다음 다이어그램은 제안을 "라이브 상태"로 만들기 위한 게시 프로세스의 주요 단계를 보여줍니다.

![컨테이너 제품에 대한 게시 단계](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>게시 단계에 대한 자세한 설명

다음 표에서는 각 게시 단계를 설명합니다. 각 단계를 완료하는 예상 시간이 지정됩니다.


|  **게시 단계**           | **Time**    | **설명**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 필수 구성 요소 유효성 검사         | 15분   | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                        |
| 인증                  | 1주 | Azure 인증 팀에서 제안을 분석합니다. 이 제품은 바이러스, 맬웨어, 안전 규정 준수 및 보안 문제에 대해 검사됩니다. 모든 자격 조건을 충족하는지 제품을 확인합니다. 자세한 내용은 [필수 구성 요소](./cpp-prerequisites.md) 및 [기술 자산 준비](./cpp-create-technical-assets.md)를 참조하세요. 문제가 발견되면 피드백이 제공됩니다. |
| 패키징 | 1시간  | 제안의 기술 자산이 고객의 사용에 맞게 패키지되고, 잠재 고객 시스템이 구성 및 설정됩니다. |
|  게시자 승인             |  -        | 제안이 라이브 상태가 되기 전에 최종 게시자가 검토 및 확인합니다. 선택한 구독에 제안을 배포하여(제안 정보 단계에서) 모든 요구 사항을 충족하는지 확인할 수 있습니다.  제안을 다음 단계로 넘길 수 있도록 **라이브로 전환**을 선택합니다. |
| 패키징                 | 1시간 | 완성된 제품은 마켓플레이스 프로덕션 시스템 및 지역에 복제됩니다. | 
| 라이브                           | 4일 |제안이 릴리스되고, 필요한 지역에 복제되고, 공개적으로 사용할 수 있게 됩니다. |

10영업일 내에 게시 프로세스를 완료하고 제안을 릴리스하면 됩니다. 게시 프로세스를 완료하면 컨테이너 제품이 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)에 나열됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Marketplace에서 기존 컨테이너 제품 업데이트](./cpp-update-existing-offer.md)
