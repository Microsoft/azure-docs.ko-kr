---
title: 레지스트리 지역 복제
description: 레지스트리가 다중 마스터 지역 복제본을 사용 하 여 여러 지역을 제공할 수 있도록 하는 지리적 복제 Azure container registry 만들기 및 관리를 시작 합니다.
author: stevelas
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: d238de30e458261a11c941c03ac127c732ca8d3d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456452"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure Container Registry의 지리적 복제

로컬 상태나 핫 백업이 필요한 기업은 둘 이상의 Azure 지역에서 서비스를 실행하도록 선택할 수 있습니다. 이미지가 실행되는 각 지역에 컨테이너 레지스트리를 배치하면 네트워크와 가까운 곳에서 작업하여 이미지 레이어를 빠르고 안정적으로 전송할 수 있습니다. 지리적 복제를 사용하면 Azure Container Registry가 단일 레지스트리로 기능하여 다중 마스터 지역 레지스트리가 있는 둘 이상의 지역에 서비스를 제공할 수 있습니다. 

지리적 복제된 레지스트리는 다음과 같은 이점을 제공합니다.

* 둘 이상의 지역에서 단일 레지스트리/이미지/태그 이름 사용 가능
* 지역별 배포 환경에서 네트워크와 가까운 곳에 위치한 레지스트리에 액세스 가능
* 컨테이너 호스트와 동일한 지역에 있는 복제된 로컬 레지스트리에서 이미지를 가져오므로 추가 송신 요금이 부과되지 않음
* 둘 이상의 지역에서 레지스트리를 단일하게 관리

> [!NOTE]
> 둘 이상의 Azure 컨테이너 레지스트리에서 컨테이너 이미지 복사본을 유지해야 하는 경우 Azure Container Registry가 [이미지 가져오기](container-registry-import-images.md)도 지원합니다. 예를 들어 Docker 명령을 사용할 필요 없이 DevOps 워크플로에서 개발 레지스트리의 이미지를 프로덕션 레지스트리로 가져올 수 있습니다.
>

## <a name="example-use-case"></a>사용 사례
Contoso는 미국, 캐나다, 유럽에서 공개 웹 사이트를 운영하고 있습니다. Contoso는 미국, 캐나다, 유럽 시장에 네트워크와 가까운 곳에 위치한 로컬 콘텐츠를 제공하기 위해 미국 서부, 미국 동부, 캐나다 중부 및 유럽 서부에서 [Azure Kubernetes Service](/azure/aks/)(AKS) 클러스터를 실행합니다. Docker 이미지로서 배포된 웹 사이트 애플리케이션은 모든 지역에서 동일한 코드와 이미지를 사용합니다. 해당 지역의 로컬 콘텐츠는 지역별로 고유하게 프로비전된 데이터베이스에서 가져옵니다. 각 지역별 배포는 로컬 데이터베이스와 리소스에 대해 고유한 구성을 갖습니다.

워싱턴주 시애틀에 있는 개발팀은 미국 서부 데이터 센터를 이용합니다.

![복수의 레지스트리에 밀어넣기](media/container-registry-geo-replication/before-geo-replicate.png)<br />*복수의 레지스트리에 밀어넣기*

