---
title: 컨테이너 인스턴스의 보안
description: Azure Container Instances에 대한 이미지 및 암호를 보호 하기 위한 권장 사항 및 컨테이너 플랫폼에 대한 일반적인 보안 고려 사항
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: b5f2c4d9ca80318574e288110fd4ce7f490af00d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260500"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure Container Instances에 대한 보안 고려 사항

이 문서에서는 Azure Container Instances 사용 하 여 컨테이너 앱을 실행 하기 위한 보안 고려 사항을 소개 합니다. 토픽은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Container Instances에 대한 이미지 및 암호 관리에 대한 **보안 권장 사항**
> * 컨테이너 플랫폼의 컨테이너 수명 주기 내내 컨테이너 **에코 시스템에 대한 고려 사항**

## <a name="security-recommendations-for-azure-container-instances"></a>Azure Container Instances에 대한 보안 권장 사항

### <a name="use-a-private-registry"></a>개인 레지스트리 사용

컨테이너는 하나 이상의 리포지토리에 저장된 이미지에서 작성됩니다. 이러한 리포지토리는 [Docker Hub](https://hub.docker.com)와 같은 공용 레지스트리 또는 개인 레지스트리에 속할 수 있습니다. 프라이빗 레지스트리의 예는 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/)이며 온-프레미스 또는 가상 프라이빗 클라우드에 설치할 수 있습니다. [Azure Container Registry](../container-registry/container-registry-intro.md)를 포함 하 여 클라우드 기반 개인 컨테이너 레지스트리 서비스를 사용할 수도 있습니다. 

공개적으로 사용 가능한 컨테이너 이미지는 보안이 보장 되지 않습니다. 컨테이너 이미지는 여러 소프트웨어 계층으로 구성 되며 각 소프트웨어 계층에는 취약성이 있을 수 있습니다. 공격 위협을 줄이기 위해 Azure Container Registry 또는 Docker Trusted Registry와 같은 개인 레지스트리에서 이미지를 저장 하 고 검색 해야 합니다. 관리 되는 개인 레지스트리를 제공 하는 것 외에도 Azure Container Registry는 기본 인증 흐름에 대해 Azure Active Directory를 통해 [서비스 주체 기반 인증](../container-registry/container-registry-authentication.md) 을 지원 합니다. 이 인증에는 읽기 전용 (끌어오기), 쓰기 (푸시) 및 기타 권한에 대한 역할 기반 액세스가 포함 됩니다.

### <a name="monitor-and-scan-container-images"></a>컨테이너 이미지 모니터링 및 검색

솔루션을 활용 하 여 개인 레지스트리에서 컨테이너 이미지를 검색 하 고 잠재적 취약성을 식별 합니다. 여러 솔루션에서 제공 하는 위협 검색의 깊이를 이해 하는 것이 중요 합니다.

예를 들어 Azure Container Registry은 선택적으로 [Azure Security Center와 통합 되어](../security-center/azure-container-registry-integration.md) 레지스트리에 푸시된 모든 Linux 이미지를 자동으로 검색 합니다. Azure Security Center의 통합 Qualys 스캐너는 이미지 취약성을 검색 하 고 분류 하며 수정 지침을 제공 합니다.

[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) 및 [바다색 보안과](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) 같은 보안 모니터링 및 이미지 스캔 솔루션도 Azure Marketplace를 통해 사용할 수 있습니다.  

### <a name="protect-credentials"></a>자격 증명 보호

컨테이너는 여러 클러스터 및 Azure 지역에 걸쳐 분산 될 수 있습니다. 따라서 암호나 토큰 등의 로그인 또는 API 액세스에 필요한 자격 증명을 보호 해야 합니다. 권한 있는 사용자만 전송 중 및 휴지 상태의 컨테이너에 액세스할 수 있도록 합니다. 모든 자격 증명 암호를 인벤토리에 추가한 다음 개발자가 컨테이너 플랫폼용으로 설계 된 새로운 비밀 관리 도구를 사용 하도록 요구 합니다.  솔루션에 암호화 된 데이터베이스, 전송 중인 암호 데이터에 대한 TLS 암호화 및 최소 권한 [역할 기반 액세스 제어](../role-based-access-control/overview.md)가 포함 되어 있는지 확인 합니다. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) 는 컨테이너 화 된 응용 프로그램에 대한 암호화 키와 비밀 (예: 인증서, 연결 문자열 및 암호)을 보호 하는 클라우드 서비스입니다. 이 데이터는 중요 하 고 업무상 중요 하기 때문에 인증 된 응용 프로그램 및 사용자만 액세스할 수 있도록 키 자격 증명 모음에 대한 액세스를 보호 합니다.

