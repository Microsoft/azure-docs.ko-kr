---
title: Azure IoT Central 애플리케이션을 CSP로 만들기 및 관리 | Microsoft Docs
description: CSP로서 고객을 대신하여 Azure IoT Central 애플리케이션을 만드는 방법.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/03/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8c73c55b059b4d3ca7136889122c11ab332b3f76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886383"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>CSP로서 고객을 대신하여 Azure IoT Central 애플리케이션 만들기 및 관리 

Microsoft CSP(클라우드 솔루션 공급자) 프로그램은 Microsoft 재판매인 프로그램입니다. 이의 의도는 모든 Microsoft 상용 온라인 서비스를 재판매하기 위한 원스톱 프로그램을 채널 파트너에게 제공하는 것입니다. [클라우드 솔루션 공급자 프로그램](https://partner.microsoft.com/cloud-solution-provider)을 자세히 알아봅니다.

CSP로서 고객을 대신해 [Microsoft 파트너 센터](https://partnercenter.microsoft.com/partner/home)를 통해 Microsoft Azure IoT Central 애플리케이션을 만들고 관리할 수 있습니다. CSP가 고객을 대신해 Azure IoT Central 애플리케이션을 만들 경우 다른 CSP 관리 Azure 서비스와 마찬가지로 CSP가 고객에 대한 청구를 관리합니다. Azure IoT Central에 대한 요금은 Microsoft 파트너 센터의 총 청구서에 표시됩니다.

시작하려면 Microsoft 파트너 포털의 계정에 로그인하고 Azure IoT Central 애플리케이션을 만들어 주려는 고객을 선택합니다. 왼쪽 탐색에서 고객에 대한 서비스 관리로 이동합니다.

![Microsoft 파트너 센터, 고객 보기](media/howto-create-application-asCSP/image1.png)

Azure IoT Central은 관리할 수 있는 서비스로 나열됩니다. 새 응용 프로그램을 만들거나이 고객에 대 한 기존 응용 프로그램 관리 페이지에서 Azure IoT Central 링크를 선택 합니다.

![관리할 수 있는 Azure IoT Central](media/howto-create-application-asCSP/image2.png)

Azure IoT Central 애플리케이션 관리자 페이지로 이동합니다. Azure IoT Central은 Microsoft 파트너 센터에서 제공하고 해당 특정 고객을 관리하기 위해 제공된 컨텍스트를 유지합니다. 애플리케이션 관리자 페이지의 헤더에 승인이 표시됩니다. 여기에서 이 고객을 위해 전에 만든 기존 애플리케이션으로 이동하거나 고객을 위한 새 애플리케이션을 만들 수 있습니다.

![CSP를 위한 관리자 만들기](media/howto-create-application-asCSP/image3.png)

Azure IoT Central 응용 프로그램을 만들려면 선택 합니다 **새 응용 프로그램** 바둑판식으로 배열 합니다. 그러면 애플리케이션 만들기 페이지가 로드됩니다. 이 페이지의 모든 필드를 완료한 다음, **만들기**를 선택해야 합니다. 아래에 각 필드에 대한 자세한 내용이 있습니다.

![CSP에 대한 애플리케이션 페이지 만들기](media/howto-create-application-asCSP/image4.png)

![CSP에 대한 애플리케이션 페이지 만들기](media/howto-create-application-asCSP/image4-1.png)

## <a name="payment-plan"></a>결제 계획

CSP로서 종량제 애플리케이션만 만들 수 있습니다. 고객에게 Azure IoT Central을 소개하려면 평가판 애플리케이션을 별도로 만들 수 있습니다. [Azure IoT Central 가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-central/)에서 평가판 및 종량제 애플리케이션에 대해 자세히 알아보세요.

## <a name="application-name"></a>애플리케이션 이름

애플리케이션 이름은 **애플리케이션 관리자** 페이지 그리고 각 Azure IoT Central 애플리케이션 내에서 표시됩니다. Azure IoT Central 애플리케이션의 이름으로 원하는 이름을 선택할 수 있습니다. 본인에게 그리고 조직 내 다른 사람들에게 의미 있는 이름을 선택하면 됩니다.

## <a name="application-url"></a>애플리케이션 URL

애플리케이션 URL은 애플리케이션의 링크입니다. 브라우저에서 URL의 책갈피를 저장하거나 다른 사람들과 공유할 수 있습니다.

애플리케이션의 이름을 입력하면 애플리케이션 URL이 자동으로 생성됩니다. 원한다면 애플리케이션의 다른 URL을 선택해도 됩니다. 각 Azure IoT Central URL은 Azure IoT Central 내에서 고유해야 합니다. 선택한 URL이 이미 사용 중인 경우 오류 메시지가 표시됩니다.

## <a name="directory"></a>디렉터리

Azure IoT Central에 Microsoft 파트너 포털에서 선택한 고객을 관리하기 위해 제공된 컨텍스트가 있으므로 디렉터리 필드에서 해당 고객에 대한 Azure Active Directory 테넌트를 참조합니다. 

Azure Active Directory 테넌트에는 사용자 ID, 자격 증명 및 기타 조직 정보가 포함됩니다. 단일 Azure Active Directory 테넌트에 여러 Azure 구독을 연결할 수 있습니다.

자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)를 참조하세요.

## <a name="azure-subscription"></a>Azure 구독

Azure 구독을 사용하여 Azure 서비스 인스턴스를 만들 수 있습니다. Azure IoT Central은 액세스 권한이 있는 고객의 모든 Azure 구독을 자동으로 찾아서 **애플리케이션 만들기** 페이지의 드롭다운에 표시합니다. 새 Azure IoT Central 애플리케이션을 만들 Azure 구독을 선택하세요.

Azure 구독이 아직 없는 경우 Microsoft 파트너 센터에서 만들 수 있습니다. Azure 구독을 만든 후 다시 **애플리케이션 만들기** 페이지로 돌아갑니다. **Azure 구독** 드롭다운에 새 구독이 표시됩니다.

자세한 내용은 [Azure 구독](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)을 참조하세요.

## <a name="region"></a>지역

Azure IoT Central 애플리케이션을 만들 지역을 선택합니다. 일반적으로 최적의 성능을 얻으려면 디바이스와 물리적으로 가장 가까운 지역을 선택해야 합니다.

자세한 내용은 [Azure 지역](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions)을 참조하세요.

Azure IoT Central을 사용할 수 있는 지역은 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 페이지에서 확인할 수 있습니다.

> [!Note]
> 일단 지역을 선택하면 응용 프로그램을 다른 지역으로 이동할 수 없습니다.

## <a name="application-template"></a>애플리케이션 템플릿

새 Azure IoT Central 애플리케이션에 사용 가능한 애플리케이션 템플릿 중 하나를 선택할 수 있습니다. 응용 프로그램 템플릿에는 시작하는 데 도움이 되는 디바이스 템플릿이나 대시보드 같은 미리 정의된 항목이 포함될 수 있습니다.

| 애플리케이션 템플릿 | 설명 |
| -------------------- | ----------- |
| 사용자 지정 애플리케이션   | 사용자 고유의 장치 템플릿 및 장치로 채울 빈 애플리케이션을 만듭니다. |
| 샘플 Contoso       | 간단한 연결된 장치를 만들 수 있는 장치 템플릿이 포함된 애플리케이션을 만듭니다. 이 템플릿을 사용하여 Azure IoT Central 탐색을 시작하세요. |
| 샘플 Devkits       | MXChip 또는 Raspberry Pi 장치를 연결할 수 있는 장치 템플릿을 사용하여 애플리케이션을 만듭니다. 다음 디바이스 중 하나에서 코드를 실험하는 디바이스 개발자인 경우 이 템플릿을 사용하세요. |

## <a name="next-steps"></a>다음 단계

CSP로서 Azure IoT Central 애플리케이션을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)