---
title: Azure Marketplace에 대한 Virtual Machine 제품 게시 가이드
description: 이 아티클에서는 Marketplace에서 배포할 가상 머신 및 소프트웨어 평가판을 게시하기 위한 요구 사항을 설명합니다.
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
ms.openlocfilehash: eb49d0fe610a78b8d1a407644a206fddaba0a67a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764104"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Virtual Machines 제품 게시 가이드

Virtual Machines 이미지는 Azure Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

Marketplace를 통해 배포되고 청구되는 트랜잭션 제품은 다음과 같습니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

## <a name="free-trial"></a>평가판 

BYOL(사용자 라이선스 필요) 청구 모델을 사용할 때 한정된 기간 소프트웨어 라이선스에 액세스하여 제품을 테스트하도록 사용자를 위해 정렬할 수 있습니다. 이 제품을 배포하는 요구 사항은 다음과 같습니다. 

|요구 사항  |세부 정보  |
|---------|---------|
|평가판 기간 및 체험 환경     |   고객은 제한된 기간 동안 앱을 체험해 볼 수 있습니다. 고객은 제품에 대한 라이선스 또는 구독 요금을 지급할 필요가 없습니다. 고객은 기본 Microsoft 자사 제품 또는 서비스에 대한 요금을 지급할 필요가 없습니다. 모든 평가판 옵션은 Azure 구독에 배포됩니다. 비용 최적화 및 관리를 단독으로 제어할 수 있습니다. 평가판 또는 대화형 데모 중에서 선택할 수 있습니다. 어떤 방식을 선택하든, 평가판은 추가 비용 없이 제품을 사용해볼 수 있는 미리 설정된 기간을 제공해야 합니다.|
|쉽게 구성 가능하고 즉시 사용할 수 있는 솔루션    |  앱은 구성 및 설정이 빠르고 쉬워야 합니다.       |
|가용성/작동 시간    |    SaaS 또는 플랫폼의 가동 시간은 최소 99.9%여야 합니다.     |
|Azure Active Directory     |    제품은 동의가 이루어질 경우 Azure AD(Azure Active Directory) 페더레이션 SSO(Single Sign-On)(Azure AD 페더레이션 SSO)를 허용해야 합니다.     |

## <a name="test-drive"></a>시험 사용

IaaS(Infrastructure-as-a-Service) 또는 SaaS(Software-as-a-Service) 앱을 통해 하나 이상의 가상 머신을 배포합니다. 시험 사용 게시 옵션의 이점은 파트너가 호스트하는 둘러보기를 통해 이루어지는 가상 머신 또는 전체 솔루션의 자동화된 프로비전이 가능하다는 것입니다. 시험 사용은 추가 비용 없이 고객에게 평가를 제공합니다. 고객은 평가판 환경을 사용하기 위해 기존 Azure 고객일 필요가 없습니다. 

시작하려면 [amp-testdrive](mailto:amp-testdrive@microsoft.com)를 통해 문의하세요. 

|요구 사항  |세부 정보 |
|---------|---------|
| Marketplace 앱이 있습니다.   |    IaaS 또는 SaaS를 통한 하나 이상의 가상 머신이 있습니다.      |

## <a name="interactive-demo"></a>대화형 데모

대화형 데모를 사용하여 솔루션의 안내 방식 환경을 고객에게 제공합니다. 대화형 데모 게시 옵션의 이점은 복잡한 솔루션에 대한 복잡한 프로비전 없이도 평가판 환경을 제공한다는 것입니다. 

## <a name="virtual-machine-offer"></a>Virtual Machine 제품

고객과 연결된 구독에 가상 어플라이언스를 배포할 경우 가상 머신 제품 형식을 사용합니다. VM은 종량제 또는 BYOL(사용자 라이선스 필요) 라이선스 모델을 사용하여 완전하게 거래될 수 있습니다. Microsoft는 사용자 대신, 상거래를 주도하고 고객에게 요금을 청구합니다. 사용자는 기업계약을 비롯하여 고객과 Microsoft 간에 선호하는 결제 방식을 채택할 수 있습니다.

> [!NOTE]
> 현재 기업계약과 연결된 금액 약정은 VM의 Azure 사용량에 대해 사용할 수 있지만, 소프트웨어 라이선스 요금에는 사용할 수 없습니다.  
> 
> [!NOTE]
> 이미지를 게시하고 개인 제품으로 가격 책정하여 고객의 특정 집합으로 VM의 배포 및 검색을 제한할 수 있습니다. 전용 제품은 사용자의 능력을 풀어주어 가장 가까운 고객에게 독점적인 혜택을 만들고 사용자 지정된 소프트웨어 및 사용 약관을 제공합니다. 이러한 사용자 지정된 사용 약관을 사용하면 사용자가 전문화된 가격 책정 및 조건뿐 아니라 제한된 릴리스 소프트웨어에 대한 초기 액세스를 사용하여 선도적인 거래를 포함한 다양한 시나리오를 강조 표시할 수 있습니다. 전용 제품을 통해 이러한 세부 정보로 새 SKU를 만들어 특정 가격 책정 또는 제품을 제한된 고객 집합에게 제공할 수 있습니다.  
> *   전용 제품에 대한 자세한 내용은 [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)에 있는 Azure Marketplace의 전용 제품 페이지를 참조하세요.  

| 요구 사항 | 세부 정보 |  
|:--- |:--- | 
| 청구 및 계량 | VM은 BYOL 또는 종량제 월간 청구를 지원해야 합니다. |  
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다. <ul> <li>Linux VHD를 만드는 방법에 대한 자세한 내용은 [Azure 보증 Linux 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 참조하세요.</li> <li>Windows VHD를 만드는 방법에 대한 자세한 내용은 [Azure 호환 VHD 만들기](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)를 참조하세요.</li> </ul> |  

>[!Note]
>클라우드 솔루션 공급자 (CSP) 파트너 채널 옵트인를 출시 되었습니다.  참조 하세요 [클라우드 솔루션 공급자](./cloud-solution-providers.md) Microsoft CSP를 통해 제품을 마케팅 하는 방법은 채널 파트너입니다.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않았다면 

- Marketplace에 [등록](https://azuremarketplace.microsoft.com/sell).

새 제품을 등록하고 만들거나 기존에서 작업하는 경우

- 제품을 만들거나 완료하기 위해 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com).
- 자세한 내용은 [가상 머신 제품](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer)을 참조하세요.
