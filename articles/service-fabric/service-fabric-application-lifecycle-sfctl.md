---
title: Azure Service Fabric CLI(sfctl)를 사용하여 Azure Service Fabric 애플리케이션 관리
description: Azure Service Fabric CLI를 사용하여 Azure Service Fabric 클러스터에서 애플리케이션을 배포하고 제거하는 방법을 알아봅니다.
services: service-fabric
author: rockboyfor
manager: digimobile
ms.service: service-fabric
ms.topic: conceptual
origin.date: 07/31/2018
ms.date: 09/10/2018
ms.author: v-yeche
ms.openlocfilehash: 9b0f785a6a43f984708645084a8a8036326d3d24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621380"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI(sfctl)를 사용하여 Azure Service Fabric 애플리케이션 관리

Azure Service Fabric 클러스터에서 실행 중인 애플리케이션을 만들고 삭제하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* Service Fabric CLI를 설치합니다. 그런 다음, Service Fabric 클러스터를 선택합니다. 자세한 내용은 [Service Fabric CLI 시작](service-fabric-cli.md)을 참조하세요.

* 배포를 위해 Service Fabric 애플리케이션 패키지를 준비합니다. 애플리케이션 작성 및 패키지 방법에 대한 자세한 정보는 [Service Fabric 애플리케이션 모델](service-fabric-application-model.md)에서 찾을 수 있습니다.

## <a name="overview"></a>개요

새 애플리케이션을 배포하려면 다음 단계를 완료합니다.

1. Service Fabric 이미지 저장소에 애플리케이션 패키지를 업로드합니다.
2. 애플리케이션 유형을 프로비전합니다.
3. 이미지 저장소 콘텐츠를 삭제합니다.
4. 애플리케이션을 지정하고 만듭니다.
5. 서비스를 지정하고 만듭니다.

기존 애플리케이션을 제거하려면 다음이 단계를 완료합니다.

1. 애플리케이션을 삭제합니다.
2. 관련된 애플리케이션 유형의 프로비전을 해제합니다.

## <a name="deploy-a-new-application"></a>새 애플리케이션 배포

새 애플리케이션을 배포하려면 다음 작업을 완료합니다.

### <a name="upload-a-new-application-package-to-the-image-store"></a>이미지 저장소에 새 애플리케이션 패키지 업로드

애플리케이션을 만들기 전에 Service Fabric 이미지 저장소에 애플리케이션 패키지를 업로드합니다.

예를 들어, 애플리케이션 패키지가 `app_package_dir` 디렉터리에 있는 경우 다음 명령을 사용하여 디렉터리를 업로드합니다.

```azurecli
sfctl application upload --path ~/app_package_dir
```

대형 애플리케이션 패키지의 경우 `--show-progress` 옵션을 지정하여 업로드 진행률을 표시할 수 있습니다.

### <a name="provision-the-application-type"></a>애플리케이션 유형 프로비전

업로드가 완료되면 애플리케이션을 프로비전합니다. 애플리케이션을 프로비전하려면 다음 명령을 사용합니다.

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

`application-type-build-path`의 값은 애플리케이션 패키지를 업로드하는 디렉터리의 이름입니다.

### <a name="delete-the-application-package"></a>애플리케이션 패키지 삭제

애플리케이션이 성공적으로 등록된 후에는 애플리케이션 패키지를 제거하는 것이 좋습니다.  이미지 저장소에서 애플리케이션 패키지를 삭제하면 시스템 리소스가 해제됩니다.  사용되지 않는 애플리케이션 패키지를 그대로 두면 디스크 스토리지를 소비하고 애플리케이션 성능 문제로 이어집니다. 

