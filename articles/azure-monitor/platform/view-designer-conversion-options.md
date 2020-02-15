---
title: Azure Monitor 뷰 디자이너에서 통합 문서 변환 옵션
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f04ebc1a4a53825709479ca3f1dc7ce1245fc67f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171554"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor 뷰 디자이너에서 통합 문서 변환 옵션
[뷰 디자이너](view-designer.md) 는 Azure Monitor 기능으로, 차트, 목록 및 타임 라인을 사용 하 여 Log Analytics 작업 영역에서 데이터를 시각화 하는 데 도움이 되는 사용자 지정 보기를 만들 수 있습니다. 이러한 기능은 추가 기능을 제공 하는 통합 문서로 단계적으로 교체 됩니다. 이 문서에서는 뷰를 통합 문서로 변환 하기 위한 두 가지 옵션 간의 기본적인 개념을 비교 합니다.

## <a name="basic-workbook-designs"></a>기본 통합 문서 디자인

뷰 디자이너에는 고정 된 정적 스타일이 표시 되 고, 통합 문서에서는 데이터가 표시 되는 방식을 자유롭게 포함 하 고 수정할 수 있습니다. 아래 이미지에서는 뷰를 변환할 때 통합 문서를 정렬 하는 방법에 대 한 두 가지 예를 보여 줍니다.

세로 [통합 문서](view-designer-conversion-examples.md#vertical)
세로](media/view-designer-conversion-options/view-designer-vertical.png) ![

탭 [통합 문서](view-designer-conversion-examples.md#tabbed)
![데이터 형식 배포 탭](media/view-designer-conversion-options/distribution-tab.png)
시간 탭의 데이터 형식 ![](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>타일 변환
뷰 디자이너는 개요 타일 기능을 사용 하 여 전체 상태를 나타내고 요약 합니다. 이러한 값은 숫자에서 차트에 이르는 일곱 개의 타일로 표시 됩니다. 통합 문서에서 사용자는 비슷한 시각화를 만들어 개요 타일의 원래 스타일과 비슷하게 고정할 수 있습니다. 

![갤러리](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>대시보드 변환 보기
뷰 디자이너 타일은 일반적으로 시각화의 데이터와 일치 하는 시각화와 목록 (예: **도넛형 & 목록** 타일)으로 구성 됩니다.

![도넛형](media/view-designer-conversion-options/donut-example.png)

통합 문서를 사용 하면 사용자가 보기의 하나 또는 두 섹션을 쿼리하도록 선택할 수 있습니다. 통합 문서에서 쿼리를 작성 하는 것은 간단한 2 단계 프로세스입니다. 먼저 쿼리에서 데이터를 생성 하 고 두 번째로 데이터를 시각화로 렌더링 합니다.  통합 문서에서이 뷰를 다시 만드는 방법의 예는 다음과 같습니다.

![변환](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>다음 단계
- [통합 문서 & 권한 액세스](view-designer-conversion-access.md)