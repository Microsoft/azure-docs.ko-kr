---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 88be632e17912012618ab559f22f97487ad26c9c
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723499"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>키-값 쌍 및 테이블에 대한 양식 분석

다음으로, 새로 학습된 모델을 사용하여 문서를 분석하고 키-값 쌍 및 테이블을 추출합니다. 새 Python 스크립트에서 다음 코드를 실행하여 **[양식 분석](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API를 호출합니다. 스크립트를 실행하기 전에 다음과 같이 변경합니다.

1. `<file path>`을 양식의 파일 경로(예: C:\temp\file.pdf)로 바꿉니다. 이는 원격 파일의 URL일 수도 있습니다. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Test** 폴더에 있는 파일을 사용할 수 있습니다.
1. `<model_id>`를 이전 섹션에서 받은 모델 ID로 바꿉니다.
1. `<endpoint>`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `<file type>`을 파일 형식으로 바꿉니다. 지원되는 형식은 `application/pdf`, `image/jpeg`, `image/png`, `image/tiff`입니다.
1. `<subscription key>`를 구독 키로 바꿉니다.

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    
    # <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    


    ---



1. 코드를 확장명이 .py인 파일로 저장합니다. 예를 들어 *form-recognizer-analyze.py*입니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. 예들 들어 `python form-recognizer-analyze.py`입니다.

**양식 분석** API를 호출하면 **Operation-Location** 헤더가 있는 `201 (Success)` 응답을 받게 됩니다. 이 헤더의 값은 분석 작업의 결과를 추적하는 데 사용하는 ID입니다. 위의 스크립트는 이 헤더 값을 콘솔에 출력합니다.

## <a name="get-the-analyze-results"></a>분석 결과 가져오기

Python 스크립트 맨 아래에 다음 코드를 추가합니다. 새 API 호출에서 이전 호출의 ID 값을 사용하여 분석 결과를 검색합니다. **양식 분석** 작업은 비동기식이므로 이 스크립트는 결과를 사용할 수 있을 때까지 API를 일정한 간격으로 호출합니다. 1초 이상의 간격을 사용하는 것이 좋습니다.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
