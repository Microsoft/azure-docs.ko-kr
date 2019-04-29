---
title: 기존 Azure IoT Edge 모듈 제품 업데이트 | Microsoft Docs
description: Azure Marketplace에서 기존 IoT Edge 모듈 제품을 업데이트하는 방법입니다.
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
ms.topic: article
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ca7bed26d91c28304638e85d6da93708bfcfcada
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914127"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>기존 IoT Edge 모듈 제품 업데이트

이 문서에서는 [Cloud 파트너 포털](https://cloudpartner.azure.com/)에서 IoT Edge 모듈 제품을 업데이트한 다음, 해당 제품을 다시 게시하는 다양한 측면을 단계별로 안내합니다.

다음과 같이 제품을 업데이트하는 여러 가지 이유가 있습니다.

-  기존 SKU에 새 IoT Edge 모듈 이미지 버전 추가
-  새 SKU를 추가합니다.
-  제품 또는 개별 SKU에 대한 마켓플레이스 메타데이터 업데이트

이러한 수정 작업을 지원하기 위해 포털에서 **비교** 및 **기록** 기능을 제공합니다.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>IoT Edge 모듈 제품 또는 SKU에 대해 허용되지 않는 변경

IoT Edge 모듈 제품 또는 Azure Marketplace에 제품이 라이브 되 면 변경할 수 없는 SKU의 특성이 있습니다. 다음 설정을 변경할 수 없습니다.

-  해당 제품의 **제품 ID** 및 **게시자 ID**
-  기존 SKU의 **SKU ID**
-  버전 태그(예: `1.0.1`)
-  기존 SKU에 대한 청구/라이선스 모델 변경

## <a name="common-update-operations"></a>일반적인 업데이트 작업

다음 업데이트 작업이 빈번하게 이루어집니다.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>SKU에 대한 IoT Edge 모듈 이미지 버전 업데이트

보안 패치, 추가 기능 및 등을 사용 하 여 정기적으로 업데이트 하는 IoT Edge 모듈 이미지에 대 한 일반적인 것입니다. 이 시나리오에서는 다음 단계를 사용하여 SKU에서 참조하는 IoT Edge 모듈 이미지를 업데이트하려고 합니다.

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제품** 아래에서 업데이트하려는 제품을 찾습니다.

3.  **SKU** 탭에서 업데이트할 IoT Edge 모듈 이미지와 연결된 SKU를 선택합니다.

4.  **Edge 모듈 이미지** 아래에서 **+ 새 이미지 버전**을 선택하여 새 IoT Edge 모듈 이미지를 추가합니다.

5.  새 IoT Edge 모듈 **이미지 버전**을 제공합니다. 이미지 버전은 이전 버전과 동일한 태그 지침을 따라야 합니다. 버전 태그는 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다. 제공하는 새 버전이 모든 이전 버전보다 큰지 확인합니다.

6.  **게시**를 선택하여 새 IoT Edge 모듈 버전을 Azure Marketplace에 게시하는 워크플로를 시작합니다.

### <a name="add-a-new-sku"></a>새 SKU 추가

다음 단계를 사용하여 제품에 새 SKU를 사용할 수 있게 합니다. 

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제품** 아래에서 업데이트하려는 제품을 찾습니다.

3.  **SKU** 탭 아래에서 **새 SKU 추가**를 선택하고 팝업 창에서 **SKU ID**를 제공합니다.

4.  에 설명 된 단계를 사용 하 여 IoT Edge 모듈을 다시 게시할 [IoT Edge 모듈을 Azure Marketplace에 게시](./cpp-publish-offer.md)합니다.

5.  **게시**를 선택하여 새 SKU를 게시하는 워크플로를 시작합니다.


### <a name="update-offer-marketplace-metadata"></a>제품 마켓플레이스 메타데이터 업데이트

다음 단계를 사용하여 제품과 연결된 마켓플레이스 메타데이터를 업데이트합니다. (예: 회사 이름, 로고 등)

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제품** 아래에서 업데이트할 제품을 찾습니다.

3.  **Marketplace** 탭으로 이동합니다. 지침을 사용 합니다 [IoT Edge 모듈을 Azure Marketplace에 게시](./cpp-publish-offer.md) 문서 메타 데이터 변경 합니다.

4.  **게시**를 선택하여 변경 내용을 게시하는 워크플로를 시작합니다.

## <a name="compare-feature"></a>기능 비교

게시된 제품을 변경하는 경우 **비교** 기능을 사용하여 수행한 변경을 감사할 수 있습니다. 

**비교 기능을 사용하려면:**

1.  편집 프로세스 중에 언제든지 제품에 대해 **비교**를 선택합니다.

    ![기능 비교 단추](./media/iot-edge-module-compare.png)


2.  마케팅 자산 및 메타데이터 버전을 함께 비교합니다.


## <a name="history-of-publishing-actions"></a>게시 작업의 기록

이전의 모든 게시 작업을 보려면 Cloud 파트너 포털의 왼쪽 탐색 메뉴 모음에서 **기록** 탭을 선택합니다. Azure Marketplace 제품의 수명 동안 수행된 타임스탬프 지정 작업을 확인할 수 있습니다.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
