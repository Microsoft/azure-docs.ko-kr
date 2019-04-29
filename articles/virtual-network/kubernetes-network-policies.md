---
title: Azure Kubernetes 네트워크 정책 | Microsoft Docs
description: Kubernetes 클러스터를 보호하는 Kubernetes 네트워크 정책에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a5c367402bd1e61485095fd1d565a8582acc3a9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824908"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes 네트워크 정책 개요

네트워크 정책은 NSG(네트워크 보안 그룹)이 VM에 대한 마이크로 구분을 제공하는 것처럼 pod에 대한 마이크로 구분을 제공합니다. Azure 네트워크 정책 구현은 표준 Kubernetes 네트워크 정책 사양을 지원합니다. 레이블을 사용하여 Pod의 그룹을 선택하고 이러한 pod에서 허용되는 트래픽의 종류를 지정하는 수신 및 송신 규칙의 목록을 정의할 수 있습니다. [Kubernetes 설명서](https://kubernetes.io/docs/concepts/services-networking/network-policies/)에서 Kubernetes 네트워크 정책에 대해 자세히 알아보세요.

![Kubernetes 네트워크 정책 개요](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure 네트워크 정책은 컨테이너를 위한 VNet 통합을 제공하는 Azure CNI와 함께 작동합니다. 이는 현재 Linux 노드에 대해서만 지원합니다. 구현은 트래픽 필터링을 적용하기 위해 정의된 정책에 따라 Linux IP 테이블 규칙을 구성합니다.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Kubernetes 클러스터에 대한 보안 계획
클러스터에 대한 보안을 구현하는 경우 클러스터 서브넷에 들어오고 나가는 트래픽인 북-남 트래픽을 필터링하는 데 NSG(네트워크 보안 그룹)를 사용하고, 클러스터의 pod 간 트래픽인 동-서 트래픽에 대해서는 Kubernetes 네트워크 정책을 사용합니다.

## <a name="using-azure-kubernetes-network-policies"></a>Azure Kubernetes 네트워크 정책 사용
Azure 네트워크 정책은 pod에 대한 마이크로 구분을 제공하기 위해 다음과 같은 방법에서 사용할 수 있습니다.

### <a name="acs-engine"></a>ACS 엔진
ACS 엔진은 Azure에서 Kubernetes 클러스터 배포를 위한 Azure Resource Manager 템플릿을 생성하는 도구입니다. 클러스터 구성은 템플릿 생성 시 도구로 전달되는 JSON 파일에서 지정됩니다. 지원되는 클러스터 설정 및 해당 설명의 전체 목록을 자세히 알아보려면 Microsoft Azure Container Service Engine - 클러스터 정의를 참조하세요.

acs 엔진을 사용하여 배포된 클러스터에서 정책을 사용하도록 설정하려면 클러스터 정의 파일의 networkPolicy 설정의 값을 “azure”로 지정합니다.

#### <a name="example-configuration"></a>예제 구성

다음 JSON 예제 구성은 새 가상 네트워크 및 서브넷을 만들고 Azure CNI 내에서 Kubernetes 클러스터를 배포합니다. “Notepad”를 사용하여 JSON 파일을 편집하는 것이 좋습니다. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Azure에서 사용자 고유의 Kubernetes 클러스터 만들기
구현은 ACS 엔진 같은 도구에 의존하지 않고 사용자가 직접 배포하는 Kubernetes 클러스터에서 Pod에 대한 네트워크 정책을 제공하기 위해 사용될 수 있습니다. 이 경우 먼저 CNI 플러그 인을 설치하고 클러스터의 모든 가상 머신에서 사용하도록 설정합니다. 자세한 지침은 [직접 배포하는 Kubernetes 클러스터를 위한 플러그 인 배포](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)를 참조하세요.

클러스터가 배포되면 다음 `kubectl` 명령을 실행하여 Azure 네트워크 정책 *daemonset*을 클러스터에 다운로드하고 적용합니다.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
솔루션도 오픈 소스이며 코드는 [Azure Container Networking 리포지토리](https://github.com/Azure/azure-container-networking/tree/master/npm)에서 제공됩니다.



## <a name="next-steps"></a>다음 단계
- [Azure Kubernetes Service](../aks/intro-kubernetes.md)에 대해 알아봅니다.
-  [컨테이너 네트워크](container-networking-overview.md)에 대해 알아봅니다.
- Kubernetes 클러스터 또는 Docker 컨테이너에 대한 [플러그 인을 배포](deploy-container-networking.md)합니다.