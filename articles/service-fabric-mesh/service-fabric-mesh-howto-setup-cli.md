---
title: Azure Service Fabric Mesh CLI 설정 | Microsoft Docs
description: Azure Service Fabric Mesh CLI를 설정하는 방법에 대해 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c716ae0a2bb30e7e8eb249a1d230097efc0d3795
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097796"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정
Service Fabric Mesh CLI(명령줄 인터페이스)는 Azure Service Fabric Mesh에서 리소스를 로컬로 배포하고 관리하는 데 필요합니다. 

사용할 수 있는 CLI에는 세 가지 유형이 있으며 아래 표에 요약되어 있습니다. 

| CLI 모듈 | 대상 환경 |  설명 | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Azure Service Fabric Mesh 환경에 대해 애플리케이션을 배포하고 리소스를 관리할 수 있게 해주는 기본 CLI입니다. 
| sfctl | 로컬 클러스터 | 로컬 클러스터에 대해 Service Fabric CLI 리소스를 배포 및 테스트할 수 있도록 해주는 Service Fabric CLI입니다.  
| Maven CLI | 로컬 클러스터 및 Azure Service Fabric Mesh | 에 대 한 래퍼로 `az mesh` 및 `sfctl` Java 개발자가 로컬 및 Azure 개발 환경을 위해 친숙 한 명령줄 환경을 사용할 수 있도록 합니다.  

미리 보기를 위해 Azure CLI에 대한 확장으로 Azure Service Fabric Mesh CLI가 작성되었습니다. Azure Cloud Shell에서 설치하거나 Azure CLI의 로컬 설치에서 설치할 수 있습니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Azure Service Fabric Mesh CLI 설치
1. Azure CLI 버전 2.0.43 이상을 설치해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 최신 버전의 CLI를 설치하거나 업그레이드하려면 [Azure CLI 설치][azure-cli-install]를 참조하세요.

2. 다음 명령을 사용하여 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. 다음 명령을 사용하여 기존 Azure Service Fabric Mesh CLI 모듈을 업데이트합니다.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Service Fabric CLI(sfctl) 설치 

[Service Fabric CLI 설치](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)의 지침을 따르세요. **sfctl** 모듈은 로컬 머신의 Service Fabric 클러스터에 대한 리소스 모델을 기반으로 애플리케이션을 배포하는 데 사용할 수 있습니다. 

## <a name="install-the-maven-cli"></a>Maven CLI 설치 

Maven CLI를 사용하려면 다음을 머신에 설치해야 합니다. 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI(az mesh) - Azure Service Fabric Mesh를 대상으로 지정 
* SFCTL(sfctl) - 로컬 클러스터를 대상으로 지정 

Service Fabric용 Maven CLI는 아직 미리 보기 버전입니다. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

자세한 사용 방법을 알아보려면 [Maven CLI 참조](service-fabric-mesh-reference-maven.md)를 읽어보세요.

## <a name="next-steps"></a>다음 단계

[Windows 개발 환경](service-fabric-mesh-howto-setup-developer-environment-sdk.md)을 설정할 수도 있습니다.

[일반적인 질문 및 문제](service-fabric-mesh-faq.md)에 대한 답변을 찾습니다.

[azure-cli-install]: /cli/azure/install-azure-cli
