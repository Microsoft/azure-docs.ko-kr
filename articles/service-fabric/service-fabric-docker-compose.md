---
title: Azure Service Fabric Docker Compose 배포 미리 보기
description: Azure Service Fabric은 Service Fabric을 사용하여 기존 컨테이너를 보다 쉽게 조정할 수 있도록 Docker Compose 형식을 수락합니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 02/23/2018
ms.date: 04/29/2019
ms.author: v-yeche
ms.openlocfilehash: da86ed9a3e6979bd1dc05aef6ef70c7b8533a8c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60948837"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Azure Service Fabric의 Docker Compose 배포 지원(미리 보기)

Docker는 다중 컨테이너 애플리케이션을 정의하기 위해 [docker-compose.yml](https://docs.docker.com/compose) 파일을 사용합니다. Docker에 익숙한 고객이 Azure Service Fabric에서 기존 컨테이너 애플리케이션을 쉽게 조정하도록 하기 위해 플랫폼에 기본적으로 Docker Compose 배포에 대한 미리 보기 지원을 포함했습니다. Service Fabric은 `docker-compose.yml` 파일의 버전 3 이상을 수락할 수 있습니다. 

이 지원은 미리 보기로 제공되므로 Compose 지시문의 하위 집합만 지원됩니다. 예를 들어 애플리케이션 업그레이드는 지원되지 않습니다. 그러나 애플리케이션을 업그레이드하는 대신 항상 제거한 후 배포할 수 있습니다.

이 미리 보기를 사용하려면 해당하는 SDK와 함께 Azure Portal을 통해 Service Fabric 런타임 버전 5.7 이상을 사용하여 클러스터를 만듭니다. 

> [!NOTE]
> 이 기능은 미리 보기로 제공되며 프로덕션에서 지원되지 않습니다.
> 다음 예제는 런타임 버전 6.0 및 SDK 버전 2.8을 기준으로 합니다.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Service Fabric에서 Docker Compose 파일 배포

다음 명령은 다른 모든 Service Fabric 애플리케이션과 같이 모니터링하고 관리할 수 있는 Service Fabric 애플리케이션(`fabric:/TestContainerApp`이라 함)을 만듭니다. 상태 쿼리에 대해서는 지정된 애플리케이션 이름을 사용할 수 있습니다.
Service Fabric은 "DeploymentName"을 Compose 배포의 식별자로 인식합니다.

### <a name="use-powershell"></a>PowerShell 사용

PowerShell에서 다음 명령을 실행하여 docker-compose.yml 파일에서 Service Fabric Compose 배포를 만듭니다.

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` 및 `RegistryPassword`는 컨테이너 레지스트리 사용자 이름 및 암호를 가리킵니다. 배포를 완료한 후 다음 명령을 사용하여 그 상태를 확인할 수 있습니다.

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

PowerShell을 통해 Compose 배포를 삭제하려면 다음 명령을 사용합니다.

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

PowerShell을 통해 Compose 배포 업그레이드를 시작하려면 다음 명령을 사용합니다.

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

PowerShell을 통해 Compose 배포 업그레이드를 롤백하려면 다음 명령을 사용합니다.

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

업그레이드가 수락되면 다음 명령을 사용하여 업그레이드 진행률을 추적할 수 있습니다.

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI(sfctl) 사용

또는 다음 Service Fabric CLI 명령을 사용할 수 있습니다.

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

배포를 만든 후 다음 명령을 사용하여 그 상태를 확인할 수 있습니다.

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Compose 배포를 삭제하려면 다음 명령을 사용합니다.

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Compose 배포 업그레이드를 시작하려면 다음 명령을 사용합니다.

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Compose 배포 업그레이드를 롤백하려면 다음 명령을 사용합니다.

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

업그레이드가 수락되면 다음 명령을 사용하여 업그레이드 진행률을 추적할 수 있습니다.

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>지원되는 Compose 지시문

이 미리 보기에서는 다음과 같은 기본 형식을 포함하여 Compose 버전 3형식의 구성 옵션 하위 집합을 지원합니다.

* 서비스 > 배포 > 복제본
* 서비스 > 배포 > 배치 > 제약 조건
* 서비스 > 배포 > 리소스 > 제한
    * -cpu-shares
    * -memory
    * -memory-swap
* 서비스 > 명령
* 서비스 > 환경
* 서비스 > 포트
* 서비스 > 이미지
* 서비스 > 격리(Windows에만 해당)
* 서비스 > 로깅 > 드라이버
* 서비스 > 로깅 > 드라이버 > 옵션
* 볼륨 및 배포 > 볼륨

클러스터는 [Service Fabric 리소스 관리](service-fabric-resource-governance.md)에 설명된 대로 리소스 제한을 적용하도록 설정합니다. 다른 모든 Docker Compose 지시문은 이 미리 보기에서 지원되지 않습니다.

### <a name="ports-section"></a>포트 섹션

Service Fabric 서비스 수신기에서 사용될 포트 섹션에서 http 또는 https 프로토콜을 지정합니다. 이렇게 하면 역방향 프록시가 요청을 전달할 수 있는 이름 지정 서비스를 사용하여 엔드포인트 프로토콜이 올바르게 게시되도록 합니다.
* 비보안 Service Fabric 계산 서비스에 라우팅하려면 **/http**를 지정합니다. 예를 들면 **"80:80/http"** 와 같습니다.
* 보안 Service Fabric 계산 서비스에 라우팅하려면 **/http**를 지정합니다. 예를 들면 **"443:443/https"** 와 같습니다.

> [!NOTE]
> 올바른 Service Fabric 수신기 URL을 등록하기 위해 /http 및 /https 포트 섹션 구문은 Service Fabric에 특정됩니다.  프로그래밍 방식으로 Docker 계산 파일 구문의 유효성을 검사하면 유효성 검사 오류가 발생할 수 있습니다.

## <a name="servicednsname-computation"></a>ServiceDnsName 계산

Compose 파일에 지정하는 서비스 이름이 정규화된 도메인 이름(즉, 마침표 [.] 포함)인 경우 Service Fabric에서 등록된 DNS 이름은 `<ServiceName>`(마침표 포함)입니다. 정규화된 도메인 이름이 아닌 경우 애플리케이션 이름의 각 경로 세그먼트는 서비스 DNS 이름의 도메인 레이블이 됩니다. 이때 첫 번째 경로 세그먼트가 최상위 도메인 레이블이 됩니다.

예를 들어 지정된 애플리케이션 이름이 `fabric:/SampleApp/MyComposeApp`인 경우 `<ServiceName>.MyComposeApp.SampleApp`은 등록된 DNS 이름이 됩니다.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Compose 배포(인스턴스 정의) 및 Service Fabric 앱 모델(형식 정의)

docker-compose.yml 파일은 해당 속성 및 구성을 포함하는 컨테이너의 배포 가능 집합을 설명합니다.
예를 들어 파일에는 환경 변수 및 포트가 포함될 수 있습니다. 배치 제약 조건, 리소스 제한, DNS 이름과 같은 배포 매개 변수는 docker-compose.yml 파일에도 지정할 수 있습니다.

[Service Fabric 애플리케이션 모델](service-fabric-application-model.md)은 서비스 형식 및 애플리케이션 형식을 사용합니다. 여기서 동일한 형식의 여러 애플리케이션 인스턴스를 유지할 수 있습니다. 예를 들어 고객이 각자 하나의 애플리케이션 인스턴스를 사용할 수 있습니다. 이 형식 기반 모델은 런타임에 등록된 동일한 애플리케이션 유형의 여러 버전을 지원합니다.

예를 들어 고객 A에는 1.0 형식의 AppTypeA로 인스턴스화된 애플리케이션이 있고, 고객 B에는 동일한 형식 및 버전으로 인스턴스화된 다른 애플리케이션을 유지할 수 있습니다. 애플리케이션 유형은 애플리케이션 매니페스트에서 정의하고, 애플리케이션 이름 및 배포 매개 변수는 애플리케이션을 만들 때 지정합니다.

이 모델을 사용하면 유연하게 작업할 수 있지만 매니페스트 파일에서 형식이 암시되는 좀 더 간단한 인스턴스 기반 모델을 지원하려고 합니다. 이 모델에서 각 애플리케이션은 자체의 독립적인 매니페스트를 갖게 됩니다. 인스턴스 기반 배포 형식인 docker-compose.yml에 대한 지원을 추가하여 이러한 방식을 미리 검토하고 있습니다.

## <a name="next-steps"></a>다음 단계

* [Service Fabric 애플리케이션 모델](service-fabric-application-model.md)에 대해 자세히 알아보기
* [Service Fabric CLI 시작](service-fabric-cli.md)

<!-- Update_Description: wording update -->
