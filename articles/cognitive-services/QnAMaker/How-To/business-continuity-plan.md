---
title: 비즈니스 연속성 플랜 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 비즈니스 연속성 계획의 주요 목표는 Bot 또는 이를 사용하는 애플리케이션에 대한 작동 중지 시간이 없도록 보장하는 복원력 있는 기술 자료 엔드포인트를 만드는 것입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: a31076d17ebaf8020fe4b3f82594b98a7c3bde74
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977457"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>QnA Maker 서비스를 위한 비즈니스 연속성 계획 만들기

비즈니스 연속성 계획의 주요 목표는 Bot 또는 이를 사용하는 애플리케이션에 대한 작동 중지 시간이 없도록 보장하는 복원력 있는 기술 자료 엔드포인트를 만드는 것입니다.

![QnA Maker bcp 계획](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

위에 표시된 것처럼 대략적인 아이디어는 다음과 같습니다.

1. [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)에서 두 개의 병렬 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)를 설정합니다.

2. 기본 및 보조 Azure 검색 인덱스를 동기화 된 상태로 유지 합니다. Azure 인덱스를 백업 하는 방법을 보려면 [여기](https://github.com/pchoudhari/QnAMakerBackupRestore) 에서 GitHub 샘플을 사용 하세요.

3. [연속 내보내기](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)를 사용하여 Application Insights를 백업합니다.

4. 기본 및 보조 스택이 설치되면 [트래픽 관리자](https://docs.microsoft.com/azure/traffic-manager/)를 사용하여 두 개의 엔드포인트를 구성하고 라우팅 메서드를 설정합니다.

5. Traffic manager 엔드포인트에 대 한 SSL(Secure Sockets Layer) (SSL) 인증서를 만들어야 합니다. 앱 서비스에서 [SSL 인증서를 바인딩합니다](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings).

6. 마지막으로, 봇 또는 앱에서 트래픽 관리자 엔드포인트를 사용합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker 배포를 위한 용량 선택](../Tutorials/choosing-capacity-qnamaker-deployment.md)
