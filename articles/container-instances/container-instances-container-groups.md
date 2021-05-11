---
title: 컨테이너 그룹 소개
description: 수명 주기와 CPU, 스토리지, 네트워크 등의 리소스를 공유하는 인스턴스 컬렉션인 Azure Container Instances의 컨테이너 그룹에 대해 알아봅니다.
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: a2cb3eac5baa5b1035749d28b9fb99bbb45b9ee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790890"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances의 컨테이너 그룹

Azure Container Instances의 최상위 리소스는 *컨테이너 그룹* 입니다. 이 문서에서는 컨테이너 그룹의 정의와 이들이 지원하는 시나리오 유형에 대해 설명합니다.

## <a name="what-is-a-container-group"></a>컨테이너 그룹이란?

컨테이너 그룹은 같은 호스트 컴퓨터에서 예약되어 있는 컨테이너 컬렉션입니다. 컨테이너 그룹의 컨테이너는 수명 주기, 리소스, 로컬 네트워크, 스토리지 볼륨을 공유합니다. [Kubernetes][kubernetes-pod]의 *Pod* 와 개념이 비슷합니다.

다음 다이어그램은 여러 컨테이너가 포함된 컨테이너 그룹의 예를 보여줍니다.

![컨테이너 그룹 다이어그램][container-groups-example]

이 예제 컨테이너 그룹은 다음과 같습니다.

* 단일 호스트 컴퓨터에서 예약됩니다.
* DNS 이름 레이블이 할당됩니다.
* 1개의 노출 포트가 있는 단일 공용 IP 주소를 노출합니다.
* 두 개의 컨테이너로 구성됩니다. 하나의 컨테이너는 포트 80을 수신하고, 다른 컨테이너는 포트 5000을 수신합니다.
* 볼륨 탑재로 2개의 Azure 파일 공유가 포함되어 있으며, 각 컨테이너는 공유 중 하나를 로컬에서 탑재합니다.

