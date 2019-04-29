---
title: 라이브 스트리밍 문제 해결 가이드 | Microsoft Docs
description: 이 토픽에서는 라이브 스트리밍 문제를 해결하는 방법에 대한 제안을 제공합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f502e3228274840d23b9f52512280fc0d9f0553b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544697"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>라이브 스트리밍 문제 해결 가이드  

이 문서에서는 일부 라이브 스트리밍 문제를 해결하는 방법에 대한 제안을 제공합니다.

## <a name="issues-related-to-on-premises-encoders"></a>온-프레미스 인코더와 관련된 문제
이 섹션에서는 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내도록 구성된 온-프레미스 인코더와 관련된 문제를 해결하는 방법에 대한 제안을 제공합니다.

### <a name="problem-would-like-to-see-logs"></a>문제: 로그를 참조하려고 합니다.
* **잠재적인 문제**: 문제를 디버그할 때 도움이 될 만한 인코더 로그를 찾을 수 없습니다.
  
  * **Telestream Wirecast**: 보통 C:\Users\{username}\AppData\Roaming\Wirecast\에서 로그를 찾을 수 있습니다. 
  * **Elemental Live**: 관리 포털에서 로그에 대한 링크를 찾을 수 있습니다. **통계**, **로그**를 차례로 클릭합니다. **로그 파일** 페이지에 모든 LiveEvent 항목에 대한 로그의 목록이 표시됩니다. 현재 세션과 일치하는 로그를 선택합니다. 
  * **Flash Media Live Encoder**: **Encoding Log** 탭으로 이동하여 **Log Directory...** 를 찾을 수 있습니다.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>문제: 점진적 스트림을 출력하기 위한 옵션이 없습니다.
* **잠재적인 문제**: 사용 중인 인코더가 자동으로 디인터레이스하지 않습니다. 
  
    **문제 해결 단계**: 인코더 인터페이스 내에서 디인터레이스 옵션을 찾아봅니다. 디인터레이스를 사용하도록 설정한 후 다시 점진적 출력 설정을 확인합니다. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>문제: 여러 인코더 출력 설정을 시도했는데 여전히 연결할 수 없습니다.
* **잠재적인 문제**: Azure 인코딩 채널이 올바르게 다시 설정되지 않았습니다. 
  
    **문제 해결 단계**: 인코더가 더 이상 AMS에 푸시하지 않는지 확인하고 채널을 중지한 다음, 다시 설정합니다. 다시 실행한 후 새 설정으로 인코더를 연결해 보십시오. 여전히 문제가 해결되지 않으면 새 채널을 전체적으로 다시 만들어 보십시오. 때에 따라 여러 번 시도가 실패한 후 채널이 손상될 수 있습니다.  
* **잠재적인 문제**: GOP 크기 또는 키 프레임 설정이 최적의 상태가 아닙니다. 
  
    **문제 해결 단계**: 권장 GOP 크기 또는 키 프레임 간격은 2초입니다. 일부 인코더는 이 설정을 프레임 단위로 계산하는 반면, 다른 인코더는 초를 사용합니다. 예를 들면 다음과 같습니다. 30fps를 출력할 때 GOP 크기는 60프레임이며, 이는 2초와 같습니다.  
* **잠재적인 문제**: 닫힌 포트가 스트림을 차단하고 있습니다. 
  
    **문제 해결 단계**: RTMP를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 포트 1935 및 1936이 열려 있는지 확인합니다. 

> [!NOTE]
> 문제 해결 단계를 수행한 후에도 여전히 성공적으로 스트리밍되지 않으면 Azure Portal을 사용하여 지원 티켓을 제출하세요.
> 
> 

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

