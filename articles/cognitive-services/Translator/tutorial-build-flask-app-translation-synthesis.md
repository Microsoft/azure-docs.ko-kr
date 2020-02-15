---
title: '자습서: 텍스트를 번역, 합성 및 분석하는 Flask 앱 만들기 - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Flask 기반 웹앱을 빌드하여 텍스트를 번역하고, 감정을 분석하고 번역된 텍스트를 음성으로 합성합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: b41b68725b6747cbada13a9acc321724b3f89d67
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118583"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>자습서: Azure Cognitive Services를 사용하여 Flask 앱 만들기

이 자습서에서는 Azure Cognitive Services를 사용하여 텍스트를 번역하고 감정을 분석하고 번역된 텍스트 음성으로 합성하는 Flask 웹앱을 만듭니다. 주된 초점은 애플리케이션을 사용할 수 있게 해주는 Python 코드 및 Flask 경로이지만, 앱을 통합하는 HTML 및 Javascript로 도와 드리겠습니다. 문제가 발생한 경우 아래 피드백 단추를 사용하여 알려 주시기 바랍니다.

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
> * Azure 구독 키 받기
> * 개발 환경 설정 및 종속성 설치
> * Flask 앱 만들기
> * Translator Text API를 사용하여 텍스트 번역
> * Text Analytics를 사용하여 입력 텍스트 및 번역에 대한 긍정적/부정적 감정 분석
> * Speech Services를 사용하여 번역된 텍스트를 합성된 음성으로 변환
> * 로컬에서 Flask 앱 실행

> [!TIP]
> 앞으로 건너뛰고 모든 코드를 동시에 보려면 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)에서 전체 샘플을 빌드 명령과 함께 사용할 수 있습니다.

## <a name="what-is-flask"></a>Flask란 무엇인가요?

Flask는 웹 애플리케이션을 만들기 위한 마이크로 프레임워크입니다. 즉, Flask는 웹 애플리케이션을 만들 수 있게 해주는 도구, 라이브러리 및 기술을 제공합니다. 여기서 웹 애플리케이션은 일부 웹 페이지, 블로그, Wiki일 수도 있고 웹 기반 일정 애플리케이션 또는 상용 웹 사이트만큼 실질적인 애플리케이션일 수도 있습니다.

이 자습서를 완료한 후 좀 더 깊이 공부하려는 경우 아래 몇몇 링크가 도움이 될 수 있습니다.

