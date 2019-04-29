---
title: Azure 애플리케이션 관리되는 애플리케이션 제품 게시 가이드
description: 이 아티클에서는 관리되는 애플리케이션을 Marketplace에 게시하기 위한 요구 사항을 설명합니다.
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
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: 15c559bb8b357b7776c101f88db8316b05edb677
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764240"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure 애플리케이션: 관리형 애플리케이션 제품 게시 가이드

관리되는 애플리케이션은 Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

Marketplace를 통해 배포되고 청구되는 트랜잭션 제품은 다음과 같습니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

다음 조건이 필요한 경우 Azure 앱: 관리되는 앱 제품 형식을 사용합니다.
- VM 또는 전체 IaaS 기반 솔루션을 사용하여 고객에게 구독 기반 솔루션을 배포합니다.
- 사용자 또는 고객이 솔루션을 파트너가 관리하도록 요구합니다.

>[!NOTE]
>예를 들어, 파트너는 SI 또는 MSP(관리 서비스 제공자)일 수 있습니다.  

## <a name="managed-application-offer"></a>관리되는 애플리케이션 제품

|요구 사항 |세부 정보  |
|---------|---------|
|고객의 Azure 구독에 배포 | 관리되는 앱은 고객의 구독에 배포되어야 하고 타사에서 관리될 수 있습니다. | 
|청구 및 계량    |  고객의 Azure 구독에서 리소스를 프로비전합니다. 종량제(PAYGO) 가상 머신은 Microsoft를 통해 고객과 거래되고 고객의 Azure 구독(PAYGO)을 통해 청구됩니다. 
BYOL(사용자 라이선스 필요)의 경우 Microsoft가 고객 구독에서 발생하는 인프라 비용을 청구하는 동안 소프트웨어 라이선스 요금을 고객과 직접 거래합니다.        |
|Azure 호환 VHD(가상 하드 디스크)    |   VM은 Windows 또는 Linux에서 빌드해야 합니다.<ul> <ul> <li>Linux VHD를 만드는 방법에 대한 자세한 내용은 [Azure 보증 Linux 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 참조하세요.</li> <li>Windows VHD를 만드는 방법에 대한 자세한 내용은 [Azure 호환 VHD 만들기](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)를 참조하세요.</li> </ul> |

>[!NOTE]
> 관리되는 앱은 마켓플레이스를 통해 배포 가능해야 합니다. 고객 커뮤니케이션이 중요한 경우, 잠재 고객 공유를 사용하도록 설정한 후 관심 있는 고객에게 연결해야 합니다.  

>[!Note]
>클라우드 솔루션 공급자 (CSP) 파트너 채널 옵트인를 출시 되었습니다.  참조 하세요 [클라우드 솔루션 공급자](./cloud-solution-providers.md) Microsoft CSP를 통해 제품을 마케팅 하는 방법은 채널 파트너입니다.

## <a name="next-steps"></a>다음 단계
아직 수행하지 않았다면 

- Marketplace에 [등록](https://azuremarketplace.microsoft.com/sell).

새 제품을 등록하고 만들거나 기존에서 작업하는 경우

- 제품을 만들거나 완료하기 위해 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com).
