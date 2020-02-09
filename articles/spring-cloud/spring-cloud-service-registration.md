---
title: 서비스 레지스트리 및 검색 자동화
description: 스프링 클라우드 서비스 레지스트리를 사용 하 여 서비스 검색 및 등록을 자동화 하는 방법을 알아봅니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278851"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>스프링 클라우드 서비스 검색 및 등록

서비스 검색은 마이크로 서비스 기반 아키텍처의 핵심 요구 사항입니다.  각 클라이언트를 수동으로 구성 하면 시간이 걸리고 사용자 오류가 발생할 가능성이 있습니다.  Azure 스프링 클라우드 서비스 레지스트리는이 문제를 해결 합니다.  구성 된 서비스 레지스트리 서버는 응용 프로그램의 마이크로 서비스에 대 한 서비스 등록과 검색을 제어 합니다. 서비스 레지스트리 서버는 배포 된 마이크로 서비스의 레지스트리를 유지 관리 하 고, DNS에 의존 하지 않고 클라이언트 쪽 부하 분산 및 클라이언트의 분리 서비스 공급자를 사용 하도록 설정 합니다.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>스프링 클라우드 서비스 레지스트리를 사용 하 여 응용 프로그램 등록

응용 프로그램에서 스프링 클라우드 서비스 레지스트리를 사용 하 여 서비스 등록과 검색을 관리 하려면 먼저 응용 프로그램의 *pom .xml* 파일에 몇 가지 종속성이 포함 되어야 합니다.
*Netflix-eureka* 에 대 한 *종속성을 포함* *합니다.* --------------------------

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>최상위 클래스 업데이트

마지막으로 응용 프로그램의 최상위 수준 클래스에 주석을 추가 합니다.

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

스프링 클라우드 서비스 레지스트리 서버 엔드포인트은 응용 프로그램에 환경 변수로 삽입 됩니다.  마이크로 서비스는 이제 서비스 레지스트리 서버에 자신을 등록 하 고 다른 종속 마이크로 서비스를 검색할 수 있습니다.

변경 내용이 서버에서 모든 마이크로 서비스로 전파 되는 데 몇 분 정도 걸릴 수 있습니다.
