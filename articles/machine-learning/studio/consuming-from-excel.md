---
title: Excel에서 웹 서비스 사용
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio를 사용하면 코드를 작성할 필요 없이 Excel에서 직접 웹 서비스를 쉽게 호출할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773793"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Excel에서 Azure Machine Learning Studio 웹 서비스 사용

 Azure Machine Learning Studio를 사용하면 코드를 작성할 필요 없이 Excel에서 직접 웹 서비스를 쉽게 호출할 수 있습니다.

Excel 2013(이후 버전) 또는 Excel Online을 사용하는 경우 [Excel 추가 기능](excel-add-in-for-web-services.md)을 사용하는 것이 좋습니다.



## <a name="steps"></a>단계
웹 서비스를 게시합니다. [자습서 3: 신용 위험 모델 배포](tutorial-part3-credit-risk-deploy.md)에서는 이 작업을 수행하는 방법을 설명합니다. 현재 Excel 통합 문서 기능은 단일 출력(즉, 단일 점수 매기기 레이블)이 있는 요청/응답 서비스에 대해서만 지원됩니다. 

웹 서비스를 만든 후 스튜디오의 왼쪽에서 **웹 서비스** 섹션을 클릭한 다음 Excel에서 사용할 웹 서비스를 선택합니다.

**기존 웹 서비스**

1. 웹 서비스의 **대시보드** 탭에는 **요청/응답** 서비스의 행이 있습니다. 이 서비스에 단일 출력이 있는 경우 해당 행에 **Excel 통합 문서 다운로드** 링크가 표시됩니다.

    ![Studio 웹 서비스 포털을 사용 하 여 Excel 통합 문서를 다운로드 합니다.](./media/consuming-from-excel/excellink.png)
2. **Excel 통합 문서 다운로드**를 클릭합니다.

**새 웹 서비스**

1. Azure Machine Learning 웹 서비스 포털에서 **사용**을 선택합니다.
2. 사용 페이지의 **웹 서비스 사용 옵션** 섹션에서 Excel 아이콘을 클릭합니다.

**통합 문서 사용**

1. 통합 문서를 엽니다.
2. 보안 경고가 표시되면 **편집 사용** 단추를 클릭합니다.

    ![보호 된 뷰 보안 경고를 제거 하려면 편집을 사용 하도록 설정](./media/consuming-from-excel/enableeditting.png)
3. 보안 경고가 표시됩니다. **콘텐츠 사용** 단추를 클릭하여 스프레드시트에서 매크로를 실행합니다.

    ![매크로 사용 하지 않도록 설정 하는 보안 경고를 해제 하는 콘텐츠 사용](./media/consuming-from-excel/enablecontent.png)
4. 매크로가 활성화되면 테이블이 생성됩니다. 파란색 열은 RRS 웹 서비스의 입력 또는 **매개 변수**로 필요합니다. RRS 서비스의 출력인 **예측 값** 은 녹색입니다. 주어진 행의 모든 열이 채워지면 통합 문서에서 점수 매기기 API를 자동으로 호출하고 점수가 매겨진 결과를 표시합니다.

    ![테이블 매개 변수 입력 및 결과 대 한 예측 값](./media/consuming-from-excel/sampletable.png)
5. 둘 이상의 행에 대한 점수를 매기려면 두 번째 행을 데이터로 채웁니다. 그러면 예측 값이 생성됩니다. 여러 행을 한 번에 붙여 넣을 수도 있습니다.

데이터를 시각화하는 예측 값을 사용하여 Excel 기능(그래프, 파워 맵, 조건부 서식 등) 중 하나를 사용할 수 있습니다.

## <a name="sharing-your-workbook"></a>통합 문서 공유
매크로를 작동하려면 API 키가 스프레드시트의 일부여야 합니다. 즉, 신뢰할 수 있는 엔터티/개인과만 통합 문서를 공유해야 합니다.

## <a name="automatic-updates"></a>자동 업데이트
RRS 호출은 다음 두 가지 상황에서 수행됩니다.

1. 행의 모든 **매개 변수**
2. 모든 **매개 변수**가 있는 행에서 **매개 변수** 중 하나 이상이 변경될 때마다