---
title: Azure Marketplace에서 게시 옵션 결정 | Azure
description: 이 문서에서는 Azure Marketplace의 앱을 게시하는 방법을 이해하려는 파트터의 자격 기준 및 게시 요구 사항에 대해 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/13/2018
ms.author: qianw211
ms.openlocfilehash: 9dd5b3232184170a59b82d5cafdc1a4cdcb4c172
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733796"
---
# <a name="publishing-guide-by-offer-type"></a>제품 형식별 게시 가이드

[게시 옵션을 결정](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)했으면 제품을 제공하는 데 사용할 제품 유형을 선택할 준비가 된 것입니다. 

제품은 [Cloud 파트너 포털](https://cloudpartner.azure.com)에서 만들고 게시하고 관리할 게시 개체입니다. Marketplace에 제품을 제공하는 데 사용된 메타데이터, 아티팩트 및 기타 콘텐츠를 포함한 제품 구조는 *제품 유형*에 의해 정의됩니다.

제품을 만들기 전에 제품 유형을 선택해야 합니다. 제품 유형은 게시하려는 솔루션, 앱 또는 서비스 제품의 유형뿐만 아니라 Microsoft 제품 및 서비스에 대한 맞춤도 해당됩니다. 

단일 제품 유형은 다른 게시 옵션, CTA(calls-to-action), 프로비전 또는 가격 책정을 사용하도록 설정하려면 Cloud 파트너 포털에서 달리 구성될 수 있습니다. 제품 유형의 게시 옵션과 구성은 제품 자격 및 기술 요구 사항에도 맞춥니다. 

제품을 만들기 전에 상점 및 제품 유형 자격 요구 사항 및 기술 게시 요구 사항을 검토해야 합니다.

## <a name="list-of-offer-types"></a>제품 유형 목록

Azure Marketplace 제품 유형은 아래 표에 나열되어 있습니다.

| **제품 유형**    | **설명**  |
| :------------------- | :-------------------|
| [**가상 컴퓨터**](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) | 고객과 연결된 구독에 가상 어플라이언스를 배포할 경우 가상 머신 제품 형식을 사용합니다. |
| [**솔루션 템플릿**](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates) | 솔루션에 단순 VM 이외의 추가 배포 및 구성 자동화가 필요한 경우, 솔루션 템플릿 제품 유형을 사용합니다. |
| [**관리형 애플리케이션**](https://docs.microsoft.com/azure/marketplace/marketplace-managed-apps) | 다음 조건이 필요한 경우 Azure 앱: 관리되는 앱 제품 형식을 사용합니다. <br> <ul> <li> VM 또는 전체 IaaS 기반 솔루션을 사용하여 고객에게 구독 기반 솔루션을 배포합니다. </li> <li>사용자 또는 고객이 솔루션을 파트너가 관리하도록 요구합니다. </li> <ul> |
| [**SaaS 애플리케이션**](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide) | SaaS 앱 제품 유형을 사용하면 고객이 SaaS 기반, 기술 솔루션을 구독으로 구매할 수 있습니다. |
| [**컨테이너 제품**](https://docs.microsoft.com/azure/marketplace/marketplace-containers) | 솔루션이 Kubernetes 기반 Azure Container Service로 프로비전된 Docker 컨테이너 이미지인 경우 컨테이너 제품 유형을 사용합니다. |
| [**IoT Microsoft Edge 모듈**](https://docs.microsoft.com/azure/marketplace/iot-edge-module) | Azure IoT Edge 모듈은 IoT Edge가 관리하는 최소 계산 단위이며 Microsoft 서비스(예: Azure Stream Analytics), 타사 서비스 또는 고유한 솔루션별 코드를 포함할 수 있습니다. |
| [**컨설팅 서비스**](https://docs.microsoft.com/azure/marketplace/consulting-services) | Azure Marketplace의 컨설팅 서비스는 고객과 서비스를 연결하여 고객의 Azure 사용을 지원하고 확대하는 데 유용합니다. |
| [**AppSource 제품**](https://docs.microsoft.com/azure/marketplace/appsource-offer-publishing-guide) | AppSource 상점에는 Dynamics 365, Office 365, Power BI 및 Power Apps에서 빌드하거나 확장하는 제품이 포함됩니다. |

마지막으로, Single Sign-On 요구 사항에 대한 자세한 내용은 옵션 및 제품 유형이 나열된 [**Azure AD 요구 사항**](https://docs.microsoft.com/azure/marketplace/enable-appsource-marketplace-using-azure-ad)을 참조하세요.

## <a name="next-steps"></a>다음 단계

*   제품의 선택 및 구성을 완료하려면 제품 유형 섹션별 게시 옵션에서 자격 요구 사항을 검토합니다.
*   솔루션이 제품 유형 및 구성에 매핑되는 방법에 대한 예제는 상점별 게시 패턴을 검토합니다.
*   제품을 만들고 구성하려면 [Cloud 파트너 포털](https://cloudpartner.azure.com)에 로그인합니다.
