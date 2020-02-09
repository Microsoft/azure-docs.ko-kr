---
title: 검토 도구 개념 알아보기-Content Moderator
titleSuffix: Azure Cognitive Services
description: 결합 된 AI와 인간 리뷰 중재 작업을 조정 하는 웹 사이트인 Content Moderator 검토 도구에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169513"
---
# <a name="content-moderator-review-tool"></a>Content Moderator 검토 도구

Azure Content Moderator는 기계 학습 콘텐츠 조정을 사용자 리뷰와 결합 하는 서비스를 제공 하 고, [검토 도구](https://contentmoderator.cognitive.microsoft.com) 웹 사이트는 이러한 서비스에 대 한 자세한 액세스를 제공 하는 사용자에 게 친숙 한 프런트 엔드입니다.

![브라우저의 검토 도구 대시보드](./images/0-dashboard.png)

## <a name="what-it-does"></a>수행하는 작업

[검토 도구](https://contentmoderator.cognitive.microsoft.com)를 컴퓨터 기반 중재 api와 함께 사용 하면 콘텐츠 조정 프로세스에서 다음 작업을 수행할 수 있습니다.

- 도구 집합 하나를 사용 하 여 여러 형식 (텍스트, 이미지 및 비디오)으로 콘텐츠를 중간에 사용 합니다.
- 중재 API 결과가 발생 하는 경우 인간 [리뷰](../review-api.md#reviews) 생성을 자동화 합니다.
- 콘텐츠 범주나 환경 수준 별로 구성 된 여러 검토 팀에 콘텐츠 검토를 할당 하거나 에스컬레이션 합니다.
- 기본 또는 사용자 지정 논리 필터 ([워크플로](../review-api.md#workflows))를 사용 하 여 코드를 작성 하지 않고 콘텐츠를 정렬 하 고 추적 합니다.
- [커넥터](./configure.md#connectors) 를 사용 하 여 Content Moderator api 외에 Microsoft 사진 dna, Text Analytics 및 얼굴 서비스로 콘텐츠를 처리 합니다.
- API 또는 비즈니스 프로세스에 대 한 워크플로를 만들려면 고유한 커넥터를 빌드합니다.
- 콘텐츠 수정 프로세스에 대한 주요 성능 메트릭을 가져옵니다.

## <a name="review-tool-dashboard"></a>검토 도구 대시보드

**대시보드** 탭에서 도구 내에서 수행 된 콘텐츠 검토에 대 한 주요 메트릭을 확인할 수 있습니다. 이미지, 텍스트 및 비디오 콘텐츠의 합계, 완료 및 보류 중인 검토 수를 확인 합니다. 또한 검토가 완료 된 사용자 및 팀의 분석 및 적용 된 중재 태그를 확인할 수 있습니다.

![대시보드 보기](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>검토 도구 자격 증명

[검토 도구](https://contentmoderator.cognitive.microsoft.com)를 사용 하 여 등록 하면 계정에 대 한 Azure 지역을 선택 하 라는 메시지가 표시 됩니다. 이는 [검토 도구](https://contentmoderator.cognitive.microsoft.com) 에서 Azure Content Moderator services에 대 한 무료 평가판 키를 생성 하기 때문입니다. REST 호출 또는 클라이언트 SDK에서 서비스에 액세스 하려면이 키가 필요 합니다. **설정** > **자격 증명**을 선택 하 여 키 및 API 엔드포인트 URL을 볼 수 있습니다.

![Content Moderator 자격 증명](images/settings-6-credentials.png)

## <a name="next-steps"></a>다음 단계

검토 도구 리소스에 액세스 하 고 설정을 변경 하는 방법을 알아보려면 [검토 도구 구성](./configure.md) 을 참조 하세요.