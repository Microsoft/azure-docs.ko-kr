---
title: '자습서: Azure Logic Apps에서 Form Recognizer를 사용하여 청구서 분석 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Azure Logic Apps에서 Form Recognizer를 사용하여 모델을 학습시키고 샘플 데이터로 테스트하는 프로세스를 자동화하는 워크플로를 만듭니다.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118278"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>자습서: Azure Logic Apps에서 Form Recognizer를 사용하여 청구서 분석

이 자습서에서는 Azure Logic Apps에서 Azure Cognitive Services 제품군에 속한 서비스인 Form Recognizer를 사용하여 청구서로부터 데이터를 추출하는 워크플로를 만듭니다. 먼저 샘플 데이터 세트를 사용하여 Recognizer 모델을 학습한 다음, 다른 데이터 세트에서 모델을 테스트합니다.

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
> * Form Recognizer에 대한 액세스 요청
> * Azure Storage Blob 컨테이너 만들기
> * Azure Blob 컨테이너에 샘플 데이터 업로드
> * Azure Logic App 만들기
> * Form Recognizer 리소스를 사용하도록 논리 앱 구성
> * 논리 앱을 실행하여 워크플로 테스트

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

## <a name="understand-the-invoice-to-be-analyzed"></a>분석할 청구서 이해

모델을 학습하고 테스트하는 데 사용할 샘플 데이터 세트는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451)에서 .zip 파일로 제공됩니다. .zip 파일을 다운로드하여 압축을 풀고, **/Train** 폴더 아래에 있는 청구서 PDF 파일을 엽니다. 청구서 번호, 청구서 날짜 등이 포함된 테이블이 있습니다. 

