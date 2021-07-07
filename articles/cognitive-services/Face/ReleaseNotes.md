---
title: Face 서비스의 새로운 기능이란?
titleSuffix: Azure Cognitive Services
description: Face 서비스의 릴리스 정보에는 다양한 버전에 대한 릴리스 변경 기록이 포함되어 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
ms.custom: contperf-fy21q3
ms.openlocfilehash: c580828d29e92ecef7ecc73b8f3e5843c3ecd23d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078883"
---
# <a name="whats-new-in-face-service"></a>Face 서비스의 새로운 기능이란?

Azure Face 서비스는 지속적으로 업데이트됩니다. 이 문서를 사용하여 향상된 기능, 수정 및 설명서 업데이트를 최신 상태로 유지하세요.

## <a name="february-2021"></a>2021년 2월

### <a name="new-face-api-detection-model"></a>새 Face API 감지 모델
* 새 감지 03 모델은 현재 사용할 수 있는 가장 정확한 감지 모델입니다. 신규 고객인 경우 이 모델을 사용하는 것이 좋습니다. 감지 03은 이미지(64x64 픽셀) 내에서 검색되는 작은 얼굴의 재현율과 정밀도를 모두 향상시킵니다. 추가 개선 사항으로 가양성이 전반적으로 감소되고, 회전된 얼굴 방향에 대한 감지가 향상되었습니다. 감지 03을 새 인식 04 모델과 결합하면 인식 정확성도 향상됩니다. 자세한 내용은 [얼굴 감지 모델 지정](./face-api-how-to-topics/specify-detection-model.md)을 참조하세요.
### <a name="new-detectable-face-attributes"></a>새 감지 가능한 Face 특성
* `faceMask` 특성은 얼굴 마스크가 코와 입을 모두 덮어 의도한 대로 착용되었는지 여부를 감지하는 `"noseAndMouthCovered"` 추가 특성과 함께 최신 감지 03 모델에서 사용할 수 있습니다. 최신 마스크 감지 기능을 사용하려면 사용자가 API 요청에서 감지 모델을 지정해야 합니다. 이렇게 하려면 _detectionModel_ 매개 변수를 사용하여 모델 버전을 `detection_03`에 할당합니다. 자세한 내용은 [얼굴 감지 모델 지정](./face-api-how-to-topics/specify-detection-model.md)을 참조하세요.
### <a name="new-face-api-recognition-model"></a>새 Face API 인식 모델
* 새 인식 04 모델은 현재 사용할 수 있는 가장 정확한 인식 모델입니다. 신규 고객인 경우 검증 및 식별을 위해 이 모델을 사용하는 것이 좋습니다. 또한 얼굴 가리개(수술용 마스크, N95 마스크, 천 마스크)를 착용한 등록된 사용자에 대한 향상된 인식을 포함하여 인식 03의 정확도를 향상시킵니다. 고객은 이제 최신 감지 03 모델을 사용하여 등록된 사용자가 얼굴 가리개를 착용하고 있는지 여부를 감지하고, 최신 인식 04 모델을 사용하여 사용자가 누구인지 인식하는 안전하고 원활한 사용자 환경을 빌드할 수 있습니다. 자세한 내용은 [얼굴 인식 모델 지정](./face-api-how-to-topics/specify-recognition-model.md)을 참조하세요.


## <a name="january-2021"></a>2021년 1월
### <a name="mitigate-latency"></a>대기 시간 완화
* Face 팀은 서비스를 사용할 때 발생할 수 있는 대기 시간의 잠재적 원인과 가능한 완화 전략을 자세히 설명하는 새 문서를 게시했습니다. [Face 서비스를 사용할 때 대기 시간 완화](./face-api-how-to-topics/how-to-mitigate-latency.md)를 참조하세요.

