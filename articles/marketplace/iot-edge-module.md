---
title: Azure IoT Edge 모듈
description: 앱 및 서비스 게시자용으로 Azure Marketplace에서 제공되는 IoT Edge 모듈에 대해 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
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
ms.date: 09/22/2018
ms.author: qianw211
ms.openlocfilehash: f13d3f780e24d71babb1bda48a9b85d1208e3c4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709300"
---
# <a name="iot-edge-modules"></a>IoT Edge 모듈

Azure Cloud를 통해 지원되는 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 플랫폼에서  사용자는 IoT 디바이스에서 직접 실행할 클라우드 워크로드를 배포할 수 있습니다.  IoT Edge 모듈은 오프라인 워크로드를 실행할 수 있으며 로컬에서 데이터 분석을 수행합니다. 이러한 유형의 플랫폼을 사용하면 대역폭을 절약하고 로컬 데이터와 중요한 데이터를 보호할 수 있으며 응답 시의 대기 시간을 줄일 수 있습니다.  이제는 미리 작성된 워크로드를 활용하는 옵션이 제공됩니다. 지금까지는 소수의 Microsoft 솔루션만 제공되었습니다.  그러므로 시간과 리소스를 투자하여 사용자 지정 IoT 솔루션을 직접 빌드해야 했습니다.

하지만 이제는 [Azure Marketplace에서 IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)이 제공되므로 게시자는 하나의 플랫폼에서 IoT 고객을 대상으로 솔루션을 알리고 판매할 수 있습니다. 그리고 IoT 개발자는 솔루션을 신속하게 개발하는 데 사용할 수 있는 기능을 찾아서 구매할 수 있습니다.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure Marketplace의 IoT Edge 모듈이 제공하는 주요 이점은 다음과 같습니다.

| **게시자**    | **고객(IoT 개발자)**  |
| :------------------- | :-------------------|
| IoT Edge 솔루션을 빌드 및 배포하고자 하는 수백만 명의 개발자에게 솔루션을 제공할 수 있습니다.  | 테스트를 통해 검증된 안전한 구성 요소를 사용하는 믿을 수 있는 IoT Edge 솔루션을 작성할 수 있습니다. |
| 솔루션을 한 번만 게시하면 컨테이너를 지원하는 모든 IoT Edge 하드웨어에서 실행할 수 있습니다. | 특정 요구 사항에 맞게 자사 및 타사 IoT Edge 모듈을 찾고 배포하여 출시 시간을 줄일 수 있습니다. |
| 유동적인 청구 옵션을 활용하여 수익을 창출할 수 있습니다. <ul> <li> 무료 및 BYOL(Bring Your Own License) 중에서 선택할 수 있습니다. </li> </ul> | 원하는 청구 모델을 선택하여 솔루션을 구매할 수 있습니다. <ul> <li> 무료 및 BYOL(Bring Your Own License) 중에서 선택할 수 있습니다. </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge 모듈 소개

Azure IoT Edge를 사용하면 에지에서 비즈니스 논리를 모듈 형태로 배포하고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 최소 계산 단위이며 Microsoft 서비스(예: Azure Stream Analytics), 타사 서비스 또는 고유한 솔루션별 코드를 포함할 수 있습니다. IoT Edge 모듈에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)를 참조하세요.

**컨테이너 제품 유형과 IoT Edge 모듈 제품 유형 간의 차이점**

IoT Edge 모듈 제품 유형은 IoT Edge 디바이스에서 실행되는 특정 유형의 컨테이너로, IoT Edge 컨텍스트에서 실행하기 위한 기본 구성 설정이 적용된 상태로 제공되며 필요에 따라 IoT Edge 런타임과의 통합을 위해 IoT Edge 모듈 SDK를 사용합니다.

## <a name="publishing-your-iot-edge-module"></a>IoT Edge 모듈 게시

**적절한 상점 선택**

IoT Edge 모듈은 Azure Marketplace에만 게시되며 AppSource는 적용되지 않습니다.  상점 간의 차이점과 각 상점의 대상 사용자에 대한 자세한 내용은 [솔루션 게시 옵션 결정](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)을 참조하세요.
 
**청구 옵션**

Marketplace는 현재 IoT Edge 모듈에 대해 **무료** 하 고 **BYOL(Bring Your Own License)** 청구 옵션을 지원합니다.
 
**게시 옵션**

모든 경우에 IoT Edge 모듈은 **거래** 게시 옵션을 선택해야 합니다.  게시 옵션에 대한 자세한 내용은 [게시 옵션 선택](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)을 참조하세요.  

## <a name="eligibility-criteria"></a>자격 기준

IoT Edge 모듈 제품에는 Microsoft Azure Marketplace 계약 및 정책의 모든 약관이 적용됩니다.  또한 IoT Edge 모듈 관련 필수 구성 요소와 기술 요구 사항도 적용됩니다.  

**필수 구성 요소**

IoT Edge 모듈을 Azure Marketplace에 게시하려면 다음 필수 구성 요소를 갖춰야 합니다.

- CPP(Cloud 파트너 포털) 액세스 권한. 자세한 내용은 [Azure Marketplace 및 AppSource 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)를 참조하세요.
- Azure Container Registry에서 IoT Edge 모듈 호스팅 
- 다음과 같은 IoT Edge 모듈 메타데이터 준비(아래 목록에는 일부 메타데이터만 나와 있음) 
    - 제목
    - 설명(HTML 형식)
    - 로고 이미지(PNG 형식 및 40x40px, 90x90px, 115x115px, 255x115px를 포함한 고정 이미지 크기)
    - 사용 약관 및 개인정보처리방침
    - 기본 모듈 구성(경로, 원하는 속성 쌍, createOptions, 환경 변수)
    - 문서화
    - 지원 연락처

**기술 요구 사항**

IoT Edge 모듈 인증을 받고 Azure Marketplace에 모듈을 게시하기 위한 기본적인 기술 요구 사항은 [IoT Edge 모듈 기술 자산 준비](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)에 자세히 나와 있습니다.  

## <a name="documentation-and-resources"></a>설명서 및 리소스

[IoT Edge 모듈 제품 만들기](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) - 클라우드 게시 포털에서 새 IoT Edge 모듈 제품을 게시하는 단계가 나와 있습니다.

## <a name="next-steps"></a>다음 단계

아래 작업을 아직 수행하지 않았다면 수행합니다.

- [Microsoft 파트너 네트워크](https://partner.microsoft.com/membership)에 등록합니다.
- [Microsoft 계정](https://account.microsoft.com/account/)을 만듭니다(Azure Marketplace 거래 제품의 경우 필수, 다른 사용자도 만드는 것이 좋음).
- [Marketplace 등록 양식](https://azuremarketplace.microsoft.com/sell/signup)을 제출합니다.

새 제품을 등록하고 만들거나 기존에서 작업하는 경우

- 제품을 만들거나 완성할 수 있도록 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com/)합니다.
- IoT Edge 모듈 제품을 게시하는 방법에 대한 정보는 [IoT Edge 모듈 제품 게시 개요](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts)를 참조하세요.
