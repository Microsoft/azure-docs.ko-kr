---
title: 음성 서비스 할당량 및 한도
titleSuffix: Azure Cognitive Services
description: Azure 인식 음성 서비스 할당량 및 제한에 대 한 빠른 참조, 자세한 설명 및 모범 사례
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: alexeyo
ms.openlocfilehash: 554dd0967979bc2457c3a9c8371152e09535381f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690139"
---
# <a name="speech-services-quotas-and-limits"></a>음성 서비스 할당량 및 한도

이 문서에는 Azure 인식 음성 서비스 할당량 및 모든 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)에 대 한 제한에 대 한 빠른 참조와 **자세한 설명이** 포함 되어 있습니다. 또한 요청 제한을 방지 하기 위한 몇 가지 모범 사례가 포함 되어 있습니다. 

## <a name="quotas-and-limits-quick-reference"></a>할당량 및 제한 빠른 참조
[텍스트 음성 변환 할당량 및 한도](#text-to-speech-quotas-and-limits-per-speech-resource) 로 이동
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>음성 리소스 당 음성 텍스트 할당량 및 한도
아래 테이블에서 "조정 가능한" 행이 없는 매개 변수는 모든 가격 책정 계층에 대해 조정할 **수 없습니다** .

| 할당량 | 무료 (F0)<sup>1</sup> | Standard(S0) |
|--|--|--|
| **온라인 기록 동시 요청 제한 (기본 및 사용자 지정 모델)** |  |  |
| 기본값 | 1 | 20 |
| 빗변 | 아니요<sup>2</sup> | 예<sup>2</sup> |
| **REST API 요청 제한 ([API Management](../../api-management/api-management-key-concepts.md) 끝점)** | 10 초 당 100 요청 | 10 초 당 100 요청 |
| **데이터 가져오기에 대 한 최대 데이터 집합 파일 크기** | 2GB | 2GB |
| **일괄 처리 기록을 위한 최대 입력 blob 크기** | 해당 없음 | 2.5GB |
| **일괄 처리 기록을 위한 최대 blob 컨테이너 크기** | 해당 없음 | 5GB |
| **일괄 처리 기록을 위한 컨테이너 당 최대 blob 수** | 해당 없음 | 10000 |
| **일괄 처리 기록을 위해 동시에 실행 되는 최대 작업 수** | 해당 없음 | 2000  |

<sup>1</sup> **무료 (F0)** 가격 책정 계층에 대해서는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)에서 월간 허용량를 참조 하세요.<br/>
<sup>2</sup> [추가](#detailed-description-quota-adjustment-and-best-practices)설명, [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)및 [조정 지침](#speech-to-text-increasing-online-transcription-concurrent-request-limit)을 참조 하세요.<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>음성 리소스 당 텍스트 음성 변환 할당량 및 한도
아래 테이블에서 "조정 가능한" 행이 없는 매개 변수는 모든 가격 책정 계층에 대해 조정할 **수 없습니다** .

| 할당량 | 무료 (F0)<sup>3</sup> | Standard(S0) |
|--|--|--|
| **표준 및 신경망에 대 한 최대 수의 TPS (초당 트랜잭션 수)** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **사용자 지정 음성의 동시 요청 제한** |  |  |
| 기본값 | 10 | 10 |
| 빗변 | 아니요<sup>5</sup> | 예<sup>5</sup> |
| **HTTP 관련 할당량** |  |
| 요청당 생성 된 최대 오디오 길이 | 10분 | 10분 |
| SSML의 최대 고유 `<voice>` 태그 수 | 50 | 50 |
| **Websocket 특정 할당량** |  |  |
|턴 당 생성 된 최대 오디오 길이 | 10분 | 10분 |
|테이블당 최대 SSML 메시지 크기 |64KB |64KB |
| **REST API 요청 제한** | 분당 20 개 요청 | 5 초 당 25 개의 요청 |


<sup>3</sup> **무료 (F0)** 가격 책정 계층에 대해서는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)에서 월간 허용량을 참조 하세요.<br/>
<sup>4</sup> [추가](#detailed-description-quota-adjustment-and-best-practices) 설명 및 [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)를 참조 하세요.<br/>
<sup>5</sup> [추가](#detailed-description-quota-adjustment-and-best-practices)설명, [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)및 [조정 지침](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)을 참조 하세요.<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>세부 설명, 할당량 조정 및 모범 사례
할당량 증가를 요청 하기 전에 (해당 하는 경우) 필요한 지 확인 합니다. Speech service는 자동 크기 조정 기술을 사용 하 여 필요한 계산 리소스를 "주문형" 모드로 전환 하 고, 과도 한 하드웨어 용량을 유지 하지 않고 고객 비용을 적게 유지 합니다. 응용 프로그램에서 응답 코드 429 ("요청이 너무 많음")를 받을 때마다 작업이 정의 된 제한에 도달 하는 경우 ( [할당량 및 제한 빠른 참조](#quotas-and-limits-quick-reference)참조) 가장 가능성이 높은 설명은 서비스를 요구에 따라 확장 하 고 필요한 규모에 아직 도달 하지 않은 것 이므로 요청을 처리할 수 있는 충분 한 리소스를 보유 하 고 있지 않은 것입니다. 이 상태는 일반적으로 일시적 이며, 마지막 시간이 아니어야 합니다.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>자동 크기 조정 중 조정을 완화 하는 일반적인 모범 사례
제한과 관련 된 문제를 최소화 하려면 (응답 코드 429) 다음 방법을 사용 하는 것이 좋습니다.
- 응용 프로그램에서 다시 시도 논리를 구현하세요.
- 워크 로드가 선명 하 게 변경 되지 않도록 합니다. 작업을 점진적으로 증가 <br/>
*예제.* 응용 프로그램이 텍스트를 음성으로 사용 하 고 현재 워크 로드가 5 개 (초당 트랜잭션 수)입니다. 다음 초는 로드를 20 개 TPS로 늘립니다 (4 배 이상). 서비스는 새 부하를 달성 하기 위해 확장을 즉시 시작 하기 시작 하지만, 두 번째 작업 내에서 수행할 수 없게 될 가능성이 있으므로 일부 요청은 응답 코드 429을 받게 됩니다.   
- 다른 부하 증가 패턴 테스트
  - [음성 텍스트 예제](#speech-to-text-example-of-a-workload-pattern-best-practice) 참조
- 동일 하거나 다른 지역에 추가 음성 리소스를 만들고 "라운드 로빈" 기술을 사용 하 여 워크 로드 간에 작업을 분산 합니다. 이는 음성 리소스 당 200으로 설정 되 고 조정할 수 없는 **텍스트-음성 TPS (초당 트랜잭션 수)** 매개 변수에서 특히 중요 합니다.  

다음 섹션에서는 할당량 조정의 특정 사례에 대해 설명 합니다.<br/>
[텍스트 음성 변환으로 이동 합니다. 사용자 지정 음성에 대 한 기록 동시 요청 한도 높이기](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>음성 텍스트: 온라인 기록 늘어난 동시 요청 제한
기본적으로 동시 요청 수는 음성 리소스 (기본 모델) 또는 사용자 지정 끝점 (사용자 지정 모델) 당 20 개로 제한 됩니다. 표준 가격 책정 계층의 경우이 금액을 늘릴 수 있습니다. 요청을 제출 하기 전에 [이 섹션](#detailed-description-quota-adjustment-and-best-practices) 의 자료와 이러한 [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)에 대해 잘 알고 있어야 합니다.

동시 요청 제한은 증가 해도 비용에 직접적인 영향을 주지 **않습니다** . 음성 서비스는 "사용한 만큼만 요금 지불" 모델을 사용 합니다. 이 제한은 서비스에서 요청을 제한 하기 전에 확장할 수 있는 정도를 정의 합니다.

**기본** 모델 및 **사용자 지정** 모델에 대 한 동시 요청 제한은 **별도로**조정 해야 합니다.

동시 요청 제한 매개 변수의 기존 값은 Azure Portal, 명령줄 도구 또는 API 요청을 통해 표시 **되지** 않습니다. 기존 값을 확인 하려면 Azure 지원 요청을 만듭니다.

>[!NOTE]
>컨테이너는 호스트 되는 하드웨어의 Cpu에 의해서만 제한 되므로 [음성 컨테이너](speech-container-howto.md) 는 동시 요청 제한의 증가가 필요 하지 않습니다.

#### <a name="have-the-required-information-ready"></a>필요한 정보가 준비 되어 있어야 합니다.
- **기본 모델**의 경우:
  - 음성 리소스 ID
  - 지역
- **사용자 지정 모델**의 경우: 
  - 지역
  - 사용자 지정 끝점 ID

- **정보를 가져오는 방법 (기본 모델)**:  
  - [Azure Portal](https://portal.azure.com/) 로 이동
  - 동시성 요청 제한을 늘릴 음성 리소스를 선택 하세요.
  - *속성* 선택 (*리소스 관리* 그룹) 
  - 다음 필드의 값을 복사 하 여 저장 합니다.
    - **리소스 ID**
    - **위치** (끝점 영역)

- **정보를 가져오는 방법 (사용자 지정 모델)**:
  - [Speech Studio](https://speech.microsoft.com/) 포털로 이동
  - 필요한 경우 로그인
  - Custom Speech로 이동
  - 프로젝트 선택
  - *배포* 로 이동
  - 필요한 끝점을 선택 합니다.
  - 다음 필드의 값을 복사 하 여 저장 합니다.
    - **서비스 지역** (끝점 지역)
    - **끝점 ID**
  
#### <a name="create-and-submit-support-request"></a>지원 요청 만들기 및 제출
리소스에 대 한 동시 요청 제한의 증가를 시작 하거나 필요한 경우 지원 요청을 제출 하 여 오늘 제한을 확인 합니다.

- [필요한 정보가](#have-the-required-information-ready) 있는지 확인 합니다.
- [Azure Portal](https://portal.azure.com/) 로 이동
- 동시성 요청 제한을 늘리거나 확인할 음성 리소스를 선택 합니다.
- *새 지원 요청* (*지원 + 문제 해결* 그룹)을 선택 합니다. 
- Azure 구독 및 Azure 리소스에 대 한 자동 채우기 정보를 포함 하는 새 창이 표시 됩니다.
- *Summary* 를 입력 합니다 (예: "STT Concurrency 요청 제한").
- *문제 유형에* 서 "할당량 또는 구독 문제"를 선택 합니다.
- 표시 되는 *문제 하위 유형* 선택:
  - "할당량 또는 동시 요청 증가"-증가 하는 요청의 경우
  - 기존 제한을 확인 하려면 "할당량 또는 사용 유효성 검사"
- *다음: 솔루션* 을 클릭 합니다.
- 요청 만들기를 계속 진행 합니다.
- *자세히* 탭의 *설명* 필드에를 입력 합니다.
  - 이 요청은 **음성-텍스트** 할당량에 대 한 것입니다.
  - **기본** 또는 **사용자 지정** 모델
  - [이전에 수집한](#have-the-required-information-ready) Azure 리소스 정보 
  - 필요한 정보 입력을 완료 하 고 *검토 + 만들기* 탭에서 *만들기* 단추를 클릭 합니다.
  - Azure Portal 알림에서 지원 요청 번호를 확인 합니다. 추가 처리를 위해 곧 연락을 드릴 것입니다.

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>음성 텍스트: 작업 패턴 모범 사례 예
이 예에서는 자동 크기 조정 [진행](#detailed-description-quota-adjustment-and-best-practices)중에 발생할 수 있는 요청 조정을 완화 하기 위해 권장 하는 접근 방식을 제공 합니다. "정확한 조리법"은 아니지만 필요에 따라 수행 하 고 조정 하기 위해 초대 하는 템플릿만 있으면 됩니다.

음성 리소스의 동시 요청 한도가 300로 설정 되어 있다고 가정해 보겠습니다. 20 개의 동시 연결에서 작업을 시작 하 고 1.5-2 분 마다 20 개의 동시 연결에서 로드를 늘립니다. 너무 많은 응답 코드 429가 발생 하는 경우 서비스 응답을 제어 하 고이를 대체 하는 논리를 구현 합니다 (부하 감소). 그런 다음 1-2-4-4 분 패턴으로 다시 시도 합니다. (그래도 작동 하지 않는 경우 1 분 내에 부하 증가를 다시 시도 합니다.

일반적으로 프로덕션으로 이동 하기 전에 작업 및 워크 로드 패턴을 테스트 하는 것이 좋습니다.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>텍스트 음성 변환: 사용자 지정 음성에 대 한 기록의 동시 요청 한도를 늘립니다.
기본적으로 사용자 지정 음성 끝점에 대 한 동시 요청 수는 10 개로 제한 됩니다. 표준 가격 책정 계층의 경우이 금액을 늘릴 수 있습니다. 요청을 제출 하기 전에 [이 섹션](#detailed-description-quota-adjustment-and-best-practices) 의 자료와 이러한 [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)에 대해 잘 알고 있어야 합니다.

동시 요청 제한은 증가 해도 비용에 직접적인 영향을 주지 **않습니다** . 음성 서비스는 "사용한 만큼만 요금 지불" 모델을 사용 합니다. 이 제한은 서비스에서 요청을 제한 하기 전에 확장할 수 있는 정도를 정의 합니다.

동시 요청 제한 매개 변수의 기존 값은 Azure Portal, 명령줄 도구 또는 API 요청을 통해 표시 **되지** 않습니다. 기존 값을 확인 하려면 Azure 지원 요청을 만듭니다.

>[!NOTE]
>컨테이너는 호스트 되는 하드웨어의 Cpu에 의해서만 제한 되므로 [음성 컨테이너](speech-container-howto.md) 는 동시 요청 제한의 증가가 필요 하지 않습니다.

#### <a name="prepare-the-required-information"></a>필요한 정보를 준비 합니다.
증가 요청을 만들려면 배포 지역과 사용자 지정 끝점 ID를 제공 해야 합니다. 이를 가져오려면 다음 작업을 수행 합니다. 

- [Speech Studio](https://speech.microsoft.com/) 포털로 이동
- 필요한 경우 로그인
- *사용자 지정 음성* 으로 이동
- 프로젝트 선택
- *배포* 로 이동
- 필요한 끝점을 선택 합니다.
- 다음 필드의 값을 복사 하 여 저장 합니다.
    - **서비스 지역** (끝점 지역)
    - **끝점 ID**
  
#### <a name="create-and-submit-support-request"></a>지원 요청 만들기 및 제출
리소스에 대 한 동시 요청 제한의 증가를 시작 하거나 필요한 경우 지원 요청을 제출 하 여 오늘 제한을 확인 합니다.

- [필요한 정보가](#prepare-the-required-information) 있는지 확인 합니다.
- [Azure Portal](https://portal.azure.com/) 로 이동
- 동시성 요청 제한을 늘리거나 확인할 음성 리소스를 선택 합니다.
- *새 지원 요청* (*지원 + 문제 해결* 그룹)을 선택 합니다. 
- Azure 구독 및 Azure 리소스에 대 한 자동 채우기 정보를 포함 하는 새 창이 표시 됩니다.
- *요약* 입력 (예: "TTS 사용자 지정 끝점 동시성 요청 제한 증가")
- *문제 유형에* 서 "할당량 또는 구독 문제"를 선택 합니다.
- 표시 되는 *문제 하위 유형* 선택:
  - "할당량 또는 동시 요청 증가"-증가 하는 요청의 경우
  - 기존 제한을 확인 하려면 "할당량 또는 사용 유효성 검사"
- *다음: 솔루션* 을 클릭 합니다.
- 요청 만들기를 계속 진행 합니다.
- *자세히* 탭의 *설명* 필드에를 입력 합니다.
  - **텍스트 음성 변환** 에 대 한 요청에 대 한 참고 사항
  - [이전에 수집한](#prepare-the-required-information) Azure 리소스 정보 
  - 필요한 정보 입력을 완료 하 고 *검토 + 만들기* 탭에서 *만들기* 단추를 클릭 합니다.
  - Azure Portal 알림에서 지원 요청 번호를 확인 합니다. 추가 처리를 위해 곧 연락을 드릴 것입니다.

