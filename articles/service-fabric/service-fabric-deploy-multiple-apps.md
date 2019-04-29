---
title: MongoDB를 사용하는 Node.js 애플리케이션을 Azure Service Fabric에 배포 | Microsoft Docs
description: 여러 게스트 실행 파일을 패키지하여 Azure 서비스 패브릭 클러스터에 배포하는 방법에 대한 연습
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 69df9eff85d96c9cc6ca7fa1d3aabd2c54fae416
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583867"
---
# <a name="deploy-multiple-guest-executables"></a>여러 개의 게스트 실행 파일 배포
이 문서에서는 여러 게스트 실행 파일을 패키징하고 Azure Service Fabric에 배포하는 방법을 보여 줍니다. 단일 Service Fabric 패키지를 빌드 및 배포하는 방법은 [Service Fabric에 게스트 실행 파일 배포](service-fabric-deploy-existing-app.md) 방법을 참조하세요.

이 연습에서는 MongoDB를 데이터 스토리지로 사용하는 Node.js 프런트 엔드를 통해 애플리케이션을 배포하는 방법을 보여 줍니다. 이 단계는 다른 애플리케이션에 종속된 모든 애플리케이션에 적용할 수 있습니다.   

Visual Studio를 사용하여 여러 게스트 실행 파일이 포함된 애플리케이션 패키지를 생성할 수 있습니다. [Visual Studio를 사용하여 기존 애플리케이션 패키징](service-fabric-deploy-existing-app.md)을 참조하세요. 첫 번째 게스트 실행 파일을 추가한 후 애플리케이션 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가-&gt;새 Service Fabric 서비스**를 선택하여 솔루션에 두 번째 게스트 실행 프로젝트를 추가합니다. 참고: Visual Studio 솔루션을 구축하는 Visual Studio 프로젝트에서 원본을 연결하려는 경우 애플리케이션 패키지는 원본의 변경 내용으로 최신 상태로 업데이트됩니다. 