* [Flask 설명서](http://flask.pocoo.org/)
* [Flask for Dummies - 초보자용 Flask 가이드](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 위해 필요한 소프트웨어 및 구독 키를 살펴보겠습니다.

* [Python 3.5.2 이상](https://www.python.org/downloads/)
* [Git 도구](https://git-scm.com/downloads)
* IDE 또는 [Visual Studio Code](https://code.visualstudio.com/)나 [Atom](https://atom.io/) 같은 텍스트 편집기  
* [Chrome](https://www.google.com/chrome/browser/) 또는 [Firefox](https://www.mozilla.org/firefox)
* **Translator Text** 구독 키(지역을 선택할 필요는 없습니다.)
* **미국 서부** 지역의 **Text Analytics** 구독 키.
* **미국 서부** 지역의 **Speech Services** 구독 키.

## <a name="create-an-account-and-subscribe-to-resources"></a>계정 만들기 및 리소스 구독

앞에서 설명한 대로 이 자습서를 실행하려면 구독 키 3개가 필요합니다. 즉, Azure 계정 내에 다음을 위한 리소스를 만들어야 합니다.
* Translator Text
* 텍스트 분석
* Speech Services

[Azure Portal에서 Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 단계별 지침을 사용하여 리소스를 만듭니다.

> [!IMPORTANT]
> 이 자습서를 위해 미국 서부 지역에서 리소스를 만드십시오. 다른 지역을 사용하는 경우 각 Python 파일에서 기본 URL을 조정해야 합니다.

## <a name="set-up-your-dev-environment"></a>개발 환경 설정

Flask 웹앱을 만들기 전에 프로젝트용 작업 디렉터리를 만들고 몇몇 Python 패키지를 설치해야 합니다.

### <a name="create-a-working-directory"></a>작업 디렉터리 만들기

1. 명령줄(Windows) 또는 터미널(macOS/Linux)을 엽니다. 그런 다음, 프로젝트용 작업 디렉터리 및 하위 디렉터리를 만듭니다.  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. 프로젝트의 작업 디렉터리로 변경합니다.  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>`virtualenv`를 사용하여 가상 환경을 만들고 활성화합니다.

이제 `virtualenv`를 사용하여 Flask 앱용 가상 환경을 만들어 보겠습니다. 가상 환경을 사용하면 정리된 작업 환경을 갖게 됩니다.

1. 작업 디렉터리에서 다음 명령을 실행하여 가상 환경을 만듭니다. **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   가상 환경이 Python 3을 사용해야 한다는 것을 명시적으로 선언했습니다. 이렇게 하면 여러 버전의 Python을 설치한 사용자가 올바른 버전을 사용하게 됩니다.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   자습서 사용을 단순하게 유지하기 위해 가상 환경의 이름을 venv로 지정합니다.

2. 가상 환경을 활성화하는 명령은 플랫폼/셸에 따라 달라집니다.   

   | 플랫폼 | 셸 | 명령 |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | 명령줄 | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   이 명령을 실행한 후 `venv`를 사용하여 명령줄 또는 터미널 세션을 시작해야 합니다.

3. 언제든지 명령줄 또는 터미널에 `deactivate` 명령을 입력하여 세션을 비활성화할 수 있습니다.

> [!NOTE]
> Python에는 가상 환경 만들기 및 관리에 대한 광범위한 설명서가 있습니다. [virtualenv](https://virtualenv.pypa.io/en/latest/)를 참조하세요.

### <a name="install-requests"></a>Requests 설치

Requests는 HTTP 1.1 요청을 보내는 데 사용되는 인기 있는 모듈입니다. 수동으로 URL에 쿼리 문자열을 추가하거나 POST 데이터에 대한 양식 인코딩을 수행할 필요는 없습니다.

1. Requests를 설치하려면 다음을 실행합니다.

   ```
   pip install requests
   ```

> [!NOTE]
> Requests에 대해 자세히 알아보려면 [Requests: 사용자를 위한 HTTP](https://2.python-requests.org/en/master/)를 참조하세요.

### <a name="install-and-configure-flask"></a>Flask 설치 및 구성

다음으로, Flask를 설치해야 합니다. 웹앱에 대한 라우팅은 Flask에서 처리하므로, 최종 사용자로부터 구독 키를 숨기는 서버 간 호출을 실행할 수 있습니다.

1. Flask를 설치하려면 다음을 실행합니다.
   ```
   pip install Flask
   ```
   Flask가 설치되어 있는지 확인해 보겠습니다. 다음을 실행합니다.
   ```
   flask --version
   ```
   터미널에 버전이 표시될 것입니다. 다른 내용이 표시되면 문제가 발생했음을 의미합니다.

2. Flask 앱을 실행하려면 Flask에서 flask 명령 또는 Python의 -m 스위치를 사용할 수 있습니다. `FLASK_APP` 환경 변수를 내보내서 작동할 앱을 터미널에 지시해야 그렇게 할 수 있습니다.

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Flask 앱 만들기

이 섹션에서는 사용자가 앱의 루트에 도달하면 HTML 파일을 반환하는 기본 Flask 앱을 만듭니다. 코드를 찾아내는 데 너무 많은 시간을 보내지 마십시오. 이 파일을 나중에 돌아와 업데이트할 것입니다.

### <a name="what-is-a-flask-route"></a>Flask 경로란?

잠시 "[경로](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)"에 대해 설명하겠습니다. 라우팅은 URL을 특정 함수에 바인딩하기 위해 사용됩니다. Flask는 경로 데코레이터를 사용하여 특정 URL에 대한 함수를 등록합니다. 예를 들어 사용자가 웹앱의 루트(`/`)로 이동하는 경우 `index.html`이 렌더링됩니다.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

이 홈을 만드는 예제를 하나 더 살펴보겠습니다.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

이 코드는 사용자가 `http://your-web-app.com/about`으로 이동할 때 `about.html` 파일이 렌더링되도록 합니다.

이 샘플에서는 사용자를 위해 HTML 페이지를 렌더링하는 방법을 보여줍니다. 라우팅을 이용하면 단추를 누를 때 경로를 사용하여 API를 호출하거나 홈 페이지를 떠날 필요 없이 얼마든지 많은 작업을 실행할 수도 있습니다. 번역, 감정 및 음성 합성에 대한 경로를 만들 때 이 기능이 작동하는 것을 보게 됩니다.

### <a name="get-started"></a>시작하기

1. IDE에서 프로젝트를 연 다음, 작업 디렉터리의 루트에 `app.py`라는 디렉터리를 만듭니다. 다음으로, 이 코드를 `app.py`에 복사하고 저장합니다.

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   이 코드 블록은 사용자가 웹앱의 루트(`/`)로 이동할 때마다 `index.html`을 표시하도록 앱에 지시합니다.

2. 다음으로, 웹앱용 프런트 엔드를 만들어 보겠습니다. `templates` 디렉터리에 `index.html`이라는 파일을 만듭니다. 그런 다음, 이 코드를 `templates/index.html`에 복사합니다.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Flask 앱을 테스트해 보겠습니다. 터미널에서 다음을 실행합니다.

   ```
   flask run
   ```

4. 브라우저를 열고 제공된 URL로 이동합니다. 단일 페이지 앱이 표시될 것입니다. **Ctrl + C**를 눌러 앱을 종료합니다.

## <a name="translate-text"></a>텍스트 번역

이제 간단한 Flask 앱의 작동 원리를 파악했으므로 다음 단계로 넘어가겠습니다.

* Translator Text API를 호출하고 응답을 반환하는 몇몇 Python 작성
* Python 코드를 호출하는 Flask 경로 만들기
* 텍스트 입력 및 번역 영역, 언어 선택, 그리고 번역 단추를 사용하여 HTML 업데이트
* 사용자가 HTML에서 Flask 앱과 상호 작용할 수 있게 해주는 Javascript 작성

### <a name="call-the-translator-text-api"></a>Translator Text API 호출

가장 먼저 해야 할 일은 Translator Text API를 호출하는 함수를 작성하는 것입니다. 이 함수는 `text_input` 및 `language_output` 두 인수를 사용합니다. 이 함수는 사용자가 앱에서 번역 단추를 누를 때마다 호출됩니다. HTML의 텍스트 영역은 `text_input`으로 전송되며, HTML의 언어 선택 값은 `language_output`으로 전송됩니다.

1. 먼저 작업 디렉터리의 루트에 `translate.py`라는 파일을 만들어 보겠습니다.
2. 다음으로, 이 코드를 `translate.py`에 추가합니다. 이 함수는 `text_input` 및 `language_output` 두 인수를 사용합니다.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Translator Text 구독 키를 추가하고 저장합니다.

### <a name="add-a-route-to-apppy"></a>`app.py`에 경로 추가

다음으로, Flask 앱에 `translate.py`를 호출하는 경로를 만들어야 합니다. 이 경로는 사용자가 앱에서 번역 단추를 누를 때마다 호출됩니다.

이 앱의 경우 경로는 `POST` 요청을 수락할 것입니다. 왜냐하면 이 함수에는 번역할 텍스트 및 번역에 대한 출력 언어가 필요하기 때문입니다.

Flask는 각 요청을 구문 분석하고 관리하도록 돕기 위한 도우미 함수를 제공합니다. 제공된 코드에서 `get_json()`은 `POST` 요청의 데이터를 JSON으로 반환합니다. 그런 다음, `data['text']` 및 `data['to']`를 사용하여 텍스트 및 출력 언어 값을 `translate.py`에서 사용할 수 있는 `get_translation()` 함수에 전달합니다. 이 데이터를 웹앱에 표시해야 하므로, 마지막 단계는 응답을 JSON으로 반환하는 것입니다.

다음 섹션에서는 감정 분석 및 음성 합성을 위한 경로를 만들면서 이 프로세스를 반복합니다.

1. `app.py`를 열고 `app.py`의 위쪽에 있는 import 문을 찾은 후 다음 줄을 추가합니다.

   ```python
   import translate
   ```
   이제 Flask 앱은 `translate.py`를 통해 이 메서드를 사용할 수 있습니다.

2. 이 코드를 `app.py`의 끝에 복사하고 저장합니다.

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` 업데이트

이제 텍스트를 번역할 함수 및 Flask 앱에서 해당 함수를 호출하는 경로가 있으므로, 다음 단계는 앱용 HTML의 빌드를 시작하는 것입니다. 아래의 HTML은 몇 가지 기능을 수행합니다.

* 사용자가 번역할 텍스트를 입력할 수 있는 텍스트 영역을 제공합니다.
* 언어 선택을 포함합니다.
* 검색된 언어 및 번역 중에 반환된 신뢰 점수를 렌더링하는 HTML 요소를 포함합니다.
* 번역 출력이 표시되는 읽기 전용 텍스트 영역을 제공합니다.
* 이 자습서의 뒷부분에서 이 파일을 추가할 감정 분석 및 음성 합성 코드에 대한 자리 표시자를 포함합니다.

`index.html`을 업데이트해 보겠습니다.

1. `index.html`을 열고 이 코드 주석을 찾습니다.
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. 해당 코드 주석을 이 HTML 블록으로 바꿉니다.
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

다음 단계는 몇몇 Javascript를 작성하는 것입니다. 이는 HTML과 Flask 경로 간의 다리 같은 역할을 합니다.

### <a name="create-mainjs"></a>`main.js`을 만듭니다.  

`main.js` 파일은 HTML과 Flask 경로 간의 다리 같은 역할을 합니다. 이 앱은 jQuery, Ajax 및 XMLHttpRequest의 조합을 사용하여 콘텐츠를 렌더링하고 Flask 경로에 대해 `POST`를 요청합니다.

아래 코드에서는 HTML의 콘텐츠를 사용하여 Flask 경로에 대한 요청을 생성합니다. 구체적으로, 텍스트 영역 및 언어 선택의 콘텐츠를 변수에 할당한 다음, 
`translate-text` 요청에 함께 전달합니다.

그런 다음, 코드에서는 응답을 반복하고 번역문, 검색된 언어 및 신뢰 점수를 사용하여 HTML을 업데이트합니다.

1. IDE에서 `static/scripts` 디렉터리에 `main.js`라는 파일을 만듭니다.
2. 이 코드를 `static/scripts/main.js`에 복사합니다.
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>번역 테스트

앱에서 번역을 테스트해 보겠습니다.

```
flask run
```

제공된 서버 주소로 이동합니다. 입력 영역에 텍스트를 입력하고 언어를 선택하고 번역을 누릅니다. 번역문이 표시될 것입니다. 표시되지 않는다면 구독 키를 추가했는지 확인하십시오.

> [!TIP]
> 변경한 내용이 표시되지않거나 앱이 예상한 방법으로 작동하지 않는 경우 캐시를 지우거나 프라이빗/incognito 창을 열어 보십시오.

**CTRL + C**를 눌러 앱을 종료한 다음, 다음 섹션으로 이동합니다.

## <a name="analyze-sentiment"></a>감정 분석

[Text Analytics API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)를 사용하여 감정 분석을 수행하거나 텍스트에서 핵심 문구를 추출하거나 원본 언어를 검색할 수 있습니다. 이 앱에서는 감정 분석을 사용하여 제공된 텍스트가 긍정적인지, 중립적인지 또는 부정적인지 결정합니다. 이 API는 0~1 사이의 숫자 점수를 반환합니다. 점수가 1에 가까울수록 긍정적 감정을 나타내고 점수가 0에 가까울수록 부정적 감정을 나타냅니다.

이 섹션에서는 몇 가지 작업을 수행합니다.

* Text Analytics API를 호출하여 감정 분석을 수행하고 응답을 반환하는 몇몇
Python 작성
* Python 코드를 호출하는 Flask 경로 만들기
* 감정 점수에 대한 영역 및 분석을 수행하는 단추를 사용하여 HTML 업데이트
* 사용자가 HTML에서 Flask 앱과 상호 작용할 수 있게 해주는 Javascript 작성

### <a name="call-the-text-analytics-api"></a>텍스트 분석 API 호출

Text Analytics API를 호출하는 함수를 작성해 보겠습니다. 이 함수는 `input_text`, `input_language`, `output_text` 및 `output_language` 네 인수를 사용합니다. 이 함수는 사용자가 앱에서 감정 분석 실행 단추를 누를 때마다 호출됩니다. 사용자가 텍스트 영역 및 언어 선택에서 제공하는 데이터, 그리고 검색된 언어 및 번역 출력은 요청할 때마다 제공됩니다. 응답 개체는 원본 및 번역에 대한 감정 점수를 포함합니다. 다음 섹션에서는 응답을 구문 분석하고 해당 응답을 앱에 사용하는 몇몇 Javascript를 작성하게 됩니다. 지금은 Text Analytics API 호출에 집중하겠습니다.

1. 작업 디렉터리의 루트에 `sentiment.py`라는 파일을 만들어 보겠습니다.
2. 다음으로, 이 코드를 `sentiment.py`에 추가합니다.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Text Analytics 구독 키를 추가하고 저장합니다.

### <a name="add-a-route-to-apppy"></a>`app.py`에 경로 추가

Flask 앱에 `sentiment.py`를 호출하는 경로를 만들어 보겠습니다. 이 경로는 사용자가 앱에서 감정 분석 실행 단추를 누를 때마다 호출됩니다. 이 함수에는 인수가 필요하므로 이 경로도 번역에 대한 경로와 마찬가지로 `POST` 요청을 수락할 것입니다.

1. `app.py`를 열고 `app.py`의 위쪽에 있는 import 문을 찾은 후 해당 명령문을 업데이트합니다.

   ```python
   import translate, sentiment
   ```
   이제 Flask 앱은 `sentiment.py`를 통해 이 메서드를 사용할 수 있습니다.

2. 이 코드를 `app.py`의 끝에 복사하고 저장합니다.
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` 업데이트

이제 감정 분석을 실행하는 함수 및 Flask 앱에서 해당 함수를 호출하는 경로가 있으므로, 다음 단계는 앱용 HTML의 작성을 시작하는 것입니다. 아래의 HTML은 몇 가지 기능을 수행합니다.

* 앱에 감정 분석을 실행하는 단추 추가
* 감정 점수 매기기에 대해 설명하는 요소 추가
* 감정 점수를 표시하는 요소 추가

1. `index.html`을 열고 이 코드 주석을 찾습니다.
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. 해당 코드 주석을 이 HTML 블록으로 바꿉니다.
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>`main.js` 업데이트

아래 코드에서는 HTML의 콘텐츠를 사용하여 Flask 경로에 대한 요청을 생성합니다. 구체적으로, 텍스트 영역 및 언어 선택의 콘텐츠를 변수에 할당한 다음, 
`sentiment-analysis` 경로 요청에 포함하여 함께 전달합니다.

그런 다음, 코드에서는 응답을 반복하고 감정 점수를 사용하여 HTML을 업데이트합니다.

1. IDE에서 `static` 디렉터리에 `main.js`라는 파일을 만듭니다.

2. 이 코드를 `static/scripts/main.js`에 복사합니다.
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>감정 분석 테스트

앱에서 감정 분석을 테스트해 보겠습니다.

```
flask run
```

제공된 서버 주소로 이동합니다. 입력 영역에 텍스트를 입력하고 언어를 선택하고 번역을 누릅니다. 번역문이 표시될 것입니다. 다음으로, 감정 분석 실행 단추를 누릅니다. 두 개의 점수가 표시될 것입니다. 표시되지 않는다면 구독 키를 추가했는지 확인하십시오.

> [!TIP]
> 변경한 내용이 표시되지않거나 앱이 예상한 방법으로 작동하지 않는 경우 캐시를 지우거나 프라이빗/incognito 창을 열어 보십시오.

**CTRL + C**를 눌러 앱을 종료한 다음, 다음 섹션으로 이동합니다.

## <a name="convert-text-to-speech"></a>텍스트 음성 변환

[텍스트 음성 변환 API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)를 사용하면 앱에서 텍스트를 자연스러운 사람의 음성처럼 합성된 음성으로 변환할 수 있습니다. 이 서비스는 표준, 인공신경망 및 사용자 지정 음성을 지원합니다. 샘플 앱은 몇 가지 사용할 수 있는 음성을 사용합니다. 전체 목록은 [지원되는 언어](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)를 참조하세요.

이 섹션에서는 몇 가지 작업을 수행합니다.

* 텍스트 음성 변환 API를 사용하여 텍스트를 음성으로 변환하는 몇몇 Python 작성
* Python 코드를 호출하는 Flask 경로 만들기
* 텍스트를 음성으로 변환하는 단추 및 오디오 재생을 위한 요소를 사용하여 HTML 업데이트
* 사용자가 Flask 앱과 상호 작용할 수 있게 해주는 Javascript 작성

### <a name="call-the-text-to-speech-api"></a>텍스트 음성 변환 API 호출

텍스트를 음성으로 변환하는 함수를 작성해 보겠습니다. 이 함수는 `input_text` 및 `voice_font` 두 인수를 사용합니다. 이 함수는 사용자가 앱에서 텍스트 음성 변환 단추를 누를 때마다 호출됩니다. `input_text`는 텍스트를 번역하는 호출에 의해 반환된 번역 출력이고 `voice_font`는 HTML의 음성 글꼴 선택기에서 가져온 값입니다.

1. 작업 디렉터리의 루트에 `synthesize.py`라는 파일을 만들어 보겠습니다.

2. 다음으로, 이 코드를 `synthesize.py`에 추가합니다.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Speech Services 구독 키를 추가하고 저장합니다.

### <a name="add-a-route-to-apppy"></a>`app.py`에 경로 추가

Flask 앱에 `synthesize.py`를 호출하는 경로를 만들어 보겠습니다. 이 경로는 사용자가 앱에서 텍스트 음성 변환 단추를 누를 때마다 호출됩니다. 이 함수에는 합성할 텍스트 및 재생용 음성 글꼴 두 인수가 필요하므로 이 경로도 번역 및 감정 분석용 경로와 마찬가지로 `POST` 요청을 수락할 것입니다.

1. `app.py`를 열고 `app.py`의 위쪽에 있는 import 문을 찾은 후 해당 명령문을 업데이트합니다.

   ```python
   import translate, sentiment, synthesize
   ```
   이제 Flask 앱은 `synthesize.py`를 통해 이 메서드를 사용할 수 있습니다.

2. 이 코드를 `app.py`의 끝에 복사하고 저장합니다.

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>`index.html` 업데이트

이제 텍스트를 음성으로 변환하는 함수 및 Flask 앱에서 해당 함수를 호출하는 경로가 있으므로, 다음 단계는 앱용 HTML의 작성을 시작하는 것입니다. 아래의 HTML은 몇 가지 기능을 수행합니다.

* 음성 선택 드롭다운 메뉴 제공
* 텍스트를 음성으로 변환하는 단추 추가
* 합성된 음성을 재생하는 데 사용되는 오디오 요소 추가

1. `index.html`을 열고 이 코드 주석을 찾습니다.
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. 해당 코드 주석을 이 HTML 블록으로 바꿉니다.
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. 다음으로, 아래 코드 주석을 찾습니다.
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. 해당 코드 주석을 이 HTML 블록으로 바꿉니다.

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. 작업 내용을 저장합니다.

### <a name="update-mainjs"></a>`main.js` 업데이트

아래 코드에서는 HTML의 콘텐츠를 사용하여 Flask 경로에 대한 요청을 생성합니다. 구체적으로, 번역문 및 음성 글꼴을 변수에 할당한 다음, `text-to-speech` 경로 요청에 함께 전달합니다.

그런 다음, 코드에서는 응답을 반복하고 감정 점수를 사용하여 HTML을 업데이트합니다.

1. IDE에서 `static` 디렉터리에 `main.js`라는 파일을 만듭니다.
2. 이 코드를 `static/scripts/main.js`에 복사합니다.
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. 이제 거의 완료되었습니다. 마지막으로 할 작업은 `main.js`에서 번역을 위해 선택한 언어를 기반으로 음성 글꼴을 자동으로 선택하는 몇몇 코드를 추가하는 것입니다. 이 코드 블록을 `main.js`에 추가합니다.
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>앱 테스트

앱에서 음성 합성을 테스트해 보겠습니다.

```
flask run
```

제공된 서버 주소로 이동합니다. 입력 영역에 텍스트를 입력하고 언어를 선택하고 번역을 누릅니다. 번역문이 표시될 것입니다. 다음으로, 음성을 선택한 다음, 텍스트 음성 변환 단추를 누릅니다. 번역문이 합성된 음성으로 재생될 것입니다. 표시되지 않는다면 구독 키를 추가했는지 확인하십시오.

> [!TIP]
> 변경한 내용이 표시되지않거나 앱이 예상한 방법으로 작동하지 않는 경우 캐시를 지우거나 프라이빗/incognito 창을 열어 보십시오.

끝났습니다. 이제 번역을 수행하고 감정을 분석하고 음성을 합성하는 제대로 작동하는 앱을 갖게 되었습니다. **Ctrl + C**를 눌러 앱을 종료합니다. 다른 [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)도 꼭 확인해 보세요.

## <a name="get-the-source-code"></a>소스 코드 가져오기

이 프로젝트에 대한 소스 코드는 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Translator Text API 참조](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [텍스트 분석 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [텍스트를 음성으로 변환 API 참조](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
