---
title: Azure Service Fabric 네트워킹 모범 사례 | Microsoft Docs
description: Service Fabric 네트워킹 관리를 위한 모범 사례를 소개합니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 86ad6fce34f323d94f7b9c318ba81f547360d4df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038518"
---
# <a name="networking"></a>네트워킹

Azure Service Fabric 클러스터를 만들고 관리할 때는 노드와 애플리케이션용 네트워크 연결을 제공합니다. 네트워킹 리소스에는 IP 주소 범위, 가상 네트워크, 부하 분산 장치 및 네트워크 보안 그룹이 포함됩니다. 이 문서에서는 이러한 리소스와 관련된 모범 사례에 대해 알아봅니다.

Azure [Service Fabric 네트워킹 패턴](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)을 검토하면 기존 가상 네트워크 또는 서브넷, 정적 공용 IP 주소, 내부 전용 부하 분산 장치 또는 내부 및 외부 부하 분산 장치 등의 기능을 사용하는 클러스터를 만드는 방법을 파악할 수 있습니다.

## <a name="infrastructure-networking"></a>인프라 네트워킹
Resource Manager 템플릿에서 enableAcceleratedNetworking을 선언하면 가속화된 네트워킹을 통해 가상 머신 성능을 최대화할 수 있습니다. 아래에는 가속화된 네트워킹을 사용하도록 설정하는 Virtual Machine Scale Set NetworkInterfaceConfigurations의 코드 조각이 나와 있습니다.

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
[가속화된 네트워킹을 사용하는 Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 및 [가속화된 네트워킹을 사용하는 Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)에서 Service Fabric 클러스터를 프로비전할 수 있습니다.

가속화된 네트워킹은 Azure Virtual Machine Series SKU D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2, Ms/Mms에서 지원됩니다. Service Fabric Windows 클러스터의 경우 2019년 1월 23일에 Standard_DS8_v3 SKU를 사용하여, Service Fabric Linux 클러스터의 경우 2019년 1월 29일에 Standard_DS12_v2를 사용하여 가속화된 네트워킹을 테스트한 결과 정상 작동이 확인되었습니다.

기존 Service Fabric 클러스터에서 가속화된 네트워킹을 사용하도록 설정하려면 먼저 [Virtual Machine Scale Set를 추가하여 Service Fabric 클러스터를 확장](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)해 다음 작업을 수행해야 합니다.
1. 가속화된 네트워킹이 사용하도록 설정된 NodeType 프로비전
2. 가속화된 네트워킹이 사용하도록 설정되어 있는 프로비전된 NodeType으로 서비스와 상태 마이그레이션

가속화된 네트워킹을 현재 위치에서 사용하도록 설정하면 가동 중지 시간이 발생하므로, 기존 클러스터에서 가속화된 네트워킹을 사용하려면 인프라를 확장해야 합니다. [기존 NIC에서 가속화된 네트워킹을 사용하도록 설정하기 전에 가용성 세트의 모든 가상 머신을 중지하고 할당을 취소](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)해야 하기 때문입니다.

## <a name="cluster-networking"></a>클러스터 네트워킹

* [Service Fabric 네트워킹 패턴](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)에 요약되어 있는 단계를 수행하면 기존 가상 네트워크에 Service Fabric 클러스터를 배포할 수 있습니다.

* 클러스터로의 인바운드 및 아웃바운드 트래픽을 제한하는 노드 형식의 경우에는 NSG(네트워크 보안 그룹)를 사용하는 것이 좋습니다. NSG에서 필요한 포트가 열려 있는지 확인합니다. 예를 들면 다음과 같습니다. ![Service Fabric NSG 규칙][NSGSetup]

* Service Fabric 시스템 서비스를 포함하는 주 노드 형식은 외부 부하 분산 장치를 통해 표시하지 않아도 되며 [내부 부하 분산 장치](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)를 통해 표시할 수 있습니다.

* 클러스터에는 [고정 공용 IP 주소](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1)를 사용합니다.

## <a name="application-networking"></a>애플리케이션 네트워킹

* Windows 컨테이너 워크로드를 실행하려는 경우 [오픈 네트워킹 모드](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)를 사용하면 서비스 간 통신을 더 쉽게 수행할 수 있습니다.

* [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) 또는 [Service Fabric 역방향 프록시](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) 등의 역방향 프록시를 사용하여 80, 443 등의 일반적인 애플리케이션 포트를 표시합니다.

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
