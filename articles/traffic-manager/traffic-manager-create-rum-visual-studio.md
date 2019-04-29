---
title: Visual Studio Mobile Center를 사용하여 실제 사용자 측정을 Azure Traffic Manager에 보내기 | Microsoft Docs
description: 실제 사용자 측정을 Traffic Manager에 보내도록 Visual Studio Mobile Center를 사용하여 개발한 모바일 애플리케이션 설정
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1a5b883a8c9688d4545c0e98c00f78a2e982a611
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884056"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Visual Studio Mobile Center를 사용하여 실제 사용자 측정을 Traffic Manager에 보내는 방법

Visual Studio Mobile Center를 사용하여 개발한 모바일 애플리케이션을 다음 단계에 따라 실제 사용자 측정을 Traffic Manager에 보내도록 설정할 수 있습니다.

>[!NOTE]
> 현재 실제 사용자 측정을 Traffic Manager에 보내는 것은 Android에서만 지원됩니다.

실제 사용자 측정을 구성하려면 RUM 패키지로 키를 가져오고 앱을 계측해야 합니다.

## <a name="step-1-obtain-a-key"></a>1단계: 키를 얻으려면
    
클라이언트 애플리케이션에서 Traffic Manager로 보낸 측정값은 RUM(실제 사용자 측정) 키라고 하는 고유한 문자열을 사용하여 서비스에 의해 식별됩니다. Azure Portal, REST API 또는 PowerShell/CLI 인터페이스를 사용하여 RUM 키를 가져올 수 있습니다.

Azure Portal을 사용하여 RUM Key를 얻으려면 다음 절차를 수행합니다.
1. 브라우저에서 Azure Portal에 로그인합니다. 아직 계정이 없는 경우 1개월 평가판에 등록할 수 있습니다.
2. 포털의 검색 창에서 수정하려는 Traffic Manager 프로필 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. Traffic Manager 프로필 페이지에서 **설정** 아래의 **실제 사용자 측정**을 클릭합니다.
4. **키 생성**을 클릭하여 새 RUM 키를 만듭니다.
        
   ![실제 사용자 측정 키 생성](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **그림 1: 실제 사용자 측정 키 생성**

5. 페이지에는 생성된 RUM 키와 HTML 페이지에 포함되어야 하는 JavaScript 코드 조각이 표시됩니다.
 
   ![실제 사용자 측정 키에 대한 Javascript 코드](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **그림 2: 실제 사용자 측정 키 및 측정 JavaScript**
 
6. **복사** 단추를 클릭하여 RUM 키를 복사합니다. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>2단계: Mobile Center SDK의 RUM 패키지로 앱 계측

Visual Studio Mobile Center를 처음 사용하는 경우 해당 [웹 사이트](https://mobile.azure.com)를 방문하세요. SDK 통합에 대한 자세한 내용은 [Android SDK 시작](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android)을 참조하세요.

실제 사용자 측정을 사용하려면 다음 절차를 완료합니다.

1.  프로젝트에 SDK 추가

    ATM RUM SDK 미리 보기 중 패키지 리포지토리를 명시적으로 참조해야 합니다.

    **app/build.gradle** 파일에 다음 줄을 추가합니다.

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    **app/build.gradle** 파일에 다음 줄을 추가합니다.

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. SDK 시작

    앱의 기본 작업 클래스를 열고 다음 import 문을 추가합니다.

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    동일한 파일에서 `onCreate` 콜백을 찾고 다음 코드를 추가합니다.

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>다음 단계
- [실제 사용자 측정](traffic-manager-rum-overview.md)에 대한 자세한 정보
- [Traffic Manager 작동 방식](traffic-manager-overview.md)
- [Mobile Center](https://docs.microsoft.com/mobile-center/)에 대해 자세히 알아봅니다.
- Mobile Center [등록](https://mobile.azure.com)
- Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
- [Traffic Manager 프로필을 만드는](traffic-manager-create-profile.md)

