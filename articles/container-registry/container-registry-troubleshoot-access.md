---
title: 레지스트리의 네트워크 문제 해결
description: 가상 네트워크에서 또는 방화벽 뒤에 있는 Azure container registry에 액세스할 때 발생 하는 일반적인 문제에 대 한 증상, 원인 및 해결 방법
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 227eeeadb2aef4b4d3feb7923a198b129a6267d3
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227466"
---
# <a name="troubleshoot-network-issues-with-registry"></a>레지스트리의 네트워크 문제 해결

이 문서는 가상 네트워크에서 또는 방화벽 뒤에 있는 Azure container registry에 액세스할 때 발생할 수 있는 문제를 해결 하는 데 도움이 됩니다. 

## <a name="symptoms"></a>증상

다음 중 하나 이상을 포함할 수 있습니다.

* 이미지를 푸시 또는 끌어올 수 없으며 오류가 발생 합니다. `dial tcp: lookup myregistry.azurecr.io`
* 이미지를 푸시 또는 끌어올 수 없습니다. 오류 Azure CLI 수신 됩니다. `Could not connect to the registry login server`
* 레지스트리에서 Azure Kubernetes Service 또는 다른 Azure 서비스로 이미지를 끌어올 수 없습니다.
* HTTPS 프록시 뒤에 있는 레지스트리에 액세스할 수 없어서 오류가 발생 했습니다. `Error response from daemon: login attempt failed with status: 403 Forbidden`
* Azure Portal 또는 Azure CLI를 사용 하 여 레지스트리 설정을 확인 하거나 볼 수 없습니다.
* 가상 네트워크 설정 또는 공용 액세스 규칙을 추가 하거나 수정할 수 없습니다.
* ACR 작업에서 이미지를 푸시 하거나 끌어올 수 없습니다.
* Azure Security Center 레지스트리에서 이미지를 검색할 수 없거나 검색 결과가 표시 되지 않습니다 Azure Security Center

## <a name="causes"></a>원인