## <a name="considerations-for-the-container-ecosystem"></a>컨테이너 에코 시스템에 대한 고려 사항

효과적으로 구현 되 고 관리 되는 다음과 같은 보안 조치는 컨테이너 에코 시스템을 보호 하 고 보호 하는 데 도움이 될 수 있습니다. 이러한 측정값은 컨테이너 수명 주기 전체에 적용 되며, 개발에서 프로덕션 배포까지, 그리고 컨테이너 orchestrator, 호스트 및 플랫폼의 범위에 적용 됩니다. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>컨테이너 개발 수명 주기의 일부로 취약성 관리 사용 

컨테이너 개발 수명 주기 전반에 걸쳐 효과적인 취약성 관리를 사용 하 여 보안 문제를 식별 하 고 해결 하는 것이 더 심각한 문제를 해결 하는 것을 개선 합니다. 

### <a name="scan-for-vulnerabilities"></a>취약성 검색 

새 취약점은 항상 검색 되므로 취약성을 검색 하 고 식별 하는 것은 지속적인 프로세스입니다. 컨테이너 수명 주기 전체에서 취약성 검사를 통합 합니다.

* 개발 파이프라인에서 최종 검사로, 공개 또는 개인 레지스트리에 이미지를 푸시 하기 전에 컨테이너에 대한 취약성 검사를 수행 해야 합니다. 
* 레지스트리의 컨테이너 이미지를 계속 검색 하 여 개발 중에 누락 된 결함을 식별 하 고 컨테이너 이미지에 사용 되는 코드에 있을 수 있는 새로 검색 된 취약점을 해결 합니다.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>실행 중인 컨테이너에 이미지 취약점 매핑 

컨테이너 이미지에 식별 된 취약성을 실행 중인 컨테이너에 매핑하여 보안 문제를 완화 하거나 해결할 수 있는 방법이 있어야 합니다.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>승인 된 이미지만 사용자 환경에서 사용 되는지 확인 합니다. 

컨테이너 에코 시스템에는 알 수 없는 컨테이너를 허용 하지 않고 충분 한 변경 및 변동성 있습니다. 승인 된 컨테이너 이미지만 허용 합니다. 승인 되지 않은 컨테이너 이미지의 사용을 모니터링 하 고 방지 하기 위해 도구와 프로세스를 준비 해야 합니다. 

공격 노출 영역을 줄이고 개발자가 중요 한 보안 실수를 만들지 못하도록 하는 효과적인 방법은 개발 환경으로 컨테이너 이미지의 흐름을 제어 하는 것입니다. 예를 들어 잠재적 공격에 대한 노출 영역을 최소화 하기 위해 단일 Linux 배포를 기본 이미지로 사용 권한 부여 수 있습니다. 