## <a name="december-2020"></a>2020년 12월
### <a name="customer-configuration-for-face-id-storage"></a>Face ID 스토리지에 대한 고객 구성
* Face 서비스는 고객 이미지를 저장하지 않지만 추출된 얼굴 특징은 서버에 저장됩니다. Face ID는 얼굴 특징의 식별자이며, [Face - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - 검증](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 및 [Face - 유사 항목 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)에서 사용됩니다. 저장된 얼굴 특징은 원래 감지 호출 후 24시간 후에 만료되어 삭제됩니다. 이제 고객은 이러한 Face ID가 캐시되는 기간을 결정할 수 있습니다. 최댓값은 여전히 최대 24시간이지만, 이제 최솟값으로 60초를 설정할 수 있습니다. 캐시되는 Face ID의 새로운 시간 범위는 60초~24시간의 값입니다. 자세한 내용은 [Face - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 참조(*faceIdTimeToLive* 매개 변수)를 참조하세요.

## <a name="november-2020"></a>2020년 11월
### <a name="sample-face-enrollment-app"></a>샘플 Face 등록 앱
* 팀은 의미 있는 동의를 설정하고 고품질 등록을 통해 정확도가 높은 얼굴 인식 시스템을 만드는 모범 사례를 보여 주기 위해 샘플 Face 등록 앱을 게시했습니다. 오픈 소스 샘플은 [등록 앱 빌드](build-enrollment-app.md) 가이드와 [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample)에서 찾을 수 있으며, 개발자가 배포하거나 사용자 지정할 수 있도록 준비되었습니다. 

## <a name="august-2020"></a>2020년 8월
### <a name="customer-managed-encryption-of-data-at-rest"></a>고객 관리형 저장소 데이터 암호화
* Face 서비스는 데이터를 클라우드에 유지할 때 자동으로 암호화합니다. Face 서비스 암호화는 데이터를 보호하여 조직의 보안 및 규정 준수 약속을 충족하는 데 도움이 됩니다. 기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 또한 CMK(고객 관리형 키)라고 하는 사용자 고유의 키를 사용하여 구독을 관리하는 새로운 옵션도 있습니다. 자세한 내용은 [고객 관리형 키](./encrypt-data-at-rest.md)를 참조하세요.

## <a name="april-2020"></a>2020년 4월
### <a name="new-face-api-recognition-model"></a>새 Face API 인식 모델
* 새 인식 03 모델은 현재 사용할 수 있는 가장 정확한 모델입니다. 신규 고객인 경우 이 모델을 사용하는 것이 좋습니다. 인식 03은 유사성 비교와 사람 일치 비교 모두에 대해 향상된 정확도를 제공합니다. 자세한 내용은 [얼굴 인식 모델 지정](./face-api-how-to-topics/specify-recognition-model.md)을 참조하세요.

## <a name="june-2019"></a>2019년 6월

### <a name="new-face-api-detection-model"></a>새 Face API 감지 모델
* 새 감지 02 모델의 작은 얼굴, 옆 얼굴, 가려진 얼굴 및 흐린 얼굴에 대한 정확도가 향상되었습니다. 새 얼굴 인식 모델 이름인 `detection_02`를 `detectionModel` 매개 변수에 지정하여 [Face - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 및 [LargePersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)를 통해 사용합니다. 자세한 내용은 [감지 모델을 지정하는 방법](Face-API-How-to-Topics/specify-detection-model.md)을 참조하세요.

## <a name="april-2019"></a>2019년 4월

### <a name="improved-attribute-accuracy"></a>향상된 특성 정확도
* `age` 및 `headPose` 특성의 전반적인 정확도가 향상되었습니다. `headPose` 특성도 이제 사용하도록 설정된 `pitch` 값으로 업데이트됩니다. 이러한 특성은 [Face - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수의 `returnFaceAttributes` 매개 변수에 지정하여 사용합니다. 
### <a name="improved-processing-speeds"></a>향상된 처리 속도
* [Face - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 및 [LargePersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) 작업의 속도가 향상되었습니다.

## <a name="march-2019"></a>2019년 3월

### <a name="new-face-api-recognition-model"></a>새 Face API 인식 모델
* 인식 02 모델의 정확도가 향상되었습니다. 새 얼굴 인식 모델 이름인 `recognition_02`를 `recognitionModel` 매개 변수에 지정하여 [Face - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) 및 [LargePersonGroup - 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)를 통해 사용합니다. 자세한 내용은 [인식 모델을 지정하는 방법](Face-API-How-to-Topics/specify-recognition-model.md)을 참조하세요.

## <a name="january-2019"></a>2019년 1월

### <a name="face-snapshot-feature"></a>Face Snapshot 기능
* [Snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get) 기능을 통해 서비스에서 구독 간 데이터 마이그레이션을 지원할 수 있습니다. 자세한 내용은 [얼굴 데이터를 다른 Face 구독으로 마이그레이션하는 방법](Face-API-How-to-Topics/how-to-migrate-face-data.md)을 참조하세요.

## <a name="october-2018"></a>2018년 10월

### <a name="api-messages"></a>API 메시지
* [PersonGroup - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) 및 [LargeFaceList - 학습 상태 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)에서 `status`, `createdDateTime`, `lastActionDateTime` 및 `lastSuccessfulTrainingDateTime`의 자세한 설명이 추가되었습니다.

## <a name="may-2018"></a>2018년 5월

### <a name="improved-attribute-accuracy"></a>향상된 특성 정확도
* `age`, `glasses`, `facialHair`, `hair`, `makeup` 특성과 함께 `gender` 특성이 크게 향상되었습니다. [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수를 통해 사용합니다.
### <a name="increased-file-size-limit"></a>파일 크기 제한 증가
* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 및 [LargePersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)에서 입력 이미지 파일 크기 제한이 4MB에서 6MB로 증가했습니다.

## <a name="march-2018"></a>2018년 3월

### <a name="new-data-structure"></a>새 데이터 구조
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 및 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)이 추가되었습니다. [대규모 기능을 사용하는 방법](Face-API-How-to-Topics/how-to-use-large-scale.md)의 자세한 내용입니다.
* [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` 매개 변수가 [1, 5]에서 [1, 100]으로 증가하고 기본값이 10으로 증가했습니다.

## <a name="may-2017"></a>2017년 5월

### <a name="new-detectable-face-attributes"></a>새 감지 가능한 Face 특성
* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수에서 `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` 및 `noise` 특성이 추가되었습니다.
* PersonGroup 및 [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에서만 10K의 사람이 지원되었습니다.
* `start` 및 `top`이라는 선택적 매개 변수가 있는 [PersonGroup 사람 - 목록](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241)에 페이지 매김이 지원되었습니다.
* 다른 FaceLists 및 PersonGroup의 다른 사람에 대해 얼굴이 추가/삭제되는 경우 동시성이 지원되었습니다.

## <a name="march-2017"></a>2017년 3월

### <a name="new-detectable-face-attribute"></a>새 감지 가능한 Face 특성
* [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 매개 변수에서 `emotion` 특성이 추가되었습니다.
### <a name="fixed-issues"></a>해결된 문제
* 얼굴은 [Face - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에서 [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) 및 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)의 `targetFace`로 반환된 사각형을 사용하여 다시 감지할 수 없습니다.
* 감지 가능한 얼굴 크기는 36x36에서 4096x4096 픽셀 사이로 엄격하게 설정됩니다.

## <a name="november-2016"></a>2016년 11월
### <a name="new-subscription-tier"></a>새 구독 계층
* 식별이나 유사성 검사를 위해 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) 또는 [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)를 사용할 경우 추가로 유지될 얼굴을 저장하는 Face Storage 표준을 추가했습니다. 저장된 이미지는 1,000개 얼굴 기준으로 $0.5로 청구되며, 이 요금은 일별로 계산됩니다. 무료 계층 구독은 총 1,000명의 사람으로 계속 제한됩니다.

## <a name="october-2016"></a>2016년 10월
### <a name="api-messages"></a>API 메시지
* [FaceList - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) 및 [PersonGroup 사람 - 얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)에서 `targetFace`에 있는 둘 이상의 얼굴에 대한 오류 메시지가 'There are more than one face in the image(이미지에 둘 이상의 얼굴이 있습니다.)'에서 'There is more than one face in the image(이미지에 둘 이상의 얼굴이 있습니다.)'로 변경되었습니다.

## <a name="july-2016"></a>2016년 7월
### <a name="new-features"></a>새로운 기능
* [얼굴 - 확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)에서 사람 개체 인증에 얼굴이 지원되었습니다.
* 두 작업 모드의 선택 영역을 사용하도록 설정하는 선택적 `mode` 매개 변수가 추가되었습니다. [얼굴 - 유사 항목 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)에서 `matchPerson` 및 `matchFace`이며 기본값은 `matchPerson`입니다.
* [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에서 한 얼굴이 사람 개체에 속하는지 여부의 임계값을 설정하도록 사용자에 대한 선택 사항 `confidenceThreshold` 매개 변수를 추가했습니다.
* 사용자가 시작점 및 목록에 대한 총 PersonGroups 수를 지정할 수 있도록 [PersonGroup - 목록](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248)에서 선택 사항 `start` 및 `top` 매개 변수를 추가했습니다.

## <a name="v10-changes-from-v0"></a>V0에서 변경된 V1.0 내용

* 서비스 루트 엔드포인트를 ```https://westus.api.cognitive.microsoft.com/face/v0/```에서 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```으로 업데이트했습니다. [얼굴 - 검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [얼굴 - 식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [얼굴 - 유사 항목 찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 및 [얼굴 - 그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)에 적용된 변경 내용입니다.
* 36x36픽셀로 감지 가능한 최소 얼굴 크기를 업데이트했습니다. 36x36픽셀보다 작은 얼굴은 검색되지 않습니다.
* Face V0에서 PersonGroup 및 사람 데이터가 사용되지 않았습니다. Face V1.0 서비스를 사용하여 해당 데이터에 액세스할 수 없습니다.
* 2016년 6월 30 일에 Face API의 V0 엔드포인트가 사용되지 않았습니다.