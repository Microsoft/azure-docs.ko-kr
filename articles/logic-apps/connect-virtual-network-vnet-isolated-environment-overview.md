---
title: Azure 가상 네트워크에 액세스
description: ISEs (integration service environment)가 Azure 가상 네트워크에 액세스 하는 방법에 대 한 개요 (Vnet)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 07/05/2020
ms.openlocfilehash: f152283b1280cde2a26569b8acf10738e883e39e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816027"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스

경우에 따라 논리 앱은 가상 머신 (Vm), 다른 시스템 또는 서비스와 같은 보안 리소스에 대 한 액세스 권한이 있어야 하며, [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)내부 또는 연결 됩니다. 이 액세스를 설정 하기 위해 [ISE ( *integration service environment* )를 만들](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)수 있습니다. ISE는 전용 리소스를 사용 하 고 "전역" 다중 테 넌 트 Logic Apps 서비스와 별도로 실행 되는 Logic Apps 서비스의 인스턴스입니다.

예를 들어 일부 Azure virtual network는 azure [개인 링크](../private-link/private-link-overview.md)를 통해 설정할 수 있는 개인 끝점을 사용 하 여 azure에서 호스트 되는 Azure Storage, Azure Cosmos DB, Azure SQL Database, 파트너 서비스 또는 고객 서비스와 같은 azure PaaS 서비스에 대 한 액세스를 제공 합니다. 논리 앱에서 개인 끝점을 사용 하는 가상 네트워크에 액세스 해야 하는 경우에는 ISE 내에서 해당 논리 앱을 만들고, 배포 하 고, 실행 해야 합니다.

ISE를 만들 때 Azure는 해당 ISE를 Azure 가상 네트워크에 *삽입*하거나 배포합니다. 그런 다음, 액세스해야 하는 논리 앱 및 통합 계정의 위치로 이 ISE를 사용할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

