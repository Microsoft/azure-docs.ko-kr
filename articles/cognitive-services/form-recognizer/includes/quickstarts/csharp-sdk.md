---
title: '빠른 시작: .NET용 Form Recognizer 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 Form Recognizer 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 46c94ec64cd0b82a191c63069a8619bb3dc62908
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864897"
---
> [!IMPORTANT]
> * Form Recognizer SDK는 현재 Form Recognizer 서비스의 v2.0을 대상으로 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 아래의 참조 설명서를 참조하세요. 

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다.
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `formrecognizer-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 _Program.cs_라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```console
dotnet new console -n formrecognizer-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 그러면 다음을 사용하여 애플리케이션을 빌드합니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Form Recognizer 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!TIP]
> Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다. 다음 `using` 지시문을 추가합니다.

```csharp
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
```

## <a name="object-model"></a>개체 모델 

Form Recognizer를 사용하면 두 가지 다른 클라이언트 유형을 만들 수 있습니다. 첫 번째, `FormRecognizerClient`는 인식된 양식 필드 및 콘텐츠에 대한 서비스를 쿼리하는 데 사용됩니다. 두 번째, `FormTrainingClient`는 인식 기능을 향상시키는 데 사용할 수 있는 사용자 지정 모델을 만들고 관리하는 데 사용됩니다. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient`는 다음에 대한 작업을 제공합니다.

 - 사용자 지정 양식을 인식하도록 학습된 사용자 지정 모델을 사용하여 양식 필드 및 콘텐츠를 인식합니다.  이러한 값은 `RecognizedForm` 개체의 컬렉션에서 반환됩니다. 예제 [사용자 지정 양식 분석](#analyze-forms-with-a-custom-model)을 참조하세요.
 - 모델을 학습하지 않고도 테이블, 줄 및 단어를 비롯한 양식 콘텐츠를 인식합니다.  양식 콘텐츠는 `FormPage` 개체의 컬렉션에서 반환됩니다. 예제 [양식 콘텐츠 인식](#recognize-form-content)을 참조하세요.
 - Form Recognizer 서비스에서 미리 학습된 영수증 모델을 사용하여 미국 영수증에서 공통 필드를 인식합니다.  이러한 필드 및 메타데이터는 `RecognizedForm` 개체의 컬렉션에서 반환됩니다. 예제 [영수증 인식](#recognize-receipts)을 참조하세요.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient`는 다음에 대한 작업을 제공합니다.

- 사용자 지정 양식에서 발견된 모든 필드와 값을 인식하도록 사용자 지정 모델을 학습합니다.  모델이 인식할 양식 유형과 각 양식 유형에 대해 추출할 필드를 나타내는 `CustomFormModel`이 반환됩니다.
- 사용자 지정 모델을 학습하여 사용자 지정 양식에 레이블을 지정하여 사용자가 지정한 특정 필드 및 값을 인식합니다.  모델이 추출하는 필드와 각 필드에 대한 예상 정확도를 나타내는 `CustomFormModel`이 반환됩니다.
- 계정에서 생성된 모델을 관리합니다.
- 하나의 Form Recognizer 리소스에서 다른 리소스로 사용자 지정 모델을 복사합니다.

