---
title: Azure Kubernetes 서비스에서 송신 트래픽 제한 (AKS)
description: Azure Kubernetes 서비스 (AKS)에서 송신 트래픽을 제어 하는 데 필요한 포트 및 주소에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/21/2020
ms.author: mlearned
ms.openlocfilehash: 1206c20ec4f547dd591ac711d546d1dad0b7a19a
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251603"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 클러스터 노드에 대 한 송신 트래픽 제어

기본적으로 AKS 클러스터는 무제한 아웃 바운드 (송신) 인터넷 액세스를 포함 합니다. 이러한 수준의 네트워크 액세스를 통해 실행 하는 노드 및 서비스를 사용 하 여 필요에 따라 외부 리소스에 액세스할 수 있습니다. 송신 트래픽을 제한 하려는 경우 정상 클러스터 유지 관리 작업을 유지 하기 위해 제한 된 수의 포트와 주소를 액세스할 수 있어야 합니다. 클러스터는 기본적으로 MCR (Microsoft Container Registry) 또는 Azure Container Registry (ACR)의 기본 시스템 컨테이너 이미지만 사용 하도록 구성 됩니다. 이러한 필수 포트와 주소를 허용 하도록 기본 방화벽 및 보안 규칙을 구성 합니다.

이 문서에서는 AKS 클러스터에서 송신 트래픽을 제한 하는 경우 필요한 네트워크 포트 및 Fqdn (정규화 된 도메인 이름) 및 선택 사항에 대해 자세히 설명 합니다.

> [!IMPORTANT]
> 이 문서에서는 AKS 서브넷에서 나가는 트래픽을 잠그는 방법에 대해서만 설명 합니다. AKS에는 수신 요구 사항이 없습니다.  NSGs (네트워크 보안 그룹) 및 방화벽을 사용 하는 내부 서브넷 트래픽을 차단 하는 것은 지원 되지 않습니다. 클러스터 내에서 트래픽을 제어 하 고 차단 하려면 [네트워크 정책을][network-policy]사용 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.66 이상이 설치 및 구성 되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="egress-traffic-overview"></a>송신 트래픽 개요

관리 및 운영 목적의 AKS 클러스터의 노드는 특정 포트 및 Fqdn (정규화 된 도메인 이름)에 액세스 해야 합니다. 이러한 작업은 API 서버와 통신 하거나 핵심 Kubernetes 클러스터 구성 요소 및 노드 보안 업데이트를 다운로드 하 여 설치 하는 것일 수 있습니다. 기본적으로 AKS 클러스터의 노드에 대해서는 송신 (아웃 바운드) 인터넷 트래픽이 제한 되지 않습니다. 클러스터는 외부 리포지토리에서 기본 시스템 컨테이너 이미지를 끌어올 수 있습니다.

AKS 클러스터의 보안을 강화 하기 위해 송신 트래픽을 제한할 수 있습니다. 클러스터는 MCR 또는 ACR에서 기본 시스템 컨테이너 이미지를 가져오도록 구성 됩니다. 이러한 방식으로 송신 트래픽을 잠그는 경우 특정 포트 및 Fqdn을 정의 하 여 AKS 노드가 필요한 외부 서비스와 올바르게 통신할 수 있도록 합니다. 이러한 권한이 있는 포트 및 Fqdn이 없으면 AKS 노드가 API 서버와 통신할 수 없거나 핵심 구성 요소를 설치할 수 없습니다.

[Azure 방화벽][azure-firewall] 또는 타사 방화벽 어플라이언스를 사용 하 여 송신 트래픽을 보호 하 고 이러한 필수 포트 및 주소를 정의할 수 있습니다. AKS는 이러한 규칙을 자동으로 만들지 않습니다. 네트워크 방화벽에서 적절 한 규칙을 만들 때 다음 포트와 주소는 참조용입니다.

