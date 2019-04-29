---
title: Azure Marketplace에서 Virtual Machine 제안 만들기 | Microsoft Docs
description: Azure Marketplace용 새 VM(가상 머신) 제안을 만드는 데 필요한 단계를 나열합니다.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.openlocfilehash: a3124ca2f163291d266fba77255d2ee98d30fd23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744167"
---
# <a name="create-virtual-machine-offer"></a>Virtual Machine 제안 만들기

이 섹션에서는 Azure Marketplace용 새 VM(가상 머신) 제안 요청을 만드는 데 필요한 단계를 나열합니다.  모든 제안은 Azure Marketplace에서 자체의 고유한 엔터티로 나타나며 하나 이상의 SKU와 연결됩니다.  VM 제안을 구성하는 자산 및 지원 서비스의 그룹은 다음과 같습니다. 

![VM 제안에 대한 자산](./media/publishvm_002.png)

설명:

|  **자산 그룹**   |  **설명**  |
|  ---------------   |  ---------------  |
|    SKU            |  제안의 가장 작은 구매 가능 단위입니다. 단일 제안(제품 클래스)은 여러 개의 SKU를 연결하여 지원되는 기능, VM 이미지 유형 및 청구 모델을 구별할 수 있습니다. |
|  Marketplace       | 마케팅, 법적 정보 및 잠재 고객 관리 자산 및 사양이 포함됩니다.  <ul><li> 마케팅 자산에는 제안 이름, 설명 및 로고가 포함됩니다.</li> <li> 법적 정보 자산에는 개인정보처리방침, 사용 약관 및 기타 법률 문서가 포함됩니다.</li>  <li> 잠재 고객 관리 정책을 사용하면 Azure Marketplace 최종 사용자 포털에서 잠재 고객을 처리하는 방법을 지정할 수 있습니다.</li> </ul> |
| 지원            | 지원 연락처 및 정책 정보가 포함됩니다. |
| 시험 사용         | 최종 사용자가 제안을 구입하기 전에 해당 제안을 테스트할 수 있도록 하는 자산을 정의합니다. |
|  |  |


## <a name="new-offer-form"></a>새 제안 양식

[Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인하면 왼쪽 메뉴 모음에서 **+ 새 제안** 항목을 클릭합니다. 결과 메뉴에서 **Virtual Machines**를 클릭하여 **새 제안** 양식을 표시하고, 새 VM 제안에 대한 자산을 정의하는 프로세스를 시작합니다. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![새 가상 머신 제안에 대한 사용자 인터페이스 선택](./media/publishvm_003.png)

> [!WARNING]
> **Virtual Machines** 옵션이 표시되지 않거나 활성화되지 않으면 이 제안 유형을 만들 수 있는 권한이 계정에 없습니다.  개발자 계정에 등록하는 것을 포함하여 이 제안 유형에 대한 모든 [필수 조건](./cpp-prerequisites.md)이 충족되었는지 확인합니다.


## <a name="next-steps"></a>다음 단계

이 섹션의 이후 항목에서는 **새 제안** 페이지(VM 제안 유형의 경우)의 탭을 미러링합니다.  각 문서에서는 관련 탭을 사용하여 새 VM 제안에 대한 자산 그룹과 지원 서비스를 정의하는 방법에 대해 설명합니다.

- [제품 설정 탭](./cpp-offer-settings-tab.md)
- [SKU 탭](./cpp-skus-tab.md)
- [시험 사용 탭](./cpp-test-drive-tab.md)
- [Marketplace 탭](./cpp-marketplace-tab.md)
- [지원 탭](./cpp-support-tab.md)
