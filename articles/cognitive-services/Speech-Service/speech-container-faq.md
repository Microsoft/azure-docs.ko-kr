---
title: 음성 서비스 컨테이너 FAQ (질문과 대답)
titleSuffix: Azure Cognitive Services
description: 음성 컨테이너를 설치하고 실행합니다. 음성 텍스트 speech 오디오는 응용 프로그램, 도구 또는 장치에서 사용 하거나 표시할 수 있는 텍스트에 실시간으로 스트리밍합니다. 텍스트 음성 변환은 입력 텍스트를 인간과 유사한 합성 음성으로 변환합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: b13a6944290f58f5ede239dee60610d67fff8b1c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918471"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>음성 서비스 컨테이너 FAQ (질문과 대답)

컨테이너에서 음성 서비스를 사용 하는 경우 지원으로 에스컬레이션 하기 전에이 질문과 대답 컬렉션을 사용 합니다. 이 문서에서는 일반에서 기술까지 다양 한 질문을 캡처합니다. 답변을 확장 하려면 해당 질문을 클릭 합니다.

## <a name="general-questions"></a>일반적인 질문

<details>
<summary>
<b>음성 컨테이너는 어떻게 작동 하며 어떻게 설정 하나요?</b>
</summary>

**답변:** 프로덕션 클러스터를 설정할 때 고려해 야 할 몇 가지 사항이 있습니다. 첫째, 단일 언어를 설정 하는 것은 동일한 컴퓨터에서 여러 컨테이너를 설정 하는 것이 중요 한 문제가 되어서는 안 됩니다. 문제가 발생 하는 경우 하드웨어 관련 문제일 수 있습니다. 즉, 먼저 리소스를 살펴보세요. CPU 및 메모리 사양입니다.

잠시 후 `ja-JP` 컨테이너 및 최신 모델을 고려 합니다. 어쿠스틱 모델은 CPU를 가장 많이 필요로 하는 부분으로, 언어 모델은 대부분의 메모리를 요구 합니다. 하도록 벤치마킹를 사용 하는 경우 오디오를 실시간으로 이동 하는 경우 (예: 마이크에서) 한 번의 음성-텍스트 요청을 처리 하기 위해 약 0.6 CPU 코어가 소요 됩니다. 파일에서와 같이 실시간으로 오디오를 공급 하는 경우 해당 사용은 이중 (1.2 x 코어)이 될 수 있습니다. 한편, 아래에 나열 된 메모리는 음성을 디코딩하는 데 사용할 수 있는 운영 체제입니다. 파일 캐시에 상주 하는 언어 모델의 실제 전체 크기를 고려 *하지* 않습니다. 의 `ja-JP` 추가 2gb 인 경우에는 `en-US` 더 많은 (6-7 g b)이 될 수 있습니다.

메모리가 부족 한 컴퓨터에서 여러 언어를 배포 하려는 경우에는 파일 캐시가 가득 찼을 수 있으며 OS는 페이지를 통해 모델의 페이지에 강제로 적용 됩니다. 실행 중인 기록의 경우이로 인해 문제가 발생 하 여 성능 저하가 발생 하 고 다른 성능에 영향을 미칠 수 있습니다.

