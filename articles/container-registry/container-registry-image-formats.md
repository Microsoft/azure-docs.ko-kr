---
title: 지원 되는 콘텐츠 형식
description: Docker 호환 컨테이너 이미지, 투구 차트, OCI 이미지 및 OCI 아티팩트를 비롯 하 여 Azure Container Registry에서 지 원하는 콘텐츠 형식에 대해 알아봅니다.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455014"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry에서 지원되는 콘텐츠 서식

Azure Container Registry에서 프라이빗 리포지토리를 사용하여 다음 콘텐츠 서식 중 하나를 관리합니다. 

## <a name="docker-compatible-container-images"></a>Docker 호환 컨테이너 이미지

다음 Docker 컨테이너 이미지 형식이 지원 됩니다.

* [Docker 이미지 매니페스트 V2, 스키마 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 이미지 매니페스트 V2, 스키마 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 레지스트리가 단일 "image:tag" 참조에서 다중 플랫폼 이미지를 저장할 수 있는 매니페스트 목록이 포함됩니다.

## <a name="oci-images"></a>OCI 이미지

Azure Container Registry은 [OCI (Open Container 이니셔티브) 이미지 형식 사양을](https://github.com/opencontainers/image-spec/blob/master/spec.md)만족 하는 이미지를 지원 합니다. 패키지 형식에는 [SIF (특이성 Image Format)](https://github.com/sylabs/sif)가 포함 됩니다.

## <a name="oci-artifacts"></a>OCI 아티팩트

Azure Container Registry는 컨테이너 이미지 및 기타 아티팩트 (콘텐츠 형식)를 저장, 공유, 보호 및 배포할 수 있는 공급 업체 중립적인 클라우드 독립적인 사양의 [OCI 배포 사양을](https://github.com/opencontainers/distribution-spec)지원 합니다. 사양을 통해 레지스트리는 컨테이너 이미지 뿐만 아니라 광범위 한 아티팩트를 저장할 수 있습니다. 아티팩트를 푸시하고 풀 하는 데 적합 한 도구를 사용 합니다. 예제는 [Azure container registry를 사용 하 여 OCI 아티팩트 푸시 및 끌어오기](container-registry-oci-artifacts.md)를 참조 하세요.

OCI 아티팩트에 대한 자세한 내용은 GitHub에서 [저장소 (oras)](https://github.com/deislabs/oras) 리포지토리 및 [oci 아티팩트](https://github.com/opencontainers/artifacts) 리포지토리를 참조 하세요.

## <a name="helm-charts"></a>Helm 차트

Kubernetes에 대한 응용 프로그램을 신속 하 게 관리 하 고 배포 하는 데 사용 되는 패키징 형식인 [투구 차트](https://helm.sh/)의 리포지토리를 호스트할 수 Azure Container Registry. [투구 클라이언트](https://docs.helm.sh/using_helm/#installing-helm) 버전 2 (2.11.0 이상)가 지원 됩니다.

## <a name="next-steps"></a>다음 단계

* Azure Container Registry를 사용하여 이미지를 [푸시하고 끌어오는](container-registry-get-started-docker-cli.md) 방법을 참조하세요.

* [ACR 작업](container-registry-tasks-overview.md)을 사용하여 컨테이너 이미지를 빌드하고 테스트합니다. 

* [ BuildKit](https://github.com/moby/buildkit)를 사용하여 OCI 형식으로 컨테이너를 빌드하고 패키징합니다.

* Azure Container Registry에서 호스트되는 [Helm 리포지토리](container-registry-helm-repos.md)를 설정합니다. 