[모델 학습](#train-a-custom-model) 및 [사용자 지정 모델 관리](#manage-custom-models)의 예제를 참조하세요.

[Form Recognizer 레이블 지정 도구](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습할 수도 있습니다.

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 .NET용 Form Recognizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [양식 콘텐츠 인식](#recognize-form-content)
* [영수증 확인](#recognize-receipts)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>클라이언트 인증

`Main()` 아래에 `AuthenticateClient`라는 새 메서드를 만듭니다. 이후 작업에서 이를 사용하여 Form Recognizer 서비스에 대한 요청을 인증합니다. 이 메서드는 `AzureKeyCredential` 개체를 사용하므로 필요한 경우 새 클라이언트 개체를 만들지 않고도 API 키를 업데이트할 수 있습니다.

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Form Recognizer 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)입니다.

```csharp
static private FormRecognizerClient AuthenticateClient(){
    string endpoint = "<replace-with-your-form-recognizer-endpoint-here>";
    string apiKey = "<replace-with-your-form-recognizer-key-here>";
    var credential = new AzureKeyCredential(apiKey);
    var client = new FormRecognizerClient(new Uri(endpoint), credential);
    return client;
}
```

## <a name="assets-for-testing"></a>테스트 자산 

이 가이드의 코드 조각은 URL을 통해 액세스되는 원격 양식을 사용합니다. 로컬 양식 문서를 대신 처리하려는 경우 [참조 설명서](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) 및 [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)의 관련 메서드를 참조하세요.

또한 학습 및 테스트 데이터에 대한 참조를 URL에 추가해야 합니다.
* 사용자 지정 모델 학습 데이터에 대한 SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 마우스 오른쪽 단추로 컨테이너를 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
* 아래 샘플에 포함된 샘플 및 영수증 이미지([GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)에서도 사용 가능)를 사용하거나 위의 단계를 사용하여 Blob 스토리지에 있는 개별 문서의 SAS URL을 가져올 수 있습니다. 

> [!NOTE]
> 이 가이드의 코드 조각은 URL을 통해 액세스되는 원격 양식을 사용합니다. 로컬 양식 문서를 대신 처리하려는 경우 [참조 설명서](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)의 관련 메서드를 참조하세요.

## <a name="recognize-form-content"></a>양식 콘텐츠 인식

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 줄 및 단어를 인식할 수 있습니다. 그러면 제출된 문서의 각 페이지당 하나씩 **FormPage** 개체 컬렉션이 반환됩니다. 

지정된 URI에서 파일의 콘텐츠를 인식하려면 `StartRecognizeContentFromUri` 메서드를 사용합니다.

```csharp
static async Task RecognizeContent(){
    var invoiceUri = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    var recognizeClient =  AuthenticateClient();
    FormPageCollection formPages = await recognizeClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
    foreach (FormPage page in formPages)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
        }

        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
            }
        }
    }
}
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    var analyzeForm = RecognizeContent();
    Task.WaitAll(analyzeForm);
}
```

### <a name="output"></a>출력

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="recognize-receipts"></a>영수증 확인

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 인식 및 추출하는 방법을 보여 줍니다.

URI를 통해 영수증을 확인하려면 `StartRecognizeReceiptsFromUri` 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 `RecognizedReceipt` 개체 컬렉션이 반환됩니다. 다음 코드는 지정된 URI에서 영수증을 처리하고 주요 필드와 값을 콘솔에 출력합니다.

```csharp
static async Task RecognizeReceipts(){
    var receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png";
    var recognizeClient = AuthenticateClient();
    RecognizedFormCollection receipts = await recognizeClient.StartRecognizeReceiptsFromUri(new Uri(receiptUrl)).WaitForCompletionAsync();

    // To see the list of the supported fields returned by service and its corresponding types, consult:
    // https://aka.ms/formrecognizer/receiptfields

    foreach (RecognizedForm receipt in receipts)
    {
        FormField merchantNameField;
        if (receipt.Fields.TryGetValue("MerchantName", out merchantNameField))
        {
            if (merchantNameField.Value.ValueType == FieldValueType.String)
            {
                string merchantName = merchantNameField.Value.AsString();

                Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
            }
        }

        FormField transactionDateField;
        if (receipt.Fields.TryGetValue("TransactionDate", out transactionDateField))
        {
            if (transactionDateField.Value.ValueType == FieldValueType.Date)
            {
                DateTime transactionDate = transactionDateField.Value.AsDate();

                Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
            }
        }

        FormField itemsField;
        if (receipt.Fields.TryGetValue("Items", out itemsField))
        {
            if (itemsField.Value.ValueType == FieldValueType.List)
            {
                foreach (FormField itemField in itemsField.Value.AsList())
                {
                    Console.WriteLine("Item:");

                    if (itemField.Value.ValueType == FieldValueType.Dictionary)
                    {
                        IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                        FormField itemNameField;
                        if (itemFields.TryGetValue("Name", out itemNameField))
                        {
                            if (itemNameField.Value.ValueType == FieldValueType.String)
                            {
                                string itemName = itemNameField.Value.AsString();

                                Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                            }
                        }

                        FormField itemTotalPriceField;
                        if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                        {
                            if (itemTotalPriceField.Value.ValueType == FieldValueType.Float)
                            {
                                float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                                Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                            }
                        }
                    }
                }
            }
        }

        FormField totalField;
        if (receipt.Fields.TryGetValue("Total", out totalField))
        {
            if (totalField.Value.ValueType == FieldValueType.Float)
            {
                float total = totalField.Value.AsFloat();

                Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
            }
        }
    }
}
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    var analyzeReceipts = RecognizeReceipts();
    Task.WaitAll(analyzeReceipts);
}
```

### <a name="output"></a>출력 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

이 섹션에서는 사용자 고유의 데이터로 모델을 학습시키는 방법을 보여 줍니다. 학습된 모델은 원본 양식 문서의 키/값 관계가 포함된 구조적 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="authenticate-the-training-client"></a>학습 클라이언트 인증

`AuthenticateClient` 아래에 `AuthenticateTrainingClient`라는 새 메서드를 만듭니다. 이후 작업에서 이를 사용하여 사용자 지정 모델을 학습합니다. 이 메서드는 `AzureKeyCredential` 개체(예: `AuthenticateClient`)를 사용하므로 필요한 경우 새 클라이언트 개체를 만들지 않고도 API 키를 업데이트할 수 있습니다.

```csharp
static private FormTrainingClient AuthenticateTrainingClient()
{
    string endpoint = "https://formre-ga-sdk-testing.cognitiveservices.azure.com/";
    string apiKey = "<replace-with-your-form-recognizer-key-here>";
    var credential = new AzureKeyCredential(apiKey);
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    return trainingClient;
}
```

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

학습 문서에 수동으로 레이블을 지정하지 않고 사용자 지정 양식에서 발견된 모든 필드와 값을 인식하도록 사용자 지정 모델을 학습시킬 수 있습니다. 다음 메서드는 지정된 문서 세트를 모델에 학습시키고 모델의 상태를 콘솔에 출력합니다. 반환되는 `CustomFormModel` 개체는 모델에서 인식할 수 있는 양식 유형과 각 양식 유형에서 추출할 수 있는 필드에 대한 정보를 포함합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

```csharp
static async Task TrainCustomModelNoLabels()
{
    var trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    var trainingClient = AuthenticateTrainingClient();
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingDataUrl), useTrainingLabels: false)
        .WaitForCompletionAsync();
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Training model started on: {model.TrainingStartedOn}");
    Console.WriteLine($"    Training model completed on: {model.TrainingCompletedOn}");

    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
}
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    var trainCustomModel = TrainCustomModelNoLabels();
    Task.WaitAll(trainCustomModel);
}
```

### <a name="output"></a>출력

이 응답은 가독성을 위해 잘렸습니다.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

학습 문서에 레이블을 수동으로 지정하여 사용자 지정 모델을 학습시킬 수도 있습니다. 일부 시나리오에서는 레이블을 사용하여 학습시키면 성능이 향상됩니다. 레이블을 사용하여 학습하려면 학습 문서와 별도로 Blob 스토리지 컨테이너에 특별한 레이블 정보 파일(`\<filename\>.pdf.labels.json`)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../quickstarts/label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 레이블 파일이 있으면 `true`로 설정된 `uselabels` 매개 변수를 사용하여 `StartTrainingAsync` 메서드를 호출할 수 있습니다. 반환된 `CustomFormModel`은 각 필드의 예상 정확도와 함께 모델이 추출할 수 있는 필드를 표시합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

```csharp
static async Task TrainCustomModelWithLabels()
{
    var trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    var trainingClient = AuthenticateTrainingClient();

    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingDataUrl), useTrainingLabels: true)
        .WaitForCompletionAsync();
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Training model started on: {model.TrainingStartedOn}");
    Console.WriteLine($"    Training model completed on: {model.TrainingCompletedOn}");

    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
}
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    var trainCustomModel = TrainCustomModelNoLabels();
    Task.WaitAll(trainCustomModel);
}
```

### <a name="output"></a>출력

이 응답은 가독성을 위해 잘렸습니다.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

이 섹션에서는 사용자 고유의 양식으로 학습시킨 모델을 사용하여 사용자 지정 양식 유형에서 키/값 정보 및 기타 콘텐츠를 추출하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 시나리오를 구현하려면 해당 ID를 아래 메서드에 전달할 수 있도록 모델을 이미 학습시킨 상태여야 합니다.

`StartRecognizeCustomFormsFromUri` 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 `RecognizedForm` 개체 컬렉션이 반환됩니다. 다음 코드에서는 분석 결과를 콘솔에 출력합니다. 인식된 각 필드와 해당 값을 신뢰도 점수와 함께 출력합니다.

```csharp
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var invoiceUri = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    var recognizeClient = AuthenticateClient();

    RecognizedFormCollection forms = await recognizeClient
    .StartRecognizeCustomFormsFromUri(modelId, new Uri(invoiceUri))
    .WaitForCompletionAsync();
    foreach (RecognizedForm form in forms)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");

            if (field.LabelData != null)
            {
                Console.WriteLine($"    Label: '{field.LabelData.Text}");
            }

            Console.WriteLine($"    Value: '{field.ValueData.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    var recognizeContentCustomModel = RecognizeContentCustomModel();
    Task.WaitAll(recognizeContentCustomModel);
}
```

### <a name="output"></a>출력

이 응답은 가독성을 위해 잘렸습니다.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>사용자 지정 모델 관리

이 섹션에서는 계정에 저장된 사용자 지정 모델을 관리하는 방법을 보여 줍니다. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer 리소스 계정의 모델 수 확인

다음 코드 블록은 Form Recognizer 계정에 저장된 모델 수를 확인하고 이를 계정 제한과 비교합니다.

```csharp
static void CheckNumberOfModels()
{
    var trainingClient = AuthenticateTrainingClient();
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit} models.");
}
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    CheckNumberOfModels();
}
```

### <a name="output"></a>출력 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>리소스 계정에 현재 저장된 모델 나열

다음 코드 블록은 계정의 현재 모델을 나열하고 해당 세부 정보를 콘솔에 출력합니다.

```csharp
static void ListAllModels()
{
    var trainingClient = AuthenticateTrainingClient();

    Pageable<CustomFormModelInfo> models = trainingClient.GetCustomModels();

    foreach (CustomFormModelInfo modelInfo in models)
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Training model started on: {modelInfo.TrainingStartedOn}");
        Console.WriteLine($"    Training model completed on: {modelInfo.TrainingCompletedOn}");
    }   
}
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    ListAllModels();
}
```

### <a name="output"></a>출력 

이 응답은 가독성을 위해 잘렸습니다.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>모델 ID를 사용하여 특정 모델 가져오기

다음 코드 블록은 [모델 학습](#train-a-model-without-labels) 섹션에서와 마찬가지로 새 모델을 학습시킨 다음, 해당 ID를 사용하여 두 번째 참조를 검색합니다.

```csharp
static void GetModelById()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var trainingClient = AuthenticateTrainingClient();
    CustomFormModel modelCopy = trainingClient.GetCustomModel(modelId);

    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following form types:");

    foreach (CustomFormSubmodel submodel in modelCopy.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
}    
```

이 메서드를 실행하려면 `Main`에서 호출해야 합니다. 

```csharp
static void Main(string[] args)
{
    GetModelById();
}
```

### <a name="output"></a>출력 

이 응답은 가독성을 위해 잘렸습니다.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>리소스 계정에서 모델 삭제

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다.

```csharp
static void DeleteModel()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var trainingClient = AuthenticateTrainingClient();
    trainingClient.DeleteModel(modelId);
} 
```

## <a name="run-the-application"></a>애플리케이션 실행

다음 명령을 사용하여 이 빠른 시작에서 읽은 많은 수의 함수를 사용하여 언제든지 애플리케이션을 실행할 수 있습니다.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>문제 해결

.NET SDK를 사용하여 Cognitive Services Form Recognizer 클라이언트 라이브러리와 상호 작용하는 경우 서비스에서 반환되는 오류로 인해 `RequestFailedException`이 발생합니다. REST API 요청에서 반환된 것과 동일한 HTTP 상태 코드를 포함합니다.

예를 들어 잘못된 URI를 사용하여 영수증 이미지를 제출하는 경우 "잘못된 요청"을 나타내는 `400` 오류가 반환됩니다.

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

작업의 클라이언트 요청 ID와 같은 추가 정보가 로깅됩니다.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer .NET 클라이언트 라이브러리를 사용하여 다양한 방식으로 모델을 학습시키고 양식을 분석했습니다. 다음으로, 더 나은 학습 데이터 세트를 만들고 더 정확한 모델을 생성하기 위한 팁에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](../../build-training-data-set.md)

* [Form Recognizer란?](../../overview.md)
* [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)에서 이 가이드의 샘플 코드 등을 확인할 수 있습니다.