> [!NOTE]
> 다중 컨테이너 그룹은 현재 Linux 컨테이너만 지원합니다. Windows 컨테이너의 경우 Azure Container Instances는 단일 컨테이너 인스턴스 배포만 지원합니다. Windows 컨테이너에서 모든 기능을 제공하기 위해 노력하고 있습니다. 그 동안 서비스 [개요](container-instances-overview.md#linux-and-windows-containers)에서 현재 플랫폼의 차이점을 확인할 수 있습니다.

## <a name="deployment"></a>배포

다중 컨테이너 그룹을 배포하는 두 가지 일반적인 방법은 [Resource Manager 템플릿][resource-manager template] 또는 [YAML 파일][yaml-file]을 사용하는 것입니다. 컨테이너 인스턴스를 배포할 때 추가 Azure 서비스 리소스(예: [Azure Files 공유][azure-files])를 배포해야 하는 경우 Resource Manager 템플릿을 사용하는 것이 좋습니다. YAML 형식이 좀 더 간결하기 때문에 배포에 컨테이너 인스턴스만 포함된 경우에는 YAML 파일을 사용하는 것이 좋습니다. 설정할 수 있는 속성에 대한 자세한 내용은 [Resource Manager 템플릿 참조](/azure/templates/microsoft.containerinstance/containergroups) 또는 [YAML 참조](container-instances-reference-yaml.md) 설명서를 참조하세요.

컨테이너 그룹의 구성을 유지하려면 Azure CLI 명령 [az container export][az-container-export]를 사용하여 해당 구성을 YAML 파일로 내보낼 수 있습니다. 내보내기를 사용하면 컨테이너 그룹 구성을 “코드로 구성”의 버전 제어에 저장할 수 있습니다. 또는 YAML에서 새 구성을 개발할 때 내보낸 파일을 시작점으로 사용할 수 있습니다.



## <a name="resource-allocation"></a>리소스 할당

Azure Container Instances는 인스턴스의 [리소스 요청][resource-requests]을 그룹에 추가하여 CPU, 메모리, 선택적으로 [GPU][gpus](미리 보기) 등의 리소스를 다중 컨테이너 그룹에 할당합니다. CPU 리소스를 예로 들면, 각각 CPU 1개를 요청하는 컨테이너 인스턴스 2개로 만든 컨테이너 그룹에는 CPU 2개가 할당됩니다.

### <a name="resource-usage-by-container-instances"></a>컨테이너 인스턴스별 리소스 사용량

그룹의 각 컨테이너 인스턴스에는 해당 리소스 요청에서 지정된 리소스가 할당됩니다. 그러나 선택적 [리소스 한도][resource-limits] 속성을 구성하면 그룹의 컨테이너 인스턴스에서 사용되는 최대 리소스가 달라질 수 있습니다. 컨테이너 인스턴스의 리소스 한도는 필수 [리소스 요청][resource-requests] 속성보다 크거나 같아야 합니다.

* 리소스 한도를 지정하지 않은 경우 컨테이너 인스턴스의 최대 리소스 사용량은 해당 리소스 요청과 동일합니다.

* 컨테이너 인스턴스의 한도를 지정한 경우 인스턴스의 최대 사용량은 설정된 한도까지 요청보다 클 수 있습니다. 따라서 그룹에 속한 다른 컨테이너 인스턴스의 리소스 사용량이 줄어들 수 있습니다. 컨테이너 인스턴스에 대해 설정할 수 있는 최대 리소스 한도는 그룹에 할당된 총 리소스입니다.
    
예를 들어 각각 CPU 1개를 요청하는 컨테이너 인스턴스 2개로 이루어진 그룹에서 컨테이너 중 하나가 다른 컨테이너보다 더 많은 CPU가 필요한 워크로드를 실행할 수 있습니다.

이 시나리오에서는 해당 컨테이너 인스턴스의 리소스 한도를 최대 2개의 CPU로 설정할 수 있습니다. 이 구성을 통해 컨테이너 인스턴스는 사용 가능한 경우 최대 2개의 CPU를 사용할 수 있습니다.

> [!NOTE]
> 서비스의 기본 인프라는 적은 양의 컨테이너 그룹 리소스를 사용합니다. 컨테이너는 그룹에 할당된 리소스를 모두는 아니라도 대부분 액세스할 수 있습니다. 이런 이유로, 그룹의 컨테이너에 대한 리소스를 요청할 때는 작은 리소스 버퍼를 계획합니다.

### <a name="minimum-and-maximum-allocation"></a>최소 및 최대 할당

* **최소한** CPU 1개와 메모리 1GB를 컨테이너 그룹에 할당합니다. 그룹 내의 개별 컨테이너 인스턴스는 CPU 1개와 메모리 1GB 미만으로 프로비저닝할 수 있습니다. 

* 컨테이너 그룹의 **최대** 리소스는 배포 지역의 Azure Container Instances에 대한 [리소스 가용성][region-availability]을 참조하세요.

## <a name="networking"></a>네트워킹

컨테이너 그룹은 외부 연결 IP 주소, 해당 IP 주소에 있는 하나 이상의 포트, FQDN(정규화된 도메인 이름)이 포함된 DNS 레이블을 공유할 수 있습니다. 외부 클라이언트가 그룹 내 컨테이너에 도달하게 지원하려면 IP 주소와 컨테이너에서 해당 포트를 공개해야 합니다. 컨테이너 그룹을 삭제하면 컨테이너 그룹의 IP 주소와 FQDN이 해제됩니다. 

컨테이너 그룹 내에서 컨테이너 인스턴스는 임의 포트에서 localhost를 통해 서로 연결할 수 있습니다. 단, 해당 포트가 그룹의 IP 주소에 외부적으로 또는 컨테이너에서 노출되지는 않습니다.

필요에 따라 [Azure 가상 네트워크][virtual-network]에 컨테이너 그룹을 배포하여 컨테이너가 가상 네트워크의 다른 리소스와 안전하게 통신할 수 있도록 합니다.

## <a name="storage"></a>스토리지

컨테이너 그룹 내에서 탑재할 외부 볼륨을 지정할 수 있습니다. 지원되는 볼륨은 다음과 같습니다.
* [Azure 파일 공유][azure-files]
* [비밀][secret]
* [빈 디렉터리][empty-directory]
* [복제된 Git 리포지토리][volume-gitrepo]

해당 볼륨을 그룹의 개별 컨테이너 내에 있는 특정 경로에 매핑할 수 있습니다. 

## <a name="common-scenarios"></a>일반적인 시나리오

다중 컨테이너 그룹은 단일 기능 작업을 적은 수의 컨테이너 이미지로 나누려는 경우에 유용합니다. 이러한 이미지는 다른 팀에서 제공될 수 있으며 별도의 리소스 요구 사항을 가질 수 있습니다.

사용 예는 다음과 같습니다.

* 웹 애플리케이션을 처리하는 컨테이너와 원본 제어에서 최신 콘텐츠를 풀링하는 컨테이너
* 애플리케이션 컨테이너 및 로깅 컨테이너. 로깅 컨테이너는 주 애플리케이션에서 출력한 로그 및 메트릭을 수집하여 장기 스토리지로 기록합니다.
* 애플리케이션 컨테이너 및 모니터링 컨테이너. 모니터링 컨테이너는 애플리케이션이 올바르게 실행 중이고 응답하고 있는지 확인하기 위해 애플리케이션에 정기적으로 요청을 보내고, 그렇지 않은 경우 경고를 올립니다.
* 프런트 엔드 컨테이너 및 백 엔드 컨테이너. 프런트 엔드는 웹 애플리케이션에 서비스를 제공할 수 있고, 백 엔드는 서비스를 실행하여 데이터를 검색할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 통해 다중 컨테이너 그룹 배포 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [컨테이너 그룹 배포][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az_container_export