이미지 저장소에서 애플리케이션 패키지를 삭제하려면 다음 명령을 사용합니다.

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path`는 애플리케이션을 만들 때 업로드한 디렉터리의 이름이어야 합니다.

### <a name="create-an-application-from-an-application-type"></a>애플리케이션 유형에서 애플리케이션 만들기

애플리케이션을 프로비전한 후에는 다음 명령을 사용하여 애플리케이션 이름을 지정하고 만듭니다.

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`은 애플리케이션 인스턴스에 사용하려는 이름입니다. 이전에 프로비전된 애플리케이션 매니페스트에서 추가 매개 변수를 얻을 수 있습니다.

애플리케이션 이름은 `fabric:/` 접두사로 시작해야 합니다.

### <a name="create-services-for-the-new-application"></a>새 애플리케이션에 대한 서비스 만들기

애플리케이션이 만들어진 후에는 애플리케이션에서 서비스를 만듭니다. 다음 예의 경우, 애플리케이션에서 새 상태 비저장 서비스를 만듭니다. 애플리케이션에서 만들 수 있는 서비스는 이전에 프로비전된 애플리케이션 패키지 내의 서비스 매니페스트에 정의됩니다.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>애플리케이션 배포 및 상태 확인

모든 것이 정상 상태인지 확인하려면 다음 상태 명령을 사용합니다.

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

서비스가 정상 상태인지 확인하려면 유사한 명령을 사용하여 서비스와 애플리케이션의 상태를 검색합니다.

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

정상 서비스 및 애플리케이션은 `HealthState` 값이 `Ok`입니다.

## <a name="remove-an-existing-application"></a>기존 애플리케이션 제거

애플리케이션을 제거하려면 다음 작업을 완료합니다.

### <a name="delete-the-application"></a>애플리케이션 삭제

애플리케이션을 삭제하려면 다음 명령을 사용합니다.

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>애플리케이션 유형 프로비전 해제

애플리케이션을 삭제한 후에는 더 이상 필요 없는 경우 애플리케이션 유형의 프로비전을 해제할 수 있습니다. 애플리케이션 유형을 프로비전 해제하려면 다음 명령을 사용합니다.

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

유형 이름 및 유형 버전은 이전에 프로비전된 애플리케이션 매니페스트의 이름 및 버전과 일치해야 합니다.

## <a name="upgrade-application"></a>애플리케이션 업그레이드

애플리케이션을 만든 후 동일한 단계 모음을 반복하여 애플리케이션의 두 번째 버전을 프로비전할 수 있습니다. 그런 다음, Service Fabric 애플리케이션 업그레이드를 사용하여 애플리케이션의 두 번째 버전 실행으로 전환할 수 있습니다. 자세한 내용은 [Service Fabric 애플리케이션 업그레이드](service-fabric-application-upgrade.md)에 대한 설명서를 참조하세요.

업그레이드를 수행하려면 먼저 이전과 동일한 명령을 사용하여 애플리케이션의 다음 버전을 프로비전합니다.

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

모니터링되는 자동 업그레이드를 수행하고 다음 명령을 실행하여 업그레이드를 시작하는 것이 좋습니다.

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

업그레이드를 수행하면 지정된 설정으로 기존 매개 변수가 재정의됩니다. 필요한 경우 애플리케이션 매개 변수를 업그레이드 명령에 대한 인수로 전달해야 합니다. 애플리케이션 매개 변수는 JSON 개체로 암호화되어야 합니다.

이전에 지정한 모든 매개 변수를 검색하려면 `sfctl application info` 명령을 사용할 수 있습니다.

애플리케이션 업그레이드가 진행 중일 때 `sfctl application upgrade-status` 명령을 사용하여 상태를 검색할 수 있습니다.

마지막으로 진행 중인 업그레이드를 취소해야 하는 경우 `sfctl application upgrade-rollback`을 사용하여 업그레이드를 롤백할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Service Fabric CLI 기본 사항](service-fabric-cli.md)
* [Linux에서 Service Fabric 시작](service-fabric-get-started-linux.md)
* [Service Fabric 애플리케이션 업그레이드 시작](service-fabric-application-upgrade.md)

<!--Update_Description: update meta properties -->