* 클라이언트 방화벽 또는 프록시가 액세스를 금지 합니다.- [솔루션](#configure-client-firewall-access)
* 레지스트리의 공용 네트워크 액세스 규칙 액세스 방지- [솔루션](#configure-public-access-to-registry)
* 가상 네트워크 구성으로 인 한 액세스 방지- [솔루션](#configure-vnet-access)
* Azure Security Center를 개인 끝점 또는 서비스 끝점이 있는 레지스트리와 통합 하려고 합니다. [solution](#configure-image-scanning-solution)

## <a name="further-diagnosis"></a>추가 진단 

[Az acr check-health](/cli/azure/acr#az-acr-check-health) 명령을 실행 하 여 레지스트리 환경의 상태에 대 한 자세한 정보를 가져오고 선택적으로 대상 레지스트리에 액세스 합니다. 예를 들어 특정 네트워크 연결 또는 구성 문제를 진단 합니다. 

명령 예제는 [Azure container registry의 상태 확인](container-registry-check-health.md) 을 참조 하세요. 오류가 보고 되 면 [오류 참조](container-registry-health-error-reference.md) 및 다음 섹션에서 권장 해결 방법을 검토 합니다.

> [!NOTE]
> 일부 네트워크 연결 증상은 레지스트리 인증 또는 권한 부여에 문제가 있는 경우에도 발생할 수 있습니다. [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md)을 참조 하세요.

## <a name="potential-solutions"></a>잠재적 해결 방법

### <a name="configure-client-firewall-access"></a>클라이언트 방화벽 액세스 구성

클라이언트 방화벽이 나 프록시 서버 뒤에서 레지스트리에 액세스 하려면 레지스트리의 REST 및 데이터 끝점에 액세스 하는 방화벽 규칙을 구성 합니다. [전용 데이터 끝점](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) 을 사용 하는 경우 다음에 액세스할 수 있는 규칙이 필요 합니다.

* REST 끝점: `<registryname>.azurecr.io`
* 데이터 끝점: `<registry-name>.<region>.data.azurecr.io`

지리적으로 복제 된 레지스트리의 경우 각 지역 복제본의 데이터 끝점에 대 한 액세스를 구성 합니다.

HTTPS 프록시 뒤에는 Docker 클라이언트와 Docker 데몬이 모두 프록시 동작에 대해 구성 되어 있는지 확인 합니다.

ContainerRegistryLoginEvents 테이블의 레지스트리 리소스 로그는 차단 된 연결 시도를 진단 하는 데 도움이 될 수 있습니다.

관련 링크:

* [방화벽 뒤에서 Azure 컨테이너 레지스트리에 액세스하기 위한 규칙 구성](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS 프록시 구성](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [지리적 replicationin Azure Container Registry](container-registry-geo-replication.md)
* [진단 평가 및 감사에 대 한 Azure Container Registry 로그](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>레지스트리에 대 한 공용 액세스 구성

인터넷을 통해 레지스트리에 액세스 하는 경우 레지스트리가 클라이언트에서 공용 네트워크 액세스를 허용 하는지 확인 합니다. 기본적으로 Azure container registry는 모든 네트워크에서 공용 레지스트리 끝점에 대 한 액세스를 허용 합니다. 레지스트리는 선택한 네트워크 또는 선택한 IP 주소에 대 한 액세스를 제한할 수 있습니다. 

서비스가 서비스 엔드포인트를 사용 하 여 가상 네트워크에 대해 구성 된 경우 공용 네트워크 액세스를 사용 하지 않도록 설정 하면 서비스 끝점에 대 한 액세스도 비활성화 됩니다. 레지스트리가 개인 링크를 사용 하 여 가상 네트워크에 대해 구성 된 경우 IP 네트워크 규칙은 레지스트리의 개인 끝점에 적용 되지 않습니다. 

관련 링크:

* [공용 IP 네트워크 규칙 구성](container-registry-access-selected-networks.md)
* [Azure 개인 링크를 사용 하 여 Azure container registry에 비공개로 연결](container-registry-private-link.md)
* [Azure Virtual Network의 서비스 엔드포인트를 사용하여 컨테이너 레지스트리에 대한 액세스 제한](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>VNet 액세스 구성

가상 네트워크가 개인 링크 또는 서비스 끝점 (미리 보기)에 대 한 개인 끝점으로 구성 되어 있는지 확인 합니다. 현재 Azure 방호 끝점은 지원 되지 않습니다.

네트워크의 다른 리소스에서 레지스트리로의 트래픽을 제한 하는 데 사용 되는 NSG 규칙 및 서비스 태그를 검토 합니다. 

레지스트리에 대 한 서비스 끝점이 구성 된 경우 해당 네트워크 서브넷의 액세스를 허용 하는 네트워크 규칙이 레지스트리에 추가 되어 있는지 확인 합니다. 서비스 끝점은 네트워크의 가상 컴퓨터 및 AKS 클러스터 에서만 액세스할 수 있도록 지원 합니다.

네트워크에서 Azure 방화벽 또는 유사한 솔루션을 구성 하는 경우 AKS 클러스터와 같은 다른 리소스의 송신 트래픽이 레지스트리 끝점에 도달 하도록 설정 되었는지 확인 합니다.

관련 링크:

* [Azure 개인 링크를 사용 하 여 Azure container registry에 비공개로 연결](container-registry-private-link.md)
* [Azure Virtual Network의 서비스 엔드포인트를 사용하여 컨테이너 레지스트리에 대한 액세스 제한](container-registry-vnet.md)
* [AKS 클러스터에 대 한 필수 아웃 바운드 네트워크 규칙 및 Fqdn](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: DNS 확인 디버깅](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)

### <a name="configure-image-scanning-solution"></a>이미지 스캔 솔루션 구성

레지스트리가 개인 끝점 또는 서비스 끝점을 사용 하 여 구성 된 경우 현재 이미지 검색에 대 한 Azure Security Center와 통합할 수 없습니다. 필요에 따라 다음을 비롯 하 여 Azure Marketplace에서 사용할 수 있는 다른 이미지 검색 솔루션을 구성 합니다.

* [바다색 클라우드 네이티브 보안 플랫폼](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security)
* [Twistlock Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock)

관련 링크:

* [Security Center Azure Container Registry 이미지 검색](../security-center/azure-container-registry-integration.md)
* [사용자 의견](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are) 제공


## <a name="advanced-troubleshooting"></a>고급 문제 해결

레지스트리에서 [리소스 로그 컬렉션](container-registry-diagnostics-audit-logs.md) 을 사용 하는 경우 ContainterRegistryLoginEvents 로그를 검토 합니다. 이 로그는 들어오는 id와 IP 주소를 포함 하 여 인증 이벤트와 상태를 저장 합니다. 로그에서 [레지스트리 인증 실패](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)를 쿼리 합니다. 

관련 링크:

* [진단 평가 및 감사에 대 한 로그](container-registry-diagnostics-audit-logs.md)
* [컨테이너 레지스트리 FAQ](container-registry-faq.md)
* [Azure Container Registry에 대 한 Azure 보안 기준](security-baseline.md)
* [Azure Container Registry의 모범 사례](container-registry-best-practices.md)

## <a name="next-steps"></a>다음 단계

여기에서 문제를 해결 하지 않으면 다음 옵션을 참조 하세요.

* 다른 레지스트리 문제 해결 항목은 다음과 같습니다.
  * [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md) 
  * [레지스트리 성능 문제 해결](container-registry-troubleshoot-performance.md)
* [커뮤니티 지원](https://azure.microsoft.com/support/community/) 옵션
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [지원 티켓 열기](https://azure.microsoft.com/support/create-ticket/)