## <a name="samples"></a>샘플
* [게스트 실행 파일을 패키징 및 배포하는 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST를 사용하여 이름 지정 서비스를 통해 통신하는 두 게스트 실행 파일(C# 및 nodejs)의 샘플](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>수동으로 여러 게스트 실행 애플리케이션 패키징
또는 실행 게스트를 수동으로 패키징할 수 있습니다. 수동 패키징의 경우 이 문서에서는 [https://aka.ms/servicefabricpacktool](https://aka.ms/servicefabricpacktool)에서 제공되는 Service Fabric 패키징 도구를 사용합니다.

### <a name="packaging-the-nodejs-application"></a>Node.js 애플리케이션 패키징
이 문서에서는 서비스 패브릭 클러스터의 노드에 Node.js가 아직 설치되지 않은 것으로 가정합니다. 결과적으로 패키징 전에 노드 애플리케이션의 루트 디렉터리에 node.exe를 추가해야 합니다. Node.js 애플리케이션의 디렉터리 구조(Express 웹 프레임워크 및 Jade 템플릿 엔진 사용)는 다음과 비슷합니다.

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

다음 단계는 Node.js 애플리케이션에 대한 애플리케이션 패키지를 만드는 것입니다. 아래 코드는 Node.js 애플리케이션을 포함하는 Service Fabric 애플리케이션 패키지를 만듭니다.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

다음은 사용 중인 매개 변수에 대한 설명입니다.

* **/source** : 패키지할 애플리케이션의 디렉터리를 가리킵니다.
* **/target** : 패키지를 만들 디렉터리를 정의합니다. 이 디렉터리는 원본 디렉터리와 달라야 합니다.
* **/appname** : 기존 애플리케이션의 애플리케이션 이름을 정의합니다. 이 이름은 매니페스트에서 Service Fabric 애플리케이션 이름이 아니라 서비스 이름으로 변환된다는 점을 이해하는 것이 중요합니다.
* **/exe**: Service Fabric이 시작할 실행 파일을 정의합니다. 이 예에서는 `node.exe`입니다.
* **/ma** : 실행 파일을 시작하는 데 사용되는 인수를 정의합니다. Node.js가 설치되지 않았기 때문에 서비스 패브릭에서 `node.exe bin/www`를 실행하여 Node.js 웹 서버를 시작해야 합니다.  `/ma:'bin/www'`는 패키징 도구에 `bin/www`를 node.exe의 인수로 사용하도록 지시합니다.
* **/AppType**: Service Fabric 애플리케이션 형식 이름을 정의합니다.

/target 매개 변수에서 지정한 디렉터리로 이동하면 다음과 같이 도구가 완벽하게 작동하는 서비스 패브릭 패키지를 만든 것을 볼 수 있습니다.

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
생성된 ServiceManifest.xml에는 이제 아래의 코드 조각과 같이 Node.js 웹 서버를 시작하는 방법을 설명하는 섹션이 있습니다.

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
이 샘플에서 Node.js 웹 서버는 포트 3000에서 수신하므로 ServiceManifest.xml 파일의 엔드포인트 정보를 아래와 같이 업데이트해야 합니다.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>MongoDB 애플리케이션 패키징
Node.js 애플리케이션을 패키지했으므로 이제 MongoDB를 패키지할 수 있습니다. 앞서 언급했듯이 이제부터 수행할 단계는 Node.js 및 MongoDB에만 적용되는 것이 아니라 하나의 Service Fabric 애플리케이션으로 패키지되어야 하는 모든 애플리케이션에 적용됩니다.  

MongoDB를 패키지하려면 Mongod.exe 및 Mongo.exe를 패키지해야 합니다. 이 두 이진 파일은 MongoDB 설치 디렉터리의 `bin` 디렉터리에 있습니다. 디렉터리 구조는 아래와 유사합니다.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
서비스 패브릭에서 아래와 유사한 명령을 사용하여 MongoDB를 시작해야 하므로 MongoDB를 패키지할 때 `/ma` 매개 변수를 사용해야 합니다.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> MongoDB 데이터 디렉터리를 노드의 로컬 디렉터리에 넣으면 노드 오류 발생 시 데이터가 보존되지 않습니다. 데이터 손실을 방지하려면 지속형 저장소를 사용하거나 MongoDB 복제본 세트를 구현해야 합니다.  
>
>

PowerShell 또는 명령 셸에서 다음 매개 변수와 함께 패키징 도구를 실행합니다.

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Service Fabric 애플리케이션 패키지에 MongoDB를 추가하려면 /target 매개 변수가 애플리케이션 매니페스트와 Node.js 애플리케이션이 이미 포함된 디렉터리를 가리켜야 합니다. 또한 동일한 ApplicationType 이름을 사용해야 합니다.

해당 디렉터리로 이동하여 도구가 만든 항목을 확인합니다.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
도구가 MongoDB 이진 파일이 포함된 디렉터리에 새 폴더 MongoDB를 추가했음을 확인할 수 있습니다. 이제 `ApplicationManifest.xml` 파일을 열면 패키지에 Node.js 애플리케이션과 MongoDB가 모두 포함된 것을 볼 수 있습니다. 아래 코드는 애플리케이션 매니페스트의 내용입니다.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>애플리케이션 게시
마지막 단계는 아래의 PowerShell 스크립트를 사용하여 로컬 Service Fabric 클러스터에 애플리케이션을 게시하는 것입니다.

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

응용 프로그램이 성공적으로 로컬 클러스터에 게시 되 면에서는 Node.js 응용 프로그램-예를 들어 http의 서비스 매니페스트에 입력 한 포트에서 Node.js 응용 프로그램을 액세스할 수 있습니다:\//localhost:3000 합니다.

이 자습서에서는 간편하게 두 기존 애플리케이션을 하나의 서버 패브릭 애플리케이션으로 패키지하는 방법을 알아보았습니다. 또한 고가용성 및 상태 시스템 통합 같은 서비스 패브릭의 장점을 활용할 수 있도록 응용 프로그램을 서비스 패브릭에 배포하는 방법도 알아보았습니다.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Linux에서 Yeoman를 사용하여 기존 애플리케이션에 더 많은 게스트 실행 파일 추가

`yo`을 사용하여 만든 애플리케이션에 다른 서비스를 추가하려면 다음 단계를 수행합니다. 
1. 기존 애플리케이션의 루트로 디렉터리를 변경합니다.  예를 들어 `MyApplication`이 Yeoman에서 만든 애플리케이션인 경우 `cd ~/YeomanSamples/MyApplication`입니다.
2. `yo azuresfguest:AddService`를 실행하고 필요한 세부 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계
* [Service Fabric 및 컨테이너 개요](service-fabric-containers-overview.md)에서 컨테이너 배포 방법을 알아봅니다.
* [게스트 실행 파일을 패키징 및 배포하는 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST를 사용하여 이름 지정 서비스를 통해 통신하는 두 게스트 실행 파일(C# 및 nodejs)의 샘플](https://github.com/Azure-Samples/service-fabric-containers)
