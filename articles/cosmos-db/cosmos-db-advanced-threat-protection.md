---
title: Azure Cosmos DB에 대한 Advanced Threat Protection
description: 미사용 데이터의 암호화 및 구현 방법을 Azure Cosmos DB 하는 방법에 대해 알아봅니다.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 5cea2f1dac50744c974917347b4428bc39aa737d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445538"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB에 대한 Advanced Threat Protection (미리 보기)

Azure Cosmos DB에 대한 Advanced Threat Protection은 Azure Cosmos DB 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 계층을 사용 하면 보안 전문가 없이도 위협을 해결 하 고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합 되며, 의심 스러운 활동의 세부 정보와 위협을 조사 하 고 수정 하는 방법에 대한 권장 사항을 포함 하 여 전자 메일을 통해 구독 관리자에 게 전송 됩니다.

> [!NOTE]
>
> * Azure Cosmos DB에 대한 Advanced Threat Protection은 현재 SQL API에 대해서만 사용할 수 있습니다.
> * Azure Cosmos DB에 대한 Advanced Threat Protection은 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

보안 경고에 대한 전체 조사 환경을 위해 모든 문서, 컨테이너 및 데이터베이스에 대한 CRUD 작업을 포함 하 여 데이터베이스 자체에 대한 작업을 기록 하는 [Azure Cosmos DB에서 진단 로깅을](https://docs.microsoft.com/azure/cosmos-db/logging)사용 하는 것이 좋습니다.

## <a name="threat-types"></a>위협 유형

Azure Cosmos DB에 대한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다. 현재 다음 경고를 트리거할 수 있습니다.

- **비정상적인 위치에서 액세스**: 누군가가 비정상적인 지리적 위치에서 Azure Cosmos DB 엔드포인트에 연결 하는 Azure Cosmos 계정에 대한 액세스 패턴이 변경 된 경우이 경고가 트리거됩니다. 경우에 따라 경고는 합법적인 작업 (새 응용 프로그램 또는 개발자 유지 관리 작업)을 검색 합니다. 다른 경우에 경고는 이전 직원, 외부 공격자 등의 악의적인 작업을 검색 합니다.

- **비정상적인 데이터 추출**:이 경고는 클라이언트가 Azure Cosmos DB 계정에서 비정상적인 양의 데이터를 추출 하는 경우에 트리거됩니다. 이는 계정에 저장 된 모든 데이터를 외부 데이터 저장소로 전송 하기 위해 수행 해야 하는 일부 데이터에 대한 증상이 될 수 있습니다.

## <a name="set-up-advanced-threat-protection"></a>Advanced Threat Protection 설정

### <a name="set-up-atp-using-the-portal"></a>포털을 사용 하 여 ATP 설정

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal를 시작 합니다.

2. Azure Cosmos DB 계정의 **설정** 메뉴에서 **고급 보안**을 선택 합니다.

    ![ATP 설정](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. **고급 보안** 구성 블레이드에서 다음을 수행 합니다.

    * **Advanced Threat Protection** 옵션을 클릭 하 여 **켜기**로 설정 합니다.
    * **저장**을 클릭하여 새로운 또는 업데이트된 Advanced Threat Protection 정책을 저장합니다.   

### <a name="set-up-atp-using-rest-api"></a>REST API를 사용 하 여 ATP 설정

Rest API 명령을 사용 하 여 특정 Azure Cosmos DB 계정에 대한 Advanced Threat Protection 설정을 만들거나 업데이트 하거나 가져옵니다.

* [Advanced Threat Protection-만들기](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection-Get](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 ATP 설정

다음 PowerShell cmdlet을 사용 합니다.

* [Advanced Threat Protection 사용](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Advanced Threat Protection 가져오기](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Advanced Threat Protection 사용 안 함](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

Azure Resource Manager 템플릿을 사용 하 여 Advanced Threat Protection을 사용 하도록 설정 된 Cosmos DB를 설정 합니다.
자세한 내용은 [Advanced Threat Protection을 사용 하 여 CosmosDB 계정 만들기](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)를 참조 하세요.

### <a name="using-azure-policy"></a>Azure Policy 사용

Azure Policy를 사용 하 여 Cosmos DB에 대한 고급 위협 방지를 사용 하도록 설정 합니다.

1. Azure **정책 정의** 페이지를 시작 하 고 **Cosmos DB 정책에 대한 Advanced Threat Protection 배포** 를 검색 합니다.

    ![검색 정책](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. **CosmosDB에 대한 Advanced Threat Protection 배포** 정책을 클릭 한 다음 **할당**을 클릭 합니다.

    ![구독 또는 그룹 선택](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. **범위** 필드에서 세 개의 점을 클릭 하 고 Azure 구독 또는 리소스 그룹을 선택한 다음 **선택**을 클릭 합니다.

    ![정책 정의 페이지](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. 다른 매개 변수를 입력 하 고 **할당**을 클릭 합니다.

## <a name="manage-atp-security-alerts"></a>ATP 보안 경고 관리

Azure Cosmos DB 작업에 대한 예외가 발생할 경우 의심 스러운 보안 이벤트에 대한 정보를 사용 하 여 보안 경고가 트리거됩니다. 

 Azure Security Center에서 현재 [보안 경고](../security-center/security-center-alerts-overview.md)를 검토 하 고 관리할 수 있습니다.  가능한 원인 및 권장 조치를 확인 하 여 잠재적인 위협을 조사 하 고 완화 하는 [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) 에서 특정 경고를 클릭 합니다. 다음 이미지는 Security Center에서 제공 하는 경고 정보의 예를 보여 줍니다.

 ![위협 세부 정보](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

또한 경고 세부 정보 및 권장 조치를 사용 하 여 전자 메일 알림이 전송 됩니다. 다음 그림은 경고 전자 메일의 예를 보여 줍니다.

 ![경고 세부 정보](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>ATP 경고 Cosmos DB

 Azure Cosmos DB 계정을 모니터링할 때 생성 되는 경고 목록을 보려면 Security Center 설명서의 [Cosmos DB 경고](../security-center/security-center-alerts-data-services.md#cosmos-db) 섹션을 참조 하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 진단 로깅](cosmosdb-monitor-resource-logs.md) 에 대해 자세히 알아보세요.
* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
