---
title: Azure IoT Edge 모듈 제품 만들기 | Microsoft Docs
description: Marketplace에 새 IoT Edge 모듈을 게시하는 방법입니다.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 69ee0c0add2895b30a60db577cc11c83a0e00bb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60911529"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Cloud 파트너 포털을 사용하여 새 IoT Edge 모듈 제품 만들기

이 문서에서는 Azure Marketplace에 IoT Edge 모듈 제품 항목을 만들고 게시하는 방법을 설명합니다. 모든 제품은 Azure Marketplace에 고유 엔터티로 나타나 있으며 하나 이상의 SKU와 연결됩니다.  IoT Edge 모듈 제품은 다음 자산 그룹 및 지원 서비스로 구성됩니다.

|  **자산 그룹**   |  **설명**  |
|  ---------------   |  ---------------  |
|    SKU            |  제품의 가장 작은 배포 가능한 단위입니다. 단일 제품(product 클래스)은 제품과 관련된 여러 SKU를 가질 수 있습니다. SKU를 사용하여 지원되는 기능과 청구 모델을 구분할 수 있습니다. |
|  Marketplace       | 마케팅, 법적 정보 및 잠재 고객 관리 자산 및 사양이 포함됩니다.  <ul><li> 마케팅 자산에는 제안 이름, 설명 및 로고가 포함됩니다.</li> <li> 법적 자산은 개인 정보 취급 방침, 사용 약관 및 기타 법률 문서를 포함합니다.</li>  <li> 잠재 고객 관리 정책을 사용하면 Azure Marketplace 최종 사용자 포털에서 잠재 고객을 처리하는 방법을 지정할 수 있습니다.</li> </ul> |
| 지원            | 지원 연락처 및 정책 정보가 포함됩니다. |


## <a name="new-offer-form"></a>새 제품 양식 

[Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인한 다음, 왼쪽 메뉴 모음에서 **+ 새 제품**을 선택합니다. 새 제품 메뉴에서 **IoT Edge 모듈**을 선택하여 **새 제품** 양식을 표시하고 새 ioT Edge 모듈 제품에 대한 자산을 정의하는 과정을 시작합니다. 

![새 IoT Edge 모듈 제품 사용자 인터페이스 선택](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>다음 단계

IoT Edge 모듈 제품 유형에 대한 **새 제품** 페이지는 새 제품을 만드는 데 사용할 탭 집합 및 양식 필드를 제공합니다. 다음 각 문서는 탭을 사용하여 새 IoT Edge 모듈 제품에 대한 자산 그룹 및 지원 서비스를 정의하는 방법을 설명합니다.

- [제품 설정 탭](./cpp-offer-settings-tab.md)
- [SKU 탭](./cpp-skus-tab.md)
- [Marketplace 탭](./cpp-marketplace-tab.md)
- [지원 탭](./cpp-support-tab.md)
