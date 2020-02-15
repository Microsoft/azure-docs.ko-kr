---
title: API Management를 사용 하 여 웹 서비스 관리
titleSuffix: ML Studio (classic) - Azure
description: API Management를 사용하여 AzureML 웹 서비스를 관리하는 방법에 대한 가이드입니다. 사용자 액세스, 사용량 제한 및 대시보드 모니터링을 정의하여 REST API 엔드포인트를 관리합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: d0e9fff56949125c5fa797e0e4ef7e1183448dd0
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168581"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>API Management를 사용 하 여 Azure Machine Learning Studio (클래식) 웹 서비스 관리
## <a name="overview"></a>개요
이 가이드에서는 API Management 사용을 빠르게 시작 하 여 Azure Machine Learning Studio (클래식) 웹 서비스를 관리 하는 방법을 보여 줍니다.

## <a name="what-is-azure-api-management"></a>Azure API Management란?
Azure API Management는 사용자 액세스, 사용 제한 및 대시보드 모니터링을 정의하여 REST API 엔드포인트를 관리할 수 있는 Azure 서비스입니다. 자세한 내용은 [AZURE API management 사이트](https://azure.microsoft.com/services/api-management/) 를 참조 하세요. Azure API Management를 시작 하려면 [가져오기 및 게시 가이드](/azure/api-management/import-and-publish)를 참조 하세요. 이 가이드를 기반으로 하는 이 다른 가이드에서는 알림 구성, 가격 책정 계층, 응답 처리, 사용자 인증, 제품 생산, 개발자 구독 및 사용량 대시보딩을 포함하는 다양한 주제를 다룹니다.

## <a name="prerequisites"></a>사전 요구 사항
이 가이드를 완료하려면 다음이 필요합니다.

* Azure 계정.
* AzureML 계정.
* AzureML 실험에 대한 작업 영역, 서비스 및 api_key는 웹 서비스로 배포됩니다. AzureML 실험을 만드는 방법에 대 한 자세한 내용은 [Studio 빠른](create-experiment.md)시작을 참조 하세요. Studio (클래식) 실험을 웹 서비스로 배포 하는 방법에 대 한 자세한 내용은 [studio 배포 방법](deploy-a-machine-learning-web-service.md) 에서 AzureML 실험을 웹 서비스로 배포 하는 방법에 대 한 자세한 내용을 참조 하세요. 또는 간단한 AzureML 실험을 만들고 테스트하고 이를 웹 서비스로 배포하는 방법에 대한 지침이 부록 A에 있습니다.

## <a name="create-an-api-management-instance"></a>API Management 인스턴스 만들기

API Management 인스턴스를 사용하여 Azure Machine Learning 웹 서비스를 관리할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **+ 리소스 만들기**를 선택합니다.
3. 검색 상자에 "API Management"를 입력한 다음 "API Management" 리소스를 선택합니다.
4. **만들기**를 클릭합니다.
5. **이름** 값은 고유한 URL을 만드는 데 사용됩니다(이 예제에서는 "demoazureml" 사용).
6. 서비스 인스턴스의 **구독**, **리소스 그룹** 및 **위치**를 선택합니다.
7. **조직 이름**의 값을 지정합니다(이 예제에서는 "demoazureml" 사용).
8. **관리자 전자 메일** 입력 - 이 전자 메일은 API Management 시스템의 알림에 사용됩니다.
9. **만들기**를 클릭합니다.

새 서비스를 만드는 데에는 최대 30분이 걸릴 수 있습니다.

![create-service](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>API 만들기
서비스 인스턴스를 만든 후 다음 단계는 API를 만드는 것입니다. API는 클라이언트 애플리케이션에서 호출할 수 있는 작업 집합으로 구성됩니다. API 작업은 기존 웹 서비스로 프록시 처리됩니다. 이 가이드는 기존 AzureML RRS 및 BES 웹 서비스에 대한 프록시인 API를 만듭니다.

API를 만들려면:

1. Azure Portal에서 사용자가 만든 서비스 인스턴스를 엽니다.
2. 왼쪽 탐색 창에서 **API**를 선택합니다.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. **API 추가**를 클릭합니다.
2. **Web API 이름**을 입력합니다(이 예제에서는 "AzureML 데모 API" 사용).
3. **웹 서비스 URL**에 "`https://ussouthcentral.services.azureml.net`"을 입력합니다.
4. **Web API URL 접미사"를 입력합니다. 고객이 서비스 인스턴스에 요청을 보내는 데 사용할 URL의 마지막 부분이 됩니다(이 예제에서는 "azureml-demo" 사용).
5. **Web API URL 구성표**에서 **HTTPS**를 선택합니다.
6. **제품**에서 **시작**을 선택합니다.
7. **저장**을 클릭합니다.


## <a name="add-the-operations"></a>작업 추가

게시자 포털에서 작업을 API에 추가하고 구성합니다. 게시자 포털에 액세스하려면 API Management 서비스에 대해 Azure Portal에서 **게시자 포털**을 클릭하고, **API**, **작업**을 선택하고, **작업 추가**를 클릭합니다.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

**새 작업** 창이 표시되고 **서명** 탭이 기본으로 선택됩니다.

## <a name="add-rrs-operation"></a>RRS 작업 추가
먼저 AzureML RRS 서비스에 대한 작업을 만듭니다.

1. **HTTP 동사**에서 **게시**를 선택합니다.
2. **URL 템플릿**에서 "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`"를 입력합니다.
3. **표시 이름**을 입력합니다(이 예제에서는 "RRS 실행" 사용).

   ![add-rrs-operation-signature](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. 왼쪽의 **응답** > **추가**를 클릭하여 **200 확인**을 선택합니다.
5. **저장** 을 클릭하여 이 작업을 저장합니다.

   ![add-rrs-operation-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES 작업 추가

> [!NOTE]
> BES 작업에 대한 스크린샷은 RRS 작업을 추가하는 스크린샷과 매우 유사하므로 여기에 포함되어 있지 않습니다.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Batch 실행 작업 제출(시작하지는 않음)

1. API에 BES 작업을 추가하려면 **작업 추가**를 클릭합니다.
2. **HTTP 동사**에서 **게시**를 선택합니다.
3. **URL 템플릿**에서 "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`"를 입력합니다.
4. **표시 이름**을 입력합니다(이 예제에서는 "BES 제출" 사용).
5. 왼쪽의 **응답** > **추가**를 클릭하여 **200 확인**을 선택합니다.
6. **저장**을 클릭합니다.

### <a name="start-a-batch-execution-job"></a>일괄 처리 실행 작업 시작

1. API에 BES 작업을 추가하려면 **작업 추가**를 클릭합니다.
2. **HTTP 동사**에서 **게시**를 선택합니다.
3. **HTTP 동사**에 "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`"을 입력합니다.
4. **표시 이름**을 입력합니다(이 예제에서는 "BES 시작" 사용).
6. 왼쪽의 **응답** > **추가**를 클릭하여 **200 확인**을 선택합니다.
7. **저장**을 클릭합니다.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>일괄 처리 실행 작업의 상태 또는 결과 가져오기

1. API에 BES 작업을 추가하려면 **작업 추가**를 클릭합니다.
2. **HTTP 동사**에서 **가져오기**를 선택합니다.
3. **URL 템플릿**에서 "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`"를 입력합니다.
4. **표시 이름**을 입력합니다(이 예제에서는 "BES 상태" 사용).
6. 왼쪽의 **응답** > **추가**를 클릭하여 **200 확인**을 선택합니다.
7. **저장**을 클릭합니다.

### <a name="delete-a-batch-execution-job"></a>일괄 처리 실행 작업 삭제

1. API에 BES 작업을 추가하려면 **작업 추가**를 클릭합니다.
2. **HTTP 동사**에서 **삭제**를 선택합니다.
3. **URL 템플릿**에서 "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`"를 입력합니다.
4. **표시 이름**을 입력합니다(이 예제에서는 "BES 삭제" 사용).
5. 왼쪽의 **응답** > **추가**를 클릭하여 **200 확인**을 선택합니다.
6. **저장**을 클릭합니다.

## <a name="call-an-operation-from-the-developer-portal"></a>개발자 포털에서 작업 호출

개발자 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. 이 단계에서는 **AzureML 데모 API**에 추가 된 **rr Execute** 메서드를 호출 합니다. 

1. **개발자 포털**을 클릭합니다.

   ![developer-portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. 맨 위 메뉴에서 **API**를 클릭하고 **AzureML 데모 API**를 클릭하여 사용 가능한 작업을 확인합니다.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. 작업에 대해 **RRS 실행** 을 선택합니다. **사용해 보세요.** 를 클릭합니다.

   ![try-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. **요청 매개 변수**에 **작업 영역** 및 **서비스**를 입력하고, **apiversion**에 "2.0을 입력하고, **세부 정보**에 "true"를 입력합니다. AzureML 웹 서비스 대시보드에서 **작업 영역** 및 **서비스**를 찾을 수 있습니다(부록 A에서 **웹 서비스 테스트** 참조).

   **요청 헤더**에서 **헤더 추가**를 클릭하고, "콘텐츠 형식" 및 "application/json"을 입력합니다. **헤더 추가**를 다시 클릭하고, "인증" 및 "전달자 *\<사용자 서비스 API-KEY\>* "를 입력합니다. AzureML 웹 서비스 대시보드에서 API-KEY를 찾을 수 있습니다(부록 A에서 **웹 서비스 테스트** 참조).

   **요청 본문**에 `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`를 입력합니다.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. **보내기**를 클릭합니다.

   ![send](./media/manage-web-service-endpoints-using-api-management/send.png)

작업 호출 후에는 개발자 포털에 백 엔드 서비스의 **요청된 URL** 및 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

![response-status](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>부록 A - 간단한 AzureML 웹 서비스 만들기 및 테스트
### <a name="creating-the-experiment"></a>실험 만들기
간단한 AzureML 실험을 만들고 웹 서비스로 배포하는 단계는 다음과 같습니다. 웹 서비스에서는 임의 텍스트 열을 입력으로 사용하고 정수로 표시되는 기능 집합을 반환합니다. 다음은 그 예입니다.

| 텍스트 | 해시된 텍스트 |
| --- | --- |
| This is a good day |1 1 2 2 0 2 0 1 |

먼저 선택한 브라우저를 사용하여 [https://studio.azureml.net/](https://studio.azureml.net/)으로 이동하고 자격 증명을 입력하여 로그인합니다. 그리고 새 실험을 만듭니다.

![search-experiment-templates](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

실험 이름을 **SimpleFeatureHashingExperiment**로 바꿉니다. **저장된 데이터 세트**를 확장하고 **Amazon의 도서 리뷰**를 실험으로 끌어서 놓습니다.

![simple-feature-hashing-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

**데이터 변환** 및 **조작**을 확장하고 **데이터 세트의 열 선택**을 실험으로 끌어서 놓습니다. **Amazon의 도서 리뷰**를 **데이터 세트의 열 선택**에 연결합니다.

![책 리뷰 데이터 집합 모듈을 프로젝트 열 모듈에 연결](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

**데이터 세트의 열 선택**, **열 선택기 시작**을 차례로 클릭하고 **Col2**를 선택합니다. 확인 표시를 클릭하여 변경 내용을 적용합니다.

![열 이름을 사용 하 여 열 선택](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

**텍스트 분석**을 확장하고 **기능 해싱**을 실험으로 끌어서 놓습니다. **데이터 세트의 열 선택**을 **기능 해싱**에 연결합니다.

![connect-project-columns](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

**해싱 비트 크기**로 **3**을 입력합니다. 8(23)개 열이 생성됩니다.

![hashing-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

이때 **실행** 을 클릭하여 실험을 테스트할 수 있습니다.

![실행](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>웹 서비스 만들기
이제 웹 서비스를 만듭니다. **웹 서비스**를 확장하고 **입력**을 실험으로 끌어서 놓습니다. **입력**을 **기능 해싱**에 연결합니다. **출력** 을 실험으로 끌어서 놓습니다. **출력**을 **기능 해싱**에 연결합니다.

![output-to-feature-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

**웹 서비스 게시**를 클릭합니다.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

**예** 를 클릭하여 실험을 게시합니다.

![yes-to-publish](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>웹 서비스 테스트
AzureML 웹 서비스는 RSS(요청/응답 서비스) 및 BES(일괄 처리 실행 서비스) 엔드포인트로 구성됩니다. RSS는 동기 실행에 사용됩니다. BES는 비동기 작업 실행에 사용됩니다. 다음 샘플 Python 소스로 웹 서비스를 테스트하려면 Python용 Azure SDK를 다운로드해야 할 수 있습니다( [Python을 설치하는 방법](/azure/python/python-sdk-azure-install)참조).

또한 다음 샘플 원본에 대한 실험의 **workspace**, **service** 및 **api_key**가 필요합니다. 웹 서비스 대시보드에서 실험의 **요청/응답** 또는 **Batch 실행**을 클릭하여 workspace 및 service를 찾을 수 있습니다.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

웹 서비스 대시보드에서 실험을 클릭하여 **api_key**를 찾을 수 있습니다.

![find-api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>RRS 엔드포인트 테스트
##### <a name="test-button"></a>테스트 단추
RRS 엔드포인트를 테스트하는 간편한 방법은 웹 서비스 대시보드에서 **테스트** 를 클릭하는 것입니다.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

**col2**로 **This is a good day**를 입력합니다. 확인 표시를 클릭합니다.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

다음과 같이 표시됩니다.

![sample-output](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>샘플 코드
RRS를 테스트하는 또 다른 방법은 클라이언트 코드를 사용하는 것입니다. 대시보드에서 **요청/응답**을 클릭하고 아래쪽으로 스크롤하면 C#, Python 및 R에 대한 샘플 코드가 표시됩니다. 요청 URI, 헤더 및 본문을 포함한 RRS 요청 구문도 표시됩니다.

이 가이드에서는 작동하는 Python 예제를 보여 줍니다. 실험의 **workspace**, **service** 및 **api_key**를 사용하여 예제를 수정해야 합니다.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>BES 엔드포인트 테스트
대시보드에서 **Batch 실행**을 클릭하고 아래쪽으로 스크롤합니다. C#, Python 및 R에 대한 샘플 코드가 표시됩니다. 작업을 제출하고, 작업을 시작하고, 작업의 상태나 결과를 가져오고, 작업을 삭제하기 위한 BES 요청 구문도 표시됩니다.

이 가이드에서는 작동하는 Python 예제를 보여 줍니다. 실험의 **workspace**, **service** 및 **api_key**를 사용하여 예제를 수정해야 합니다. 또한 **스토리지 계정 이름**, **스토리지 계정 키** 및 **스토리지 컨테이너 이름**을 수정해야 합니다. 마지막으로 **입력 파일**의 위치와 **출력 파일**의 위치를 수정해야 합니다.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "wb+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
