---
title: 웹 서비스용 Excel 추가 기능
titleSuffix: Azure Machine Learning Studio
description: 코드를 작성하지 않고 Excel에서 직접 Azure Machine Learning 웹 서비스를 사용하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 9e801e0d7a26cd4d6c43118959aee1dec7216b1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750249"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Azure Machine Learning Studio 웹 서비스용 Excel 추가 기능
Excel을 사용하면 코드를 작성할 필요 없이 쉽게 직접 웹 서비스를 호출할 수 있습니다.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>통합 문서에서 기존 웹 서비스를 사용하는 단계

1. 타이타닉호의 승객에 대한 데이터와 Excel 추가 기능이 들어 있는 [샘플 Excel 파일](https://aka.ms/amlexcel-sample-2)을 엽니다. 
 
    > [!NOTE]
    > 파일과 관련된 웹 서비스 목록이 보이고 하단에 "자동 예측" 확인란이 표시됩니다. 자동 예측을 사용하도록 설정하면 입력이 변경될 때마다 **모든** 서비스의 예측이 업데이트됩니다. 선택하지 않는 경우 새로 고치려면 "모두 예측"을 클릭해야 합니다. 서비스 수준에서 자동 예측을 사용하도록 설정하려면 6단계로 이동합니다.

2. 웹 서비스를 클릭하여 선택합니다(이 예제의 경우 "Titanic Survivor Predictor (Excel Add-in Sample) [Score]").
   
    ![웹 서비스 선택](./media/excel-add-in-for-web-services/image1.png)
3. 이렇게 하면 **Predict** 섹션으로 이동합니다.  이 통합 문서에는 이미 샘플 데이터가 포함되어 있지만 통합 문서가 비어 있는 경우에는 Excel에서 셀 하나를 선택하고 **샘플 데이터 사용**을 클릭할 수 있습니다.
4. 머리글이 있는 데이터를 선택하고 입력 데이터 범위 아이콘을 클릭합니다.  "My data has headers" 상자가 선택되어 있는지 확인합니다.
5. **Output** 아래에 출력을 배치할 셀 번호를 입력합니다(여기서는 "H1").
6. **Predict**를 클릭합니다. "자동 예측" 확인란을 선택할 경우 선택한 영역(입력으로 지정된 영역)에서 변경 작업이 수행될 때 예측 단추를 누르지 않아도 요청 및 출력 셀 업데이트가 트리거됩니다.
   
    ![Predict 섹션](./media/excel-add-in-for-web-services/image1.png)

웹 서비스를 배포하거나 기존 웹 서비스를 사용합니다. 웹 서비스 배포 방법에 대한 자세한 내용은 [자습서 3: 신용 위험 모델 배포](tutorial-part3-credit-risk-deploy.md)를 참조하세요.

웹 서비스에 대한 API 키를 가져옵니다. 새 Machine Learning 웹 서비스의 기존 Machine Learning 웹 서비스를 게시했는지 여부에 따라 이 작업을 수행하는 위치가 달라집니다.

**기존 웹 서비스 사용** 

1. Machine Learning Studio에서 왼쪽 창의 **WEB SERVICES** 섹션을 클릭한 다음 웹 서비스를 선택합니다.
   
    ![Studio 웹 서비스 선택](./media/excel-add-in-for-web-services/image4.png)
2. 웹 서비스에 대한 API 키를 복사합니다.
   
    ![Studio API 키](./media/excel-add-in-for-web-services/image5.png)
3. 웹 서비스의 **DASHBOARD** 탭에서 **REQUEST/RESPONSE** 링크를 클릭합니다.
4. **요청 URI** 섹션을 찾습니다.  URL을 복사하고 저장합니다.

> [!NOTE]
> 이제 [Azure Machine Learning 웹 서비스](https://services.azureml.net) 포털에 로그인하여 기존 Machine Learning 웹 서비스에 대한 API 키를 가져올 수 있습니다.
> 
> 

**새 웹 서비스 사용**

1. [Azure Machine Learning 웹 서비스](https://services.azureml.net) 포털에서 **웹 서비스**를 클릭한 다음 웹 서비스를 선택합니다. 
2. **사용**을 클릭합니다.
3. **기본 사용량 정보** 섹션을 찾습니다. **기본 키** 및 **요청-응답** URL을 복사하여 저장합니다.

## <a name="steps-to-add-a-new-web-service"></a>새 웹 서비스 추가 단계

1. 웹 서비스를 배포하거나 기존 웹 서비스를 사용합니다. 웹 서비스 배포 방법에 대한 자세한 내용은 [자습서 3: 신용 위험 모델 배포](tutorial-part3-credit-risk-deploy.md)를 참조하세요.
2. **사용**을 클릭합니다.
3. **기본 사용량 정보** 섹션을 찾습니다. **기본 키** 및 **요청-응답** URL을 복사하여 저장합니다.
4. Excel에서 **웹 서비스** 섹션으로 이동합니다(**Predict** 섹션에 있는 경우 뒤로 화살표를 클릭하여 웹 서비스 목록으로 이동).
   
    ![웹 서비스 선택으로 이동](./media/excel-add-in-for-web-services/image3.png)
5. **Add Web Service**를 클릭합니다.
6. URL을 **URL**이라고 레이블이 지정된 텍스트 상자에 붙여 넣습니다.
7. API/기본 키를 **API 키**텍스트 상자에 붙여넣습니다.
8. **추가**를 클릭합니다.
   
    ![기존 웹 서비스에 대한 URL 및 API 키.](./media/excel-add-in-for-web-services/image6.png)
9. 웹 서비스를 사용하려면 위의 지침 "기존 웹 서비스를 사용하는 단계"를 따릅니다.

## <a name="sharing-your-workbook"></a>통합 문서 공유
통합 문서를 저장하면 추가한 웹 서비스의 API/기본 키도 저장됩니다. 즉, 신뢰할 수 있는 사용자와만 통합 문서를 공유해야 합니다.

다음 설명 섹션 또는 [포럼](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)에서 질문을 할 수 있습니다.
