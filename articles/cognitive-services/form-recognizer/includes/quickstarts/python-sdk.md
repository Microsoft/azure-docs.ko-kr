---
title: '빠른 시작: Python용 Form Recognizer 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Python용 Form Recognizer 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/21/2020
ms.author: pafarley
ms.openlocfilehash: b178a0b347888f22d9a3c0ee88a203e377cb15be
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864763"
---
> [!IMPORTANT]
> * Form Recognizer SDK는 현재 Form Recognizer 서비스의 v2.0을 대상으로 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 아래의 참조 설명서를 참조하세요. 

[참조 설명서](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [패키지(PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다.
* [Python 2.7 또는 3.5 이상](https://www.python.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="object-model"></a>개체 모델 

Form Recognizer를 사용하면 두 가지 다른 클라이언트 유형을 만들 수 있습니다. 첫 번째, `form_recognizer_client`는 인식된 양식 필드 및 콘텐츠에 대한 서비스를 쿼리하는 데 사용됩니다. 두 번째, `form_training_client`는 인식 기능을 향상시키는 데 사용할 수 있는 사용자 지정 모델을 만들고 관리하는 데 사용됩니다. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client`는 다음에 대한 작업을 제공합니다.

 * 사용자 지정 양식을 인식하도록 학습된 사용자 지정 모델을 사용하여 양식 필드 및 콘텐츠를 인식합니다. 
 * 모델을 학습하지 않고도 테이블, 줄 및 단어를 비롯한 양식 콘텐츠를 인식합니다. 
 * Form Recognizer 서비스에서 미리 학습된 영수증 모델을 사용하여 영수증에서 공통 필드를 인식합니다.

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client`는 다음에 대한 작업을 제공합니다.

* 사용자 지정 양식에서 발견된 모든 필드와 값을 인식하도록 사용자 지정 모델을 학습합니다. 학습 데이터 세트를 만드는 방법에 대한 자세한 설명은 [레이블이 없는 모델 학습에 대한 서비스의 설명서](#train-a-model-without-labels)를 참조하세요.
* 사용자 지정 모델을 학습하여 사용자 지정 양식에 레이블을 지정하여 사용자가 지정한 특정 필드 및 값을 인식합니다. 학습 데이터 세트에 레이블을 적용하는 방법에 대한 자세한 설명은 [레이블이 지정된 모델 학습에 대한 서비스의 설명서](#train-a-model-with-labels)를 참조하세요.
* 계정에서 생성된 모델을 관리합니다.
* 하나의 Form Recognizer 리소스에서 다른 리소스로 사용자 지정 모델을 복사합니다.

[Form Recognizer 레이블 지정 도구](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습할 수도 있습니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후 다음을 사용하여 최신 버전의 Form Recognizer 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-ai-formrecognizer
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 Python 애플리케이션을 새로 만듭니다. 그런 다음, 다음 라이브러리를 가져옵니다. 학습 및 양식 인식 모두에 필요한 라이브러리를 가져오는 것을 기억하세요.

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import FormRecognizerClient
from azure.ai.formrecognizer import FormTrainingClient
from azure.core.credentials import AzureKeyCredential
```

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 

```python
endpoint = "<paste-your-form-recognizer-endpoint-here>"
key = "<paste-your-form-recognizer-key-here>"
```

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Form Recognizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [양식 콘텐츠 인식](#recognize-form-content)
* [영수증 확인](#recognize-receipts)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>클라이언트 인증

여기서는 위에서 정의한 구독 변수를 사용하여 클라이언트 개체 두 개를 인증합니다. `AzureKeyCredential` 개체를 사용하면 필요한 경우 새 클라이언트 개체를 만들지 않고 API 키를 업데이트할 수 있습니다.

```python
form_recognizer_client = FormRecognizerClient(endpoint, AzureKeyCredential(key))
form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="assets-for-testing"></a>테스트 자산

이 가이드의 코드 조각은 URL을 통해 액세스되는 원격 양식을 사용합니다. 로컬 양식 문서를 대신 처리하려는 경우 [참조 설명서](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) 및 [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)의 관련 메서드를 참조하세요.

또한 학습 및 테스트 데이터에 대한 참조를 URL에 추가해야 합니다.
* 사용자 지정 모델 학습 데이터에 대한 SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 마우스 오른쪽 단추로 컨테이너를 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
* 아래 샘플에 포함된 샘플 및 영수증 이미지([GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)에서도 사용 가능)를 사용하거나 위의 단계를 사용하여 Blob 스토리지에 있는 개별 문서의 SAS URL을 가져올 수 있습니다. 

> [!NOTE]
> 이 가이드의 코드 조각은 URL을 통해 액세스되는 원격 양식을 사용합니다. 로컬 양식 문서를 대신 처리하려는 경우 [참조 설명서](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)의 관련 메서드를 참조하세요.

## <a name="recognize-form-content"></a>양식 콘텐츠 인식

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 줄 및 단어를 인식할 수 있습니다.

지정된 URL에서 파일의 콘텐츠를 인식하려면 `begin_recognize_content` 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 `FormPage` 개체 컬렉션이 반환됩니다. 다음 코드는 이러한 개체에서 반복되고 추출된 키/값 쌍 및 테이블 데이터를 출력합니다.

```Python
formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf"

poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
page = poller.result()

table = page[0].tables[0] # page 1, table 1
print("Table found on page {}:".format(table.page_number))
for cell in table.cells:
    print("Cell text: {}".format(cell.text))
    print("Location: {}".format(cell.bounding_box))
    print("Confidence score: {}\n".format(cell.confidence))
```

### <a name="output"></a>출력

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0

...

```

## <a name="recognize-receipts"></a>영수증 확인

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 인식 및 추출하는 방법을 보여 줍니다. URL을 통해 영수증을 확인하려면 `begin_recognize_receipts_from_url` 메서드를 사용합니다. 

```python
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"

poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
result = poller.result()

for receipt in result:
    for name, field in receipt.fields.items():
        if name == "Items":
            print("Receipt Items:")
            for idx, items in enumerate(field.value):
                print("...Item #{}".format(idx + 1))
                for item_name, item in items.value.items():
                    print("......{}: {} has confidence {}".format(item_name, item.value, item.confidence))
        else:
            print("{}: {} has confidence {}".format(name, field.value, field.confidence))
```

### <a name="output"></a>출력

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

이 섹션에서는 사용자 고유의 데이터로 모델을 학습시키는 방법을 보여 줍니다. 학습된 모델은 원본 양식 문서의 키/값 관계가 포함된 구조적 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

학습 문서에 수동으로 레이블을 지정하지 않고 사용자 지정 양식에서 발견된 모든 필드와 값을 인식하도록 사용자 지정 모델을 학습시킬 수 있습니다.

다음 코드는 `begin_training` 함수와 학습 클라이언트를 사용하여 지정된 문서 세트에 대한 모델을 학습합니다. 반환되는 `CustomFormModel` 개체는 모델에서 인식할 수 있는 양식 유형과 각 양식 유형에서 추출할 수 있는 필드에 대한 정보를 포함합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>출력

[Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)에서 사용할 수 있는 학습 데이터로 학습된 모델의 출력입니다.

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

학습 문서에 레이블을 수동으로 지정하여 사용자 지정 모델을 학습시킬 수도 있습니다. 일부 시나리오에서는 레이블을 사용하여 학습시키면 성능이 향상됩니다. 반환된 `CustomFormModel`은 각 필드의 예상 정확도와 함께 모델이 추출할 수 있는 필드를 표시합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

> [!IMPORTANT]
> 레이블을 사용하여 학습하려면 학습 문서와 별도로 Blob 스토리지 컨테이너에 특별한 레이블 정보 파일(`\<filename\>.pdf.labels.json`)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 레이블 파일이 있으면 `true`로 설정된 *use_training_labels* 매개 변수를 통해 `begin_training` 함수를 호출할 수 있습니다.

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>출력

[Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)에서 사용할 수 있는 학습 데이터로 학습된 모델의 출력입니다.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

이 섹션에서는 사용자 고유의 양식으로 학습시킨 모델을 사용하여 사용자 지정 양식 유형에서 키/값 정보 및 기타 콘텐츠를 추출하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 시나리오를 구현하려면 해당 ID를 아래 메서드에 전달할 수 있도록 모델을 이미 학습시킨 상태여야 합니다. [모델 학습](#train-a-model-without-labels) 섹션을 참조하세요.

`begin_recognize_custom_forms_from_url` 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 `RecognizedForm` 개체 컬렉션이 반환됩니다. 다음 코드에서는 분석 결과를 콘솔에 출력합니다. 인식된 각 필드와 해당 값을 신뢰도 점수와 함께 출력합니다.

```python
# Model ID from when you trained your model.
model_id = "<your custom model id>"

poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model_id, form_url=formUrl)
result = poller.result()

for recognized_form in result:
    print("Form type: {}".format(recognized_form.form_type))
    for name, field in recognized_form.fields.items():
        print("Field '{}' has label '{}' with value '{}' and a confidence score of {}".format(
            name,
            field.label_data.text if field.label_data else name,
            field.value,
            field.confidence
        ))
```

### <a name="output"></a>출력

이전 예제의 모델을 사용하면 다음 출력이 제공됩니다.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>사용자 지정 모델 관리

이 섹션에서는 계정에 저장된 사용자 지정 모델을 관리하는 방법을 보여 줍니다. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer 리소스 계정의 모델 수 확인

다음 코드 블록은 Form Recognizer 계정에 저장된 모델 수를 확인하고 이를 계정 제한과 비교합니다.

```python
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="output"></a>출력

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>리소스 계정에 현재 저장된 모델 나열

다음 코드 블록은 계정의 현재 모델을 나열하고 해당 세부 정보를 콘솔에 출력합니다. 첫 번째 모델에 대한 참조도 저장합니다.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="output"></a>출력

이는 테스트 계정에 대한 샘플 출력입니다.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>모델 ID를 사용하여 특정 모델 가져오기

다음 코드 블록에서는 이전 섹션에서 저장한 모델 ID를 사용하여 모델에 대한 세부 정보를 검색합니다.

```python
model_id = "<model_id from the Train a Model sample>"

custom_model = form_training_client.get_custom_model(model_id=model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Training started on: {}".format(custom_model.training_started_on))
print("Training completed on: {}".format(custom_model.training_completed_on))
```

### <a name="output"></a>출력

이는 이전 예제에서 만든 사용자 지정 모델에 대한 샘플 출력입니다.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>리소스 계정에서 모델 삭제

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다. 이 코드는 이전 섹션에서 사용된 모델을 삭제합니다.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
```

## <a name="run-the-application"></a>애플리케이션 실행

다음 명령을 사용하여 이 빠른 시작에서 읽은 많은 수의 함수를 사용하여 언제든지 애플리케이션을 실행할 수 있습니다.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>문제 해결

### <a name="general"></a>일반

Form Recognizer 클라이언트 라이브러리에서 [Azure Core](https://aka.ms/azsdk-python-azure-core)에 정의된 예외가 발생합니다.

## <a name="logging"></a>로깅

이 라이브러리는 로깅에 [표준 로깅 라이브러리](https://docs.python.org/3/library/logging.html)를 사용합니다. HTTP 세션(URL, 헤더 등)에 대한 기본 정보는 정보 수준에서 기록됩니다.

`logging_enable` 키워드 인수를 통해 클라이언트에서 요청/응답 본문 및 미작성 헤더를 포함한 상세 디버그 수준 로깅을 사용할 수 있습니다.

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

마찬가지로 `logging_enable`은 클라이언트에 대해 상세 로깅을 사용하지 않는 경우에도 한 작업에만 사용하게 설정할 수 있습니다.

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer Python 클라이언트 라이브러리를 사용하여 다양한 방식으로 모델을 학습시키고 양식을 분석했습니다. 다음으로, 더 나은 학습 데이터 세트를 만들고 더 정확한 모델을 생성하기 위한 팁에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](../../build-training-data-set.md)

## <a name="see-also"></a>참조

* [Form Recognizer란?](../../overview.md)
