---
title: Azure Container 이미지 제품 | Microsoft Docs
description: Azure Marketplace에서 컨테이너 제안을 게시하는 프로세스에 대한 개요입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pbutlerm
ms.openlocfilehash: e40e83e16ab2bfd43c3bb5fa38e52a778694e90e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473113"
---
# <a name="containers"></a>컨테이너

<table> <tr> <td>이 섹션에서는 <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>에 컨테이너 이미지를 게시하는 방법에 대해 설명합니다.  
컨테이너 제안 유형은 <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> 인스턴스 또는 <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a>로 프로비전되고 <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> 리포지토리에서 호스팅된 Docker 컨테이너 이미지를 지원합니다. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>제안 구성 요소

이 섹션에서는 컨테이너를 게시하는 요소에 대해 간략히 설명하고, Azure Marketplace의 게시자를 위한 지침으로 사용됩니다. 게시는 다음과 같은 주요 부분으로 나뉩니다.

- [필수 조건](./cpp-prerequisites.md) - 컨테이너 제품을 만들거나 게시하기 전에 기술 및 비즈니스 요구 사항을 나열합니다.
- [제안 만들기](./cpp-create-offer.md) - Cloud 파트너 포털을 사용하여 새 컨테이너 제안 항목을 만드는 데 필요한 단계를 나열합니다.
- [기술 자산 준비](./cpp-create-technical-assets.md) - Azure Marketplace에서 제안으로 컨테이너 솔루션에 대한 기술 자산을 만드는 방법입니다.
- [제안 게시](./cpp-publish-offer.md) - Azure Marketplace에 게시할 제안을 제출하는 방법입니다.

## <a name="container-publishing-process"></a>컨테이너 게시 프로세스

다음 다이어그램에서는 VM 제안을 게시하는 고급 단계를 보여 줍니다.
![제안 게시 단계](./media/containers-offer-process.png)

컨테이너 제안을 게시하기 위한 대략적인 단계는 다음과 같습니다.

1. 제안 만들기 - 제안에 대한 자세한 정보를 제공합니다. 이 정보는 제안 설명, 마케팅 자료, 지원 정보 및 자산 사양을 포함합니다.
2. 비즈니스 및 기술 자산 만들기 - 연결된 솔루션에 대한 비즈니스 자산(법률 문서 및 마케팅 자료) 및 기술 자산(Azure Container Registry에서 호스트되는 컨테이너 이미지)을 만듭니다.
3. SKU 만들기 - 제안과 연결된 SKU를 만듭니다. 게시하려는 각 이미지마다 고유한 SKU가 필요합니다.
4. 제안 인증 및 게시 - 제안 및 기술 자산이 완성되면 해당 제안을 제출할 수 있습니다. 이 제출은 게시 프로세스를 시작합니다. 이 프로세스 중 솔루션을 테스트하고, 유효성을 검사한 다음, Azure Marketplace에서 "라이브 상태가 됩니다".

## <a name="next-steps"></a>다음 단계

이러한 단계를 고려하기 전에 컨테이너를 Microsoft Azure Marketplace에 게시하기 위한 [기술 및 비즈니스 요구 사항](./cpp-prerequisites.md)이 충족되어야 합니다.