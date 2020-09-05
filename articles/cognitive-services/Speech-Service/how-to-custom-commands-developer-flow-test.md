---
title: 사용자 지정 명령 앱 테스트
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램을 테스트 하는 다양 한 방법을 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: ec8e40c0908855cd06d647bdd9121106e3553c11
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918913"
---
# <a name="test-your-custom-commands-application"></a>사용자 지정 명령 응용 프로그램 테스트

이 문서에서는 사용자 지정 명령 응용 프로그램을 테스트 하는 다양 한 방법을 알아봅니다.

## <a name="test-in-the-portal"></a>포털에서 테스트

포털에서 테스트는 사용자 지정 명령 응용 프로그램이 예상 대로 작동 하는지 확인 하는 가장 간단 하 고 빠른 방법입니다. 앱이 성공적으로 학습 된 후 `Test` 단추를 클릭 하 여 테스트를 시작 합니다.

> [!div class="mx-imgBorder"]
> ![포털에서 테스트](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트를 사용 하 여 테스트

Windows 음성 도우미 클라이언트는 사용자 지정 클라이언트 응용 프로그램을 만들기 전에 봇과의 상호 작용을 쉽게 테스트할 수 있게 해 주는 c #의 Windows Presentation Foundation (WPF) 응용 프로그램입니다.

이 도구는 사용자 지정 명령 응용 프로그램 ID를 수락할 수 있습니다. 사용자 지정 명령 서비스에서 호스트 되는 작업 완료 또는 명령 및 제어 시나리오를 테스트할 수 있습니다.

클라이언트를 설정 하려면 [Windows 음성 도우미 클라이언트](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)를 체크 아웃 합니다.

> [!div class="mx-imgBorder"]
> ![WVAC 프로필 만들기](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>음성 SDK 사용 클라이언트 응용 프로그램을 사용 하 여 테스트 
음성 SDK (소프트웨어 개발 키트)는 음성 지원 응용 프로그램을 개발할 수 있는 많은 음성 서비스 기능을 제공 합니다. 또한 많은 프로그래밍 언어 및 모든 플랫폼에서 사용할 수 있습니다.

Speech SDK를 사용 하 여 유니버설 Windows 플랫폼 (UWP) 클라이언트 응용 프로그램을 설정 하 고 사용자 지정 명령 응용 프로그램과 통합 하려면 다음을 수행 합니다.  
- [방법: Speech SDK를 사용 하 여 클라이언트 응용 프로그램과 통합](./how-to-custom-commands-setup-speech-sdk.md)
- [방법: 클라이언트 응용 프로그램에 작업 보내기](./how-to-custom-commands-send-activity-to-client.md)
- [방법: 웹 끝점 설정](./how-to-custom-commands-setup-web-endpoints.md)

기타 프로그래밍 언어 및 플랫폼:
- [음성 SDK 프로그래밍 언어, 플랫폼, 시나리오 용량](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)
- [음성 도우미 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 샘플을 참조 하세요.](https://aka.ms/speech/cc-samples)

