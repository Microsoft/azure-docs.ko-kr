---
title: Azure Marketplace IoT Edge 모듈 제품
description: Azure Marketplace에서 IoT Edge 모듈 제품 게시에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/18/2020
ms.openlocfilehash: c475e548cc8e29a83f2d440df43fb8ac31dc1c1e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816255"
---
# <a name="iot-edge-modules"></a>IoT Edge 모듈

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 플랫폼은 Microsoft Azure에서 지원됩니다.  사용자는 IoT 디바이스에서 직접 실행할 클라우드 워크로드를 배포할 수 있습니다.  IoT Edge 모듈은 오프라인 워크로드를 실행할 수 있으며 로컬에서 데이터 분석을 수행합니다. 이러한 유형의 플랫폼을 사용하면 대역폭을 절약하고 로컬 데이터와 중요한 데이터를 보호할 수 있으며 응답 시의 대기 시간을 줄일 수 있습니다.  이제는 미리 작성된 워크로드를 활용하는 옵션이 제공됩니다. 지금까지는 소수의 Microsoft 솔루션만 제공되었습니다.  그러므로 시간과 리소스를 투자하여 사용자 지정 IoT 솔루션을 직접 빌드해야 했습니다.

[Azure Marketplace의 IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)을 통해 이제 게시자는 하나의 플랫폼에서 IoT 고객을 대상으로 솔루션을 알리고 판매할 수 있습니다. 그리고 IoT 개발자는 솔루션을 신속하게 개발하는 데 사용할 수 있는 기능을 찾아서 구매할 수 있습니다.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure Marketplace의 IoT Edge 모듈이 제공하는 주요 이점

| **게시자**    | **고객(IoT 개발자)**  |
| :------------------- | :-------------------|
| IoT Edge 솔루션을 빌드 및 배포하고자 하는 수백만 명의 개발자에게 솔루션을 제공할 수 있습니다.  | 테스트를 통해 검증된 안전한 구성 요소를 사용하는 믿을 수 있는 IoT Edge 솔루션을 작성할 수 있습니다. |
| 솔루션을 한 번만 게시하면 컨테이너를 지원하는 모든 IoT Edge 하드웨어에서 실행할 수 있습니다. | 특정 요구 사항에 맞게 자사 및 타사 IoT Edge 모듈을 찾고 배포하여 출시 시간을 줄일 수 있습니다. |
| 유동적인 청구 옵션을 활용하여 수익을 창출할 수 있습니다. <ul> <li> 무료 및 BYOL(Bring Your Own License) 중에서 선택할 수 있습니다. </li> </ul> | 원하는 청구 모델을 선택하여 솔루션을 구매할 수 있습니다. <ul> <li> 무료 및 BYOL(Bring Your Own License) 중에서 선택할 수 있습니다. </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge 모듈 소개

Azure IoT Edge를 사용하면 에지에서 비즈니스 논리를 모듈 형태로 배포하고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 최소 계산 단위이며 Microsoft 서비스(예: Azure Stream Analytics), 타사 서비스 또는 고유한 솔루션별 코드를 포함할 수 있습니다. IoT Edge 모듈에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해](../iot-edge/iot-edge-modules.md)를 참조하세요.

**컨테이너 제품 유형과 IoT Edge 모듈 제품 유형 간의 차이점**

IoT Edge 모듈 제품 유형은 IoT Edge 디바이스에서 실행되는 특정 유형의 컨테이너로, IoT Edge 컨텍스트에서 실행하기 위한 기본 구성 설정이 적용된 상태로 제공되며 필요에 따라 IoT Edge 런타임과의 통합을 위해 IoT Edge 모듈 SDK를 사용합니다.

## <a name="publishing-your-iot-edge-module"></a>IoT Edge 모듈 게시

**올바른 온라인 저장소 선택**

IoT Edge 모듈은 Azure Marketplace에만 게시 됩니다. AppSource는 적용 되지 않습니다. 온라인 상점에서의 차이점과 대상 사용자에 대 한 자세한 내용은 [게시 옵션 결정](determine-your-listing-type.md)을 참조 하세요.

**청구 옵션**

Marketplace는 현재 IoT Edge 모듈에 대해 **무료** 하 고 **BYOL(Bring Your Own License)** 청구 옵션을 지원합니다.

### <a name="publishing-options"></a>게시 옵션

모든 경우에 IoT Edge 모듈은 **거래** 게시 옵션을 선택해야 합니다.  게시 옵션에 대한 자세한 내용은 [게시 옵션 선택](determine-your-listing-type.md)을 참조하세요.  

## <a name="eligibility-criteria"></a>자격 기준

IoT Edge 모듈 제품에는 Microsoft Azure Marketplace 계약 및 정책의 모든 약관이 적용됩니다.  또한 IoT Edge 모듈 관련 필수 구성 요소와 기술 요구 사항도 적용됩니다.  

### <a name="prerequisites"></a>사전 요구 사항

IoT Edge 모듈을 Azure Marketplace에 게시하려면 다음 필수 구성 요소를 갖춰야 합니다.

- 파트너 센터에 액세스합니다. 자세한 내용은 [Azure Marketplace 및 AppSource 게시 가이드](marketplace-publishers-guide.md)를 참조하세요.
- Azure Container Registry에서 IoT Edge 모듈 호스팅
- 다음과 같은 IoT Edge 모듈 메타데이터 준비(아래 목록에는 일부 메타데이터만 나와 있음)
    - 제목
    - 설명(HTML 형식)
    - 로고 이미지 (48 x 48 (선택 사항), 90 x 90 (선택 사항) 및 216 x 216에서 350 x 350 px까지 모두 PNG 형식으로)
    - 사용 약관 및 개인정보처리방침
    - 기본 모듈 구성(경로, 원하는 속성 쌍, createOptions, 환경 변수)
    - 문서화
    - 지원 연락처

### <a name="technical-requirements"></a>기술적인 요구 사항

IoT Edge 모듈 인증을 받고 Azure Marketplace에 모듈을 게시하기 위한 기본적인 기술 요구 사항은 [IoT Edge 모듈 기술 자산 준비](./partner-center-portal/create-iot-edge-module-asset.md)에 자세히 나와 있습니다.

## <a name="next-steps"></a>다음 단계

- 상업적 marketplace에 [대해 알아보세요](https://azuremarketplace.microsoft.com/sell) .
- 제품을 만들거나 완성할 수 있도록 [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)합니다.
- 파트너 센터에서 [IoT Edge 모듈 제품을 만듭니다](./partner-center-portal/azure-iot-edge-module-creation.md) .