또한 [AVX2 (고급 벡터 확장)](speech-container-howto.md#advanced-vector-extension-support) 명령 집합을 사용 하 여 컴퓨터에 대 한 실행 파일을 사전에 패키지 합니다. AVX512 명령 집합을 사용 하는 컴퓨터는 해당 대상에 대해 코드를 생성 해야 하며 10 개 언어에 대 한 10 개의 컨테이너를 시작 하면 CPU가 일시적으로 고갈 될 수 있습니다. 다음과 같은 메시지가 docker 로그에 표시 됩니다.

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

마지막으로, 변수를 사용 하 여 *단일* 컨테이너 내에서 원하는 디코더 수를 설정할 수 있습니다 `DECODER MAX_COUNT` . 따라서 기본적으로 SKU (CPU/memory)로 시작 하 고이를 최대한 활용 하는 방법을 제안할 수 있습니다. 좋은 출발점은 권장 되는 호스트 컴퓨터 리소스 사양을 참조 하는 것입니다.

<br>
</details>

<details>
<summary>
<b>온-프레미스 음성-텍스트 컨테이너의 용량 계획 및 비용 예측에 도움이 될 수 있나요?</b>
</summary>

**답변:** 일괄 처리 모드의 컨테이너 용량의 경우 각 디코더는 단일 인식에 대해 두 개의 CPU 코어를 사용 하 여 2 ~ 3 배를 실시간으로 처리할 수 있습니다. 컨테이너 인스턴스당 2 개 이상의 동시 인식을 유지 하지 않는 것이 좋지만, 안정성/가용성을 위해 더 많은 컨테이너 인스턴스를 부하 분산 장치 뒤에 실행 하는 것이 좋습니다.

그러나 각 컨테이너 인스턴스가 더 많은 디코더를 사용 하 여 실행 될 수도 있습니다. 예를 들어 8 개 코어 컴퓨터 (각각 2 배 이상)에서 컨테이너 인스턴스당 7 개의 디코더를 설정 하 여 15x 처리량을 얻을 수 있습니다. 알고 있어야 하는 매개 변수가 있습니다 `DECODER_MAX_COUNT` . 극단적인 경우 안정성 및 대기 시간 문제가 발생 하 고 처리량이 크게 증가 합니다. 마이크의 경우,이는 1x 실시간입니다. 전체 사용량은 단일 인식에 대해 핵심입니다.

일괄 처리 모드에서 1 K 시간/일을 처리 하는 시나리오의 경우 극단적인 경우 3 개의 Vm은 24 시간 이내에 처리할 수 있지만 보장 되지는 않습니다. 스파이크 일, 장애 조치 (failover), 업데이트 및를 처리 하 여 최소 backup/BCP를 제공 하려면 클러스터당 3이 아닌 4-5 대의 컴퓨터와 2 개 이상의 클러스터를 사용 하는 것이 좋습니다.

하드웨어의 경우 표준 Azure VM을 `DS13_v2` 참조로 사용 합니다 (AVX2 명령 집합을 사용 하도록 설정한 경우 각 코어는 2.6 GHz 이상 이어야 함).

| 인스턴스  | vCPU | RAM    | 임시 저장소 | AHB를 사용 하 여 종 량 제 | AHB로 1 년 예약 (% 절약) | AHB로 예약 된 3 년 (% 절감) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112GiB      | $0.598/시간            | $0.3528/시간 (~ 41%)                 | $0.2333/시간 (~ 61%)                  |

디자인 참조 (1 K 시간/일 오디오 일괄 처리를 처리 하는 5 개의 Vm 클러스터)에 따라 1 년 하드웨어 비용은 다음과 같습니다.

> 2 (클러스터) * 5 (클러스터 당 Vm) * $0.3528/시간 * 365 (일) * 24 (시간) = $31K/year

물리적 컴퓨터에 매핑할 때 일반적인 예측은 1 개의 vCPU = 1 실제 CPU 코어입니다. 실제로 1vCPU는 단일 코어 보다 더 강력 합니다.

온-프레미스의 경우 이러한 추가 요소는 모두 다음과 같이 제공 됩니다.

- 실제 CPU의 유형 및 코어 수
- 동일한 box/컴퓨터에서 동시에 실행 되는 Cpu 수
- Vm 설정 방법
- 하이퍼 스레딩/다중 스레딩을 사용 하는 방법
- 메모리 공유 방법
- OS 등

일반적으로 Azure 환경으로는 조정 되지 않습니다. 다른 오버 헤드를 고려 하 여, 안전 추정은 10 개의 실제 CPU 코어 = 8 Azure vCPU를 말합니다. 인기 있는 Cpu에는 8 개의 코어만 있습니다. 온-프레미스 배포를 사용 하는 경우 Azure Vm을 사용 하는 것 보다 비용이 더 많이 듭니다. 또한 감가 상각 요금을 고려 합니다.

서비스 비용은 온라인 서비스 (음성-텍스트의 경우 $1/시간)와 동일 합니다. 음성 서비스 비용은 다음과 같습니다.

> $1 * 1000 * 365 = $365K

Microsoft에 부과 되는 유지 관리 비용은 서비스 수준 및 서비스 내용에 따라 달라 집니다. 이는 온사이트 서비스가 관련 된 경우 기본 수준의 경우 29.99/month부터 수백 개까지 다양 합니다. 서비스/유지 관리에 대 한 대략적인 수치는 $300/시간입니다. 사용자 비용은 포함 되지 않습니다. 다른 인프라 비용 (예: 저장소, 네트워크, 부하 분산 장치)은 포함 되지 않습니다.

<br>
</details>

<details>
<summary>
<b>문장 부호가 기록에 없는 이유는 무엇입니까?</b>
</summary>

**답변:** 는 `speech_recognition_language=<YOUR_LANGUAGE>` 요청에서 참조 클라이언트를 사용 하는 경우 명시적으로 구성 해야 합니다.

예를 들면

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
출력은 다음과 같습니다.

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>음성 컨테이너에서 사용자 지정 음향 모델 및 언어 모델을 사용할 수 있나요?</b>
</summary>

현재 사용자 지정 언어 모델이 나 사용자 지정 음향 모델 중 하나의 모델 ID만 전달할 수 있습니다.

**답변:** 어쿠스틱 및 언어 모델을 동시에 지원 *하지* 않기로 결정 했습니다. 이는 API 나누기를 줄이기 위해 통합 식별자가 만들어질 때까지 적용 된 상태로 유지 됩니다. 지금은 지금은 지원 되지 않습니다.

<br>
</details>

<details>
<summary>
<b>사용자 지정 음성-텍스트 컨테이너에서 이러한 오류를 설명할 수 있나요?</b>
</summary>

**오류 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**대답 1:** 최신 사용자 지정 모델을 사용 하 여 학습 하는 경우 현재이를 지원 하지 않습니다. 이전 버전으로 학습 하는 경우를 사용할 수 있습니다. 아직 최신 버전을 지원 하기 위해 노력 하 고 있습니다.

기본적으로 사용자 지정 컨테이너는 Halide 또는 ONNX 기반 음향 모델 (사용자 지정 학습 포털의 기본값)을 지원 하지 않습니다. 이는 사용자 지정 모델이 암호화 되지 않아서 ONNX 모델을 노출 하지 않기 때문입니다. 언어 모델은 괜찮습니다. 고객은 사용자 지정 학습에 대해 이전 비 ONNX 모델을 명시적으로 선택 해야 합니다. 정확도는 영향을 받지 않습니다. 모델 크기는 더 클 수 있습니다 (100 MB).

> 지원 모델 > 20190220 (v 4.5 통합)

**오류 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**답변 2:** 대/소문자를 구분 하는 요청에 올바른 음성 이름을 제공 해야 합니다. 전체 서비스 이름 매핑을 참조 하세요. `en-US-JessaRUS` `en-US-JessaNeural` 텍스트 음성 변환의 컨테이너 버전에서 현재 사용할 수 없으므로를 사용 해야 합니다.

**오류 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**답변 3:** Cognitive Services 리소스가 아닌 음성 리소스를 만듭니다.


<br>
</details>

<details>
<summary>
<b>지원 되는 API 프로토콜, REST 또는 WS는 무엇 인가요?</b>
</summary>

**답변:** 음성 텍스트 및 사용자 지정 음성-텍스트 컨테이너의 경우 현재 websocket 기반 프로토콜만 지원 합니다. SDK는 WS 에서만 호출을 지원 하지만 REST는 지원 하지 않습니다. REST 지원을 추가 하는 계획은 있지만 현재는 에타가 아닙니다. 항상 공식 설명서를 참조 하세요. [쿼리 예측 끝점](speech-container-howto.md#query-the-containers-prediction-endpoint)을 참조 하세요.

<br>
</details>

<details>
<summary>
<b>음성 컨테이너에 대해 CentOS 지원 되나요?</b>
</summary>

**답변:** CentOS 7은 아직 Python SDK에서 지원 되지 않으며 Ubuntu 19.04도 지원 되지 않습니다.

Python Speech SDK 패키지는 다음과 같은 운영 체제용으로 제공됩니다.
- **Windows** -x64 및 x86
- **Mac** -Macos X 버전 10.12 이상
- **Linux** -ubuntu 16.04, ubuntu 18.04, Debian 9 (x64)

환경 설정에 대 한 자세한 내용은 [Python platform setup](quickstarts/setup-platform.md?pivots=programming-language-python)을 참조 하십시오. 지금은 Ubuntu 18.04이 권장 된 버전입니다.

<br>
</details>

<details>
<summary>
<b>LUIS 예측 끝점을 호출 하려고 할 때 오류가 발생 하는 이유는 무엇 인가요?</b>
</summary>

IoT Edge 배포에서 LUIS 컨테이너를 사용 하 고 있으며 다른 컨테이너에서 LUIS 예측 끝점을 호출 하려고 합니다. LUIS 컨테이너는 포트 5001에서 수신 대기 하며, 사용 중인 URL은 다음과 같습니다.

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

수신 하는 오류는 다음과 같습니다.

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

LUIS 컨테이너 로그에 요청이 표시 되 고 메시지가 표시 됩니다.

```cmd
The request path /luis//predict" does not match a supported file type.
```

무슨 의미인가요? 무엇이 누락 되었나요? [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk)에서 음성 SDK에 대 한 예제를 수행 했습니다. 시나리오는 PC 마이크에서 직접 오디오를 검색 하 고 학습 된 LUIS 앱을 기반으로 의도를 확인 하는 것입니다. 에 연결 된 예제는 정확히이를 수행 합니다. LUIS 클라우드 기반 서비스에서 잘 작동 합니다. 음성 SDK를 사용 하면 음성 텍스트 API에 대 한 별도의 명시적 호출을 수행 하지 않고 LUIS에 대 한 두 번째 호출을 수행 하지 않아도 됩니다.

따라서 수행 하려는 모든 작업은 클라우드에서 LUIS을 사용 하 여 LUIS 컨테이너를 사용 하는 시나리오에서 전환 하는 것입니다. 음성 SDK가 하나에 대해 작동 하는 경우에는 다른 방법을 사용할 수 없습니다.

**답변:** LUIS 컨테이너에 대해 음성 SDK를 사용 하면 안 됩니다. LUIS 컨테이너를 사용 하는 경우 LUIS SDK 또는 LUIS REST API를 사용 해야 합니다. 음성 SDK는 음성 컨테이너에 대해 사용 해야 합니다.

클라우드가 컨테이너와 다릅니다. 클라우드는 여러 집계 된 컨테이너 (마이크로 서비스 라고도 함)로 구성 될 수 있습니다. 따라서 LUIS 컨테이너가 있으므로 음성 컨테이너 2 개가 별도 컨테이너에 있습니다. 음성 컨테이너는 음성만 수행 합니다. LUIS 컨테이너는 LUIS만 수행 합니다. 클라우드에서 두 컨테이너가 배포 되는 것으로 알려져 있고, 원격 클라이언트가 클라우드로 이동 하 고, 음성으로 이동 하 고, 다시 클라우드로 이동 하 고, 클라우드로 이동 하 고, LUIS를 수행할 수 있도록 하는 기능을 제공 합니다. 클라이언트에서 음성으로 이동 하 고, 클라우드에 유지 하 고, LUIS으로 이동한 다음 클라이언트에 게 다시 돌아올 수 있는 기능을 제공 합니다. 따라서이 시나리오에서 음성 SDK는 오디오를 사용 하는 음성 클라우드 컨테이너로 이동 하 고, 음성 클라우드 컨테이너는 텍스트를 사용 하 여 LUIS 클라우드 컨테이너와 통신 합니다. LUIS 컨테이너에는 오디오를 수락 하는 개념이 없습니다. LUIS 컨테이너에서 스트리밍 오디오를 수락 하는 것은 적합 하지 않습니다. LUIS는 텍스트 기반 서비스입니다. 온-프레미스를 사용 하는 경우 고객은 두 컨테이너를 모두 배포 하 고 고객의 온-프레미스에 있는 컨테이너 간에 오케스트레이션 하지 않는 것으로 가정 하 고, 두 컨테이너 모두 온-프레미스에 배포 된 경우에는 SR-IOV를 먼저 이동 하 여 클라이언트로 다시 이동 하 고, 고객은 해당 텍스트를 가져와서 LUIS로 이동 하는 것이 부담 되지 않습니다.

<br>
</details>

<details>
<summary>
<b>MacOS, Speech container 및 Python SDK를 사용 하 여 오류가 발생 하는 이유는 무엇 인가요?</b>
</summary>

Transcribed로 *.wav* 파일을 보내면 결과가 다음과 같이 반환 됩니다.

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Websocket이 올바르게 설정 되어 있음을 알 수 있습니다.

**답변:** 해당 되는 경우 [이 GitHub 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)를 참조 하세요. 여기에서 해결 된 해결 방법을 [제시](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)합니다.

참조 버전 1.8에서이 문제를 해결 했습니다.


<br>
</details>

<details>
<summary>
<b>음성 컨테이너 끝점의 차이점은 무엇 인가요?</b>
</summary>

테스트할 함수와 SDK 및 REST Api를 테스트 하는 방법을 비롯 하 여 다음과 같은 테스트 메트릭을 채울 수 있습니다. 특히 기존 doc/샘플에서 볼 수 없었던 "대화형" 및 "대화"의 차이점입니다.

| 엔드포인트                                                | 기능 테스트                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | 텍스트 합성 (텍스트 음성 변환)                                  |     | 예      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services 온-프레미스 dictation v1 websocket 끝점        | 예 | 아니요       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Cognitive Services 온-프레미스 대화형 v1 websocket 끝점  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 인식 서비스 온-프레미스 대화 v1 websocket 끝점 |     |          |

**답변:** 다음에 대 한 fusion입니다.
- 컨테이너에 대해 받아쓰기 끝점을 시도 하는 사람들 (해당 URL의 받는 방법에 대해 알 수 없음)
- 컨테이너에 있는 1 개의<sup>st</sup> 파티 끝점입니다.
- 3 개의<sup>st</sup> `speech.hypothesis` <sup>ts</sup> 파트 끝점이 받아쓰기 끝점에 대해 반환 하는 메시지 대신, 한 개의 st 파티 끝점은 메시지 대신 음성 조각화 메시지를 반환 합니다.
- 참조 퀵 스타트 모두 사용 `RecognizeOnce` (대화형 모드)
- 상호 작용을 필요로 하는 메시지에 대 한 어설션이 `speech.fragment` 대화형 모드로 반환 되지 않습니다.
- 해당 어설션이 릴리스 빌드에서 발생 하는 것을 말합니다.

해결 방법은 코드에서 연속 인식 사용으로 전환 하거나, (더 빠른) 컨테이너의 대화형 또는 연속 끝점에 연결 하는 것입니다.
코드에 대해 끝점을/speech/recognition/interactive/cognitiveservices/v1로 설정 합니다. `host:port`

다양 한 모드에 대 한 자세한 내용은 음성 모드-아래를 참조 하십시오.

[!INCLUDE [speech-modes](includes/speech-modes.md)]

적절 한 픽스는 온-프레미스 지원이 있는 SDK 1.8와 함께 제공 됩니다 (올바른 끝점을 선택 하는 것이 좋습니다 .이는 온라인 서비스 보다는 좋지 않음). 그 동안에는 연속 인식을 위한 샘플이 있습니다 .이 샘플을 가리키지 않는 이유는 무엇 인가요?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>다양 한 오디오 파일에 어떤 모드를 사용 해야 하나요?</b>
</summary>

**답변:** 다음은 [Python을 사용 하는 빠른](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python)시작입니다. 문서 사이트에서 링크 된 다른 언어를 찾을 수 있습니다.

대화형, 대화 및 받아쓰기를 명확 하 게 하기 위한 것입니다. 이 방법은 서비스가 음성 요청을 처리 하는 특정 방법을 지정 하는 고급 방법입니다. 아쉽게도 온-프레미스 컨테이너의 경우 로컬 컴퓨터를 포함 하므로 전체 URI를 지정 해야 하므로이 정보는 추상화에서 누설 됩니다. SDK 팀과 협력 하 여 나중에이를 더 사용할 수 있도록 합니다.

<br>
</details>

<details>
<summary>
<b>트랜잭션/초/코어의 대략적인 측정을 어떻게 벤치 마크 할 수 있나요?</b>
</summary>

**답변:** 다음은 기존 모델에서 예상 되는 대략적인 수치 중 일부입니다. GA에서 제공 하는 것 보다 더 나은에 대해 변경 됩니다.

- 파일의 경우 제한은 음성 SDK의 2 배입니다. 오디오의 처음 5 초는 제한 되지 않습니다. 디코더는 약 3 배 실시간 작업을 수행할 수 있습니다. 이를 위해 전체 CPU 사용량은 단일 인식을 위해 2 코어에 가깝습니다.
- Mic의 경우에는 1x 실시간입니다. 단일 인식을 위해 전체 사용량은 약 1 코어입니다.

이는 모두 docker 로그에서 확인할 수 있습니다. 실제로는 세션 및 구/utterance 통계를 사용 하 여 줄을 덤프 하며 여기에는 RTF 숫자가 포함 됩니다.


<br>
</details>

<details>
<summary>
<b>음성 컨테이너 사용을 위해 오디오 파일을 chucks로 분할 하는 것이 일반적 인가요?</b>
</summary>

현재 요금제는 기존 오디오 파일을 가져와서 10 초 청크로 분할 하 고 컨테이너를 통해 전송 하는 것입니다. 적합 한 시나리오 입니까?  컨테이너를 사용 하 여 더 큰 오디오 파일을 처리 하는 더 나은 방법이 있나요?

**답변:** 음성 SDK를 사용 하 여 파일을 제공 하면 올바른 작업을 수행 합니다. 파일의 청크를 만들어야 하는 이유는 무엇 인가요?


<br>
</details>

<details>
<summary>
<b>여러 컨테이너가 동일한 호스트에서 실행 되도록 어떻게 할까요? 하 시겠습니까?</b>
</summary>

문서는 다른 포트를 표시 하 고 있지만 LUIS 컨테이너는 여전히 포트 5000에서 수신 대기 하 고 있습니까?

**답변:** 사용해 보세요 `-p <outside_unique_port>:5000` . 예: `-p 5001:5000`


<br>
</details>

## <a name="technical-questions"></a>기술 관련 질문

<details>
<summary>
<b>15 초 길이의 오디오를 처리 하는 비 batch Api를 어떻게 가져올 수 &lt; 있나요?</b>
</summary>

**답변:** `RecognizeOnce()` 대화형 모드에서는 길이 발언가 짧을 것으로 예상 되는 음성 명령에만 사용할 수 있는 최대 15 초간 오디오를 처리 합니다. `StartContinuousRecognition()`받아쓰기 또는 대화에를 사용 하는 경우 15 초 제한이 없습니다.


<br>
</details>

<details>
<summary>
<b>권장 되는 리소스, CPU 및 RAM은 무엇 인가요? 50의 동시 요청은 무엇 인가요?</b>
</summary>

4 코어, 4gb RAM 핸들의 동시 요청 수는 몇 개입니까? 예를 들어, 50 동시 요청을 처리 해야 하는 경우 권장 되는 코어 및 RAM의 수는 몇 개입니까?

**답변:** 실시간으로는 최근 8 개를 사용 하 여 `en-US` 6 개 이상의 동시 요청 보다 많은 docker 컨테이너를 사용 하는 것이 좋습니다. 16 개 코어를 초과 하는 crazier을 가져오고, NUMA (non-uniform memory access) 노드가 중요 한 것으로 인식 됩니다. 다음 표에서는 각 음성 컨테이너에 대 한 최소 및 권장 리소스 할당을 설명 합니다.

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

| 컨테이너      | 최소             | 권장         |
|----------------|---------------------|---------------------|
| 음성 텍스트 변환 | 2 코어, 2gb 메모리 | 4 코어, 4gb 메모리 |

# <a name="custom-speech-to-text"></a>[Custom Speech 텍스트](#tab/cstt)

| 컨테이너             | 최소             | 권장         |
|-----------------------|---------------------|---------------------|
| Custom Speech 텍스트 | 2 코어, 2gb 메모리 | 4 코어, 4gb 메모리 |

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

| 컨테이너      | 최소             | 권장         |
|----------------|---------------------|---------------------|
| 텍스트 음성 변환 | 1 코어, 2gb 메모리 | 2 코어, 3gb 메모리 |

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

| 컨테이너             | 최소             | 권장         |
|-----------------------|---------------------|---------------------|
| 사용자 지정 텍스트 음성 변환 | 1 코어, 2gb 메모리 | 2 코어, 3gb 메모리 |

***

- 각 코어는 2.6 GHz 이상 이어야 합니다.
- 파일의 경우, 제한 사항은 음성 SDK에서 2x (오디오의 처음 5 초는 제한 되지 않음)입니다.
- 디코더는 약 2 ~ 3 배 실시간 작업을 수행할 수 있습니다. 이를 위해 전체 CPU 사용량은 단일 인식을 위해 두 코어에 가깝습니다. 따라서 컨테이너 인스턴스당 세 개 이상의 활성 연결을 유지 하지 않는 것이 좋습니다. 극단적인 측면은와 같은 8 개의 코어 컴퓨터에서 10 개의 디코더를 2 배 이상 실시간으로 배치 하는 것 `DS13_V2` 입니다. 컨테이너 버전 1.3 이상에서는 설정을 시도할 수 있는 매개 변수가 있습니다 `DECODER_MAX_COUNT=20` .
- 마이크의 경우,이는 1x 실시간입니다. 전체 사용량은 단일 인식에 대해 핵심입니다.

보유 하 고 있는 총 오디오 시간 수를 고려 합니다. 숫자가 클 경우 안정성/가용성을 향상 시키기 위해 부하 분산 장치 뒤에 있는 단일 상자 또는 여러 상자에서 더 많은 컨테이너 인스턴스를 실행 하는 것이 좋습니다. 오케스트레이션은 Kubernetes (K8S) 및 투구를 사용 하거나 Docker 작성을 사용 하 여 수행할 수 있습니다.

예를 들어 1000 시간/24 시간을 처리 하기 위해 VM 당 10 개의 인스턴스/디코더를 사용 하 여 3-4 Vm을 설정 하 려 했습니다.

<br>
</details>

<details>
<summary>
<b>음성 컨테이너는 문장 부호를 지원 하나요?</b>
</summary>

**답변:** 온-프레미스 컨테이너에서 사용할 수 있는 대문자 (ITN)가 있습니다. 문장 부호는 언어에 따라 다르며 중국어 및 일본어를 비롯 한 일부 언어에 대해서는 지원 되지 않습니다.

기존 *do* 컨테이너에 대해 암시적 및 기본 문장 부호를 지원 하지만 기본적으로 지원 됩니다 `off` . 즉 `.` , 예제에서 문자를 가져올 수 있지만 문자는 가져올 수 없습니다 `。` . 이 암시적 논리를 사용 하기 위해 다음은 음성 SDK를 사용 하 여 Python에서이 작업을 수행 하는 방법의 예입니다 (다른 언어와 유사 함).

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>음성 텍스트 컨테이너에 데이터를 게시 하려고 할 때 404 오류가 발생 하는 이유는 무엇 인가요?</b>
</summary>

예제 HTTP POST는 다음과 같습니다.

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**답변:** 음성-텍스트 컨테이너에서는 REST API 지원 하지 않으므로 Speech SDK를 통해 Websocket만 지원 합니다. 항상 공식 설명서를 참조 하세요. [쿼리 예측 끝점](speech-container-howto.md#query-the-containers-prediction-endpoint)을 참조 하세요.

<br>
</details>

<details>
<summary>
<b>음성 텍스트 서비스를 사용 하는 경우이 오류가 발생 하는 이유는 무엇 인가요?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**답변:** 이는 일반적으로 음성 인식 컨테이너에서 사용할 수 있는 것 보다 빠르게 오디오를 공급할 때 발생 합니다. 클라이언트 버퍼가 가득 차면 취소가 트리거됩니다. 오디오를 전송 하는 경우 동시성 및 RTF를 제어 해야 합니다.

<br>
</details>

<details>
<summary>
<b>C + + 예제에서 이러한 텍스트 음성 변환 컨테이너 오류를 설명할 수 있나요?</b>
</summary>

**답변:** 컨테이너 버전이 1.3 보다 오래 된 경우이 코드를 사용 해야 합니다.

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

이전 컨테이너에는 API와 함께 작동 하는 데 필요한 끝점이 없습니다 `FromHost` . 버전 1.3에 컨테이너를 사용 하는 경우 다음 코드를 사용 해야 합니다.

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

다음은 API를 사용 하는 예입니다 `FromEndpoint` .

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 `SetSpeechSynthesisVoiceName`업데이트 된 텍스트 음성 변환 엔진을 사용 하는 컨테이너에 음성 이름이 필요 하기 때문에 함수가 호출 됩니다.

<br>
</details>

<details>
<summary>
<b>음성 컨테이너를 사용 하 여 음성 SDK의 v 1.7을 사용 하려면 어떻게 해야 하나요?</b>
</summary>

**답변:** 음성 컨테이너에는 다른 용도에 대 한 세 가지 끝점이 있습니다. 이러한 끝점은 음성 모드로 정의 됩니다. 다음을 참조 하세요.

[!INCLUDE [speech-modes](includes/speech-modes.md)]

서로 다른 용도로 사용 되며 다르게 사용 됩니다.

Python [샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- 사용자 지정 끝점을 사용 하는 단일 인식 (대화형 모드) ( `SpeechConfig` 끝점 매개 변수 사용)의 경우을 참조 `speech_recognize_once_from_file_with_custom_endpoint_parameters()` 하세요.
- 연속 인식 (대화 모드)의 경우 위와 같이 사용자 지정 끝점을 사용 하도록를 수정 `speech_recognize_continuous_from_file()` 합니다 .를 참조 하세요.
- 위와 같은 샘플에서 받아쓰기를 사용 하도록 설정 하려면 (필요한 경우에만) 코드를 만든 후 바로 `speech_config` 코드를 추가 `speech_config.enable_dictation()` 합니다.

받아쓰기를 사용 하도록 설정 하려면 c #에서 함수를 호출 `SpeechConfig.EnableDictation()` 합니다.

### <a name="fromendpoint-apis"></a>`FromEndpoint` Api
| 언어 | API 세부 정보 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 현재 지원 되지 않으며 계획도 아닙니다. |

<br>
</details>

<details>
<summary>
<b>음성 컨테이너에서 음성 SDK의 v 1.8을 사용 하려면 어떻게 해야 하나요?</b>
</summary>

**답변:** 새 API가 있습니다 `FromHost` . 기존 Api를 바꾸거나 수정 하지는 않습니다. 사용자 지정 호스트를 사용 하 여 음성 구성을 만드는 다른 방법을 추가 하기만 하면 됩니다.

### <a name="fromhost-apis"></a>`FromHost` Api

| 언어 | API 세부 정보 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 현재 지원되지 않음 |

> 매개 변수: host (필수), 구독 키 (선택 사항, 서비스를 사용할 수 없는 경우).

호스트의 형식은 `protocol://hostname:port` 선택적입니다 `:port` (아래 참조).
- 컨테이너를 로컬로 실행 하는 경우 호스트 이름은 `localhost` 입니다.
- 컨테이너가 원격 서버에서 실행 되는 경우 해당 서버의 호스트 이름 또는 IPv4 주소를 사용 합니다.

음성 텍스트에 대 한 호스트 매개 변수 예:
- `ws://localhost:5000` -포트 5000을 사용 하 여 로컬 컨테이너에 대 한 비보안 연결
- `ws://some.host.com:5000` -원격 서버에서 실행 되는 컨테이너에 대 한 비보안 연결

위의 Python 샘플 이지만 `host` 대신 매개 변수를 사용 합니다 `endpoint` .

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너](speech-container-howto.md)
