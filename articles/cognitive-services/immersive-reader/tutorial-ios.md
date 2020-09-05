---
title: '자습서: Swift iOS 코드 샘플을 사용하여 몰입형 리더 시작'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 몰입형 리더를 시작하는 샘플 Swift 애플리케이션을 구성하고 실행합니다.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516370"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>자습서: Swift iOS 코드 샘플을 사용하여 몰입형 리더 시작

[개요](./overview.md)에서는 몰입형 판독기란 무엇이며 어떻게 언어 학습자, 신흥 독자 및 학습 차이가 있는 사람들의 독해력 향상을 위해 입증된 기술을 구현하는지에 대해 알아보았습니다. 이 자습서에서는 몰입형 리더를 시작하는 iOS 애플리케이션을 만드는 방법에 대해 설명합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 샘플 프로젝트를 사용하여 iOS용 Swift 앱을 구성 및 실행합니다.
> * 액세스 토큰 획득
> * 샘플 콘텐츠를 사용하여 몰입형 리더를 시작합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Active Directory 인증에 대해 구성된 몰입형 판독기 리소스입니다. [다음 지침](./how-to-create-immersive-reader.md)에 따라 설정하세요. 환경 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/)
* [몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>인증 자격 증명 구성

1. Xcode를 열고 **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**를 엽니다.
1. 상단 메뉴에서 **제품** > **스키마** > **스키마 편집**을 선택합니다.
1. **실행** 보기에서 **인수** 탭을 선택합니다.
1. **환경 변수** 섹션에서 다음 이름과 값을 추가합니다. 몰입형 리더 리소스를 만들 때 지정된 값을 제공합니다.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

이 변경 내용에는 공개되어서는 안 되는 비밀이 있으므로 이 변경 내용을 소스 제어로 커밋하지 마세요.

## <a name="start-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 리더 시작

Xcode에서 **Ctrl-R**을 선택하여 프로젝트를 실행합니다.

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 리더 SDK 참조](./reference.md)를 살펴봅니다.
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)에서 코드 샘플을 봅니다.