이미지 서명 또는 지문 인식을는 컨테이너의 무결성을 확인 하는 데 사용할 수 있는 관리 권의 체인을 제공할 수 있습니다. 예를 들어 Azure Container Registry는 이미지 게시자가 레지스트리에 푸시되는 이미지에 서명할 수 있도록 하 고, 이미지 소비자는 서명 된 이미지만 가져오도록 지 원하는 Docker의 [콘텐츠 신뢰](https://docs.docker.com/engine/security/trust/content_trust) 모델을 지원 합니다.

### <a name="permit-only-approved-registries"></a>승인 된 레지스트리만 허용 

사용자 환경에서 승인 된 이미지만 사용 하도록 보장 하는 것은 승인 된 컨테이너 레지스트리를 사용 하도록 허용 하는 것입니다. 승인 된 컨테이너 레지스트리를 사용 하도록 요구 하면 알 수 없는 취약점 또는 보안 문제가 발생할 가능성을 제한 하 여 위험에 대한 노출을 줄일 수 있습니다. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>수명 주기 전체에서 이미지의 무결성 보장 

컨테이너 수명 주기 전체에서 보안을 관리 하는 과정은 레지스트리에서 컨테이너 이미지의 무결성을 보장 하 고 프로덕션에 변경 되거나 배포 될 때입니다. 

* 취약성이 매우 적은 이미지는 프로덕션 환경에서 실행할 수 없습니다. 이상적으로는 프로덕션에 배포 된 모든 이미지를 선택 하 여 액세스할 수 있는 개인 레지스트리에 저장 해야 합니다. 효율적으로 관리할 수 있도록 프로덕션 이미지의 수를 작게 유지 합니다.

* 공개적으로 사용 가능한 컨테이너 이미지에서 소프트웨어의 출처를 정확 하 게 파악 하기는 쉽지 않으므로 원본에서 이미지를 빌드하여 계층의 출처에 대한 정보를 확인할 수 있습니다. 자체 구축된 컨테이너 이미지에 취약성이 있을 경우 고객은 보다 신속하게 해결 방법을 찾을 수 있습니다. 공용 이미지를 사용 하면 고객은 공용 이미지의 루트를 찾아 수정 하거나 게시자에서 다른 보안 이미지를 가져와야 합니다. 

* 프로덕션에 배포 된 철저 하 게 스캔 된 이미지는 응용 프로그램의 수명 동안 최신 상태가 아닐 수도 있습니다. 보안 취약성은 이전에 알려지지 않았거나 프로덕션 배포 후에 도입 된 이미지 계층에 대해 보고될 수 있습니다. 

  프로덕션에 배포 된 이미지를 정기적으로 감사 하 여 오래 되었거나 업데이트 되지 않은 이미지를 식별 합니다. 가동 중지 시간 없이 컨테이너 이미지를 업데이트 하는 blue-녹색 배포 방법 및 롤링 업그레이드 메커니즘을 사용할 수 있습니다. 이전 섹션에서 설명한 도구를 사용 하 여 이미지를 스캔할 수 있습니다. 

* 통합 보안 검색으로 CI (연속 통합) 파이프라인을 사용 하 여 보안 이미지를 빌드하고 개인 레지스트리에 푸시합니다. CI 솔루션에 기본 제공된 취약성 검색은 모든 테스트를 통과한 이미지가 프로덕션 워크로드가 배포된 프라이빗 레지스트리에 푸시되는지 확인합니다. 

  CI 파이프라인 오류는 취약 한 이미지가 프로덕션 워크 로드 배포에 사용 되는 개인 레지스트리에 푸시되 지 않도록 합니다. 또한 이미지 수가 많은 경우 이미지 보안 검색을 자동화 합니다. 그렇지 않고 이미지에서 보안 취약성을 수동으로 감사하는 것은 매우 번거롭고 오류가 발생하기 쉽습니다. 

### <a name="enforce-least-privileges-in-runtime"></a>런타임에 최소 권한 적용 

최소 권한 개념은 컨테이너에도 적용 되는 기본적인 보안 모범 사례입니다. 취약점이 악용 되 면 일반적으로 공격자가 손상 된 응용 프로그램 또는 프로세스와 동일한 액세스 및 권한을 부여 합니다. 컨테이너에서 작업을 수행 하는 데 필요한 가장 낮은 권한 및 액세스를 작동 하도록 하면 위험에 노출 되는 정도를 줄일 수 있습니다. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>불필요 한 권한을 제거 하 여 컨테이너 공격 노출 영역 축소 

컨테이너 런타임에서 사용 하지 않거나 불필요 한 프로세스나 권한을 제거 하 여 잠재적인 공격 노출 영역을 최소화할 수도 있습니다. 권한 있는 컨테이너는 루트로 실행 됩니다. 악의적인 사용자 또는 워크 로드가 권한 있는 컨테이너에서 이스케이프 되 면 컨테이너는 해당 시스템에서 루트로 실행 됩니다.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>컨테이너에서 액세스 하거나 실행할 수 있는 파일 및 실행 파일 사전 승인 

변수나 알려지지 않은 수를 줄이면 안정적인 안정적인 환경을 유지 관리할 수 있습니다. 컨테이너를 제한 하 여 미리 승인 된 또는 safelisted에 액세스 하거나 실행할 수 있습니다. 또한 실행 파일 및 실행 파일은 위험에 대한 노출을 제한 하는 검증 된 방법입니다.  

처음부터 구현 될 때 safelist를 관리 하는 것이 훨씬 쉽습니다. Safelist는 응용 프로그램이 제대로 작동 하는 데 필요한 파일 및 실행 파일을 배울 때 제어 및 관리 효율성을 측정 합니다. 

Safelist 공격 노출 영역을 줄일 수 있을 뿐만 아니라 변칙에 대한 기준을 제공 하 고 "잡음이 있는 환경" 및 컨테이너 중단 시나리오의 사용 사례를 방지할 수도 있습니다. 

### <a name="enforce-network-segmentation-on-running-containers"></a>실행 중인 컨테이너에서 네트워크 조각화 적용  

한 서브넷의 컨테이너를 다른 서브넷의 보안 위험 으로부터 보호 하려면 실행 중인 컨테이너 간에 네트워크 구분 (또는 nano 조각화) 또는 분리를 유지 관리 합니다. 규정 준수 조건을 충족 하는 데 필요한 업계의 컨테이너를 사용 하려면 네트워크 조각화를 유지 해야 할 수도 있습니다.  

예를 들어, 파트너 도구 [바다색](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) 은 nano 구분에 대한 자동화 된 접근 방식을 제공 합니다. 바다색은 런타임에 컨테이너 네트워크 활동을 모니터링 합니다. 다른 컨테이너, 서비스, IP 주소 및 공용 인터넷에 대한 모든 인바운드 및 아웃 바운드 네트워크 연결을 식별 합니다. Nano 조각화는 모니터링 되는 트래픽에 따라 자동으로 생성 됩니다. 

### <a name="monitor-container-activity-and-user-access"></a>컨테이너 작업 및 사용자 액세스 모니터링 

모든 IT 환경에서와 마찬가지로, 컨테이너 에코 시스템에 대한 활동 및 사용자 액세스를 일관 되 게 모니터링 하 여 의심 스러운 활동 또는 악의적인 활동을 신속 하 게 식별 해야 합니다. Azure는 다음을 비롯 한 컨테이너 모니터링 솔루션을 제공 합니다.

* [컨테이너 Azure Monitor](../azure-monitor/insights/container-insights-overview.md) 는 AKS (Azure Kubernetes Service)에서 호스트 되는 Kubernetes 환경에 배포 된 워크 로드의 성능을 모니터링 합니다. 컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 

* [Azure 컨테이너 모니터링 솔루션](../azure-monitor/insights/containers.md) 을 사용 하면 단일 위치에서 다른 Docker 및 Windows 컨테이너 호스트를 보고 관리할 수 있습니다. 예:

  * 컨테이너에 사용 되는 명령을 보여 주는 자세한 감사 정보를 표시 합니다. 
  * Docker 또는 Windows 호스트를 원격으로 보지 않고도 중앙화 된 로그를 보고 검색 하 여 컨테이너 문제를 해결 합니다.  
  * 잡음이 있을 수 있으며 호스트에서 과도 한 리소스를 소비할 수 있는 컨테이너를 찾습니다.
  * 컨테이너에 대한 중앙 집중식 CPU, 메모리, 저장소, 네트워크 사용량 및 성능 정보를 확인 합니다.  

  이 솔루션은 Docker Swarm, DC/OS, 관리 되지 않는 Kubernetes, Service Fabric 및 Red Hat OpenShift를 비롯 한 컨테이너 orchestrator 지원 합니다. 

### <a name="monitor-container-resource-activity"></a>컨테이너 리소스 작업 모니터 

컨테이너에 액세스 하는 파일, 네트워크 및 기타 리소스와 같은 리소스 활동을 모니터링 합니다. 리소스 작업 및 소비 모니터링은 성능 모니터링과 보안 조치로 모두 유용 합니다. 

[Azure Monitor](../azure-monitor/overview.md)를 사용하면 메트릭, 활동 로그 및 진단 로그를 수집하도록 허용하여 Azure 서비스에 대한 핵심 모니터링을 수행할 수 있습니다. 예를 들어, 활동 로그는 새 리소스가 만들어지거나 수정되는 시기를 알려줍니다. 

  다양한 리소스와 가상 머신 내부의 운영 체제에 대한 성능 통계를 제공하는 메트릭을 사용할 수 있습니다. Azure Portal에서 탐색기 중 하나를 사용하여 이 데이터를 보고 이러한 메트릭을 기반으로 경고를 만들 수 있습니다. Azure Monitor는 가장 빠른 메트릭 파이프라인을 제공하므로(5분에서 1분으로) 시간이 중요한 경고 및 알림에 사용해야 합니다. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>감사를 위해 모든 컨테이너 관리 사용자 액세스 기록 

Kubernetes 클러스터, 컨테이너 레지스트리 및 컨테이너 이미지를 포함 하 여 컨테이너 에코 시스템에 대한 관리 액세스의 정확한 감사 내역을 유지 관리 합니다. 이러한 로그는 감사 목적으로 필요할 수 있으며 보안 인시던트 후에 법정 증거로 사용 됩니다. Azure 솔루션은 다음과 같습니다.

* 클러스터 환경의 보안 구성을 모니터링 하 고 보안 권장 사항을 생성 하는 [Azure Security Center와 Azure Kubernetes Service 통합](../security-center/azure-kubernetes-service-integration.md)
* [Azure 컨테이너 모니터링 솔루션](../azure-monitor/insights/containers.md)
* [Azure Container Instances](container-instances-log-analytics.md) 및 [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md) 에 대한 리소스 로그

## <a name="next-steps"></a>다음 단계

* 컨테이너 화 된 환경에서 실시간 위협 감지를 위해 [Azure Security Center](../security-center/container-security.md) 를 사용 하는 방법에 대해 자세히 알아보세요.

* [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) 및 [바다색 보안](https://www.aquasec.com/solutions/azure-container-security/)의 솔루션을 사용 하 여 컨테이너 취약점 관리에 대해 자세히 알아보세요.