---
title: Java SDK에서 Maven로 마이그레이션 - 이전 Azure Service Fabric Java 애플리케이션을 업데이트하여 Maven 사용 | Microsoft Docs
description: Service Fabric Java SDK를 사용하기 위해 사용되는 이전 Java 애플리케이션을 업데이트하여 Maven에서 Service Fabric Java 종속성을 페치합니다. 이 설정을 완료한 후에 이전 Java 애플리케이션을 빌드할 수 있습니다.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: rapatchi
ms.openlocfilehash: dbd85b3647a60ce873c1a55b851bd47ece103282
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718397"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>이전 Java Service Fabric 애플리케이션을 업데이트하여 Maven에서 Java 라이브러리 페치
최근에 Service Fabric Java 이진 파일을 Service Fabric Java SDK에서 Maven 호스트로 이동했습니다. 이제 **mavencentral**을 사용하여 최신 Service Fabric Java 종속성을 페치할 수 있습니다. 이 빠른 시작을 통해 Yeoman 템플릿이나 Eclipse 중 하나를 사용하여 Maven 기반 빌드와 호환하도록 Service Fabric Java SDK에서 사용하기 위해 이전에 만들어진 기존 Java 애플리케이션을 업데이트할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
1. 먼저 기존 Java SDK를 제거해야 합니다.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```
2. [여기](service-fabric-cli.md)에서 언급한 단계에 따라 최신 Service Fabric CLI를 설치합니다.

3. Service Fabric Java 애플리케이션을 빌드하고 사용하려면 JDK 1.8 및 Gradle이 설치되어 있는지 확인해야 합니다. 설치하지 않은 경우 다음을 실행하여 JDK 1.8(openjdk-8-jdk) 및 Gradle을 설치할 수 있습니다.

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```
4. 애플리케이션의 스크립트를 설치/제거하도록 업데이트하여 [여기](service-fabric-application-lifecycle-sfctl.md)에서 언급한 단계를 수행하는 새 Service Fabric CLI를 사용합니다. 시작한 [예제](https://github.com/Azure-Samples/service-fabric-java-getting-started)를 참조할 수 있습니다.

>[!TIP]
> Service Fabric Java SDK를 제거한 후에 Yeoman이 작동하지 않습니다. [여기](service-fabric-create-your-first-linux-application-with-java.md)에서 언급한 필수 구성 요소에 따라 Service Fabric Yeoman Java 템플릿 생성기를 작동시킵니다.

## <a name="service-fabric-java-libraries-on-maven"></a>Maven의 Service Fabric Java 라이브러리
Service Fabric Java 라이브러리는 Maven에서 호스팅되었습니다. 프로젝트의 ``pom.xml`` 또는 ``build.gradle``에서 종속성을 추가하고 **mavenCentral**에서 Service Fabric Java 라이브러리를 사용할 수 있습니다.

### <a name="actors"></a>행위자

애플리케이션에 대한 Service Fabric Reliable Actor 지원입니다.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Services

애플리케이션에 대한 Service Fabric 상태 비저장 서비스 지원입니다.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>기타
#### <a name="transport"></a>전송

Service Fabric Java 애플리케이션에 대한 전송 계층 지원입니다. 전송 계층에서 프로그래밍하지 않으면 Reliable Actor 또는 Service 애플리케이션에 이 종속성을 명시적으로 추가할 필요가 없습니다.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>패브릭 지원

Service Fabric에 대한 시스템 수준 지원이며 네이티브 Service Fabric 런타임에 지시합니다. Reliable Actor 또는 Service 애플리케이션에 이 종속성을 명시적으로 추가할 필요가 없습니다. 그러면 위의 다른 종속성을 포함하는 경우 자동으로에서 Maven에서 가져옵니다.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```


## <a name="migrating-service-fabric-stateless-service"></a>Service Fabric 상태 비저장 서비스 마이그레이션

Maven에서 페치되는 Service Fabric 종속성을 사용하여 기존 Service Fabric 상태 비저장 Java 서비스를 빌드하려면 서비스 내에서 ``build.gradle`` 파일을 업데이트해야 합니다. 이전에는 다음과 같이 사용했습니다.
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```
이제 Maven에서 종속성을 페치하기 위해 **업데이트** ``build.gradle``에는 다음과 같은 내용에 해당하는 부분이 있습니다.
```
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```
일반적으로 Service Fabric 상태 비저장 Java 서비스에서 빌드 스크립트의 모양에 대한 전반적인 관념을 가져오려면 시작 예제의 샘플을 참조하면 됩니다. EchoServer 샘플의 [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle)을 다음과 같습니다.

## <a name="migrating-service-fabric-actor-service"></a>Service Fabric 행위자 서비스 마이그레이션

Maven에서 페치되는 Service Fabric 종속성을 사용하여 기존 Service Fabric 행위자 Java 애플리케이션을 빌드하려면 인터페이스 패키지 및 서비스 패키지 내에서 ``build.gradle`` 파일을 업데이트해야 합니다. TestClient 패키지를 사용하는 경우에도 해당 항목을 업데이트해야 합니다. 따라서 ``Myactor`` 작업자의 경우 업데이트해야 하는 위치는 다음과 같습니다.
```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>인터페이스 프로젝트의 빌드 스크립트 업데이트

이전에는 다음과 같이 사용했습니다.
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```
이제 Maven에서 종속성을 페치하기 위해 **업데이트** ``build.gradle``에는 다음과 같은 내용에 해당하는 부분이 있습니다.
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>작업자 프로젝트의 빌드 스크립트 업데이트

이전에는 다음과 같이 사용했습니다.
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```
이제 Maven에서 종속성을 페치하기 위해 **업데이트** ``build.gradle``에는 다음과 같은 내용에 해당하는 부분이 있습니다.
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>테스트 클라이언트 프로젝트의 빌드 스크립트 업데이트

여기에서 변경 내용은 이전 섹션에서 설명한 변경 내용, 즉, 작업자 프로젝트와 유사합니다. 이전에 Gradle 스크립트는 다음과 같이 사용되었습니다.
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```
이제 Maven에서 종속성을 페치하기 위해 **업데이트** ``build.gradle``에는 다음과 같은 내용에 해당하는 부분이 있습니다.
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>다음 단계

* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Service Fabric CLI를 사용하여 Service Fabric 클러스터와 상호 작용](service-fabric-cli.md)
