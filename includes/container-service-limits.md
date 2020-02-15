---
title: 포함 파일
description: 포함 파일
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ac928d9087ba5db312540b8ec542d7a2a29e2a99
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179012"
---
| 리소스 | 기본 제한 |
| --- | :--- |
| 구독 당 최대 클러스터 수 | 100 |
| 가상 컴퓨터 가용성 집합 및 기본 Load Balancer SKU를 사용 하는 클러스터당 최대 노드 수  | 100 |
| Virtual Machine Scale Sets 및 [표준 LOAD BALANCER SKU][standard-load-balancer] 를 사용 하는 클러스터당 최대 노드 수 | 1000 ( [노드 풀][node-pool]당 100 노드) |
| 노드당 최대 pod: [기본 네트워킹][basic-networking] (Kubenet) | 110 |
| 노드당 최대 pod: Azure Container 네트워킹 인터페이스를 사용 하 여 [고급 네트워킹][advanced-networking] | Azure CLI 배포: 30<sup>1</sup><br />Azure Resource Manager 템플릿: 30<sup>1</sup><br />포털 배포: 30 |

<sup>1</sup> Azure CLI 또는 리소스 관리자 템플릿을 사용 하 여 AKS (Azure Kubernetes Service) 클러스터를 배포 하는 경우이 값은 노드당 최대 250 pod로 구성할 수 있습니다. AKS 클러스터를 이미 배포한 후 또는 Azure Portal를 사용 하 여 클러스터를 배포 하는 경우 노드당 최대 pod을 구성할 수 없습니다.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
