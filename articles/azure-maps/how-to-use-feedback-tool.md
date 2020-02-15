---
title: Azure Maps에 대 한 데이터 피드백 제공 Microsoft Azure 맵
description: Microsoft Azure Maps 피드백 도구를 사용 하 여 데이터 피드백을 제공 합니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c4eb8932dda32ae6773e76dc7102f5afc6bc655
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209837"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Azure Maps에 대 한 데이터 피드백 제공

2018 년 5 월부터 사용할 수 Azure Maps. Azure Maps은 새로운 지도 데이터를 제공 하 고, 사용 하기 쉬운 REST Api를 제공 하 고, 다양 한 유형의 비즈니스 사용 사례로 기업 고객을 지원 하기 위한 강력한 Sdk를 제공 하 고 있습니다. 실제 세계는 매 초 마다 변경 되며 고객에 게 실제 디지털 표현을 제공 하는 데 매우 중요 합니다. 시설을 열거나 닫을 계획인 고객은 지도를 즉시 업데이트 해야 합니다. 따라서 올바른 시설에서 배달, 유지 관리 또는 고객 서비스를 효율적으로 계획할 수 있습니다. 고객에 게 직접 데이터 피드백을 제공할 수 있도록 하는 Azure Maps 데이터 피드백 사이트를 만들었습니다. 고객의 데이터 피드백은 데이터 공급자 및 해당 맵 편집기로 직접 이동 합니다. 이를 통해 사용자 의견을 신속 하 게 평가 하 고 매핑 제품에 통합할 수 있습니다.  

[Azure Maps 데이터 피드백 사이트](https://feedback.azuremaps.com) 를 통해 고객은 특히 비즈니스 관심 분야와 주거 지역에서 지도 데이터 피드백을 쉽게 제공할 수 있습니다. 이 문서에서는 Azure Maps 피드백 사이트를 사용 하 여 다양 한 종류의 피드백을 제공 하는 방법을 안내 합니다.

## <a name="add-a-business-place-or-a-residential-address"></a>비즈니스 장소 또는 주거 주소 추가 

누락 된 관심 지점 또는 주거 주소에 대 한 피드백을 제공할 수 있습니다. 그렇게 하려면 두 가지 방법이 있습니다. Azure Map 데이터 피드백 사이트를 열고 누락 된 위치 좌표를 검색 한 다음 "위치 추가"를 클릭 합니다.

  ![누락 된 위치 검색](./media/how-to-use-feedback-tool/search-poi.png)

또는 맵과 상호 작용할 수 있습니다. 좌표에 핀을 놓을 위치를 클릭 하 고 "위치 추가"를 클릭 합니다.

  ![pin 추가](./media/how-to-use-feedback-tool/add-poi.png)

클릭 하면 해당 위치의 세부 정보를 제공 하는 양식으로 이동 합니다.

  ![장소 추가](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>회사 위치 또는 주거 주소 수정 

피드백 사이트를 사용 하 여 비즈니스 장소 또는 주소를 검색 하 고 찾을 수도 있습니다. 주소가 올바르지 않으면 주소 또는 pin 위치를 수정 하는 피드백을 제공할 수 있습니다. 주소 수정에 대 한 피드백을 제공 하려면 검색 표시줄을 사용 하 여 회사 또는 주거 주소를 검색 합니다. 결과 목록에서 관심 있는 위치를 클릭 합니다. "이 장소 수정"을 클릭 합니다.

  ![수정할 검색 장소](./media/how-to-use-feedback-tool/fix-place.png)

사용자 의견을 제공 하 여 주소를 수정 하려면 "위치 수정" 양식을 작성 한 후 "제출" 단추를 클릭 합니다.

  ![수정 양식](./media/how-to-use-feedback-tool/fix-form.png)

위치에 대 한 pin 위치가 잘못 된 경우 "pin 위치가 잘못 되었습니다." 라는 "위치 수정" 양식에서 확인란을 선택 합니다. Pin을 올바른 위치로 이동한 후 "제출" 단추를 클릭 합니다.

  ![이동 핀 위치](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>설명 추가 

사용자 의견 도구를 사용 하 여 위치를 검색 하도록 하는 것 외에도 사용자 의견 도구를 사용 하 여 위치와 관련 된 세부 정보를 볼 수 있습니다. 주석을 추가 하려면 위치를 검색 하거나 위치를 클릭 합니다. "주석 추가"를 클릭 하 고, 의견을 작성 한 후 "제출"을 클릭 합니다.

  ![설명 추가](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>상태 추적 

"상태를 추적 하 고 싶습니다." 상자를 선택 하 고 요청 하는 동안 전자 메일을 제공 하 여 요청 상태를 추적할 수도 있습니다. 요청에 대 한 최신 상태를 제공 하는 추적 링크를 전자 메일로 받게 됩니다. 

  ![피드백 상태](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>다음 단계

Azure Maps와 관련 된 기술적인 질문을 게시 하려면 다음을 방문 하세요.

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure Maps 피드백 포럼](https://feedback.azure.com/forums/909172-azure-maps)
