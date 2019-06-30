---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5f919a04b47aa6fdef9500f3d7e6bef4ddaa239e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182455"
---
1. [Firebase 콘솔](https://firebase.google.com/console/)에 로그인합니다. 아직 없는 경우 새 Firebase 프로젝트를 만듭니다.
2. 프로젝트를 만든 후 **Android 앱에 Firebase 추가**를 선택합니다. 

    ![Android 앱에 Firebase 추가](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. **Android 앱에 Firebase 추가** 페이지에서 다음 단계를 수행합니다. 
    1. **Android 패키지 이름**에서 애플리케이션의 **build.gradle** 파일에 있는 **applicationId** 값을 복사합니다. 이 예에서는 `com.fabrikam.fcmtutorial1app`입니다. 

        ![패키지 이름 지정](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. **앱 등록**을 선택합니다. 
4. **google-services.json 다운로드**를 선택하고 프로젝트의 **app** 폴더에 파일을 저장한 후 **다음**을 선택합니다. 

    ![google-services.json 다운로드](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Android Studio의 프로젝트에서 다음과 같은 **구성 변경** 작업을 수행합니다. 
    1.  **project-level build.gradle** 파일(&lt;project&gt;/build.gradle)에서 **dependencies** 섹션에 다음 명령문을 추가합니다. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. **app-level build.gradle** 파일(&lt;project&gt;/&lt;app-module&gt;/build.gradle)에서 **dependencies** 섹션에 다음 명령문을 추가합니다. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. **app-level build.gradle** 파일 끝부분에서 dependenices 섹션 다음에 다음 줄을 추가합니다. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. 도구 모음에서 **지금 동기화** *를 선택합니다. 
 
        ![build.gradle 구성 변경](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. 이 페이지에서 **다음**을 선택합니다. 
7. 이 페이지에서 **이 단계 건너뛰기**를 선택합니다. 

    ![마지막 단계 건너뛰기](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. Firebase 콘솔에서 프로젝트의 톱니바퀴를 선택합니다. 그런 후 **프로젝트 설정**을 선택합니다.

    ![프로젝트 설정 선택](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Android Studio 프로젝트의 **app** 폴더에 **google-services.json** 파일을 다운로드하지 않은 경우 이 페이지에서 다운로드할 수 있습니다. 
5. 맨 위에 있는 **클라우드 메시징** 탭으로 전환합니다. 
6. 나중에 사용하기 위해 **서버 키**를 복사하고 저장합니다. 이 값은 알림 허브를 구성하는 데 사용됩니다.
