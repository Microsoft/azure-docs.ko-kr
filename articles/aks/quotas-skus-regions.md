---
title: Azure Kubernetes 서비스 (AKS)의 할당량, Sku 및 지역 가용성
description: Azure Kubernetes 서비스 (AKS)의 기본 할당량, 제한 된 노드 VM SKU 크기 및 지역 가용성에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: a8b561780e2e81499d211252648aeef86561cb9b
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658511"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)의 할당량, 가상 머신 크기 제한 및 지역 가용성

모든 Azure 서비스는 리소스 및 기능에 대한 기본 제한 및 할당량을 설정 합니다. 특정 VM (가상 머신) Sku도 사용할 수 있도록 제한 됩니다.

이 문서에서는 AKS (Azure Kubernetes Service) 리소스의 기본 리소스 제한과 Azure 지역에서 AKS의 가용성에 대해 자세히 설명 합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>프로비전된 인프라

다른 모든 네트워크, 컴퓨팅 및 스토리지 제한은 프로비전된 인프라에 적용됩니다. 관련 한도는 [Azure 구독 및 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.

> [!IMPORTANT]
> AKS 클러스터를 업그레이드 하는 경우 추가 리소스가 일시적으로 사용 됩니다. 이러한 리소스에는 가상 네트워크 서브넷 또는 가상 머신 vCPU 할당량에서 사용 가능한 IP 주소가 포함 됩니다. Windows Server 컨테이너 (현재 AKS에서 미리 보기 상태)를 사용 하는 경우 노드에 최신 업데이트를 적용 하는 유일한 보증 방법은 업그레이드 작업을 수행 하는 것입니다. 실패 한 클러스터 업그레이드 프로세스는 이러한 임시 리소스를 처리 하는 데 사용할 수 있는 IP 주소 공간 또는 vCPU 할당량이 없음을 나타낼 수 있습니다. Windows Server 노드 업그레이드 프로세스에 대한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조 하세요.

## <a name="restricted-vm-sizes"></a>제한 된 VM 크기

AKS 클러스터의 각 노드에는 vCPU 및 메모리와 같은 고정 된 양의 계산 리소스가 포함 되어 있습니다. AKS 노드에 계산 리소스가 부족 한 경우 pod이 올바르게 실행 되지 않을 수 있습니다. 필수 *kube 시스템* pod 및 응용 프로그램을 안정적으로 예약할 수 있도록 하려면 AKS에서 다음 VM sku를 사용 하지 마세요.

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM 유형 및 해당 계산 리소스에 대한 자세한 내용은 [Azure의 가상 머신 크기][vm-skus]를 참조 하세요.

## <a name="region-availability"></a>리전 가용성

클러스터를 배포 하 고 실행할 수 있는 최신 목록은 [AKS 지역 가용성][region-availability]을 참조 하세요.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 리소스가 증가를 지 원하는 경우 [Azure 지원 요청][azure-support] 을 통해 증가를 요청 합니다 ( **문제 유형에**대해 **할당량**선택).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
