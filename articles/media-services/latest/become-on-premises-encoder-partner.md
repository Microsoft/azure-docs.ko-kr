---
title: 온-프레미스 인코더 파트너가 되기-Azure Media Services
description: 이 문서에서는 온-프레미스 라이브 스트리밍 인코더를 확인 하는 방법을 설명 합니다.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 5af6a7fc988271fc350ecc6e8be19742ede8ecee
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89258642"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>온-프레미스 라이브 스트리밍 인코더를 확인 하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services는 Azure Media Services 온-프레미스 인코더 파트너로서 엔터프라이즈 고객에게 인코더를 추천하여 제품을 홍보합니다. 온-프레미스 인코더 파트너가 되려면 온-프레미스 인코더가 Media Services와 호환되는지 확인해야 합니다. 이렇게 하려면 다음 확인을 완료합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>통과 라이브 이벤트 확인

1. Media Services 계정에서 **스트리밍 엔드포인트**가 실행 중인지 확인합니다. 
2. **통과** 라이브 이벤트를 만들고 시작합니다. <br/> 자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.
3. 수집 URL을 가져오고, URL을 사용하여 다중 비트 전송률 라이브 스트림을 Media Services로 보내도록 온-프레미스 인코더를 구성합니다.
4. 미리 보기 URL을 가져와서 인코더의 입력이 실제로 수신되고 있는지 확인하는 데 사용합니다.
5. 새 **자산** 개체를 만듭니다.
6. **실시간 출력** 을 만들고 사용자가 만든 자산 이름을 사용 합니다.
7. 기본 제공 **스트리밍 정책** 형식을 사용 하 여 **스트리밍 로케이터** 를 만듭니다.
8. **스트리밍 로케이터**의 경로를 나열하여 사용할 URL을 다시 가져옵니다.
9. 스트림을 내보낼 **스트리밍 엔드포인트**의 호스트 이름을 가져옵니다.
10. 8단계의 URL을 9단계의 호스트 이름과 결합하여 전체 URL을 구합니다.
11. 약 10분 동안 라이브 인코더를 실행합니다.
12. 라이브 이벤트를 중지합니다. 
13. [Azure Media Player](https://aka.ms/azuremediaplayer) 같은 플레이어를 사용하여 재생 시 모든 품질 수준에서 결함이 표시되지 않도록 보관된 자산을 감시합니다. 또는 라이브 세션 중에 미리 보기 URL을 통해 감시하고 유효성을 검사합니다.
14. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.
15. 각 샘플을 만든 후에 라이브 이벤트 상태를 다시 설정합니다.
16. 인코더에서 지원하는 모든 구성에 대해 5-15단계를 반복합니다(광고 신호, 자막 또는 다른 인코딩 속도의 사용 여부와 관계없이).

## <a name="live-encoding-live-event-verification"></a>라이브 인코딩 라이브 이벤트 확인

1. Media Services 계정에서 **스트리밍 엔드포인트**가 실행 중인지 확인합니다. 
2. **라이브 인코딩** 라이브 이벤트를 만들고 시작합니다. <br/> 자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.
3. 수집 URL을 가져오고, 단일 비트 전송률 라이브 스트림을 Media Services로 푸시하도록 인코더를 구성합니다.
4. 미리 보기 URL을 가져와서 인코더의 입력이 실제로 수신되고 있는지 확인하는 데 사용합니다.
5. 새 **자산** 개체를 만듭니다.
6. **실시간 출력** 을 만들고 사용자가 만든 자산 이름을 사용 합니다.
7. 기본 제공 **스트리밍 정책** 형식을 사용 하 여 **스트리밍 로케이터** 를 만듭니다.
8. **스트리밍 로케이터**의 경로를 나열하여 사용할 URL을 다시 가져옵니다.
9. 스트림을 내보낼 **스트리밍 엔드포인트**의 호스트 이름을 가져옵니다.
10. 8단계의 URL을 9단계의 호스트 이름과 결합하여 전체 URL을 구합니다.
11. 약 10분 동안 라이브 인코더를 실행합니다.
12. 라이브 이벤트를 중지합니다.
13. [Azure Media Player](https://aka.ms/azuremediaplayer) 같은 플레이어를 사용하여 재생 시 모든 품질 수준에서 결함이 표시되지 않도록 보관된 자산을 감시합니다. 또는 라이브 세션 중에 미리 보기 URL을 통해 감시하고 유효성을 검사합니다.
14. 자산 ID, 라이브 보관의 게시된 스트리밍 URL, 라이브 인코더에서 사용되는 설정 및 버전을 기록합니다.
15. 각 샘플을 만든 후에 라이브 이벤트 상태를 다시 설정합니다.
16. 인코더에서 지원하는 모든 구성에 대해 5-15단계를 반복합니다(광고 신호, 자막 또는 다른 인코딩 속도의 사용 여부와 관계없이).

## <a name="longevity-verification"></a>수명 확인

11단계를 제외하고는 [통과 라이브 이벤트 확인](#pass-through-live-event-verification)과 동일한 단계를 따릅니다. <br/>10분 대신 1주 이상 라이브 인코더를 실행합니다. [Azure Media Player](https://aka.ms/azuremediaplayer) 같은 플레이어를 사용하여 재생 시 결함이 표시되지 않도록 때때로 라이브 스트리밍(또는 보관된 자산)을 감시합니다.

## <a name="email-your-recorded-settings"></a>기록된 설정을 이메일로 보내기

마지막으로, 메일을 통해 기록된 설정 및 라이브 보관 매개 변수를 amshelp@microsoft.com의 Azure Media Services에 모든 자체 확인 검사가 통과되었다는 알림으로 보냅니다. 또한 후속 작업을 위해 연락처 정보를 기재해 주세요. 이 프로세스와 관련된 질문이 있는 경우 Azure Media Services 팀에 문의할 수 있습니다.

## <a name="see-also"></a>참고 항목

[테스트 된 온-프레미스 인코더](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>다음 단계

[Media Services v3을 통한 라이브 스트리밍](live-streaming-overview.md)
