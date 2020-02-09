---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Services)의 컨테이너 이미지 관리
description: AKS(Azure Kubernetes Services)에서 컨테이너 이미지를 관리하고 보안을 유지하는 방법에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: cd859a4009782ca39732ec004a3d3e05edd377b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442911"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 컨테이너 이미지 관리 및 보안에 대한 모범 사례

AKS(Azure Kubernetes Service)에서 애플리케이션을 개발 및 실행할 경우 컨테이너 및 컨테이너 이미지의 보안 유지가 핵심 고려 사항입니다. 오래된 기본 이미지 또는 패치되지 않은 애플리케이션 런타임이 포함된 컨테이너는 보안 위험 및 가능한 공격 벡터를 초래합니다. 이러한 위험을 최소화하려면 빌드 타임 뿐만 아니라 런타임에 문제를 검색하고 해결하는 도구를 컨테이너에 통합해야 합니다. 프로세스에서 취약점 또는 오래된 기본 이미지를 더 일찍 찾아낼수록 클러스터가 더 안전해집니다. 이 문서에서 *컨테이너*는 컨테이너 레지스트리에 저장된 컨테이너와 실행 중인 컨테이너를 둘 다 의미합니다.

이 문서에서는 AKS의 컨테이너 보안 유지 방법을 중점적으로 설명합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 이미지 취약성 검색 및 해결
> * 기본 이미지가 업데이트될 때 컨테이너 이미지를 자동으로 트리거하고 다시 배포

[클러스터 보안][best-practices-cluster-security] 및 [pod 보안][best-practices-pod-security]에 대한 모범 사례를 읽을 수도 있습니다.

[Security Center의 컨테이너 보안][security-center-containers] 을 사용 하 여 컨테이너에서 취약점을 검색 하는 데 도움이 될 수도 있습니다.  또한 취약점 으로부터 이미지 및 레지스트리를 보호 하는 데 도움이 되는 Security Center와 [통합 Azure Container Registry][security-center-acr] 있습니다.

## <a name="secure-the-images-and-run-time"></a>이미지 및 런타임 보호

**모범 사례 지침** - 컨테이너 이미지의 취약성을 검사하고 유효성 검사를 통과한 이미지만 배포합니다. 정기적으로 기본 이미지 및 애플리케이션 런타임을 업데이트한 후 AKS 클러스터에서 워크로드를 다시 배포합니다.

컨테이너 기반 워크로드를 도입할 때는 사용자 고유의 애플리케이션을 빌드하는 데 사용되는 이미지 및 런타임의 보안 확인을 우선적으로 고려해야 합니다. 배포에 보안 취약성이 발생하지 않도록 하려면 어떻게 해야 하나요? 배포 워크플로에는 [Twistlock][twistlock] 또는 [바다색][aqua]과 같은 도구를 사용 하 여 컨테이너 이미지를 검색 한 다음 확인 된 이미지만 배포할 수 있는 프로세스가 포함 되어야 합니다.

![컨테이너 이미지 검색 및 수정, 유효성 검사 후 배포](media/operator-best-practices-container-security/scan-container-images-simplified.png)

실제 예제에서는 CI/CD(지속적인 통합/지속적인 배포) 파이프라인을 사용하여 이미지 검색, 확인 및 배포를 자동화할 수 있습니다. Azure Container Registry에는 이러한 취약성 검사 기능이 포함되어 있습니다.

## <a name="automatically-build-new-images-on-base-image-update"></a>기본 이미지 업데이트 시 새 이미지를 자동으로 빌드

**모범 사례 지침** - 애플리케이션 이미지에 대해 기본 이미지를 사용할 경우, 기본 이미지가 업데이트될 때 자동화를 통해 새 이미지를 빌드합니다. 이러한 기본 이미지에는 일반적으로 보안 수정 사항이 포함되어 있으므로 모든 다운스트림 애플리케이션 컨테이너 이미지를 업데이트합니다.

기본 이미지가 업데이트될 때마다 모든 다운스트림 컨테이너 이미지도 업데이트됩니다. 이 빌드 프로세스는 유효성 검사 및 배포 파이프라인 (예: [Azure Pipelines][azure-pipelines] 또는 Jenkins)에 통합 되어야 합니다. 이러한 파이프라인은 애플리케이션이 업데이트된 기본 이미지를 계속 실행하도록 합니다. 애플리케이션 컨테이너 이미지의 유효성이 검사되면 안전한 최신 이미지를 실행하도록 AKS 배포를 업데이트할 수 있습니다.

또한 기본 이미지가 업데이트되면 Azure Container Registry 작업도 컨테이너 이미지를 자동으로 업데이트할 수 있습니다. 이 기능을 사용하여 적은 수의 기본 이미지를 빌드하고, 버그 및 보안 수정 사항으로 정기적으로 업데이트할 수 있습니다.

기본 이미지 업데이트에 대한 자세한 내용은 [Azure Container Registry 작업을 사용 하 여 기본 이미지 업데이트 시 이미지 빌드 자동화][acr-base-image-update]를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 컨테이너를 보호하는 방법을 중점적으로 설명했습니다. 이러한 일부 영역을 구현하려면 다음 문서를 참조하세요.

* [Azure Container Registry 작업을 사용 하 여 기본 이미지 업데이트 시 이미지 빌드 자동화][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration