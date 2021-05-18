---
title: Azure Kubernetes Service에 연결 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL - 단일 서버와 AKS(Azure Kubernetes Service)를 연결하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b7da2fcc1310f03f894e048089658f25be3a149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708852"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Azure Kubernetes Service 및 Azure Database for PostgreSQL - 단일 서버 연결

AKS(Azure Kubernetes Service)는 Azure에서 사용할 수 있는 관리형 Kubernetes 클러스터를 제공합니다. AKS 및 Azure Database for PostgreSQL을 함께 사용하여 애플리케이션을 만드는 경우 고려할 몇 가지 옵션은 다음과 같습니다.


## <a name="accelerated-networking"></a>가속된 네트워킹
AKS 클러스터에서 가속 네트워킹이 활성화된 기본 VM을 사용합니다. VM에서 가속 네트워킹을 사용하도록 설정하면 VM의 대기 시간, 지터 및 CPU 사용률이 감소됩니다. 가속 네트워킹의 작동 방식, 지원되는 OS 버전 및 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md)에 대해 지원되는 VM 인스턴스에 대해 자세히 알아봅니다.

2018년 11월부터 AKS는 지원되는 해당 VM 인스턴스에서 가속 네트워킹을 지원합니다. 이러한 VM을 사용하는 새 AKS 클러스터에서는 가속 네트워킹이 기본적으로 사용하도록 설정됩니다.

AKS 클러스터에 가속 네트워킹이 있는지 여부를 확인할 수 있습니다.
1. Azure Portal로 이동하고 AKS 클러스터를 선택합니다.
2. 속성 탭을 선택합니다.
3. **인프라 리소스 그룹** 의 이름을 복사합니다.
4. 포털 검색 표시줄을 사용하여 인프라 리소스 그룹을 엽니다.
5. 해당 리소스 그룹의 VM을 선택합니다.
6. VM의 **네트워킹** 탭으로 이동합니다.
7. **가속 네트워킹** 이 ‘사용’하도록 설정되었는지 확인합니다.

또는 Azure CLI를 통해 다음의 두 명령을 실행합니다.
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
이 명령의 출력은 네트워크 인터페이스를 포함하는 생성된 리소스 그룹(AKS에서 작성됨)입니다. "NodeResourceGroup" 이름을 가져와 다음 명령에서 사용합니다. **EnableAcceleratedNetworking** 은 true 또는 false가 됩니다.
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="connection-pooling"></a>연결 풀링
연결 풀러는 데이터베이스에 대해 새 연결을 만들고 닫는 것과 관련된 시간과 비용을 최소화합니다. 풀은 다시 사용할 수 있는 연결의 컬렉션입니다. 

PostgreSQL에서 사용할 수 있는 여러 연결 풀러가 있습니다. 이중 하나가 [PgBouncer](https://pgbouncer.github.io/)입니다. Microsoft Container Registry에서 Microsoft는 AKS에서 Azure Database for PostgreSQL까지 연결을 풀링하기 위해 사이드카에서 사용할 수 있는 컨테이너화된 경량 PgBouncer를 제공합니다. 이 이미지에 액세스하고 사용하는 방법을 알아보려면 [docker 허브 페이지](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/)를 참조하세요. 


## <a name="next-steps"></a>다음 단계
-  [Azure Kubernetes Service 클러스터 만들기](../aks/kubernetes-walkthrough.md)