> [!IMPORTANT]
> Azure 방화벽을 사용 하 여 송신 트래픽을 제한 하 고 UDR (사용자 정의 경로)를 만들어 모든 송신 트래픽을 강제로 적용 하는 경우 수신 트래픽을 올바르게 허용 하도록 방화벽에서 적절 한 DNAT 규칙을 만들어야 합니다. UDR과 함께 Azure 방화벽을 사용 하면 비대칭 라우팅으로 인해 수신 설정이 끊어집니다. (AKS 서브넷에 방화벽의 개인 IP 주소로 이동 하는 기본 경로가 있지만, 다음 유형의 공용 부하 분산 장치 수신 또는 Kubernetes 서비스를 사용 하는 경우 문제가 발생 합니다. 이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태 저장 이므로 방화벽에서 설정 된 세션을 인식 하지 못하기 때문에 반환 하는 패킷을 삭제 합니다. Azure 방화벽과 수신 또는 서비스 부하 분산 장치를 통합 하는 방법에 대 한 자세한 내용은 azure [와 azure 표준 Load Balancer 통합](https://docs.microsoft.com/azure/firewall/integrate-lb)을 참조 하세요.
> 송신 작업자 노드 IP와 API 서버에 대 한 IP 간의 네트워크 규칙을 사용 하 여 TCP 포트 9000 및 TCP 포트 22에 대 한 트래픽을 잠글 수 있습니다.

AKS에는 두 가지 포트 및 주소 집합이 있습니다.

* [AKS 클러스터에 필요한 포트 및 주소](#required-ports-and-addresses-for-aks-clusters) 는 권한 있는 송신 트래픽에 대 한 최소 요구 사항을 자세히 설명 합니다.
* [AKS 클러스터에 대 한 선택적 권장 주소와 포트](#optional-recommended-addresses-and-ports-for-aks-clusters) 는 모든 시나리오에 필요 하지 않지만 Azure Monitor와 같은 다른 서비스와의 통합은 제대로 작동 하지 않습니다. 이 선택적 포트 및 Fqdn 목록을 검토 하 고 AKS 클러스터에서 사용 되는 서비스 및 구성 요소에 대 한 권한을 부여 합니다.

> [!NOTE]
> 송신 트래픽을 제한 하는 것은 새 AKS 클러스터 에서만 작동 합니다. 기존 클러스터의 경우 송신 트래픽을 제한 하기 전에 `az aks upgrade` 명령을 사용 하 여 [클러스터 업그레이드 작업을 수행][aks-upgrade] 합니다.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 클러스터에 필요한 포트 및 주소

AKS 클러스터에는 다음과 같은 아웃 바운드 포트/네트워크 규칙이 필요 합니다.

* TCP 포트 *443*
* TCP [IPAddrOfYourAPIServer]: API 서버와 통신 해야 하는 앱이 있는 경우 443이 필요 합니다.  이 변경 내용은 클러스터를 만든 후에 설정할 수 있습니다.
* 터널 프런트 pod가 API 서버의 터널 끝과 통신 하는 TCP 포트 *9000* 및 tcp 포트 *22*
    * 보다 구체적인 정보를 얻으려면 다음 표에 나와 있는 *. *\<위치\>. azmk8s.io* 및 * *. 실행\<\>위치* 를 참조 하십시오.
* NTP (Network Time Protocol) 시간 동기화 (Linux 노드)의 경우 UDP 포트 *123* .
* Pod API 서버에 직접 액세스 하는 경우에는 DNS에 대 한 UDP 포트 *53* 도 필요 합니다.

다음 FQDN/응용 프로그램 규칙이 필요 합니다.

> [!IMPORTANT]
> **blob.core.windows.net 및 aksrepos.azurecr.io** 에는 더 이상 송신 잠금에 대 한 FQDN 규칙이 필요 하지 않습니다. *  기존 클러스터의 경우 `az aks upgrade` 명령을 사용 하 여 [클러스터 업그레이드 작업을 수행][aks-upgrade] 하 여 이러한 규칙을 제거 합니다.

- Azure 글로벌

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *. hcp.\<위치\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. *\<location\>* 을 AKS 클러스터가 배포 된 지역으로 바꿉니다. |
| *. 실행.\<위치\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. *\<location\>* 을 AKS 클러스터가 배포 된 지역으로 바꿉니다. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR (Microsoft Container Registry)의 이미지에 액세스 하는 데 필요 합니다. 이 레지스트리에는 클러스터를 업그레이드 및 확장 하는 동안 클러스터 기능에 필요한 자사 이미지/차트 (예: moby)가 포함 되어 있습니다. |
| *.cdn.mscr.io              | HTTPS:443 | 이 주소는 Azure CDN (content delivery network)에 의해 지원 되는 MCR 저장소에 필요 합니다. |
| management.azure.com       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요 합니다. |
| login.microsoftonline.com  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요 합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요 합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시 된 *apt-get* 작업에 사용 되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI 포함 됩니다. |
| acs-mirror.azureedge.net   | HTTPS:443 | 이 주소는 kubenet 및 Azure CNI와 같은 필수 이진 파일을 설치 하는 데 필요한 리포지토리입니다. |

- Azure China 21Vianet

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *. hcp.\<위치\>. cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. *\<location\>* 을 AKS 클러스터가 배포 된 지역으로 바꿉니다. |
| *. 실행.\<위치\>. cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. *\<location\>* 을 AKS 클러스터가 배포 된 지역으로 바꿉니다. |
| *. azk8s.cn        | HTTPS:443 | 이 주소는 필요한 이진 파일과 이미지를 다운로드 하는 데 필요 합니다.|
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR (Microsoft Container Registry)의 이미지에 액세스 하는 데 필요 합니다. 이 레지스트리에는 클러스터를 업그레이드 및 확장 하는 동안 클러스터 기능에 필요한 자사 이미지/차트 (예: moby)가 포함 되어 있습니다. |
| *.cdn.mscr.io              | HTTPS:443 | 이 주소는 CDN (Azure Content Delivery Network)에서 지원 되는 MCR 저장소에 필요 합니다. |
| management.chinacloudapi.cn       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요 합니다. |
| login.chinacloudapi.cn  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요 합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요 합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시 된 *apt-get* 작업에 사용 되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI 포함 됩니다. |

- Azure Government

| FQDN                       | 포트      | 사용      |
|----------------------------|-----------|----------|
| *. hcp.\<위치\>. cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. *\<location\>* 을 AKS 클러스터가 배포 된 지역으로 바꿉니다. |
| *. 실행.\<위치\>. cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | 이 주소는 API 서버 끝점입니다. *\<location\>* 을 AKS 클러스터가 배포 된 지역으로 바꿉니다. |
| mcr.microsoft.com          | HTTPS:443 | 이 주소는 MCR (Microsoft Container Registry)의 이미지에 액세스 하는 데 필요 합니다. 이 레지스트리에는 클러스터를 업그레이드 및 확장 하는 동안 클러스터 기능에 필요한 자사 이미지/차트 (예: moby)가 포함 되어 있습니다. |
| *.cdn.mscr.io              | HTTPS:443 | 이 주소는 CDN (Azure Content Delivery Network)에서 지원 되는 MCR 저장소에 필요 합니다. |
| management.usgovcloudapi.net       | HTTPS:443 | 이 주소는 Kubernetes GET/PUT 작업에 필요 합니다. |
| login.microsoftonline.us  | HTTPS:443 | 이 주소는 Azure Active Directory 인증에 필요 합니다. |
| ntp.ubuntu.com             | UDP:123   | 이 주소는 Linux 노드에서 NTP 시간 동기화에 필요 합니다. |
| packages.microsoft.com     | HTTPS:443 | 이 주소는 캐시 된 *apt-get* 작업에 사용 되는 Microsoft 패키지 리포지토리입니다.  예제 패키지에는 Moby, PowerShell 및 Azure CLI 포함 됩니다. |
| acs-mirror.azureedge.net   | HTTPS:443 | 이 주소는 kubenet 및 Azure CNI와 같은 필수 이진 파일을 설치 하는 데 필요한 리포지토리입니다. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS 클러스터에 대 한 선택적 권장 주소 및 포트

다음 아웃 바운드 포트/네트워크 규칙은 AKS 클러스터에 대 한 선택 사항입니다.

AKS 클러스터가 올바르게 작동 하려면 다음 FQDN/응용 프로그램 규칙을 권장 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | 이 주소를 통해 Linux 클러스터 노드가 필요한 보안 패치와 업데이트를 다운로드할 수 있습니다. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>GPU 사용 AKS 클러스터에 대 한 필수 주소 및 포트

다음 FQDN/응용 프로그램 규칙은 GPU를 사용 하는 AKS 클러스터에 필요 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | 이 주소는 GPU 기반 노드에 대 한 올바른 드라이버 설치 및 작업에 사용 됩니다. |
| us.download.nvidia.com | HTTPS:443 | 이 주소는 GPU 기반 노드에 대 한 올바른 드라이버 설치 및 작업에 사용 됩니다. |
| apt.dockerproject.org | HTTPS:443 | 이 주소는 GPU 기반 노드에 대 한 올바른 드라이버 설치 및 작업에 사용 됩니다. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>컨테이너에 대해 Azure Monitor를 사용 하도록 설정 된 필수 주소 및 포트

컨테이너에 대해 Azure Monitor를 사용 하도록 설정 된 AKS 클러스터에는 다음 FQDN/application 규칙이 필요 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443  | Azure Monitor를 사용 하 여 올바른 메트릭 및 모니터링 원격 분석을 위한 것입니다. |
| *.ods.opinsights.azure.com    | HTTPS:443 | 수집 log analytics 데이터에 대 한 Azure Monitor에 사용 됩니다. |
| *.oms.opinsights.azure.com | HTTPS:443 | 이 주소는 log analytics 서비스를 인증 하는 데 사용 되는 omsagent에서 사용 됩니다. |
|*.microsoftonline.com | HTTPS:443 | Azure Monitor에 대 한 메트릭을 인증 하 고 전송 하는 데 사용 됩니다. |
|*.monitoring.azure.com | HTTPS:443 | Azure Monitor에 메트릭 데이터를 전송 하는 데 사용 됩니다. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Azure Dev Spaces 사용 하도록 설정 된 필수 주소 및 포트

Azure Dev Spaces 사용 하도록 설정 된 AKS 클러스터에는 다음 FQDN/application 규칙이 필요 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 이 주소는 linux 알파인 및 기타 Azure Dev Spaces 이미지를 가져오는 데 사용 됩니다. |
| gcr.io | HTTP: 443 | 이 주소는 투구/tiller 이미지를 가져오는 데 사용 됩니다. |
| storage.googleapis.com | HTTP: 443 | 이 주소는 투구/tiller 이미지를 가져오는 데 사용 됩니다. |
| azds-<guid>.<location>azds.io | HTTPS:443 | 컨트롤러에 대 한 Azure Dev Spaces 백 엔드 서비스와 통신 하는 데 사용 됩니다. 정확한 FQDN 은% USERPROFILE%\.azds\settings.json의 "dataplaneFqdn"에서 찾을 수 있습니다. |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Azure Policy (공개 미리 보기) 사용으로 설정 된 AKS 클러스터에 대 한 필수 주소 및 포트

> [!CAUTION]
> 아래 기능 중 일부는 미리 보기 상태입니다.  이 문서의 제안 사항은 기능이 공개 미리 보기 및 향후 릴리스 단계로 이동 됨에 따라 변경 될 수 있습니다.

Azure Policy 사용 하도록 설정 된 AKS 클러스터에는 다음 FQDN/application 규칙이 필요 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 이 주소는 Azure Policy에 대 한 올바른 작업에 사용 됩니다. (현재 AKS의 미리 보기 상태) |
| raw.githubusercontent.com | HTTPS:443 | 이 주소는 Azure Policy의 올바른 작동을 보장 하기 위해 GitHub에서 기본 제공 정책을 가져오는 데 사용 됩니다. (현재 AKS의 미리 보기 상태) |
| \* .gk.<location>azmk8s.io | HTTPS:443 | 감사 결과를 얻기 위해 마스터 서버에서 실행 되는 게이트 키퍼 감사 끝점과 통신 하는 Azure policy 추가 기능입니다. |
| dc.services.visualstudio.com | HTTPS:443 | Application insights 끝점에 원격 분석 데이터를 전송 하는 Azure policy 추가 기능입니다. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Windows Server 기반 노드 (공개 미리 보기) 사용에 필요 합니다.

> [!CAUTION]
> 아래 기능 중 일부는 미리 보기 상태입니다.  이 문서의 제안 사항은 기능이 공개 미리 보기 및 향후 릴리스 단계로 이동 됨에 따라 변경 될 수 있습니다.

Windows Server 기반 AKS 클러스터에는 다음과 같은 FQDN/응용 프로그램 규칙이 필요 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Windows 관련 이진 파일을 설치 하려면 |
| mp.microsoft.com,<span></span>msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Windows 관련 이진 파일을 설치 하려면 |
| kms.core.windows.net | TCP: 1688 | Windows 관련 이진 파일을 설치 하려면 |


## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터에 대 한 송신 트래픽을 제한 하는 경우 허용할 포트와 주소를 배웠습니다. Pod 자체에서 통신할 수 있는 방법 및 클러스터 내에서의 제한 사항을 정의할 수도 있습니다. 자세한 내용은 [AKS에서 네트워크 정책을 사용 하 여 pod 간의 트래픽 보호][network-policy]를 참조 하세요.

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
