---
title: Azure Marketplace에 대한 컨테이너 제품 게시 가이드
description: 이 아티클에서는 Marketplace에서 컨테이너를 게시하기 위한 요구 사항을 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: e81f2b42e725e50473c09622a486586fb6721ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882183"
---
# <a name="containers-offer-publishing-guide"></a>컨테이너 제품 게시 가이드

컨테이너 제품을 통해 컨테이너 이미지를 Azure Marketplace에 게시할 수 있습니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

Marketplace를 통해 배포되고 청구되는 트랜잭션 제품은 다음과 같습니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

솔루션이 Kubernetes 기반 Azure Container Service로 프로비전된 Docker 컨테이너 이미지인 경우 컨테이너 제품 유형을 사용합니다.

>[!NOTE]
>예를 들어, Kubernetes 기반 컨테이너 런타임의 Azure 고객 선택인 Azure Kubernetes Service 또는 Azure Container Instances 같은 Kubernetes 기반 Azure Container Service입니다.  

Microsoft는 현재 무료 및 BYOL(사용자 라이선스 필요) 라이선스 모델을 지원합니다.

## <a name="containers-offer"></a>컨테이너 제품

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 청구 및 계량 | 무료 또는 BYOL 청구 모델을 지원합니다. |  
| Dockerfile에서 작성된 이미지 | 컨테이너 이미지는 Docker 이미지 사양을 기반으로 해야 하며 Dockerfile에서 작성해야 합니다.<ul> <li>Docker 이미지를 빌드하는 방법에 대한 자세한 내용은 [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage)에 있는 사용법 섹션으로 이동합니다.</li> </ul> |  
| ACR에서 호스팅 | 컨테이너 이미지는 Azure Container Registry(ACR) 리포지토리에서 호스팅되어야 합니다.<ul> <li>ACR을 사용하는 방법에 대한 자세한 내용은 [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)에 있는 빠른 시작: Azure Portal 페이지를 사용하여 컨테이너 레지스트리 만들기로 이동합니다.</li> </ul> |  
| 이미지 태그 지정 | 컨테이너 이미지는 최소 1개 태그를 포함해야 합니다(최대 태그: 16).<ul> <li>이미지를 태그하는 방법에 대한 자세한 내용은 [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag)에 있는 Docker 태그 페이지로 이동합니다.</li> </ul> |  

## <a name="next-steps"></a>다음 단계

아직 수행하지 않았다면 

- Marketplace에 [등록](https://azuremarketplace.microsoft.com/sell).

새 제품을 등록하고 만들거나 기존에서 작업하는 경우

- 제품을 만들거나 완료하기 위해 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com).
- 자세한 내용은 [컨테이너](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer)를 참조하세요.