Contoso가 지리적 복제 기능을 시용하기 전에는 미국 서부 소재의 미국 기반 레지스트리와 추가로 서유럽 소재의 레지스트리가 있었습니다. 개발팀은 이렇게 여러 지역에 서비스를 제공하기 위해 두 개의 레지스트리로 이미지를 밀어넣었습니다.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![복수의 레지스트리에서 가져오기](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*복수의 레지스트리에서 가져오기*

둘 이상의 레지스트리가 있는 경우 다음과 같은 문제가 발생할 수 있습니다.

* 미국 동부, 미국 서부, 캐나다 중부 클러스터는 모두 미국 서부 레지스트리에서 콘텐츠를 가져왔는데, 이때 각 원격 컨테이너 호스트가 미국 서부 데이터 센서에서 이미지를 가져와야 했기 때문에 송신 요금이 발생했습니다.
* 개발팀은 미국 서부 레지스트리와 서유럽 레지스트리로 이미지를 밀어넣어야 했습니다.
* 개발팀은 로컬 레지스트리를 참조하는 이미지 이름을 사용하여 각 지역 배포를 구성하고 유지 관리해야 했습니다.
* 레지스트리 액세스를 지역별로 구성해야 했습니다.

## <a name="benefits-of-geo-replication"></a>지리적 복제의 이점

![지리적 복제된 레지스트리에서 가져오기](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Azure Container Registry의 지리적 복제 기능을 사용하면 다음과 같은 이점을 누릴 수 있습니다.

* 모든 지역에서 단일 레지스트리 관리: `contoso.azurecr.io`
* 모든 지역에서 동일한 이미지 URL을 사용하므로 모든 이미지 배포에 대해 단일 구성 관리 가능: `contoso.azurecr.io/public/products/web:1.2`
* 단일 레지스트리로 푸시하면 ACR이 지역 복제를 관리합니다. 특정 복제본의 이벤트를 알리도록 지역 [웹후크](container-registry-webhook.md)를 구성할 수 있습니다.

## <a name="configure-geo-replication"></a>지역에서 복제 구성

지도에서 해당 지역을 클릭하여 간편하게 지리적 복제를 구성할 수 있습니다. Azure CLI에서 [az acr replication](/cli/azure/acr/replication) 명령을 비롯 한 도구를 사용 하 여 지역에서 복제를 관리 하거나 [Azure Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)사용 하 여 지역에서 복제를 위해 사용 하도록 설정 된 레지스트리를 배포할 수도 있습니다.

지리적 복제는 [Premium 레지스트리](container-registry-skus.md) 전용 기능입니다. Basic 및 Standard 레지스트리를 사용 중인 경우, [Azure Portal](https://portal.azure.com)에서 Premium으로 변경할 수 있습니다.

![Azure Portal에서 SKU 전환하기](media/container-registry-skus/update-registry-sku.png)

프리미엄 레지스트리의 지리적 복제를 구성하려면 Azure Portal( https://portal.azure.com )에 로그인합니다.

Azure Container Registry로 이동하여 **복제**:

![Azure Portal 컨테이너 레지스트리 UI의 복제를 선택합니다.](media/container-registry-geo-replication/registry-services.png)

기존 Azure 지역이 모두 표시된 지도가 나타납니다.

 ![Azure Portal의 지역 지도](media/container-registry-geo-replication/registry-geo-map.png)

* 파란색 육각형은 기존 복제본을 나타냅니다.
* 녹색 육각형은 가능한 복제 지역을 나타냅니다.
* 회색 육각형은 복제를 사용할 수 없는 Azure 지역을 나타냅니다.

복제본을 구성하려면 녹색 육각형을 선택하고 **만들기**를 선택합니다.

 ![Azure Portal에서 복제 UI 만들기](media/container-registry-geo-replication/create-replication.png)

추가로 복제본을 구성하려면 다른 지역의 녹색 육각형을 선택하고 **만들기**를 클릭합니다.

ACR이 구성된 복제본 사이의 이미지 동기화를 시작합니다. 동기화가 완료되면 포털에 *준비*라고 표시됩니다. 포털의 복제본 상태는 자동으로 업데이트되지 않습니다. 업데이트된 상태를 보려면 새로 고침 단추를 클릭합니다.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>지역 복제 레지스트리 사용 시 고려 사항

* 지역 복제 레지스트리의 각 Azure 지역은 일단 설정되면 서로 독립적입니다. 지역 복제된 각 Azure 지역에는 Azure Container Registry SLA가 적용됩니다.
* 지역 복제된 레지스트리의 이미지를 푸시 또는 풀하면 백그라운드의 Azure Traffic Manager는 가장 가까운 레지스트리에 요청을 보냅니다.
* 가장 가까운 Azure 지역에 이미지 또는 태그 업데이트를 푸시한 후 Azure Container Registry가 매니페스트 및 레이어를 사용자가 옵트인한 나머지 Azure 지역에 복제할 때까지 어느 정도 시간이 걸립니다. 큰 이미지는 작은 이미지보다 복제 시간이 오래 걸립니다. 이미지 및 태그는 최종 일관성 모델을 사용하여 복제 지역에서 동기화됩니다.
* 지역에서 복제 된 푸시 업데이트에 종속 되는 워크플로를 관리 하려면 푸시 이벤트에 응답 하도록 [웹 후크](container-registry-webhook.md) 를 구성 하는 것이 좋습니다. 지역 복제된 Azure 지역에서 완료되는 푸시 이벤트를 추적하도록 지역 복제된 레지스트리 내부에 지역별 webhook를 설정할 수 있습니다.

## <a name="delete-a-replica"></a>복제본 삭제

레지스트리에 대한 복제본을 구성한 후에는 언제 든 지 더 이상 필요 하지 않은 복제본을 삭제할 수 있습니다. Azure Portal 또는 Azure CLI의 [az acr replication delete](/cli/azure/acr/replication#az-acr-replication-delete) 명령과 같은 기타 도구를 사용 하 여 복제본을 삭제 합니다.

Azure Portal에서 복제본을 삭제 하려면 다음을 수행 합니다.

1. Azure Container Registry로 이동 하 고 **복제**를 선택 합니다.
1. 복제본의 이름을 선택 하 고 **삭제**를 선택 합니다. 복제본을 삭제할 것인지 확인 합니다.

> [!NOTE]
> 레지스트리의 *홈 지역* , 즉 레지스트리를 만든 위치에 있는 레지스트리 복제본은 삭제할 수 없습니다. 레지스트리 자체를 삭제 하 여 홈 복제본만 삭제할 수 있습니다.

## <a name="geo-replication-pricing"></a>지역에서 복제 가격 책정

지리적 복제 기능은 Azure Container Registry의 [Premium SKU](container-registry-skus.md) 기능입니다. 레지스트리를 원하는 지역으로 복제하면 각 지역별로 Premium 레지스트리 요금이 발생합니다.

앞의 예에서 Contoso는 미국 동부, 캐나다 중부, 서유럽에 복제본을 추가하여 두 개의 레지스트리를 하나로 통합했습니다. Contoso는 지금부터 매월 4배의 프리미엄 요금을 지불하게 되며, 구성 또는 관리에 추가로 부과되는 요금은 없습니다. 각 지역은 이제 로컬에서 이미지를 가져오기 때문에 성능과 안정성이 개선되며, 미국 서부에서 캐나다와 미국 동부로 네트워크 송신 요금이 발생하지 않습니다.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>지역에서 복제된 레지스트리를 사용하여 푸시 작업 문제 해결
 
지리적으로 복제된 레지스트리에 이미지를 푸시하는 Docker 클라이언트는 모든 이미지 레이어와 해당 매니페스트를 단일 복제 지역에 푸시할 수 없습니다. 이 문제는 Azure Traffic Manager가 레지스트리 요청을 네트워크에서 가장 가까운 복제 레지스트리로 라우팅하기 때문에 발생할 수 있습니다. 레지스트리에 두 개의 *인접* 복제 지역이 있는 경우 이미지 레이어와 매니페스트를 두 사이트에 배포할 수 있으며 매니페스트의 유효성을 검사할 때 푸시 작업이 실패합니다. 이 문제는 일부 Linux 호스트에서 레지스트리의 DNS 이름이 확인되기 때문에 발생합니다. 클라이언트 쪽 DNS 캐시를 제공하는 Windows에서는 이 문제가 발생하지 않습니다.
 
이 문제가 발생하는 경우 한 가지 해결 방법은 Linux 호스트에서 `dnsmasq`와 같은 클라이언트 쪽 DNS 캐시를 적용하는 것입니다. 이렇게 하면 레지스트리의 이름이 일관되게 확인됩니다. Azure에서 Linux VM을 사용하여 레지스트리에 푸시하는 경우 [Azure의 Linux 가상 머신에 대한 DNS 이름 확인 옵션](../virtual-machines/linux/azure-dns.md)의 옵션을 참조하세요.

이미지를 푸시할 때 가장 가까운 복제본에 대한 DNS 확인을 최적화하려면 Azure 외부에서 작업할 때 푸시 작업의 원본과 동일한 Azure 지역 또는 가장 가까운 지역의 지역 복제 레지스트리를 구성합니다.

## <a name="next-steps"></a>다음 단계

총 3부로 구성된 자습서 시리즈 [Azure Container Registry의 지리적 복제](container-registry-tutorial-prepare-registry.md)를 확인하세요. 직접 지리적 복제된 레지스트리를 만들어 보고, 컨테이너를 빌드하고, `docker push` 명령 하나로 여러 지역별 Web Apps for Containers 인스턴스로 배포해 볼 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Container Registry의 지리적 복제](container-registry-tutorial-prepare-registry.md)
