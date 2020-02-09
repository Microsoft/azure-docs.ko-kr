---
title: Cognitive Services 컨테이너 FAQ (질문과 대답)
titleSuffix: Azure Cognitive Services
description: 질문과 대답입니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961883"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure Cognitive Services 컨테이너 FAQ (질문과 대답)

## <a name="general-questions"></a>일반적인 질문

**Q: 사용할 수 있는 기능은 무엇 인가요?**

**A:** [azure Cognitive Services의 컨테이너를 지원](../cognitive-services-container-support.md) 하기 위해 개발자는 azure에서 제공 되는 것과 동일한 지능형 api를 사용할 수 있지만 컨테이너 화의 [이점이](../cognitive-services-container-support.md#features-and-benefits) 있습니다. 컨테이너 지원은 현재 다음 부분을 포함 하 여 Azure Cognitive Services의 하위 집합에 대해 미리 보기로 제공 됩니다.

> [!div class="checklist"]
> * [변칙 탐지기][ad-containers]
> * [Computer Vision][cv-containers]
> * [Face][fa-containers]
> * [폼 인식기][fr-containers]
> * [LUIS(Language Understanding)][lu-containers]
> * [Speech Service API][sp-containers]
> * [텍스트 분석][ta-containers]

**Q: Cognitive Services 클라우드와 컨테이너 간에 차이가 있나요?**

**A:** Cognitive Services 컨테이너는 Cognitive Services 클라우드의 대안입니다. 컨테이너는 해당 하는 클라우드 서비스와 동일한 기능을 제공 합니다. 고객은 온-프레미스 또는 Azure에서 컨테이너를 배포할 수 있습니다. 핵심 AI 기술, 가격 책정 계층, API 키 및 API 서명은 컨테이너와 해당 클라우드 서비스 간에 동일 합니다. 해당 하는 클라우드 서비스에 대해 컨테이너를 선택 하는 [기능 및 이점은](../cognitive-services-container-support.md#features-and-benefits) 다음과 같습니다.

**Q: 모든 Cognitive Services에 대해 컨테이너를 사용할 수 있게 하 고, 다음에 필요한 컨테이너 집합은 무엇 인가요?**

**A:** 컨테이너 제품으로 사용할 수 있는 Cognitive Services 더 많이 만들어 드리겠습니다. 새 컨테이너 릴리스와 기타 Cognitive Services 공지에 대 한 업데이트를 얻으려면 로컬 Microsoft 계정 관리자에 게 문의 하세요.

**Q: Cognitive Services 컨테이너에 대해 SLA (서비스 수준 계약)는 어떻게 되나요?**

**A:** Cognitive Services 컨테이너에는 SLA가 없습니다.

리소스의 컨테이너 구성 Cognitive Services 고객에 의해 제어 되므로 Microsoft는 GA (일반 공급)에 대 한 SLA를 제공 하지 않습니다. 고객은 온-프레미스 컨테이너를 무료로 배포할 수 있으므로 호스트 환경을 정의 합니다.

> [!IMPORTANT]
> Cognitive Services 서비스 수준 계약에 대해 자세히 알아보려면 [SLA 페이지를 참조](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)하세요.

**Q: 이러한 컨테이너는 소 버린 클라우드에서 사용할 수 있나요?**

**A:** "소 버린 클라우드" 라는 용어를 모두 사용 하는 것은 아니므로 정의로 시작 하겠습니다.

> "소 버린 클라우드"는 [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure 독일](../../germany/germany-welcome.md)및 [azure 중국 21vianet](https://docs.microsoft.com/azure/china/overview-operations) 클라우드로 구성 됩니다.

아쉽게도 Cognitive Services 컨테이너는 소 버린 클라우드에서 기본적으로 지원 *되지 않습니다* . 컨테이너는 이러한 클라우드에서 실행할 수 있지만 공용 클라우드에서 끌어온 다음 사용 데이터를 공용 엔드포인트으로 보내야 합니다.

### <a name="versioning"></a>버전 관리

**Q: 컨테이너를 최신 버전으로 업데이트 하는 방법은 무엇입니까?**

**A:** 고객은 배포 된 컨테이너를 업데이트 하는 시기를 선택할 수 있습니다. 컨테이너는 최신 버전을 나타내기 위해 `latest`와 같은 표준 [Docker 태그로](https://docs.docker.com/engine/reference/commandline/tag/) 표시 됩니다. 이미지가 업데이트 될 때 알림이 표시 되는 방법에 대 한 자세한 내용은 사용자가 릴리스된 대로 최신 버전의 컨테이너를 끌어올 수 있도록 하는 것이 좋습니다 [Azure Container Registry 웹 후크](../../container-registry/container-registry-webhook.md) .
 
**Q: 지원 되는 버전은 무엇 인가요?**

**A:** 컨테이너의 현재 및 마지막 주 버전이 지원 됩니다. 그러나 최신 기술을 얻기 위해 고객이 최신 상태를 유지 하도록 권장 합니다.
 
**Q: 업데이트 버전이 어떻게 관리 되나요?**

**A:** 주 버전 변경 내용은 API 서명이 주요 변경 됨을 표시 합니다. 일반적으로 해당 하는 인지 서비스 클라우드 제품에 대 한 주 버전 변경 내용과 일치 하는 것으로 예상 됩니다. 사소한 버전 변경 내용은 버그 수정, 모델 업데이트 또는 API 서명의 주요 변경을 수행 하지 않는 새 기능을 표시 합니다.

## <a name="technical-questions"></a>기술 관련 질문

**Q: IoT 장치에서 Cognitive Services 컨테이너를 실행 하려면 어떻게 해야 하나요?**

인터넷 연결이 안정적이 지 않거나 대역폭 비용을 절약 하려고 합니다. 또는에서 대기 시간이 짧은 요구 사항이 있거나 현장에서 분석 되어야 하는 중요 한 데이터를 처리 하는 경우 [Cognitive Services 컨테이너를 사용 하 Azure IoT Edge](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) 는 클라우드와의 일관성을 제공 합니다.

**Q: 제품 사용자 의견 및 기능 권장 사항을 제공 어떻게 할까요??**

**A:** 고객은 문제를 공개적으로 [음성](https://cognitive.uservoice.com/) 으로 전달 하 고 잠재적인 문제가 겹치면 동일한 작업을 수행 하는 다른 사람을 투표 하는 것이 좋습니다. 사용자 음성 도구는 제품 사용자 의견 및 기능 권장 사항에 모두 사용할 수 있습니다.

**Q: 지원 담당자에 게 연락 하는 사람은 누구 인가요?**

**A:** 고객 지원 채널은 Cognitive Services 클라우드 제품과 동일 합니다. 모든 Cognitive Services 컨테이너에는 microsoft와 커뮤니티 지원 고객에 게 도움이 되는 로깅 기능이 포함 되어 있습니다. 추가 지원이 필요한 경우 다음 옵션을 참조 하세요.

### <a name="customer-support-plan"></a>고객 지원 계획

고객은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/) 을 참조 하 여 지원 담당자에 게 연락할 사람을 확인 해야 합니다.

### <a name="azure-knowledge-center"></a>Azure 지식 센터

고객은 [Azure 기술 센터](https://azure.microsoft.com/resources/knowledge-center/) 를 탐색 하 여 질문 및 지원 문제를 해결할 수 있습니다.

### <a name="stack-overflow"></a>스택 오버플로

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) 는 전문 및 열성적인 프로그래머를 위한 질문과 대답 사이트입니다.

요구 사항과 일치 하는 잠재적인 질문과 대답은 다음 태그를 살펴보세요.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft 인식](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**Q: 청구는 어떻게 작동 하나요?**

**A:** 고객은 Cognitive Services 클라우드와 유사한 사용량을 기준으로 요금이 청구 됩니다. Azure에 계량 데이터를 보내도록 컨테이너를 구성 해야 하며, 그에 따라 트랜잭션이 청구 됩니다. 호스팅된 및 온-프레미스 서비스에서 사용 되는 리소스는 계층화 된 가격이 포함 된 단일 할당량에 추가 되므로 두 용도를 모두 계산 합니다. 자세한 내용은 해당 제품의 가격 책정 페이지를 참조 하세요.

* [변칙 탐지기][ad-containers-billing]
* [Computer Vision][cv-containers-billing]
* [Face][fa-containers-billing]
* [폼 인식기][fr-containers-billing]
* [LUIS(Language Understanding)][lu-containers-billing]
* [Speech Service API][sp-containers-billing]
* [텍스트 분석][ta-containers-billing]

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터를 Microsoft로 보내지 않습니다.
 
**Q: 컨테이너에 대 한 현재 지원 보증은 무엇 인가요?**

**A:** 미리 보기에 대 한 보증은 없습니다. 엔터프라이즈 소프트웨어에 대 한 Microsoft의 표준 보증은 컨테이너가 GA (일반 공급)로 공식적으로 발표 될 때 적용 됩니다.
 
**Q: 인터넷 연결이 끊어지는 경우 컨테이너 Cognitive Services 어떻게 되나요?**

**A:** Cognitive Services 컨테이너는 계량을 위해 Azure에 연결 하지 않고 실행할 수 있는 *권한이 없습니다* . 고객은 컨테이너가 계량 서비스와 항상 통신할 수 있도록 해야 합니다.

**Q: Azure에 연결 하지 않고 컨테이너가 작동할 수 있는 기간은 얼마 인가요?**

**A:** Cognitive Services 컨테이너는 계량을 위해 Azure에 연결 하지 않고 실행할 수 있는 *권한이 없습니다* . 고객은 컨테이너가 계량 서비스와 항상 통신할 수 있도록 해야 합니다.
 
**Q: 이러한 컨테이너를 실행 하는 데 필요한 현재 하드웨어는 무엇 인가요?**

**A:** Cognitive Services 컨테이너는 x64 Linux Docker 컨테이너를 지 원하는 호환 가능한 Linux 노드, VM 및 edge 장치를 실행할 수 있는 x64 기반 컨테이너입니다. CPU 프로세서가 필요 합니다. 각 컨테이너 제공에 대 한 최소 및 권장 구성은 아래에서 사용할 수 있습니다.

* [변칙 탐지기][ad-containers-recommendations]
* [Computer Vision][cv-containers-recommendations]
* [Face][fa-containers-recommendations]
* [폼 인식기][fr-containers-recommendations]
* [LUIS(Language Understanding)][lu-containers-recommendations]
* [Speech Service API][sp-containers-recommendations]
* [텍스트 분석][ta-containers-recommendations]
 
**Q: 이러한 컨테이너는 현재 Windows에서 지원 되나요?**

**A:** Cognitive Services 컨테이너는 Linux 컨테이너 이지만 Windows에서 Linux 컨테이너를 지원 합니다. Windows의 Linux 컨테이너에 대 한 자세한 내용은 [Docker 설명서](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)를 참조 하세요.
 
**Q: 컨테이너를 검색 어떻게 할까요?**

**A:** Cognitive Services 컨테이너는 Azure Portal, Docker 허브, Azure container registry 등의 다양 한 위치에서 사용할 수 있습니다. 최신 컨테이너 위치는 [컨테이너 리포지토리 및 이미지](../cognitive-services-container-support.md#container-repositories-and-images)를 참조 하세요.

**Q: Cognitive Services 컨테이너가 AWS 및 Google 제품에 어떻게 비교 되나요?**

**A:** Microsoft는 고객이 클라우드 서비스를 사용 하는 경우와 마찬가지로 트랜잭션 당 간단한 청구로 컨테이너에서 미리 학습 된 AI 모델을 이동 하는 최초의 클라우드 공급자입니다. Microsoft에서는 하이브리드 클라우드를 통해 고객에 게 더 많은 옵션을 제공 한다고 가정 합니다.

**Q: 컨테이너에는 어떤 규정 준수 인증을 사용할 수 있나요?**

**A:** 인식 서비스 컨테이너에 규정 준수 인증이 없습니다.

**Q: 어떤 지역이 Cognitive Services 컨테이너를 사용할 수 있나요?**

**A:** 컨테이너는 모든 지역의 어디에서 나 실행할 수 있지만, 키가 필요 하 고 계량을 위해 Azure로 다시 호출 해야 합니다. 클라우드 서비스에 대해 지원 되는 모든 지역은 컨테이너 계량 호출에 대해 지원 됩니다.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
