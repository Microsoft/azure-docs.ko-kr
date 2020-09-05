---
title: 레지스트리 성능 문제 해결
description: 레지스트리의 성능에 대 한 증상, 원인 및 해결 방법
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 414e3a92b8ebd4ff58528fc5e9ec4794471bd775
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227477"
---
# <a name="troubleshoot-registry-performance"></a>레지스트리 성능 문제 해결

이 문서는 Azure container registry의 성능에 발생할 수 있는 문제를 해결 하는 데 도움이 됩니다. 

## <a name="symptoms"></a>증상

다음 중 하나 이상을 포함할 수 있습니다.

* Docker CLI를 사용 하는 끌어오기 또는 푸시 이미지가 예상 보다 오래 걸립니다.
* Azure Kubernetes Service와 같은 서비스에 이미지를 배포 하는 데 예상 보다 오래 걸립니다.
* 예상 시간 내에 많은 수의 동시 끌어오기 또는 푸시 작업을 완료할 수 없습니다.
* 지리적으로 복제 된 레지스트리의 끌어오기 또는 푸시 작업이 예상 보다 오래 걸리고 오류가 발생 하거나 푸시가 실패 합니다. `Error writing blob``Error writing manifest`

## <a name="causes"></a>원인

* 네트워크 연결 속도가 레지스트리 작업을 느리게 할 수 있습니다.- [솔루션](#check-expected-network-speed)
* 클라이언트에서 이미지 계층 압축 또는 추출 속도가 느릴 수 있음- [솔루션](#check-client-hardware)  
* 레지스트리 서비스 계층 또는 환경 [솔루션](#review-configured-limits) 에서 구성 된 제한에 도달 하 고 있습니다.
* 지리적으로 복제 된 레지스트리가 근처 지역의 복제본을가지고 있습니다.- [솔루션](#configure-geo-replicated-registry)
* 지리적으로 멀리 떨어진 레지스트리 복제본에서 끌어오는 경우- [솔루션](#configure-dns-for-geo-replicated-registry)

여기에서 문제를 해결 하지 않으면 [고급 문제 해결](#advanced-troubleshooting) 및 기타 옵션에 대 한 [다음 단계](#next-steps) 를 참조 하세요.

## <a name="potential-solutions"></a>잠재적 해결 방법

### <a name="check-expected-network-speed"></a>예상 네트워크 속도 확인

인터넷 업로드 및 다운로드 속도를 확인 하거나 AzureSpeed와 같은 도구를 사용 하 여 레지스트리 이미지 계층을 호스트 하는 Azure blob storage에서 [업로드](https://www.azurespeed.com/Azure/Uploadß) 및 [다운로드](https://www.azurespeed.com/Azure/Download) 를 테스트 합니다.

사용자의 지원 되는 최대 크기와 레지스트리 서비스 계층에 대해 지원 되는 다운로드 또는 업로드 대역폭에 대해 이미지 크기를 확인 합니다. 레지스트리가 기본 또는 표준 계층에 있는 경우 업그레이드를 고려 하 여 성능을 향상 시킵니다. 

다른 서비스에 대 한 이미지 배포의 경우 레지스트리와 대상이 있는 지역을 확인 합니다. 성능을 향상 하려면 동일한 네트워크 또는 네트워크에 가까운 지역에서 레지스트리와 배포 대상을 찾는 것이 좋습니다.

관련 링크:

* [Azure Container Registry 서비스 계층](container-registry-skus.md)    
* [컨테이너 레지스트리 FAQ](container-registry-faq.md)
* [Azure Blob Storage에 대 한 성능 및 확장성 목표](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>클라이언트 하드웨어 확인

Docker 클라이언트의 디스크 유형 및 CPU는 끌어오기 또는 푸시 작업의 일부로 클라이언트에서 이미지 계층을 추출 하거나 압축 하는 속도에 영향을 줄 수 있습니다. 예를 들어 하드 디스크 드라이브의 계층 추출은 반도체 디스크 보다 오래 걸립니다. Azure container registry 및 Docker Hub와 같은 공용 레지스트리의 비교 가능한 이미지에 대 한 끌어오기 작업을 비교 합니다.

### <a name="review-configured-limits"></a>구성 된 제한 검토

여러 개 또는 여러 개의 다중 계층 이미지를 동시에 푸시 하거나 풀 하는 경우 레지스트리 서비스 계층에 대해 지원 되는 ReadOps 및 WriteOps 제한을 검토 하세요. 레지스트리가 기본 또는 표준 계층에 있는 경우 업그레이드를 고려 하 여 제한을 늘립니다. 또한 많은 동시 작업에서 발생할 수 있는 네트워크 제한에 대해 네트워킹 공급자와 함께 확인 합니다. 

클라이언트에서 각 밀어넣기 또는 끌어오기 작업에 대 한 최대 동시 업로드 또는 다운로드에 대 한 Docker 디먼 구성을 검토 합니다. 필요한 경우 더 높은 한도를 구성 합니다.

각 이미지 계층에 별도의 레지스트리 읽기 또는 쓰기 작업이 필요 하므로 이미지의 계층 수를 확인 합니다. 이미지 계층 수를 줄이기 위한 전략을 고려 합니다.

관련 링크:

* [Azure Container Registry 서비스 계층](container-registry-skus.md)
* [dockerd.exe](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>지역에서 복제 된 레지스트리 구성

지리적으로 복제 된 레지스트리에 이미지를 푸시하는 Docker 클라이언트는 모든 이미지 계층과 해당 매니페스트를 단일 복제 된 지역에 푸시할 수 없습니다. 이 상황은 Azure Traffic Manager 레지스트리 요청을 네트워크에서 가장 가까운 복제 된 레지스트리로 라우팅하는 경우에 발생할 수 있습니다. 레지스트리에 두 개의 인접 복제 지역이 있는 경우 이미지 레이어와 매니페스트를 두 사이트에 배포할 수 있으며 매니페스트의 유효성을 검사할 때 푸시 작업이 실패합니다.

이미지를 푸시할 때 가장 가까운 복제본에 대한 DNS 확인을 최적화하려면 Azure 외부에서 작업할 때 푸시 작업의 원본과 동일한 Azure 지역 또는 가장 가까운 지역의 지역 복제 레지스트리를 구성합니다.

지역에서 복제 된 레지스트리를 사용 하 여 작업 문제를 해결 하기 위해 하나 이상의 복제에 대 한 Traffic Manager 라우팅을 일시적으로 사용 하지 않도록 설정할 수도 있습니다.

관련 링크:

* [Azure Container Registry의 지리적 복제](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>지역에서 복제 된 레지스트리에 대 한 DNS 구성

지리적으로 복제 된 레지스트리의 끌어오기 작업이 느리게 표시 되는 경우 클라이언트의 DNS 구성이 지리적으로 멀리 떨어진 DNS 서버로 확인 될 수 있습니다. 이 경우 Traffic Manager DNS 서버에 네트워크로 연결 되어 있지만 클라이언트에서 멀리 떨어진 복제본에 요청을 라우팅하는 것일 수 있습니다. 또는 (Linux)와 같은 도구를 실행 `nslookup` `dig` 하 여 Traffic Manager 레지스트리 요청을 라우팅하는 복제본을 확인 합니다. 예를 들면 다음과 같습니다.

```console
nslookup myregistry.azurecr.io
```

가능한 해결 방법은 더 가까운 DNS 서버를 구성 하는 것입니다.

관련 링크:

* [Azure Container Registry의 지리적 복제](container-registry-geo-replication.md)
* [지역에서 복제된 레지스트리를 사용하여 푸시 작업 문제 해결](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [일시적으로 복제에 대 한 라우팅을 사용 하지 않도록 설정](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Traffic Manager Faq](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>고급 문제 해결

레지스트리 리소스에 대 한 사용 권한이 허용 되는 경우 [레지스트리 환경의 상태를 확인](container-registry-check-health.md)합니다. 오류가 보고 되는 경우 잠재적인 솔루션에 대 한 [오류 참조](container-registry-health-error-reference.md) 를 검토 합니다.

레지스트리에서 [리소스 로그 컬렉션](container-registry-diagnostics-audit-logs.md) 을 사용 하는 경우 ContainterRegistryRepositoryEvents 로그를 검토 합니다. 이 로그는 push 또는 pull 이벤트와 같은 작업에 대 한 정보를 저장 합니다. 로그에서 [리포지토리 수준 작업 오류](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures)를 쿼리 합니다. 

관련 링크:

* [진단 평가 및 감사에 대 한 로그](container-registry-diagnostics-audit-logs.md)
* [컨테이너 레지스트리 FAQ](container-registry-faq.md)
* [Azure Container Registry의 모범 사례](container-registry-best-practices.md)

## <a name="next-steps"></a>다음 단계

여기에서 문제를 해결 하지 않으면 다음 옵션을 참조 하세요.

* 다른 레지스트리 문제 해결 항목은 다음과 같습니다.
  * [레지스트리 로그인 문제 해결](container-registry-troubleshoot-login.md)
  * [레지스트리의 네트워크 문제 해결](container-registry-troubleshoot-access.md)
* [커뮤니티 지원](https://azure.microsoft.com/support/community/) 옵션
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [지원 티켓 열기](https://azure.microsoft.com/support/create-ticket/)


