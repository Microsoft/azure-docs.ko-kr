---
title: Azure Media Services v3 API에 연결-Java
description: 이 문서에서는 Java를 사용 하 여 Azure Media Services v3 API에 연결 하는 방법을 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: dc667a46d9802332d0f8c8dc673b28a9aeab96ea
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265629"
---
# <a name="connect-to-media-services-v3-api---java"></a>Media Services v3 API에 연결-Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 사용자 로그인 메서드를 사용 하 여 Azure Media Services v3 Java SDK에 연결 하는 방법을 보여 줍니다.

이 문서에서 Visual Studio Code는 샘플 앱을 개발 하는 데 사용 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Visual Studio Code를 사용 하 여 Java 작성](https://code.visualstudio.com/docs/java/java-tutorial) 다음을 설치 합니다.

   - JDK
   - Apache Maven
   - Java 확장 팩
- `JAVA_HOME`및 환경 변수를 설정 해야 `PATH` 합니다.
- [Media Services 계정 만들기](./create-account-howto.md) 리소스 그룹 이름 및 Media Services 계정 이름을 명심 해야 합니다.
- [Api 액세스](./access-api-howto.md) 항목의 단계를 따릅니다. 이후 단계에서 필요한 구독 ID, 응용 프로그램 ID (클라이언트 ID), 인증 키 (비밀) 및 테 넌 트 ID를 기록 합니다.

또한 다음을 검토 합니다.

- [Visual Studio Code의 Java](https://code.visualstudio.com/docs/languages/java)
- [VS Code의 Java 프로젝트 관리](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토합니다.

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

명령줄 도구와 `cd` 프로젝트를 만들 디렉터리를 엽니다.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

명령을 실행 하면 `pom.xml` , `App.java` 및 기타 파일이 생성 됩니다. 

## <a name="add-dependencies"></a>종속성 추가

1. Visual Studio Code에서 프로젝트가 있는 폴더를 엽니다.
1. 을 찾아 엽니다. `pom.xml`
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

1. `App.java`에서 파일을 열고 `src\main\java\com\azure\ams` 패키지가 맨 위에 포함 되는지 확인 합니다.

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
1. 요청을 수행 하는 데 필요한 Active Directory 자격 증명을 만들려면 App 클래스의 main 메서드에 다음 코드를 추가 하 고 [액세스 api](./access-api-howto.md)에서 얻은 값을 설정 합니다.
   
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
- [windowsazure.mediaservices: v2018_07_01: azure-관리-미디어](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>다음 단계

이제 `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` 엔터티 조작을 포함 하 고 시작할 수 있습니다.

더 많은 코드 예제는 [JAVA SDK 샘플](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) 리포지토리를 참조 하세요.
