---
title: Java용 MSAL에서 오류 및 예외 로깅
titleSuffix: Microsoft identity platform
description: Java용 MSAL에서 오류 및 예외를 기록하는 방법 알아보기
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954921"
---
# <a name="logging-in-msal-for-java"></a>Java용 MSAL에서 로깅

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>Java용 MSAL 로깅

Java용 MSAL은 SLF4J와 호환되는 한 앱에서 이미 사용하고 있는 로깅 라이브러리를 사용할 수 있도록 합니다. Java용 MSAL은 다양한 로깅 프레임워크(예: [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) 및 [Log4j](https://logging.apache.org/log4j/2.x/))에 대한 간단한 외관 또는 추상화로 [Java용 단순 로깅 외관](http://www.slf4j.org/)(SLF4J)을 사용합니다. SLF4J를 사용하면 배포 시 원하는 로깅 프레임워크에 연결할 수 있습니다.

예를 들어 애플리케이션의 로깅 프레임워크로 Logback을 사용하려면 애플리케이션에 대한 Maven pom 파일에 Logback 종속성을 추가합니다.

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

그런 다음 Logback 구성 파일을 추가합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J는 배포 시에 자동으로 Logback에 바인딩됩니다. MSAL 로그가 콘솔에 기록됩니다.

다른 로깅 프레임워크에 바인딩하는 방법에 대한 지침은 [SLF4J 설명서](http://www.slf4j.org/manual.html)를 참조하세요.

### <a name="personal-and-organization-information"></a>개인 및 조직 정보

기본적으로 MSAL 로깅은 개인 데이터 또는 조직 데이터를 캡처하거나 로깅하지 않습니다. 다음 예제에서 개인 또는 조직 데이터 로깅은 기본적으로 해제되어 있습니다.

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

클라이언트 애플리케이션 작성기에서 `logPii()`를 설정하여 개인 및 조직 데이터 로깅을 설정합니다. 개인 또는 조직 데이터 로깅을 설정하는 경우 앱은 매우 중요한 데이터를 안전하게 처리하고 모든 규정 요구 사항을 준수해야 합니다.

다음 예제에서 개인 또는 조직 데이터 로깅은 설정되어 있습니다.

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>다음 단계

더 많은 코드 샘플은 [Microsoft ID 플랫폼 코드 샘플](sample-v2-code.md)을 참조하세요.