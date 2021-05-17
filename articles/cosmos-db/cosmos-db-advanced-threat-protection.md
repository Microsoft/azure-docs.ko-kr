---
title: Azure Cosmos DB용 Advanced Threat Protection
description: Azure Cosmos DB에서 저장소 데이터 암호화를 제공하는 방법 및 구현 방식을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: b73e7f8c13f621bc359a2ae79a725829420a3ecc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455598"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB용 Advanced Threat Protection(미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB용 Advanced Threat Protection은 Azure Cosmos DB 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 탐지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 통해 보안 전문가가 아니더라도 위협을 해결하고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합되며, 의심스러운 활동의 세부 정보와 위협을 조사하고 수정하는 방법에 대한 권장 사항을 포함하여 이메일을 통해 구독 관리자에게 전송됩니다.

> [!NOTE]
>
> * Azure Cosmos DB용 Advanced Threat Protection은 현재 SQL API에서만 사용할 수 있습니다.
> * Azure Cosmos DB용 Advanced Threat Protection은 현재 Azure Government 및 소버린 클라우드 지역에서 사용할 수 없습니다.

보안 경고의 전체 조사 환경의 경우 모든 문서, 컨테이너 및 데이터베이스에 대한 CRUD 작업을 포함하여 데이터베이스 자체에 대한 작업을 기록하는 [Azure Cosmos DB의 진단 로깅](./monitor-cosmos-db.md)을 사용하는 것이 좋습니다.

## <a name="threat-types"></a>위협 유형

Azure Cosmos DB용 Advanced Threat Protection은 비정상적이며 유해할 가능성이 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 감지합니다. 현재 다음과 같은 경고를 트리거할 수 있습니다.

- **비정상적인 위치에서 액세스**: 비정상적인 지리적 위치에서 누군가가 Azure Cosmos DB 엔드포인트에 연결한 Azure Cosmos 계정에 대한 액세스 패턴이 변경되면 이 경고가 트리거됩니다. 경우에 따라 경고는 합법적인 작업(새 애플리케이션 또는 개발자 유지 관리 작업을 의미)을 감지합니다. 다른 경우에는 경고가 악의적인 작업(퇴사 직원, 외부 공격자 등)을 감지합니다.

- **비정상적인 데이터 추출**: 이 경고는 클라이언트가 Azure Cosmos DB 계정에서 비정상적인 양의 데이터를 추출할 때 트리거됩니다. 이는 계정에 저장된 모든 데이터를 외부 데이터 저장소로 전송하기 위해 수행해야 하는 일부 데이터 유출의 증상일 수 있습니다.



## <a name="configure-advanced-threat-protection"></a>Advanced Threat Protection 구성

다음 섹션에서 설명하는 여러 가지 방법으로 지능형 위협 방지를 구성할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal을 시작합니다.

2. Azure Cosmos DB 계정의 **설정** 메뉴에서 **고급 보안** 을 선택합니다.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="ATP 설정":::

3. **고급 보안** 구성 블레이드에서 다음을 수행합니다.

    * **Advanced Threat Protection** 옵션을 클릭하여 **ON** 으로 설정합니다.
    * **저장** 을 클릭하여 새로운 또는 업데이트된 Advanced Threat Protection 정책을 저장합니다.   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Rest API 명령을 사용하여 특정 Azure Cosmos DB 계정에 대한 Advanced Threat Protection 설정을 만들거나, 업데이트하거나, 가져옵니다.

* [Advanced Threat Protection - 만들기](/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection - 가져오기](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell cmdlet을 사용합니다.

* [Advanced Threat Protection 사용](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Advanced Threat Protection 가져오기](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Advanced Threat Protection 사용 안 함](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

### <a name="arm-template"></a>[ARM 템플릿](#tab/arm-template)

ARM(Azure Resource Manager) 템플릿을 사용하여 Advanced Threat Protection이 활성화된 Cosmos DB를 설정합니다.
자세한 내용은 [Advanced Threat Protection을 사용하여 CosmosDB 계정 만들기](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)를 참조하세요.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Azure Policy를 사용하여 Cosmos DB에 대한 Advanced Threat Protection을 활성화합니다.

1. Azure **정책 - 정의** 페이지를 시작하고 **Cosmos DB용 Advanced Threat Protection 배포** 정책을 검색합니다.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="정책 검색"::: 

1. **CosmosDB용 Advanced Threat Protection 배포** 정책을 클릭한 다음, **할당** 을 클릭합니다.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="구독 또는 그룹 선택":::


1. **범위** 필드에서 세 개의 점을 클릭하고 Azure 구독 또는 리소스 그룹을 선택한 다음, **선택** 을 클릭합니다.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="정책 정의 페이지":::


1. 다른 매개 변수를 입력하고 **할당** 을 클릭합니다.




## <a name="manage-atp-security-alerts"></a>ATP 보안 경고 관리

Azure Cosmos DB 작업 변칙이 발생한 경우 의심스러운 보안 이벤트에 대한 정보와 함께 보안 경고가 트리거됩니다. 

 Azure Security Center에서 현재 [보안 경고](../security-center/security-center-alerts-overview.md)를 검토 및 관리할 수 있습니다.  [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0)에서 특정 경고를 클릭하여 잠재적 위협을 조사하고 완화하기 위한 가능한 원인과 권장 조치를 확인합니다. 다음 이미지는 Security Center에서 제공하는 경고 세부 정보의 예를 보여줍니다.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="위협 세부 정보":::

경고 세부 정보와 권장 조치가 포함된 이메일 알림도 전송됩니다. 다음 이미지는 경고 이메일의 예를 보여줍니다.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="경고 세부 정보":::

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP 경고

 Azure Cosmos DB 계정을 모니터링할 때 생성되는 경고 목록을 보려면 Azure Security Center 설명서의 [Cosmos DB 경고](../security-center/alerts-reference.md#alerts-azurecosmos) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 진단 로깅](cosmosdb-monitor-resource-logs.md)에 대한 자세한 정보
* [Azure Security Center](../security-center/security-center-introduction.md)에 대한 자세한 정보