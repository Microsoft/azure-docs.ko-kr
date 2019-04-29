---
title: Java-Azure Media Services v3 API에 연결
description: Java 사용 하 여 Media Services v3 API에 연결 하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: juliako
ms.openlocfilehash: 68e09ec6ce4aeb91e00c2a15caa8ec81f40064c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733915"
---
# <a name="connect-to-media-services-v3-api---java"></a>Media Services v3 API-Java에 연결

이 아티클에서 Azure Media Services v3 Java SDK에 연결 하는 메서드는 서비스 보안 주체 로그인을 사용 하 여 합니다.

이 문서에서는 샘플 앱을 개발 하려면 Visual Studio Code 사용 됩니다.

## <a name="prerequisites"></a>필수 조건

- 따릅니다 [Visual Studio Code를 사용 하 여 쓰기 Java](https://code.visualstudio.com/docs/java/java-tutorial) 설치 하려면:

   - JDK
   - Apache Maven
   - Java 확장 팩
- 설정 해야 `JAVA_HOME` 고 `PATH` 환경 변수입니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md) Media Services 계정 이름과 리소스 그룹 이름을 기억해 두어야 합니다.
- 단계를 수행 합니다 [Api 액세스](access-api-cli-how-to.md) 항목입니다. 이후 단계에서 구독 ID, 응용 프로그램 ID (클라이언트 ID), 인증 키 (암호) 및 해야 하는 테 넌 트 ID를 기록 합니다.

또한 검토 해야 합니다.

- [Visual Studio Code에서 Java](https://code.visualstudio.com/docs/languages/java)
- [VS Code에서 Java 프로젝트 관리](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

명령줄 도구를 열고 및 `cd` 프로젝트를 만들려면 원하는 디렉터리에 있습니다.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

명령을 실행할 때 합니다 `pom.xml`, `App.java`, 및 기타 파일을 만드는 합니다. 

## <a name="add-dependencies"></a>종속성 추가

1. Visual Studio Code에서 프로젝트가 있는 폴더를 엽니다
1. 찾기 및 열기를 `pom.xml`
1. 필요한 종속성을 추가 합니다.

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
```

## <a name="connect-to-the-java-client"></a>Java 클라이언트에 연결

1. 엽니다는 `App.java` 파일 `src\main\java\com\azure\ams` 패키지 맨 위에 있는 포함 되어 있는지 확인 하 고:

    ```java
    package com.azure.ams;
    ```
1. 패키지 문에서 다음과 같은 import 문을 추가합니다.
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. 요청을 해야 하는 Active Directory 자격 증명을 만들려면 App 클래스의 main 메서드에 다음 코드를 추가 하 고에서 가져온 값을 설정 [Api 액세스](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. 앱을 실행합니다.

## <a name="see-also"></a>참고 항목

- [Media Services 개념](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java 참조](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>다음 단계

이제는 `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` 받고 엔터티를 조작 합니다.