> [!div class="mx-imgBorder"]
> ![샘플 청구서](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

이 자습서에서는 Azure Logic Apps 워크플로를 사용하여 이러한 테이블에서 JSON 형식으로 정보를 추출하는 방법에 대해 알아봅니다.

## <a name="create-an-azure-storage-blob-container"></a>Azure Storage Blob 컨테이너 만들기

이 컨테이너를 사용하여 모델을 학습시키는 데 필요한 샘플 데이터를 업로드합니다.

1. [Azure Storage 계정 만들기](../../storage/common/storage-account-create.md)의 지침에 따라 스토리지 계정을 만듭니다. 스토리지 계정 이름으로 **formrecostorage**를 사용합니다.
1. [Azure Blob 컨테이너 만들기](../../storage/blobs/storage-quickstart-blobs-portal.md)의 지침에 따라 컨테이너를 Azure Storage 계정 내에 만듭니다. 컨테이너 이름으로 **formrecocontaner**를 사용합니다. 퍼블릭 액세스 수준을 **컨테이너(컨테이너와 Blob에 대한 익명 읽기 액세스)** 로 설정해야 합니다.

    > [!div class="mx-imgBorder"]
    > ![Blob 컨테이너 만들기](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Azure Blob 컨테이너에 샘플 데이터 업로드

[GitHub](https://go.microsoft.com/fwlink/?linkid=2090451)에서 사용할 수 있는 샘플 데이터를 다운로드합니다. 데이터를 로컬 폴더에 추출하고, **/Train** 폴더의 콘텐츠를 앞에서 만든 **formrecocontantaner**에 업로드합니다. [블록 Blob 업로드](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)의 지침에 따라 데이터를 컨테이너에 업로드합니다.

컨테이너의 URL을 복사합니다. 이 URL은 자습서의 뒷부분에서 필요합니다. 이 자습서에 나열된 것과 동일한 이름을 사용하여 스토리지 계정과 컨테이너를 만든 경우 URL은 *https:\//formrecostorage.blob.core.windows.net/formrecocontainer/* 입니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>논리 앱 만들기

Azure Logic Apps를 사용하여 작업 및 워크플로를 자동화하고 오케스트레이션할 수 있습니다. 이 자습서에서는 이메일 첨부 파일로 분석하려는 청구서를 받으면 트리거되는 논리 앱을 만듭니다. 이 워크플로에서는 다음 작업을 수행합니다.
* 청구서가 첨부된 이메일을 받으면 자동으로 트리거되도록 논리 앱을 구성합니다.
* Azure Blob 스토리지에 업로드한 샘플 데이터를 사용하여 모델을 학습시키는 Form Recognizer **모델 학습** 작업을 사용하도록 논리 앱을 구성합니다.
* 이미 학습된 모델을 사용하는 Form Recognizer **양식 분석** 작업을 사용하도록 논리 앱을 구성합니다. 이 구성 요소는 이전에 학습한 모델에 따라 이 논리 앱에 제공하는 청구서를 분석합니다.

다음 단계에 따라 워크플로를 설정합니다.

1. Azure 주 메뉴에서 **리소스 만들기** > **통합** > **논리 앱**을 차례로 선택합니다.

1. 여기에 보이는 것처럼 **논리 앱 만들기** 아래에서 논리 앱에 대한 정보를 제공합니다. 완료되면 **만들기**를 선택합니다.

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **이름** | <*logic-app-name*> | 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 괄호(`(`, `)`) 및 마침표(`.`)만 포함할 수 있는 논리 앱 이름입니다. 이 예제에서는 “My-First-Logic-App”을 사용합니다. |
   | **구독** | <*Azure-subscription-name*> | Azure 구독 이름 |
   | **리소스 그룹** | <*Azure-resource-group-name*> | 관련 리소스를 구성하는 데 사용되는 [Azure 리소스 그룹](./../../azure-resource-manager/management/overview.md)의 이름입니다. 이 예제에서는 “My-First-LA-RG”를 사용합니다. |
   | **위치** | <*Azure-region*> | 논리 앱 정보를 저장할 지역입니다. 이 예제에서는 “미국 서부”를 사용합니다. |
   | **Log Analytics** | 꺼짐 | 진단 로깅에 대한 설정을 **끄기**로 유지합니다. |
   ||||

1. Azure가 앱을 배포한 후 Azure 도구 모음에서 배포된 논리 앱에 대해 **알림** > **리소스로 이동**을 선택합니다. 또는 검색 상자에 이름을 입력하여 논리 앱을 찾아 선택할 수 있습니다.

   Logic Apps 디자이너가 열리고 소개 비디오 및 많이 사용되는 트리거가 포함된 페이지를 보여줍니다. **템플릿** 아래에서 **빈 논리 앱**을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![논리 앱에 대한 빈 템플릿 선택](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>이메일 도착 시 워크플로를 트리거하도록 논리 앱 구성

이 자습서에서는 첨부된 청구서가 포함된 이메일을 받으면 워크플로를 트리거합니다. 이 자습서는 Office 365를 이메일 서비스로 사용하지만, 사용하려는 다른 이메일 공급자를 사용할 수도 있습니다.

1. 탭에서 [모두], **Office 365 Outlook**을 차례로 선택한 다음, **트리거** 아래에서 **새 이메일이 도착하는 경우**를 선택합니다.

    ![들어오는 이메일을 통한 논리 앱 트리거](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. **Office 365 Outlook** 상자에서 **로그인**을 클릭하고, 세부 정보를 입력하여 Office 365 계정에 로그인합니다.

1. 다음 대화 상자에서 다음 단계를 수행합니다.
    1. 새 이메일을 모니터링해야 하는 폴더를 선택합니다.
    1. **첨부 파일 있음**에 대해 **예**를 선택합니다. 이렇게 하면 첨부 파일이 있는 이메일만 워크플로를 트리거합니다.
    1. **첨부 파일 포함**에 대해 **예**를 선택합니다. 이렇게 하면 첨부 파일의 내용이 다운스트림 처리에 사용됩니다.

        > [!div class="mx-imgBorder"]
        > ![논리 앱 이메일 트리거 구성](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. 위쪽의 도구 모음에서 **저장**을 클릭합니다.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Form Recognizer 모델 학습 작업을 사용하도록 논리 앱 구성

Form Recognizer 서비스를 사용하여 청구서를 분석하려면 먼저 모델에서 분석하고 학습할 수 있는 샘플 청구서 데이터를 제공하여 모델을 학습시켜야 합니다.

1. **새 단계**를 선택하고, **작업 선택** 아래에서 **Form Recognizer**를 검색합니다. 표시되는 결과에서 **Form Recognizer**를 선택한 다음, Form Recognizer에 사용할 수 있는 작업 아래에서 **모델 학습**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Form Recognizer 모델 학습](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Form Recognizer 대화 상자에서 연결 이름을 제공하고, Form Recognizer 리소스에 대해 검색한 엔드포인트 URL과 키를 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![Form Recognizer의 연결 이름](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    **만들기**를 클릭합니다.

1. **모델 학습** 대화 상자에서 **원본**에 대해 샘플 데이터를 업로드한 컨테이너의 URL을 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![샘플 청구서용 스토리지 컨테이너](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. 위쪽의 도구 모음에서 **저장**을 클릭합니다.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Form Recognizer 양식 분석 작업을 사용하도록 논리 앱 구성

이 섹션에서는 **양식 분석** 작업을 워크플로에 추가합니다. 이 작업은 이미 학습된 모델을 사용하여 논리 앱에 제공되는 새 청구서를 분석합니다.

1. **새 단계**를 선택하고, **작업 선택** 아래에서 **Form Recognizer**를 검색합니다. 표시되는 결과에서 **Form Recognizer**를 선택한 다음, Form Recognizer에 사용할 수 있는 작업 아래에서 **양식 분석**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Form Recognizer 모델 분석](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. **양식 분석** 대화 상자에서 다음 단계를 수행합니다.

    1. **모델 ID** 텍스트 상자를 클릭하고, 열리는 대화 상자의 **동적 콘텐츠** 탭 아래에서 **modelId**를 선택합니다. 이렇게 하면 마지막 섹션에서 학습시킨 모델의 모델 ID를 흐름 애플리케이션에 제공할 수 있습니다.

        > [!div class="mx-imgBorder"]
        > ![Form Recognizer에 ModelID 사용](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. **문서** 텍스트 상자를 클릭하고, 열리는 대화 상자의 **동적 콘텐츠** 탭 아래에서 **첨부 파일 콘텐츠**를 선택합니다. 이렇게 하면 워크플로를 트리거하는 이메일에 첨부된 샘플 청구서 파일을 사용하도록 흐름을 구성합니다.

        > [!div class="mx-imgBorder"]
        > ![이메일 첨부 파일을 사용하여 청구서 분석](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. 위쪽의 도구 모음에서 **저장**을 클릭합니다.

### <a name="extract-the-table-information-from-the-invoice"></a>청구서에서 테이블 정보 추출

이 섹션에서는 청구서 내의 테이블에서 정보를 추출하도록 논리 앱을 구성합니다.

1. **작업 추가**를 선택하고, **작업 선택** 아래에서 **작성**을 검색하고, 사용할 수 있는 작업 아래에서 **작성**을 다시 선택합니다.
    ![청구서에서 테이블 정보 추출](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. **작성** 대화 상자에서 **입력** 텍스트 상자를 클릭하고, 팝업되는 대화 상자에서 **테이블**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![청구서에서 테이블 정보 추출](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. **저장**을 클릭합니다.

## <a name="test-your-logic-app"></a>논리 앱 테스트

논리 앱을 테스트하려면 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451)에서 다운로드한 샘플 데이터 세트의 **/Test** 폴더에 있는 샘플 청구서를 사용합니다. 다음 단계를 수행하세요.

1. 앱에 대한 Azure Logic Apps 디자이너의 위쪽에 있는 도구 모음에서 **실행**을 선택합니다. 이제 워크플로가 활성화되고, 청구서가 첨부된 이메일을 받을 때까지 기다립니다.
1. 논리 앱을 만들 때 제공한 이메일 주소로 샘플 청구서가 첨부된 이메일을 보냅니다. 논리 앱을 구성하는 동안 제공한 폴더에 이메일이 배달되었는지 확인합니다.
1. 이메일이 폴더로 배달되는 즉시 Logic Apps 디자이너에는 각 단계의 진행 상황을 보여 주는 화면이 표시됩니다. 아래 스크린샷에서는 첨부 파일이 포함된 이메일을 받고 워크플로가 진행되고 있음을 알 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![이메일을 보내서 워크플로 시작](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. 모든 워크플로 단계의 실행이 완료되면 Logic Apps 디자이너에는 모든 단계에 대해 녹색 확인란이 표시됩니다. 디자이너 창에서 **각각 2**, **작성**을 차례로 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![완료된 워크플로](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    **출력** 상자에서 출력을 복사하여 텍스트 편집기에 붙여넣습니다.

1. 이메일의 첨부 파일로 보낸 샘플 청구서와 JSON 출력을 비교합니다. JSON 데이터가 청구서 내의 테이블 데이터와 일치하는지 확인합니다.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    이 자습서를 성공적으로 완료했습니다!

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Form Recognizer를 사용하여 모델을 학습시키고 청구서의 콘텐츠를 추출하도록 Azure Logic Apps 워크플로를 설정했습니다. 다음으로, 사용자 고유의 양식을 사용하여 비슷한 시나리오를 만들 수 있도록 학습 데이터 세트를 빌드하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](build-training-data-set.md)