이 개요에서는 [ISE를 사용 하려는 이유에](#benefits)대 한 자세한 내용, [전용 및 다중 테 넌 트 Logic Apps 서비스 간의 차이점](#difference), Azure 가상 네트워크 내부 또는 연결 된 리소스에 직접 액세스 하는 방법에 대해 설명 합니다.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>ISE를 사용 하는 이유

고유한 별도의 전용 인스턴스에서 논리 앱을 실행하면 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향(["사용량이 많은 인접 항목" 효과](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)로 알려짐)을 줄일 수 있습니다. ISE는 다음과 같은 이점도 제공합니다.

* 가상 네트워크 내부 또는 연결 된 리소스에 대 한 직접 액세스

  ISE에서 만들고 실행 하는 논리 앱은 [ise에서 실행 되는 특별히 설계 된 커넥터](../connectors/apis-list.md#ise-connectors)를 사용할 수 있습니다. 온-프레미스 시스템 또는 데이터 원본에 대 한 ISE 커넥터가 있는 경우 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 사용 하지 않고 직접 연결할 수 있습니다. 자세한 내용은이 항목의 뒷부분에 나오는 [전용 및 다중 테 넌 트](#difference) 및 [온-프레미스 시스템에](#on-premises) 대 한 액세스를 참조 하세요.

* 외부에 있거나 가상 네트워크에 연결 되지 않은 리소스에 계속 액세스

  ISE에서 만들고 실행 하는 논리 앱은 ISE 특정 커넥터를 사용할 수 없을 때 다중 테 넌 트 Logic Apps 서비스에서 실행 되는 커넥터를 계속 사용할 수 있습니다. 자세한 내용은 [전용 및 다중 테 넌 트](#difference)를 참조 하세요.

* 다중 테넌트 서비스의 논리 앱에서 공유되는 고정 IP 주소와는 별개의 고정 IP 주소입니다. 대상 시스템과 통신하도록 단일 공용, 정적 및 예측 가능한 아웃바운드 IP 주소를 설정할 수도 있습니다. 이런 방식으로 각 ISE에 대한 해당 대상 시스템에서 추가 방화벽을 설정할 필요가 없습니다.

* 실행 지속 시간, 스토리지 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대한 제한이 증가합니다. 자세한 내용은 [Azure Logic Apps에 대한 제한 및 구성](logic-apps-limits-and-config.md)을 참조하세요.

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>전용 및 다중 테 넌 트

ISE에서 논리 앱을 만들고 실행 하는 경우 다중 테 넌 트 Logic Apps 서비스와 동일한 사용자 환경 및 유사한 기능을 사용할 수 있습니다. 다중 테 넌 트 Logic Apps 서비스에서 사용할 수 있는 동일한 기본 제공 트리거, 작업 및 관리 되는 커넥터를 모두 사용할 수 있습니다. 일부 관리 되는 커넥터는 추가 ISE 버전을 제공 합니다. Ise 커넥터와 ise가 아닌 커넥터 간의 차이점은 ise 내에서 작업할 때 실행 되는 위치와 논리 앱 디자이너에 있는 레이블이 있습니다.

![ISE에 레이블이 있거나 없는 커넥터](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* HTTP와 같은 기본 제공 트리거와 동작은 **핵심** 레이블을 표시 하 고 논리 앱과 동일한 ISE에서 실행 됩니다.

* **ISE** 레이블을 표시 하는 관리 커넥터는 특별히 ISEs 용으로 설계 되었으며 *항상 논리 앱과 동일한 ISE로 실행*됩니다. 예를 들어 [ISE 버전을 제공 하는 일부 커넥터](../connectors/apis-list.md#ise-connectors)는 다음과 같습니다.<p>

  * Azure Blob Storage, File Storage 및 Table Storage
  * Azure Service Bus, Azure 큐, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid 및 Azure Monitor 로그
  * FTP, SFTP-SSH, 파일 시스템 및 SMTP
  * SAP, IBM MQ, IBM DB2 및 IBM 3270
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 및 EDIFACT

  드문 경우 이지만 온-프레미스 시스템 또는 데이터 원본에 ISE 커넥터를 사용할 수 있는 경우 온 [-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 사용 하지 않고 직접 연결할 수 있습니다. 자세한 내용은이 항목의 뒷부분에 있는 [온-프레미스 시스템 액세스](#on-premises) 를 참조 하세요.

* **Ise** 레이블을 표시 하지 않는 관리 커넥터는 ise 내에서 논리 앱에 대 한 작업을 계속 합니다. 이러한 커넥터는 항상 ISE가 아닌 *다중 테 넌 트 Logic Apps 서비스에서 실행*됩니다.

* [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 요구 하는지 여부에 관계 없이 *ise 외부*에서 만든 사용자 지정 커넥터는 ise 내에서 논리 앱을 계속 사용할 수 있습니다. 그러나 *ISE 내* 에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이에서 작동 하지 않습니다. 자세한 내용은 [온-프레미스 시스템에 액세스](#on-premises)를 참조 하세요.

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>온-프레미스 시스템에 대 한 액세스

ISE 내에서 실행 되는 논리 앱은 다음 항목을 사용 하 여 Azure 가상 네트워크 내에 있거나 연결 된 온-프레미스 시스템 및 데이터 원본에 직접 액세스할 수 있습니다.<p>

* **핵심** 레이블을 표시 하는 HTTP 트리거 또는 동작

* 온-프레미스 시스템 또는 데이터 원본에 대 한 **ISE** 커넥터 (사용할 수 있는 경우)

  ISE 커넥터를 사용할 수 있는 경우 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)없이 시스템 또는 데이터 원본에 직접 액세스할 수 있습니다. 그러나 ISE에서 SQL Server에 액세스 하 고 Windows 인증을 사용 해야 하는 경우에는 커넥터의 비 ISE 버전 및 온-프레미스 데이터 게이트웨이를 사용 해야 합니다. 커넥터의 ISE 버전이 Windows 인증을 지원 하지 않습니다. 자세한 내용은 [ISE 커넥터](../connectors/apis-list.md#ise-connectors) 및 [integration Service environment에서 연결](../connectors/apis-list.md#integration-service-environment)을 참조 하세요.

* 사용자 지정 커넥터

  * [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 요구 하는지 여부에 관계 없이 *ise 외부*에서 만든 사용자 지정 커넥터는 ise 내에서 논리 앱을 계속 사용할 수 있습니다.

  * *ISE 내* 에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이와 작동 하지 않습니다. 그러나 이러한 커넥터는 ISE를 호스트 하는 가상 네트워크 내에 있거나 연결 된 온-프레미스 시스템 및 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE 내에 있는 논리 앱은 일반적으로 해당 리소스에 액세스할 때 데이터 게이트웨이가 필요 하지 않습니다.

ISE 커넥터가 없거나 가상 네트워크 외부에 있거나 가상 네트워크에 연결 되지 않은 온-프레미스 시스템 및 데이터 원본에 액세스 하려면 여전히 온-프레미스 데이터 게이트웨이를 사용 해야 합니다. ISE 내의 논리 앱은 **CORE** 또는 **ise** 레이블이 없는 커넥터를 계속 사용할 수 있습니다. 이러한 커넥터는 ISE가 아닌 다중 테 넌 트 Logic Apps 서비스 에서만 실행 됩니다. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

ISE를 만들 때 개발자 SKU 또는 프리미엄 SKU를 선택할 수 있습니다. 이러한 Sku 간의 차이점은 다음과 같습니다.

* **개발자**

  는 실험, 개발 및 테스트에 사용할 수 있지만 프로덕션 또는 성능 테스트에는 사용할 수 없는 저렴 한 ISE를 제공 합니다. 개발자 SKU에는 고정 월별 가격에 대 한 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터 및 단일 [무료 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정이 포함 되어 있습니다. 그러나이 SKU에는 SLA (서비스 수준 계약), 용량을 확장 하는 옵션 또는 재활용 중 중복성을 포함 하지 않습니다. 즉, 지연 또는 가동 중지 시간이 발생할 수 있습니다.

* **Premium**

  는 프로덕션에 사용할 수 있는 ISE를 제공 하 고 SLA 지원, 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터, 단일 [표준 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정, 용량을 확장 하는 옵션 및 고정 된 월별 가격을 재활용 하는 동안 중복성을 포함 합니다.

> [!IMPORTANT]
> SKU 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다.

가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. ISE의 가격 책정 및 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조하세요.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 끝점 액세스

ISE를 만들 때 내부 또는 외부 액세스 끝점 중 하나를 사용 하도록 선택할 수 있습니다. 선택은 ISE의 논리 앱에 대 한 요청 또는 webhook 트리거가 가상 네트워크 외부에서 호출을 받을 수 있는지 여부를 결정 합니다. 이러한 끝점은 논리 앱의 실행 기록에서 입/출력에 액세스할 수 있는 방법에도 영향을 줍니다.

> [!IMPORTANT]
> ISE를 만드는 동안에만 액세스 끝점을 선택할 수 있으며 나중에이 옵션을 변경할 수 없습니다.

* **내부**: 개인 끝점은 *가상 네트워크 내부 에서만*논리 앱의 실행 기록에서 입력 및 출력을 보고 액세스할 수 있는 ISE에서 논리 앱에 대 한 호출을 허용 합니다.

  > [!IMPORTANT]
  > 실행 기록에 액세스 하려는 컴퓨터와 개인 끝점 간에 네트워크 연결이 있는지 확인 합니다. 그렇지 않으면 논리 앱의 실행 기록을 보려고 할 때 "예기치 않은 오류가 발생 했습니다. "을 (를) 가져오지 못했습니다.
  >
  > ![방화벽을 통해 트래픽을 보내지 못해 발생 하는 작업 오류 Azure Storage](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > 예를 들어 클라이언트 컴퓨터는 ISE의 가상 네트워크 내에 있거나 피어 링 또는 가상 사설망을 통해 ISE의 가상 네트워크에 연결 된 가상 네트워크 내부에 있을 수 있습니다. 

* **외부**: 공용 끝점은 ISE에서 논리 앱에 대 한 호출을 허용 하며, *가상 네트워크 외부에서*논리 앱 실행 기록의 입력 및 출력을 보고 액세스할 수 있습니다. NSGs (네트워크 보안 그룹)를 사용 하는 경우 실행 기록의 입력 및 출력에 대 한 액세스를 허용 하는 인바운드 규칙으로 설정 되어 있는지 확인 합니다. 자세한 내용은 [ISE에 대 한 액세스 사용](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)을 참조 하세요.

ISE가 내부 또는 외부 액세스 끝점을 사용 하는지 확인 하려면 ISE의 메뉴에서 **설정**아래에 있는 **속성**을 선택 하 고 **액세스 끝점** 속성을 찾습니다.

![ISE 액세스 끝점 찾기](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>가격 책정 모델

ISE에서 실행 되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 요금제와 다른 고정 요금제를 사용 합니다. 자세한 내용은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE와의 통합 계정

ISE(통합 서비스 환경) 내에서 논리 앱을 통해 통합 계정을 사용할 수 있습니다. 그러나 해당 통합 계정은 연결된 논리 앱과 *동일한 ISE*를 사용해야 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다. 통합 계정을 만들 때는 통합 계정의 위치로 ISE를 선택할 수 있습니다. ISE를 사용 하 여 통합 계정에 대 한 가격 책정 및 청구 작업 방법을 알아보려면 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기