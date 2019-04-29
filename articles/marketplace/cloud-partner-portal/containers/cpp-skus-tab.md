---
title: Azure 컨테이너 이미지에 대한 SKU | Microsoft Docs
description: Azure 컨테이너에 대한 SKU를 구성합니다.
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
ms.date: 11/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 81f7e46e626bb061881be53e8cace36e1478e0e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472855"
---
# <a name="container-skus-tab"></a>컨테이너 SKU 탭

**새 제품** 페이지의 **SKU** 탭을 사용하면 하나 이상의 SKU를 만들어 새 제품에 연결할 수 있습니다.  다양한 SKU를 사용하여 기능 집합, 청구 모델 또는 다른 특성에 따라 솔루션을 구별할 수 있습니다.

## <a name="sku-settings"></a>SKU 설정

새 제품 만들기를 시작할 때 제품과 연결된 SKU가 없습니다. 새 SKU를 만들려면 다음 단계를 수행합니다.

1. SKU 탭에서 **새 SKU**를 선택합니다.

   ![새 SKU 프롬프트](./media/containers-sku-settings.png)

2. 필요한 SKU 및 컨테이너 정보를 입력합니다. 각 SKU는 컨테이너 이미지에 해당합니다. SKU에는 다음의 두 부분이 있습니다.

    -   SKU 메타데이터
    -   컨테이너 메타데이터

### <a name="sku-metadata"></a>SKU 메타데이터

SKU 메타데이터는 컨테이너 목록에 대한 상점 디스플레이 정보를 포함합니다.

![SKU 메타데이터](./media/containers-sku-details.png)

### <a name="container-metadata"></a>컨테이너 메타데이터

컨테이너 메타데이터에는 ACR(Azure Container Registry) 내의 이미지 리포지토리 세부 정보에 대한 참조 정보가 포함되어 있습니다. Azure Marketplace는 이 이미지를 Marketplace별 공용 레지스트리에 복사한 다음, 인증 후에 해당 이미지를 고객이 사용할 수 있습니다. Azure Marketplace 컨테이너 이미지를 사용하는 Azure 사용자의 모든 요청은 ACR이 아닌 Marketplace의 공용 레지스트리에서 제공됩니다.

![컨테이너 메타데이터](./media/containers-image-repository.png)
    
위 화면 캡처에 나와 있는 **이미지 리포지토리 세부 정보**에는 다음 필드가 포함되어 있습니다.

-   **구독 ID** - ACR이 있는 Azure 구독 ID입니다.
-   **리소스 그룹 이름** - ACR의 리소스 그룹 이름입니다.
-   **레지스트리 이름** – ACR 이름입니다.
-   **리포지토리 이름** - 리포지토리 이름입니다. 이 이름이 설정된 후 이 값을 변경할 수 없습니다. 계정의 다른 제품과의 충돌을 피하기 위해 고유한 이름을 사용합니다.
-   **사용자 이름** – ACR 이미지와 연결된 사용자 이름(관리자 사용자 이름)입니다.
-   **암호** – ACR 이미지와 연결된 암호입니다.

    >[!NOTE]
    >사용자 이름과 암호가 있어야 파트너가 게시 프로세스에 설명된 ACR에 액세스할 수 있습니다.

### <a name="image-version"></a>이미지 버전

컨테이너 이미지를 게시할 때는 하나 이상의 이미지 태그 및 SHA 다이제스트를 입력할 수 있습니다.

**이미지 태그 또는 다이제스트**
 
- 이 태그 또는 다이제스트는 `latest` 태그 및 버전 태그를 포함해야 합니다(예: `xx.xx.xx-`로 시작, 여기서 xx는 숫자). 여러 플랫폼을 대상으로 하는 [매니페스트 태그](https://github.com/estesp/manifest-tool)여야 합니다. 매니페스트 태그에서 참조하는 모든 태그는 업로드할 수 있도록 추가되어야 합니다. 
- 태그를 사용하여 컨테이너의 여러 버전을 추가할 수 있습니다. 모든 매니페스트 태그(`latest` 제외)는 `X.Y-` 또는 `X.Y.Z-`로 시작해야 합니다. 여기서 X, Y, Z는 정수입니다. <br/> 예를 들어 `latest` 태그가 `1.0.1-linux-x64`, `1.0.1-linux-arm32` 및 `1.0.1-windows-arm32`를 가리키는 경우 이러한 태그는 여기에 추가되어야 합니다.

>[!NOTE]
>테스트 중에 이미지를 식별할 수 있도록 이미지에 **테스트 태그**를 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

[Marketplace 탭](./cpp-marketplace-tab.md)을 사용하여 제품에 대한 마켓플레이스 설명을 만듭니다. 