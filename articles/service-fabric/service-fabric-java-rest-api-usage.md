---
title: Azure Service Fabric Java 클라이언트 API | Microsoft Docs
description: Service Fabric 클라이언트 REST API 사양을 사용하여 Service Fabric Java 클라이언트 API를 생성하고 사용합니다.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 97bba87331965b0f7ce20ec2ee089e0e18f72457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720283"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java 클라이언트 API

Service Fabric 클라이언트 API를 사용하면 Azure, 온-프레미스, 로컬 개발 컴퓨터 또는 다른 클라우드의 Service Fabric 클러스터에 마이크로 서비스 기반 응용 프로그램 및 컨테이너를 배포하고 관리할 수 있습니다. 이 문서에서는 Service Fabric 클라이언트 REST API에 기반하여 Service Fabric Java 클라이언트 API를 생성하고 사용하는 방법에 대해 설명합니다.

## <a name="generate-the-client-code-using-autorest"></a>AutoRest를 사용하여 클라이언트 코드 생성

[AutoRest](https://github.com/Azure/autorest)는 RESTful 웹 서비스에 액세스하기 위한 클라이언트 라이브러리를 생성하는 도구입니다. AutoRest에 대한 입력은 OpenAPI 사양 형식을 사용하는 REST API를 설명하는 사양입니다. [Service Fabric 클라이언트 REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane)는 이 사양을 따릅니다.

아래에서 설명하는 단계에 따라 AutoRest 도구를 사용하여 Service Fabric Java 클라이언트 코드를 생성합니다.

1. 컴퓨터에서 Node.js 및 NPM 설치

    Linux를 사용하는 경우:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Mac OS X를 사용하는 경우:
    ```bash
    brew install node
    ```

2. NPM을 사용하여 AutoRest를 설치합니다.
    ```bash
    npm install -g autorest
    ```

3. 로컬 컴퓨터의 [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) 리포지토리를 포크 및 복제하고, 컴퓨터의 터미널에서 복제된 위치로 이동합니다.


4. 복제된 리포지토리에서 아래에서 언급하는 위치로 이동합니다.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > 클러스터 버전이 6.0.*이 아닌 경우 stable 폴더의 해당 디렉터리로 이동합니다.
    >   

5. 다음 autorest 명령을 실행하여 Java 클라이언트 코드를 생성합니다.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   아래 예제에서는 autorest의 사용을 보여 줍니다.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   다음 명령은 ``servicefabric.json`` 사양 파일을 입력으로 사용하고, ``java-rest-api-     code`` 폴더에 Java 클라이언트 코드를 생성하고, ``servicefabricrest`` 네임스페이스에 코드를 묶습니다. 이 단계가 수행되면 ``java-rest-api-code`` 폴더에 ``models`` 및 ``implementation``의 두 폴더와 ``ServiceFabricClientAPIs.java`` 및 ``package-info.java``의 두 파일이 생성되어 있습니다.


## <a name="include-and-use-the-generated-client-in-your-project"></a>프로젝트에 생성된 클라이언트 포함 및 사용

1. 생성된 코드를 프로젝트에 적절하게 추가합니다. 생성된 코드를 사용하여 라이브러리를 만들고, 이 라이브러리를 프로젝트에 포함하는 것이 좋습니다.
2. 라이브러리를 만드는 경우 라이브러리의 프로젝트에 다음 종속성을 포함합니다. 다른 방법을 따르는 경우 종속성을 적절하게 포함합니다.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    예를 들어 Maven 빌드 시스템을 사용하는 경우 ``pom.xml`` 파일에 다음을 포함합니다.

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. 다음 코드를 사용하여 RestClient를 만듭니다.

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. 클라이언트 개체를 사용하고 필요에 따라 적절하게 호출합니다. 다음은 클라이언트 개체의 사용법을 보여 주는 몇 가지 예제입니다. 여기서는 아래의 API를 사용하기 전에 애플리케이션 패키지가 빌드되어 이미지 저장소에 업로드되었다고 가정합니다.
    * 애플리케이션 프로비전
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * 애플리케이션 만들기

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>생성된 코드에 대한 이해
모든 API에 대해 네 개의 구현 오버로드를 찾을 수 있습니다. 선택적 매개 변수가 있는 경우 이러한 선택적 매개 변수를 포함하여 네 개의 변형을 더 찾을 수 있습니다. 예를 들어 ``removeReplica`` API를 살펴보겠습니다.
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * 이는 removeReplica API 호출의 동기 변형입니다.
 2. **public ServiceFuture\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout, final ServiceCallback\<Void> serviceCallback)**
    * 나중에 기반한 비동기 프로그래밍을 사용하고 콜백을 사용하려는 경우 이 변형의 API 호출을 사용할 수 있습니다.
 3. **공용 Observable\<Void > removeReplicaAsync (문자열 nodeName, UUID partitionId, 문자열 replicaId)**
    * 사후 비동기 프로그래밍을 사용하려는 경우 이 변형의 API 호출을 사용할 수 있습니다.
 4. **공용 Observable\<ServiceResponse\<Void >> removeReplicaWithServiceResponseAsync (문자열 nodeName, UUID partitionId, 문자열 replicaId)**
    * 사후 비동기 프로그래밍을 사용하고 RAW REST 응답을 처리하려는 경우 이 변형의 API 호출을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Service Fabric REST API](https://docs.microsoft.com/rest/api/servicefabric/)에 대해 알아봅니다.

