---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182493"
---
### <a name="standard-and-neural-voices"></a>표준 및 신경망 음성

이 표를 사용하여 지역/엔드포인트에서의 표준 및 신경망 음성의 가용성을 확인합니다.

| 지역 | 엔드포인트 | 표준 음성 | 신경망 음성 |
|--------|----------|-----------------|---------------|
| 오스트레일리아 동부 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 캐나다 중부 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 미국 중부 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 동아시아 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 미국 동부 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 미국 동부 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 프랑스 중부 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 인도 중부 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 일본 동부 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 한국 중부 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 미국 중북부 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 북유럽 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 미국 중남부 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 동남아시아 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 영국 남부 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 서유럽 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |
| 미국 서부 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 아닙니다. |
| 미국 서부 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | 예. | 예. |

### <a name="custom-voices"></a>사용자 지정 음성

사용자 지정 음성 글꼴을 만든 경우 사용자가 만든 엔드포인트을 사용합니다. 또한 `{deploymentId}`를 사용자의 음성 모델에 대한 배포 ID로 변경하여 아래에 나열된 엔드포인트을 사용할 수 있습니다.

| 지역 | 엔드포인트 |
|--------|----------|
| 오스트레일리아 동부 | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 캐나다 중부 | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 중부 | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 동아시아 | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 동부 | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 동부 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 프랑스 중부 | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 인도 중부 | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 일본 동부 | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 한국 중부 | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 중북부 | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 북유럽 | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 중남부 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 동남아시아 | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 영국 남부 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 서유럽 | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 서부 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 서부 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
