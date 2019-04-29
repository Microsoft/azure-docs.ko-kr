---
title: 기존 MongoDB 애플리케이션을 MongoDB용 Azure Cosmos DB API 및 OSBA(Open Service Broker for Azure)와 통합
description: 이 문서에서는 OSBA(Open Service Broker for Azure)를 사용하여 기존 Java 및 MongoDB 애플리케이션을 MongoDB용 Azure Cosmos DB API와 통합하는 방법을 알아봅니다.
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker for Azure
ms.openlocfilehash: 46fa5564e5dd3429f812b263295044d867a8511c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028425"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>기존 MongoDB 애플리케이션을 MongoDB용 Azure Cosmos DB API 및 OSBA(Open Service Broker for Azure)와 통합

Azure Cosmos DB는 전 세계에 배포된 다중 모델 데이터베이스 서비스입니다. 또한 MongoDB용을 포함한 여러 NoSQL API와의 유선 프로토콜 호환성을 제공합니다. MongoDB용 Cosmos DB API를 사용하면 애플리케이션의 데이터베이스 드라이버 또는 구현을 변경하지 않고도 기존 MongoDB 애플리케이션에서 Cosmos DB를 사용할 수 있습니다. 또한 Open Service Broker for Azure를 사용하여 Cosmos DB 서비스를 프로비전할 수도 있습니다.

이 문서에서는 MongoDB 데이터베이스를 사용하는 기존 Java 애플리케이션을 가져오고, Open Service Broker for Azure를 통해 Cosmos DB 데이터베이스를 사용하도록 업데이트합니다.

## <a name="prerequisites"></a>필수 조건

진행하기 전에 다음을 수행해야 합니다.
    
