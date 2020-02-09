---
title: Azure Service Fabric 메시에 대 한 일반적인 질문
description: Azure Service Fabric Mesh에 대한 일반적인 질문과 대답을 알아봅니다.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 3fe6289ad7616dec97706c2f1779a74c508a0f76
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461987"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Service Fabric Mesh에 대한 일반적인 질문

Azure Service Fabric Mesh는 개발자가 가상 머신, 스토리지 또는 네트워킹을 관리하지 않고 마이크로 서비스 애플리케이션을 배포할 수 있는 완전히 관리되는 서비스입니다. 이 문서에는 일반적인 질문에 대한 답변이 있습니다.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>문제를 보고하거나 질문을 하려면 어떻게 하나요?

질문을 하고, Microsoft 엔지니어의 답변을 받은 다음, [service-fabric-mesh-preview GitHub 리포지토리](https://aka.ms/sfmeshissues)에서 문제를 보고합니다.

## <a name="quota-and-cost"></a>할당량 및 비용

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>미리 보기에 참여하는 비용은 얼마인가요?

현재 메시 미리 보기에 응용 프로그램 또는 컨테이너를 배포 하는 데는 요금이 부과 되지 않습니다. 청구에 대 한 사용할 수 있는 경우의 업데이트를 시청 하세요. 그러나 배포 하는 리소스는 삭제 하 고 적극적으로 테스트 하는 경우에는 실행 하지 않는 것이 좋습니다.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>코어 수와 RAM의 할당량 제한이 있나요?

예. 각 구독에 대한 할당량은 다음과 같습니다.

- 응용 프로그램 수: 5
- 응용 프로그램당 코어 수: 12
- 응용 프로그램당 총 RAM: 48 GB
- 네트워크 및 수신 엔드포인트: 5
- 연결할 수 있는 Azure 볼륨: 10
- 서비스 복제본 수: 3
- 배포할 수 있는 최대 컨테이너는 4코어, 16GB RAM으로 제한됩니다.
- 최대 6코어까지 0.5코어 증분으로 부분 코어를 컨테이너에 할당할 수 있습니다.

### <a name="how-long-can-i-leave-my-application-deployed"></a>애플리케이션을 배포한 후 얼마나 오래 두어도 되나요?

현재 애플리케이션 수명은 2일로 제한되어 있습니다. 미리 보기에 할당된 체험용 코어의 사용률을 최대화하기 위한 조치입니다. 결과적으로, 주어진 배포를 연속 48시간 동안 실행할 수 있으며, 이 시간이 지나면 배포가 종료됩니다.

이 경우 Azure CLI에서 `az mesh app show` 명령을 실행하여 시스템에서 배포를 종료한 것인지 확인할 수 있습니다. `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."`가 반환되는지 확인합니다. 

예: 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

리소스 그룹을 삭제하려면 `az group delete <nameOfResourceGroup>` 명령을 사용합니다.

## <a name="deployments"></a>배포

### <a name="what-container-images-are-supported"></a>어떤 컨테이너 이미지가 지원 되나요?

Windows Fall Creators 업데이트(버전 1709) 머신에서 개발하는 경우 Windows 버전 1709 Docker 이미지만 사용할 수 있습니다.

Windows 10 2018년 4월 업데이트(버전 1803) 머신에서 개발하는 경우 Windows 버전 1709 또는 Windows 버전 1803의 Docker 이미지를 사용합니다.

다음 컨테이너 OS 이미지를 사용하여 서비스를 배포할 수 있습니다.
- Windows - windowsservercore 및 nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - 알려진 제한 사항 없음

> [!NOTE]
> Visual Studio tools for 메시는 아직 Windows Server 2019 및 1809 컨테이너에 배포 하는 기능을 지원 하지 않습니다.

### <a name="what-types-of-applications-can-i-deploy"></a>어떤 종류의 응용 프로그램을 배포할 수 있나요? 

응용 프로그램 리소스에 적용 되는 제한 사항에 맞는 컨테이너에서 실행 되는 모든 항목을 배포할 수 있습니다 (할당량에 대 한 자세한 내용은 위 참조). 사용자가 잘못 된 작업을 실행 하는 데 메시를 사용 하 고 있는 경우 (즉, 마이닝), 배포를 종료 하 고 서비스에서 구독이 실행 되지 않도록 하는 권한을 차단 목록 합니다. 특정 워크 로드 실행에 대 한 질문이 있으면 microsoft에 문의 하세요. 

## <a name="developer-experience-issues"></a>개발자 환경 문제

### <a name="dns-resolution-from-a-container-doesnt-work"></a>컨테이너의 DNS 확인이 작동하지 않음

컨테이너에서 Service Fabric DNS 서비스로 보내는 DNS 쿼리는 특정 상황에서 실패할 수 있습니다. 이 문제는 조사 중입니다. 문제를 완화하려면 다음을 수행합니다.

- 기본 컨테이너 이미지로 Windows Fall Creators 업데이트(버전 1709) 이상을 사용합니다.
- 서비스 이름만 작동 하지 않는 경우 정규화 된 이름: ServiceName. ApplicationName을 시도 합니다.
- 서비스의 Docker 파일에 `EXPOSE <port>`를 추가합니다. 여기서 port는 서비스를 노출하는 포트입니다. 예:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS가 Service Fabric 개발 클러스터 및 Mesh에서와 동일한 방식으로 작동하지 않음

로컬 개발 클러스터와 Azure Mesh에서 서비스를 다르게 참조해야 할 수도 있습니다.

로컬 개발 클러스터에서는 `{serviceName}.{applicationName}`을 사용합니다. Azure Service Fabric Mesh에서는 `{servicename}`을 사용합니다. 

Azure Mesh는 현재 애플리케이션 간의 DNS 확인을 지원하지 않습니다.

Windows 10에서 Service Fabric 개발 클러스터를 실행 하는 것과 관련 된 기타 알려진 DNS 문제는 [windows 컨테이너 디버그](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) 및 [알려진 dns 문제](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues)를 참조 하세요.

### <a name="networking"></a>네트워킹

로컬 머신에서 앱을 실행하는 동안 ServiceFabric 네트워크 NAT가 사라질 수 있습니다. 이 오류가 발생했는지 여부를 진단하려면 명령 프롬프트에서

`docker network ls`를 실행하고 `servicefabric_nat`가 나열되는지 여부를 확인합니다.  나열되지 않는 경우 `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat` 명령을 실행합니다.

이렇게 하면 앱이 로컬에 이미 배포되었으며 비정상 상태인 경우에도 문제가 해결됩니다.

### <a name="issues-running-multiple-apps"></a>여러 앱을 실행하는 문제

CPU 가용성 및 한도가 모든 애플리케이션에서 고정될 수 있습니다. 문제를 완화하려면 다음을 수행합니다.
- 5노드 클러스터를 만듭니다.
- 배포된 앱에서 서비스의 CPU 사용량을 줄입니다. 예를 들어 서비스의 service.yaml 파일에서 `cpu: 1.0`을 `cpu: 0.5`로 변경합니다.

단일 노드 클러스터에 여러 애플리케이션을 배포할 수 없습니다. 문제를 완화하려면 다음을 수행합니다.
- 로컬 클러스터에 여러 앱을 배포하는 경우 5노드 클러스터를 사용합니다.
- 현재 테스트하지 않는 앱을 제거합니다.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS 도구는 Windows 컨테이너에 대해 제한 된 지원을 제공 합니다.

Visual Studio 도구는 현재 Windows Server 1709 및 1803의 기본 OS 버전을 사용 하는 Windows 컨테이너 배포만 지원 합니다. 

## <a name="feature-gaps-and-other-known-issues"></a>기능 차이 및 기타 알려진 문제

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>내 애플리케이션을 배포한 후 애플리케이션과 관련된 네트워크 리소스에 IP 주소가 없음

IP 주소를 즉시 사용할 수 없는 알려진 문제가 있습니다. 관련 IP 주소를 보려면 몇 분 후에 네트워크 리소스의 상태를 확인하세요.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>내 애플리케이션이 올바른 네트워크/볼륨 리소스에 액세스하지 못함

애플리케이션 모델에서 네트워크 및 볼륨이 관련 리소스에 액세스할 수 있으려면 전체 리소스 ID를 사용합니다. 다음은 빠른 시작 샘플의 예입니다.

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>스케일 아웃할 때 실행 중인 컨테이너를 포함하여 내 컨테이너가 모두 영향을 받음

버그이며 수정 프로그램을 구현 중입니다.

## <a name="next-steps"></a>다음 단계

Service Fabric Mesh에 대한 자세한 내용은 [개요](service-fabric-mesh-overview.md)를 참조하세요.
