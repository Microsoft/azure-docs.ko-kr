---
title: StorSimple 지역 가용성 | Microsoft Docs
description: 다양한 StorSimple 디바이스 모델을 사용할 수 있는 Azure 지역에 대해 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: ff2ea89c257a4941c801f3056450e005a9b37039
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628905"
---
# <a name="available-regions-for-your-storsimple"></a>StorSimple을 사용할 수 있는 지역

## <a name="overview"></a>개요

성능, 요구 사항 및 데이터 위치 관련 기본 설정에 대한 고객의 요구 사항을 충족하기 위해 전 세계 여러 지역에서 Azure 데이터 센터가 운영됩니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함하는 지리적 위치 내의 영역입니다.

Azure 지역을 선택하는 것은 매우 중요하며 지역 선택은 데이터 상주 및 주권, 서비스 가용성, 성능, 비용 및 중복성 등의 요인에 의해 영향을 받습니다. 지역 선택 방법에 대한 자세한 내용은 [적합한 Azure 지역 선택](https://azure.microsoft.com/overview/datacenters/how-to-choose/)을 참조하세요.

StorSimple 솔루션에 대한 지역 선택은 구체적으로 다음과 같은 요인에 따라 결정됩니다.

- StorSimple Device Manager 서비스를 사용할 수 있는 지역.
- StorSimple 물리적, 클라우드 또는 가상 디바이스를 사용할 수 있는 국가.
- 최적의 성능을 위해 StorSimple 데이터를 저장하는 저장소 계정을 배치해야 할 지역.

이 자습서에서는 StorSimple Device Manager 서비스, 온-프레미스 물리적 및 클라우드 디바이스에 대한 지역 가용성을 설명합니다. 이 문서에 포함된 정보는 StorSimple 8000 및 1200 시리즈 디바이스에 적용됩니다.

## <a name="region-availability-for-storsimple-device-manager-service"></a>StorSimple Device Manager 서비스를 사용할 수 있는 지역

StorSimple Device Manager 서비스는 현재 12개 공용 지역 및 2개의 Azure Government 지역에서 지원됩니다.

StorSimple Device Manager 서비스를 처음 만들 때 지역 또는 위치를 정의합니다. 일반적으로 디바이스를 배포하려는 지리적 지역에 가장 가까운 위치를 선택합니다. 하지만 디바이스와 서비스를 서로 다른 위치에 배포할 수도 있습니다.

Azure 공용 클라우드에서 StorSimple Device Manager 서비스를 사용할 수 있으며 배포 가능한 지역 목록은 다음과 같습니다.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Azure Government 클라우드의 경우 StorSimple Device Manager 서비스는 미국 아이오와 주 정부 및 미국 버지니아 주 정부 데이터 센터에서 사용할 수 있습니다.

## <a name="region-availability-for-data-stored-in-storsimple"></a>StorSimple에 저장된 데이터에 대한 지역 가용성

StorSimple 데이터는 Azure 저장소 계정에 물리적으로 저장되며 이러한 계정은 모든 Azure 지역에서 사용할 수 있습니다. Azure 저장소 계정을 만들 때 저장소 계정의 기본 위치를 선택하고 데이터가 있는 지역을 결정합니다.

먼저 StorSimple Device Manager 서비스를 만들고 저장소 계정을 연결하면 StorSimple Device Manager 서비스와 Azure 저장소가 별도의 위치에 있을 수 있습니다. 이 경우, StorSimple 디바이스 관리자 및 Azure Storage 계정을 별도로 만들어야 합니다.

일반적으로 저장소 계정에 대한 서비스에 가장 가까운 지역을 선택합니다. 그러나 가장 가까운 Microsoft Azure 지역이 실제로는 대기 시간이 가장 낮은 지역이 아닐 수 있습니다. 네트워크 서비스 성능을 결정하고 그에 따라 솔루션의 성능도 결정하는 것이 대기 시간입니다. 따라서 다른 지역의 저장소 계정을 선택하는 경우, 사용자의 서비스와 저장소 계정에 연결된 지역 간의 대기 시간에 알아야 합니다.

StorSimple Cloud Appliance를 사용하는 경우는 서비스 및 연결된 저장소 계정을 동일한 지역에 두는 것이 좋습니다. 다른 지역에 저장소 계정이 있으면 성능이 저하될 수 있습니다.

## <a name="availability-of-storsimple-device"></a>StorSimple 디바이스의 가용성

모델에 따라서는 StorSimple 디바이스를 지리적으로 다른 위치 또는 국가에서 사용할 수 있습니다.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple 물리적 디바이스(모델 8100/8600)

StorSimple 8100 또는 8600 물리적 디바이스를 사용하는 경우 다음과 같은 국가에서 디바이스를 사용할 수 있습니다.

| #  | 국가        | #  | 국가     | #  | 국가      | #  | 국가              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | 오스트레일리아      | 16 | 홍콩 특별 행정구| 31 | 뉴질랜드  | 46 | 남아프리카         |
| 2  | 오스트리아        | 17 | 헝가리     | 32 | 나이지리아      | 47 | 대한민국          |
| 3  | 바레인        | 18 | 아이슬란드     | 33 | 노르웨이       | 48 | 스페인                |
| 4  | 벨기에        | 19 | 인도       | 34 | 페루         | 49 | 스리랑카            |
| 5  | 브라질         | 20 | 인도네시아   | 35 | 필리핀  | 50 | 스웨덴               |
| 6  | 캐나다         | 21 | 아일랜드     | 36 | 폴란드       | 51 | 스위스          |
| 7  | 칠레          | 22 | 이스라엘      | 37 | 포르투갈     | 52 | 대만               |
| 8  | 콜롬비아       | 23 | 이탈리아       | 38 | 푸에르토리코  | 53 | 태국             |
| 9  | 체코 | 24 | 일본       | 39 | 카타르        | 54 | 터키               |
| 10 | 덴마크        | 25 | 케냐       | 40 | 루마니아      | 55 | 우크라이나              |
| 11 | 이집트          | 26 | 쿠웨이트      | 41 | 러시아       | 56 | 아랍에미리트 |
| 12 | 핀란드        | 27 | 마카오   | 42 | 사우디아라비아 | 57 | 영국       |
| 13 | 프랑스         | 28 | 말레이시아    | 43 | 싱가포르    | 58 | 미국        |
| 14 | 독일        | 29 | 멕시코      | 44 | 슬로바키아     | 59 | 베트남              |
| 15 | 그리스         | 30 | 네덜란드 | 45 | 슬로베니아     | 60 | 크로아티아              |

이 목록은 국가가 추가될 때 변경됩니다. 최신 지리적 위치 목록에 대해서는 [제품 약관](https://www.microsoft.com/en-us/licensing/product-licensing/products)의 저장소 배열 약관 부록을 참조하세요.

Microsoft는 물리적 하드웨어를 제공하며 앞의 목록에 있는 지역에 StorSimple에 대한 교체용 하드웨어 예비 부품을 제공할 수 있습니다.

> [!IMPORTANT]
> StorSimple 물리적 디바이스를 StorSimple 프로그램이 지원되지 않는 지역에 배치하지 않도록 합니다. Microsoft는 StorSimple 프로그램이 지원되지 않는 국가에 어떠한 교체 부품도 공급할 수 없습니다.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance(모델 8010/8020)

StorSimple Cloud Appliance 8010 또는 8020을 사용하는 경우 해당 디바이스는 기본 VM이 지원되는 모든 지역에서 지원되며 사용할 수 있습니다. 8010은 모든 Azure 지역에서 지원되는 _Standard_A3_ VM을 사용합니다.

8020은 Premium Storage 및 _Standard_DS3_ VM을 사용하여 클라우드 어플라이언스에 만듭니다. 8020은 Premium Storage 및 _Standard_DS3_ Azure VM을 지원하는 모든 Azure 지역에서 지원됩니다. [이 목록](https://azure.microsoft.com/regions/services/)을 사용하여 **Virtual Machines > DS 시리즈** 및 **저장소 > 디스크 저장소**를 자신의 지역에서 사용할 수 있습니다.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple 가상 배열(모델 1200)

1200 시리즈 StorSimple 가상 배열을 사용하는 경우 모든 Azure 지역에서 가상 디스크 이미지가 지원됩니다.

## <a name="next-steps"></a>다음 단계

* [다양한 StorSimple 모델의 가격 책정](https://azure.microsoft.com/pricing/calculator/#storsimple2)에 대해 자세히 알아봅니다.
* [StorSimple 저장소 계정 관리](storsimple-8000-manage-storage-accounts.md)에 대해 자세히 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 자세히 알아봅니다.
