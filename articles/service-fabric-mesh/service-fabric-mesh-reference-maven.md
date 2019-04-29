---
title: Azure Service Fabric Mesh Maven 참조 | Microsoft Docs
description: Service Fabric Mesh에 대한 Maven 플러그 인을 사용하는 방법에 대한 참조를 포함합니다.
services: service-fabric-mesh
keywords: maven에서 java, cli
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811623"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Service Fabric Mesh에 대한 Maven 플러그 인

## <a name="prerequisites"></a>필수 조건

- Java SDK
- Maven
- 메시 확장을 사용하는 Azure CLI
- Service Fabric CLI

## <a name="goals"></a>목표

### `azure-sfmesh:init`
- `application.yaml` 파일이 포함된 `appresources` 폴더를 포함하는 `servicefabric` 폴더를 만듭니다. 

### `azure-sfmesh:addservice`
- 서비스 이름을 사용하여 `servicefabric` 폴더 내 폴더를 만들고 서비스의 YAML 파일을 만듭니다. 

### `azure-sfmesh:addnetwork`
- `appresources` 폴더에서 제공된 네트워크 이름을 사용하여 `network` YAML을 생성합니다. 

### `azure-sfmesh:addgateway`
- `appresources` 폴더에서 제공된 게이트웨이 이름을 사용하여 `gateway` YAML을 생성합니다. 

### `azure-sfmesh:addsecret`
- `appresources` 폴더에서 제공된 비밀 이름을 사용하여 `secret` YAML을 생성합니다. 

### `azure-sfmesh:addsecretvalue`
- `appresources` 폴더에서 제공된 비밀과 비밀 값 이름을 사용하여 `secretvalue` YAML을 생성합니다. 

### `azure-sfmesh:deploy`
- `servicefabric` 폴더에서 YAML을 병합하여 현재 폴더에서 Azure Resource Manager 템플릿 JSON을 만듭니다.
- Azure Service Fabric Mesh 환경에 모든 리소스를 배포합니다. 

### `azure-sfmesh:deploytocluster`
- Service Fabric 클러스터에 적용되는 YAML에서 생성된 JSON 배포를 포함하는 폴더(`meshDeploy`)를 만듭니다.
- Azure Service Fabric 클러스터에 모든 리소스를 배포합니다.
 

## <a name="usage"></a>사용 현황

Maven Java 앱에서 Maven 플러그 인을 사용하려면 pom.xml 파일에 다음 코드 조각을 추가합니다.

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>일반 구성

현재 Maven 플러그 인은 Azure에 대한 Maven 플러그 인의 일반구성을 지원하지 않습니다.

## <a name="how-to"></a>사용 방법

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Azure Service Fabric Mesh에 대한 Maven 프로젝트 초기화
다음 명령을 실행하여 애플리케이션 리소스 YAML 파일을 만듭니다.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- `app_helloworldserver`라고 명명된 애플리케이션 YAML을 포함하는 루터 폴더에서 `servicefabric->appresources`라고 하는 폴더를 만듭니다.

### <a name="add-resource-to-your-application"></a>애플리케이션에 리소스 추가

#### <a name="add-a-new-network-to-your-application"></a>애플리케이션에 새 네트워크 추가
아래 명령을 실행하여 네트워크 리소스 YAML을 만듭니다. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- `network_helloworldservicenetwork`라고 명명된 `servicefabric->appresources`번 폴더에서 네트워크 YAML을 만듭니다.

#### <a name="add-a-new-service-to-your-application"></a>애플리케이션에 새 서비스 추가
아래 명령을 실행하여 서비스 YAML을 만듭니다. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- `helloworldservicenetwork` 및 `helloworldserver` 앱을 참조하는 `service_helloworldservice`라고 명명된 `servicefabric->helloworldservice` 폴더에서 서비스 YAML을 만듭니다.
- 서비스는 포트 8080에서 수신 대기합니다.
- 이 서비스는 컨테이너 이미지로 ***helloworldserver:latest***를 사용합니다.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>애플리케이션에 새 게이트웨이 리소스 추가
아래 명령을 실행하여 게이트웨이 리소스 YAML을 만듭니다. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- `gateway_helloworldgateway`라고 명명된 `servicefabric->appresources`번 폴더에서 새 게이트웨이 YAML을 만듭니다.
- 이 게이트웨이에서 호출을 수신하는 서비스 수신기로 `helloworldservicelistener`를 참조합니다. 또한 서비스로 `helloworldservice`, 네트워크로 `helloworldservicenetwork`, 애플리케이션으로 `helloworldserver`를 참조합니다. 
- 포트 80에서 요청 수신

#### <a name="add-a-new-volume-to-your-application"></a>애플리케이션에 새 볼륨 추가
아래 명령을 실행하여 볼륨 리소스 YAML을 만듭니다. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- `volume_vol1`라고 명명된 `servicefabric->appresources`번 폴더에서 볼륨 YAML을 만듭니다.
- 위와 같이 필수 매개 변수, `volumeAccountKey` 및 `volumeShareName` 속성을 설정합니다.
- 만든 볼륨을 참조하는 방법에 대한 자세한 내용은 다음 [Azure Files 볼륨을 사용하여 애플리케이션 배포](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)를 참조하세요.

#### <a name="add-a-new-secret-resource-to-your-application"></a>애플리케이션에 새 비밀 리소스 추가
아래 명령을 실행하여 비밀 리소스 YAML을 만듭니다. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- `secret_secret1`라고 명명된 `servicefabric->appresources`번 폴더에서 비밀 YAML을 만듭니다.
- 만든 비밀을 참조하는 방법에 대한 자세한 내용은 다음 [비밀 관리](service-fabric-mesh-howto-manage-secrets.md)를 참조하세요.

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>애플리케이션에 새 비밀 값 리소스 추가
아래 명령을 실행하여 비밀 값 리소스 YAML을 만듭니다. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- `secretvalue_secret1_v1`라고 명명된 `servicefabric->appresources`번 폴더에 비밀 값 YAML을 만듭니다.

### <a name="run-the-application-locally"></a>로컬에서 애플리케이션 실행

`azure-sfmesh:deploytocluster` 목표를 활용하여 아래 명령을 사용해 로컬에서 애플리케이션을 실행할 수 있습니다.

```cmd
mvn azure-sfmesh:deploytocluster
```

기본적으로 이 목표를 사용하여 로컬 클러스터에 리소스를 배포합니다. 로컬 클러스터에 배포하는 경우 로컬 Service Fabric 클러스터가 제대로 실행되고 있다고 가정 합니다. 현재 리소스에 대한 로컬 Service Fabric 클러스터는 [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md)에만 지원됩니다.

- Service Fabric 클러스터를 사용할 수 있는 YAML에서 JSON을 만듭니다.
- 그 다음 클러스터 엔드포인트에 배포합니다.

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Azure Service Fabric Mesh에 애플리케이션 배포

`azure-sfmesh:deploy` 목표를 활용하여, 아래 명령을 실행해 Service Fabric Mesh 환경에 배포할 수 있습니다.

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- 존재하지 않는 경우 `todoapprg`라고 하는 리소스 그룹을 만듭니다.
- YAML을 병합하여 Azure Resource Manager 템플릿 JSON을 만듭니다. 
- Azure Service Fabric Mesh 환경에 JSON을 배포합니다.