---
title: Azure Service Fabric에서 컨테이너 로그 보기 | Microsoft Docs
description: Service Fabric Explorer를 사용하여 실행 중인 Service Fabric 컨테이너 서비스에 대한 컨테이너 로그를 보는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: aljo
ms.openlocfilehash: 0408010a49b8ec83aa02c74887139f663788ad80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881489"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric 컨테이너 서비스에 대한 로그 보기
Azure Service Fabric은 컨테이너 오케스트레이터이며 [Windows 및 Linux 컨테이너](service-fabric-containers-overview.md)를 모두 지원합니다.  이 문서에서는 문제를 진단하고 해결할 수 있도록 실행 중인 컨테이너 서비스 또는 비활성 컨테이너의 컨테이너 로그를 보는 방법을 설명합니다.

## <a name="access-the-logs-of-a-running-container"></a>실행 중인 컨테이너의 로그에 액세스
컨테이너 로그는 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)를 사용하여 액세스할 수 있습니다.  웹 브라우저에서 [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer)로 이동하여 클러스터의 관리 엔드포인트에서 Service Fabric Explorer를 엽니다.  

컨테이너 로그는 컨테이너 서비스 인스턴스가 실행 중인 클러스터 노드에 있습니다. 예를 들어, [Linux Voting 샘플 애플리케이션](service-fabric-quickstart-containers-linux.md)의 웹 프런트 엔드 컨테이너 로그를 가져옵니다. 트리 보기에서 **Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront**를 확장합니다.  그런 다음 파티션(이 예제의 d1aa737e-f22a-e347-be16-eec90be24bc1)을 확장하고 해당 컨테이너가 클러스터 노드 *_lnxvm_0*에서 실행되고 있는지 확인합니다.

트리 보기에서 **Nodes**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Code Packages**>**code**를 확장하여 *_lnxvm_0* 노드에서 코드 패키지를 찾습니다.  그런 후 **컨테이너 로그** 옵션을 선택하여 컨테이너 로그를 표시합니다.

![서비스 패브릭 플랫폼][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>비활성 또는 손상된 컨테이너의 로그에 액세스
v6.2부터 [REST API](/rest/api/servicefabric/sfclient-index) 또는 [SFCTL(Service Fabric CLI)](service-fabric-cli.md) 명령을 사용하여 비활성 또는 손상된 컨테이너에 대한 로그를 가져올 수도 있습니다.

### <a name="set-container-retention-policy"></a>컨테이너 보존 정책 설정
컨테이너 시작 오류 진단을 지원하기 위해 Service Fabric(버전 6.1 이상)은 종료되었거나 시작하지 못한 컨테이너를 보존하도록 지원합니다. 이 정책은 다음 코드 조각과 같이 **ApplicationManifest.xml** 파일에서 설정할 수 있습니다.
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

**ContainersRetentionCount** 설정은 실패할 때 유지할 컨테이너의 수를 지정합니다. 음수 값을 지정하면 실패한 모든 컨테이너가 유지됩니다. **ContainersRetentionCount** 특성을 지정하지 않으면 컨테이너는 유지되지 않습니다. 또한 **ContainersRetentionCount** 특성은 애플리케이션 매개 변수도 지원하므로 사용자는 테스트 및 프로덕션 클러스터에 대해 다른 값을 지정할 수 있습니다. 이 기능을 사용하여 컨테이너 서비스가 다른 노드로 이동하지 않도록 방지하려면 배치 제약 조건을 사용하여 특정 노드에 대한 컨테이너 서비스를 대상으로 지정합니다. 이 기능을 사용하여 유지된 컨테이너는 수동으로 제거해야 합니다.

설정 **RunInteractive**는 Docker의 `--interactive` 및 `tty` [플래그](https://docs.docker.com/engine/reference/commandline/run/#options)에 해당합니다. 매니페스트 파일에서 이 설정을 true로 지정하면 이러한 플래그가 컨테이너를 시작하는 데 사용됩니다.  

### <a name="rest"></a>REST (영문)
[노드에 배포된 컨테이너 로그 가져오기](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) 작업을 사용하여 손상된 컨테이너에 대한 로그를 가져옵니다. 컨테이너가 실행되었던 노드의 이름, 애플리케이션 이름, 서비스 매니페스트 이름 및 코드 패키지 이름을 지정합니다.  `&Previous=true`를 지정합니다. 응답은 코드 패키지 인스턴스의 비활성 컨테이너에 대한 컨테이너 로그를 포함합니다.

요청 URI의 형식은 다음과 같습니다.

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

요청 예제:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 응답 본문:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric(SFCTL)
[sfctl service get-container-logs](service-fabric-sfctl-service.md) 명령을 사용하여 손상된 컨테이너에 대한 로그를 가져옵니다.  컨테이너가 실행되었던 노드의 이름, 애플리케이션 이름, 서비스 매니페스트 이름 및 코드 패키지 이름을 지정합니다. `--previous` 플래그를 지정합니다.  응답은 코드 패키지 인스턴스의 비활성 컨테이너에 대한 컨테이너 로그를 포함합니다.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
응답:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>다음 단계
- [Linux 컨테이너 애플리케이션 만들기 자습서](service-fabric-tutorial-create-container-images.md)를 진행합니다.
- [Service Fabric 및 컨테이너](service-fabric-containers-overview.md)에 대해 자세알아히 봅니다.

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
