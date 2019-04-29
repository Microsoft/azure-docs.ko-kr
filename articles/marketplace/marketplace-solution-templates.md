---
title: Azure 애플리케이션 솔루션 템플릿 제품 게시 가이드
description: 이 문서에서는 솔루션 템플릿을 Azure Marketplace에 게시하기 위한 요구 사항을 설명합니다.
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
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: bbf9ab56bd7e070e40e2454a69f0a5e313597f14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765502"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure 애플리케이션: 솔루션 템플릿 제품 게시 가이드

솔루션 템플릿은 Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

솔루션에 단일 VM 이외의 추가 배포 및 구성 자동화가 필요한 경우, Azure 앱: 솔루션 템플릿 제품 유형을 사용합니다. Azure 앱: 솔루션 템플릿을 사용하여 하나 이상의 VM 프로비전을 자동화할 수 있습니다. 네트워킹 및 저장소 리소스를 프로비전할 수도 있습니다. Azure 앱: 솔루션 템플릿 제품 형식은 단일 VM 및 전체 IaaS 기반 솔루션에 대한 자동화 이점을 제공합니다.

이러한 솔루션 템플릿은 Marketplace를 통해 배포되고 청구되는 트랜잭션 제품입니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.


## <a name="requirements-for-solution-templates"></a>솔루션 템플릿에 대한 요구 사항

| **요구 사항** | **세부 정보**  |
| ---------------  | -----------  |
|청구 및 계량    |  고객의 Azure 구독에서 리소스를 프로비전합니다. PAYGO(종량제) 가상 머신은 Microsoft를 통해 고객과 거래되고 고객의 Azure 구독(PAYGO)을 통해 청구됩니다.  <br/> BYOL(사용자 라이선스 필요)의 경우 Microsoft가 고객 구독에서 발생하는 인프라 비용을 청구하는 한편, 소프트웨어 라이선스 요금은 고객과 직접 거래합니다.   |
|Azure 호환 VHD(가상 하드 디스크)  |   VM은 Windows 또는 Linux에서 빌드해야 합니다.  자세한 내용은 [Azure 호환 VHD 만들기](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)를 참조하세요. |
| 고객 사용량 특성 | 고객 사용량 특성 활성화는 Azure Marketplace에 게시된 모든 솔루션 템플릿에 필요합니다. 고객 사용량 특성과 활성화 방법에 대한 자세한 내용은 [Azure 파트너 고객 사용량 특성](./azure-partner-customer-usage-attribution.md)을 참조하세요.  |
|  |  |

## <a name="next-steps"></a>다음 단계
아직 수행하지 않은 경우 Marketplace에 [등록](https://azuremarketplace.microsoft.com/sell)합니다.

이미 등록되어 있으며 새로운 제품을 만들거나 기존 제품으로 작업하는 경우 [Cloud 파트너 포털](https://cloudpartner.azure.com)에 로그인하여 제품을 만들거나 완료합니다.