* [Azure Kubernetes 서비스 클러스터](kubernetes-walkthrough.md)를 만듭니다.
* [AKS 클러스터에 Open Service Broker for Azure를 설치 및 구성](integrate-azure.md)합니다. 
* [서비스 카탈로그 CLI](https://svc-cat.io/docs/install/)를 설치하고 `svcat` 명령을 실행하도록 구성합니다.
* 기존 [MongoDB](https://www.mongodb.com/) 데이터베이스를 확보합니다. 예를 들어 [개발 머신](https://docs.mongodb.com/manual/administration/install-community/) 또는 [Azure VM](../virtual-machines/linux/install-mongodb.md)에서 실행되는 MongoDB를 확보합니다.
* [mongo 셸](https://docs.mongodb.com/manual/mongo/)과 같이 MongoDB 데이터베이스에 연결하고 쿼리할 수 있는 방법을 확보합니다.

## <a name="get-application-code"></a>애플리케이션 코드 가져오기
    
이 문서에서는 [Cloud Foundry의 봄 음악 애플리케이션 샘플](https://github.com/cloudfoundry-samples/spring-music)을 통해 MongoDB 데이터베이스를 사용하는 애플리케이션을 보여 줍니다.
    
GitHub에서 애플리케이션을 복제하고 해당 디렉터리로 이동합니다.
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>MongoDB 데이터베이스를 사용할 애플리케이션 준비

봄 음악 애플리케이션 샘플은 데이터 원본에 대한 다양한 옵션을 제공합니다. 이 문서에서는 기존 MongoDB 데이터베이스를 사용하도록 구성합니다. 

*src/main/resource/application.yml*의 끝 부분에 YAML을 추가합니다. 이 추가 부분은 *mongodb*라는 프로필을 만들고 URI와 데이터베이스 이름을 구성합니다. URI를 기존 MongoDB 데이터베이스에 대한 연결 정보로 바꿉니다. 사용자 이름과 암호가 포함된 URI는 **개발 용도**로만 이 파일에 직접 추가하고, **버전 제어에는 추가하면 안 됩니다**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



애플리케이션을 시작하고 *mongodb* 프로필을 사용하도록 지시하는 경우 이 부분은 MongoDB 데이터베이스에 연결되어 애플리케이션 데이터를 저장하는 데 사용됩니다.

애플리케이션을 빌드하려면 다음을 수행합니다.

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

애플리케이션을 시작하고 *mongodb* 프로필을 사용하도록 지시합니다.

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

브라우저에서 `http://localhost:8080`으로 이동합니다.

![기본 데이터가 있는 봄 음악 앱](media/music-app.png)

애플리케이션에 일부 [기본 데이터](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)가 채워져 있습니다. 몇 개의 기존 앨범을 삭제하고 몇 개를 새로 만들면서 상호 작용합니다.

MongoDB 데이터베이스에 연결하고 *musicdb* 데이터베이스를 쿼리하여 애플리케이션에서 MongoDB 데이터베이스를 사용하고 있는지 확인할 수 있습니다.

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

위의 예제에서는 [mongo 셸](https://docs.mongodb.com/manual/mongo/)을 사용하여 MongoDB 데이터베이스에 연결하고 쿼리합니다. 또한 애플리케이션을 중지하고, 다시 시작하며, 브라우저에서 다시 이동하여 변경 내용이 유지되는지 확인할 수도 있습니다. 변경 내용이 여전히 유지되고 있습니다.


## <a name="create-a-cosmos-db-database"></a>Cosmos DB 데이터베이스 만들기

Open Service Broker를 사용하여 Azure에 Cosmos DB 데이터베이스를 만들려면 `svcat provision` 명령을 사용합니다.

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

위의 명령은 Azure의 Cosmos DB 데이터베이스를 *eastus* 지역의 *MyResourceGroup* 리소스 그룹에 프로비전합니다. *resourceGroup*, *location* 및 다른 Azure 관련 JSON 매개 변수에 대한 자세한 내용은 [Cosmos DB 모듈 참조 설명서](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3)에서 사용할 수 있습니다.

데이터베이스에서 프로비전을 완료했는지 확인하려면 `svcat get instance` 명령을 사용합니다.

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

*STATUS*(상태) 아래에 *Ready*(준비)가 표시되면 해당 데이터베이스가 준비되었습니다.

데이터베이스 프로비전이 완료되면 메타데이터를 [Kubernetes 비밀](https://kubernetes.io/docs/concepts/configuration/secret/)에 바인딩해야 합니다. 그러면 다른 애플리케이션이 비밀에 바인딩된 후 해당 데이터에 액세스할 수 있습니다. 데이터베이스의 메타데이터를 비밀에 바인딩하려면 `svcat bind` 명령을 사용합니다.

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>애플리케이션에서 Cosmos DB 데이터베이스 사용

애플리케이션에서 Cosmos DB 데이터베이스를 사용하려면 연결할 URI를 알아야 합니다. 이 정보를 가져오려면 `kubectl get secret` 명령을 사용합니다.

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

위의 명령은 *musicdb* 비밀을 가져오며 URI만 표시합니다. 비밀은 base64 형식으로 저장되므로 위의 명령에서도 디코딩합니다.

Cosmos DB 데이터베이스의 URI를 사용하여 *src/main/resource/application.yml*에서 이 URI를 사용하도록 업데이트합니다.

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

사용자 이름과 암호가 포함된 URI는 **개발 용도**로만 이 파일로 직접 업데이트하고, **버전 제어에는 추가하면 안 됩니다**.

애플리케이션을 다시 빌드하고 시작하여 Cosmos DB 데이터베이스 사용을 시작합니다.

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

애플리케이션에서 여전히 *mongodb* 프로필과 *mongodb://* 로 시작하는 URI를 사용하여 Cosmos DB 데이터베이스에 연결합니다. [MongoDB용 Azure Cosmos DB API](../cosmos-db/mongodb-introduction.md)는 이 호환성을 제공합니다. 애플리케이션에서 MongoDB 데이터베이스를 사용하는 것처럼 계속 작동할 수 있지만, 실제로는 Cosmos DB를 사용합니다.

브라우저에서 `http://localhost:8080`으로 이동합니다. 기본 데이터가 복원되었습니다. 몇 개의 기존 앨범을 삭제하고 몇 개를 새로 만들면서 상호 작용합니다. 애플리케이션을 중지하고, 다시 시작하며, 브라우저에서 다시 이동하여 변경 내용이 유지되는지 확인할 수 있습니다. 변경 내용이 여전히 유지되고 있습니다. 변경 내용은 Open Service Broker for Azure를 사용하여 만든 Cosmos DB에 유지됩니다.


## <a name="run-your-application-on-your-aks-cluster"></a>AKS 클러스터에서 애플리케이션 실행

[Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md)를 사용하여 애플리케이션을 AKS 클러스터에 배포할 수 있습니다. Azure 개발 공간을 사용 하면 Dockerfile 및 Helm 차트 등의 아티팩트를 생성 하 고 배포 하 고 AKS에서 응용 프로그램을 실행 합니다.

AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정하려면 다음을 수행합니다.

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Azure Dev Spaces 도구를 사용하여 AKS에서 실행할 애플리케이션을 준비합니다.

```cmd
azds prep --public
```

이 명령은 프로젝트의 루트에 *charts/* 폴더(Helm 차트)를 포함하여 여러 아티팩트를 생성합니다. 이 명령은 이 특정 프로젝트에 대한 *Dockerfile*을 생성할 수 없으므로 직접 만들어야 합니다.

*Dockerfile*이라는 프로젝트의 루트에 다음 내용이 포함된 파일을 만듭니다.

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

또한 *azds.yaml*의 *configurations.develop.build* 속성을 *false*로 업데이트해야 합니다.
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

또한 *charts/spring-music/templates/deployment.yaml*에서 *containerPort* 특성을 *8080*으로 업데이트해야 합니다.

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

애플리케이션을 AKS에 배포하려면 다음을 수행합니다.

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

로그에 표시된 URL로 이동합니다. 위 예제에서는 *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* 를 사용합니다. 

애플리케이션이 변경 내용과 함께 표시되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 애플리케이션을 MongoDB 사용에서 MongoDB용 Cosmos DB API 사용으로 업데이트하는 방법에 대해 설명했습니다. 또한 Open Service Broker for Azure를 사용하여 Cosmos DB 서비스를 프로비전하고 Azure Dev Spaces를 통해 해당 애플리케이션을 AKS에 배포하는 방법에 대해서도 설명했습니다.

Cosmos DB, Open Service Broker for Azure 및 Azure Dev Spaces에 대한 자세한 내용은 다음을 참조하세요.
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [Dev Spaces를 사용하여 개발](../dev-spaces/azure-dev-spaces.md)
