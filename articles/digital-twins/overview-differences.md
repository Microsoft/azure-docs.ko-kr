---
title: 이전 버전과의 차이점
titleSuffix: Azure Digital Twins
description: 새 버전의 Azure Digital Twins에서 변경된 내용 이해
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6d1786766fc61ed0edfdc53295a50ba212818fc0
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181428"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>새로운 Azure Digital Twins란? 이전 버전(2018)과 어떻게 다릅니까?

Azure Digital Twins의 첫 번째 공개 미리 보기는 2018년 10월에 릴리스됐습니다. 이전 버전의 핵심 개념은 이제 공개 미리 보기의 새로운 서비스로 제공되었지만 서비스를 보다 유연하고 액세스할 수 있도록 하기 위해 많은 인터페이스 및 구현 세부 정보가 변경되었습니다. 이러한 변경 내용은 고객의 피드백에 따라 반영되었습니다.

> [!IMPORTANT]
> 새 서비스의 확장된 기능을 사용하는 경우 이전 Azure Digital Twins 서비스는 2020년 말에 사용 중지 됩니다.

이전 공개 미리 보기 중에 Azure Digital Twins의 첫 번째 버전을 사용한 경우 이 문서의 정보 및 모범 사례를 사용하여 새 서비스로 작업하고 해당 기능을 활용하는 방법을 알아보세요.

## <a name="differences-by-topic"></a>항목의 차이점

아래 차트에서는 이전 버전의 서비스와 새(현재) 서비스 간에 변경된 개념을 나란히 표시합니다.

| 항목 | 이전 버전 | 새 버전 |
| --- | --- | --- | --- |
| **모델링**<br>*보다 유연함* | 이전 릴리스는 스마트 공간을 중심으로 설계되었으므로 빌드에 대한 기본 제공 어휘와 함께 제공됩니다. | 새 Azure Digital Twins는 도메인 애그노스틱입니다. 솔루션에 대한 사용자 지정 어휘 및 사용자 지정 모델을 정의하여 보다 유연한 방식으로 더 많은 종류의 환경을 나타낼 수 있습니다.<br><br>[*개념: 사용자 지정 모델*](concepts-models.md)에서 자세히 알아보세요. |
| **토폴로지**<br>*보다 유연함*| 이전 릴리스에서는 스마트 공간에 맞게 조정된 트리 데이터 구조를 지원했습니다. Digital twins는 계층 관계와 연결되었습니다. | 새 릴리스부터 Digital twins는 원하는 대로 구성된 임의의 그래프 토폴로지에 연결할 수 있습니다. 이렇게 하면 실제 세계의 복잡한 관계를 보다 유연하게 표현할 수 있습니다.<br><br>[*개념: Digital Twins 및 쌍 그래프*](concepts-twins-graph.md)에서 자세히 알아보세요. |
| **컴퓨팅**<br>*보다 풍부하고 유연함* | 이전 릴리스에서는 이벤트 및 원격 분석을 처리하는 논리가 JavaScript UDF(사용자 정의 함수)에서 정의되었습니다. UDF를 사용한 디버깅이 제한적입니다. | 새 릴리스에는 개방형 계산 모델이 있습니다. [Azure Functions](../azure-functions/functions-overview.md)와 같은 외부 계산 리소스를 연결하여 사용자 지정 논리를 제공합니다. 이렇게 하면 사용자가 선택한 프로그래밍 언어를 사용하고, 제한 없이 사용자 지정 코드 라이브러리에 액세스하고, 외부 서비스에 포함될 수 있는 개발 및 디버깅 리소스를 활용할 수 있습니다.<br><br>[*방법: 데이터 처리를 위한 Azure 함수 설정*](how-to-create-azure-function.md)에서 자세히 알아보세요. |
| **IoT Hub를 사용하는 디바이스 관리**<br>*액세스 가능* | [IoT Hub](../iot-hub/about-iot-hub.md)의 인스턴스를 사용하는 이전 버전의 관리 장치는 Azure Digital Twins 서비스 내부에 있습니다. 이 통합 허브는 개발자에게 완전히 액세스할 수 없습니다. | 새 릴리스에서는 독립적으로 만들어진 IoT Hub 인스턴스(이미 관리하는 모든 장치 포함)를 연결하여 IoT hub를 "직접 가져오기" 할 수 있습니다. 이를 통해 IoT Hub 기능에 대한 모든 권한을 제공하고 장치 관리를 제어할 수 있습니다.<br><br>[*방법: IoT Hub에서 원격 분석 수집*](how-to-ingest-iot-hub-data.md)에서 자세히 알아보세요. |
| **보안**<br>*추가 표준* | 이전 릴리스에는 인스턴스에 대한 액세스를 관리하는 데 사용할 수 있는 미리 정의된 역할이 있습니다. | 새 릴리스는 다른 Azure 서비스에서 사용하는 것과 동일한 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md) 백 엔드 서비스와 통합됩니다. 이를 통해 IoT Hub, Azure Functions, Event Grid 등과 같은 솔루션의 다른 Azure 서비스 간에 더 쉽게 인증할 수 있습니다.<br>RBAC를 사용하면 미리 정의된 역할을 계속 사용하거나 사용자 지정 역할을 빌드 및 구성할 수 있습니다.<br><br>[*개념: Azure Digital Twins 솔루션 보안*](concepts-security.md)에서 자세히 알아보세요. |
| **확장성**<br>*더 큼* | 이전 릴리스에는 장치, 메시지, 그래프 및 배율 단위에 대한 크기 제한이 있었습니다. 구독당 Azure Digital Twins의 인스턴스는 하나만 지원됩니다.  | 새 릴리스는 확장성이 향상된 새 아키텍처를 사용하며 계산 능력이 더 큽니다. 또한 구독당 지역당 10개의 인스턴스를 지원합니다.<br><br>현재 공개 미리 보기 제한에 대한 자세한 내용은 [*참조: 공개 미리 서비스 제한*](reference-service-limits.md)을 참조하세요. |

## <a name="service-limits-in-public-preview"></a>공개 미리 보기 서비스 제한

이 공개 미리 보기 중에 Azure Digital Twins 제한 목록은 [*참조: 공개 미리 보기 서비스 제한*](reference-service-limits.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음으로 첫 번째 자습서를 사용하여 Azure Digital Twins를 사용하는 방법을 알아봅니다.

[*자습서: 클라이언트 앱 코딩*](tutorial-code.md)