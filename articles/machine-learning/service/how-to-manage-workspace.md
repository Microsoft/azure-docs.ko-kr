---
title: 작업 영역 만들기 및 관리
titleSuffix: Azure Machine Learning service
description: Azure Portal에서 Azure Machine Learning Service 작업 영역을 만들고 확인하고 삭제하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: e65f739a9641181381205c7255d0472325e8055c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819610"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Azure Machine Learning Service 작업 영역 만들기 및 관리

이 문서에서는 Azure Portal에서 [Azure Machine Learning Service](overview-what-is-azure-ml.md)용 [**Azure Machine Learning Service 작업 영역**](concept-azure-machine-learning-architecture.md#workspace)을 만들고 확인하고 삭제해 봅니다.  [CLI를 사용하여](reference-azure-machine-learning-cli.md) 또는 [Python 코드를 사용하여](https://aka.ms/aml-sdk) 작업 영역을 만들고 삭제할 수도 있습니다.

## <a name="create-a-workspace"></a>작업 영역 만들기 

작업 영역을 만들려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>작업 영역 보기

1. 포털의 왼쪽 위 모서리에서 **모든 서비스**를 선택합니다. 

1. 에 **모든 서비스** 필터 필드에 입력 **기계 학습 서비스**합니다.  

1. 선택 **Machine Learning 서비스 작업 영역**합니다.

   ![Azure Machine Learning 서비스 작업 영역에 대 한 검색](media/how-to-manage-workspace/all-services.png)

1. 검색된 작업 영역 목록을 살펴봅니다. 구독, 리소스 그룹 및 위치를 기준으로 필터링 할 수 있습니다.  

1. 해당 속성을 표시 하기 위해 작업 영역을 선택 합니다.
   ![작업 영역 속성](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>작업 영역 삭제

삭제하려는 작업 영역 맨 위에 있는 [삭제] 단추를 사용합니다.

  ![삭제 단추](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>리소스 정리 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

전체 자습서의 단계에 따라 작업 영역을 사용하여 Azure Machine Learning 서비스로 모델을 빌드, 교육 및 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: 모델 학습](tutorial-train-models-with-aml.md)
