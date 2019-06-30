---
title: 새 구독 또는 리소스 그룹으로 Azure 리소스 이동 | Microsoft Docs
description: Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: tomfitz
ms.openlocfilehash: eb99bec74ae5444320b7098616d4bf2d81b90697
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332027"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>새 리소스 그룹 또는 구독으로 리소스 이동

이 문서에서는 Azure 리소스를 다른 Azure 구독 또는 동일한 구독의 다른 리소스 그룹으로 이동하는 방법을 보여 줍니다. Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 리소스를 이동할 수 있습니다.

이동 작업 동안 원본 그룹과 대상 그룹 모두 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 리소스 그룹에서 차단됩니다. 이 잠금은 리소스 그룹에서 리소스를 추가, 업데이트, 삭제할 수 없음을 의미하지만 리소스가 고정되었음을 의미하지는 않습니다. 예를 들어, SQL Server와 해당 데이터베이스를 새 리소스 그룹으로 이동하는 경우 해당 데이터베이스를 사용하는 애플리케이션에는 가동 중지 시간이 발생하지 않습니다. 데이터베이스에 계속해서 읽고 쓸 수 있습니다.

리소스를 이동할 때는 새 리소스 그룹으로만 이동됩니다. 이동 작업으로 리소스의 위치를 변경할 수 없습니다. 새 리소스 그룹은 다른 위치를 가질 수 있지만 리소스의 위치는 변경되지 않습니다.

> [!NOTE]
> 이 문서에서는 기존 Azure 구독 간에 리소스를 이동하는 방법을 설명합니다. 실제로 Azure 구독을 업그레이드하려면(예: 평가판에서 종량제로 업그레이드) 구독을 변환해야 합니다.
> * 평가판을 업그레이드하려면 [평가판 또는 Microsoft Imagine Azure 구독을 종량제로 업그레이드](..//billing/billing-upgrade-azure-subscription.md)를 참조하세요.
> * 종량제 계정을 변경하려면 [다른 제안으로 Azure 종량제 구독 변경](../billing/billing-how-to-switch-azure-offer.md)을 참조하세요.
> * 구독을 변환할 수 없으면 [Azure 지원 요청을 만듭니다](../azure-supportability/how-to-create-azure-support-request.md). 문제 유형으로 **구독 관리**를 선택합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>Azure 지원 서비스에 문의해야 하는 경우

대부분의 리소스는 이 문서에 나오는 셀프 서비스 작업을 통해 이동할 수 있습니다. 다음에 대해 셀프 서비스 작업을 사용합니다.

* Resource Manager 리소스를 이동합니다.
* [클래식 배포 제한 사항](#classic-deployment-limitations)에 따라 클래식 리소스를 이동합니다.

다음을 수행해야 하는 경우 [지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.

* 새 Azure 계정(및 Azure Active Directory 테넌트)로 리소스를 이동하며 앞의 섹션의 지침을 수행하는 데 지원이 필요합니다.
* 클래식 리소스를 이동하지만 제한 사항으로 문제가 발생합니다.

## <a name="services-that-can-be-moved"></a>이동할 수 있는 서비스

아래 목록에서는 새 리소스 그룹 및 구독으로 이동할 수 있는 Azure 서비스의 일반적인 요약 정보가 제공됩니다. 리소스의 종류는 이동을 지원 목록에 대해서 [이동 리소스에 대 한 작업 지원](move-support-resources.md)합니다.

* Analysis Services
* API Management
* App Service 앱(웹앱) - [App Service 제한](#app-service-limitations)
* App Service Certificate - [App Service Certificate 제한 사항](#app-service-certificate-limitations)을 참조하세요.
* Automation - Runbook이 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.
* Azure Active Directory B2C
* Azure Cache for Redis - 가상 네트워크를 사용하여 Azure Cache for Redis 인스턴스를 구성하는 경우 다른 구독으로 인스턴스를 이동할 수 없습니다. [가상 네트워크 제한 사항](#virtual-networks-limitations)을 참조하십시오.
* Azure Cosmos DB
* Azure Data Explorer
* Azure Database for MariaDB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure DevOps-단계에 따라 [대금 청구에 사용 되는 Azure 구독을 변경할](/azure/devops/organizations/billing/change-azure-subscription?view=azure-devops)합니다.
* Azure Maps
* Azure Monitor 로그
* Azure Relay
* Azure Stack - 등록
* 일괄 처리
* BizTalk Services
* Bot 서비스
* CDN
* Cloud Services - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Cognitive Services
* Container Registry
* Content Moderator
* Cost Management
* Customer Insights
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* HDInsight 클러스터 - [HDInsight 제한 사항](#hdinsight-limitations) 참조
* Iot Central
* IoT Hub
* Key Vault - 디스크 암호화에 사용되는 Key Vault를 동일한 구독의 리소스 그룹으로 또는 구독 간에 이동할 수 없습니다.
* Load Balancer - 기본 SKU Load Balancer는 이동할 수 있습니다. 표준 SKU 부하 분산 장치는 이동할 수 없습니다.
* Logic Apps
* 기계 학습 - Machine Learning Studio 웹 서비스는 동일한 구독의 리소스 그룹으로 이동할 수 있지만 다른 구독으로 이동할 수는 없습니다. 다른 Machine Learning 리소스는 구독 간에 이동할 수 있습니다.
* Managed Disks-관리 디스크가 가용성 영역에서 다른 구독으로 이동할 수 없습니다
* Media Services
* Monitor - 새 구독으로 이동해도 [구독 할당량](../azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않는지 확인합니다.
* Notification Hubs
* Operational Insights
* 운영 관리
* 포털 대시보드
* Power BI - Power BI Embedded 및 Power BI Workspace Collection 모두
* 공용 IP - 기본 SKU 공용 IP는 이동할 수 있습니다. 표준 SKU 공용 IP는 이동할 수 없습니다.
* Recovery Services 자격 증명 모음-참조 [제한 사항](#recovery-services-limitations)합니다.
* Azure의 SAP HANA
* Scheduler
* Search - 서로 다른 지역의 여러 Search 리소스를 하나의 작업으로 모두 이동할 수는 없습니다. 대신 별도 작업으로 이동합니다.
* Service Bus
* Service Fabric
* Service Fabric Mesh
* SignalR Service
* Storage
* 저장소(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)
* 저장소 동기화 서비스
* Stream Analytics - 실행 중 상태일 때는 Stream Analytics 작업을 이동할 수 없습니다.
* SQL Database 서버 - 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.
* Time Series Insights
* Traffic Manager
* 가상 컴퓨터- [Virtual Machines 제한 사항](#virtual-machines-limitations)
* Virtual Machines(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)
* Virtual Machine Scale Sets - [Virtual Machines 제한 사항](#virtual-machines-limitations) 참조
* Virtual Networks - [Virtual Networks 제한 사항](#virtual-networks-limitations) 참조
* VPN Gateway

### <a name="services-that-cannot-be-moved"></a>이동할 수 없는 서비스

아래 목록에서는 새 리소스 그룹 및 구독으로 이동할 수 없는 Azure 서비스의 일반적인 요약 정보가 제공됩니다. 자세한 내용은 [리소스에 대한 이동 작업 지원](move-support-resources.md)을 참조하세요.

* AD Domain Services
* AD 하이브리드 상태 관리 서비스
* Application Gateway
* Azure Database Migration
* Azure Databricks
* Azure Firewall
* AKS(Azure Kubernetes Service)
* Azure Migrate
* Azure NetApp Files
* 인증서 - App Service Certificate를 이동할 수 있지만 업로드된 인증서에는 [제한](#app-service-limitations)이 있습니다.
* 클래식 애플리케이션
* Container Instances
* 컨테이너 서비스
* Data Box
* Dev Spaces
* Dynamics LCS
* ExpressRoute
* Front Door
* Lab Services-클래스 룸 랩에 새 리소스 그룹 또는 구독으로 이동할 수 없습니다. DevTest Labs는 동일한 구독에 있지만 구독 전체가 아닌 새 리소스 그룹으로 이동할 수 있습니다.
* Managed Applications
* 관리 ID - 사용자 할당
* Microsoft Genomics
* 보안
* Site Recovery
* StorSimple 디바이스 관리자
* Virtual Networks(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations)

## <a name="limitations"></a>제한 사항

이 섹션에서는 리소스 이동과 관련된 복잡한 시나리오를 처리하는 방법을 설명합니다. 제한 사항은 다음과 같습니다.

* [Virtual Machines 제한 사항](#virtual-machines-limitations)
* [Virtual Networks 제한 사항](#virtual-networks-limitations)
* [App Service 제한 사항](#app-service-limitations)
* [App Service Certificate 제한 사항](#app-service-certificate-limitations)
* [클래식 배포 제한 사항](#classic-deployment-limitations)
* [Recovery Services 제한 사항](#recovery-services-limitations)
* [HDInsight 제한 사항](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>Virtual Machines 제한 사항

Managed disks를 사용 하는 가상 컴퓨터를 사용 하 여 관리 되는 디스크, 관리 되는 이미지, 관리 되는 스냅숏 및 가용성 집합을 사용 하 여 가상 컴퓨터를 이동할 수 있습니다. 가용성 영역에서 관리 디스크를 다른 구독으로 이동할 수 없습니다.

다음 시나리오는 아직 지원되지 않습니다.

* Key Vault에 저장된 인증서가 있는 Virtual Machines는 동일한 구독에서 새 리소스 그룹으로 이동할 수 있지만 구독 간에는 이동할 수 없습니다.
* 표준 SKU 부하 분산 장치 또는 표준 SKU 공용 IP를 사용 하 여 Virtual Machine Scale Sets는 이동할 수 없습니다.
* 연결된 계획이 있는 Marketplace 리소스에서 만든 가상 머신은 리소스 그룹 또는 구독 간에 이동할 수 없습니다. 현재 구독의 가상 머신을 프로비전 해제하고 새 구독에 다시 배포합니다.
* 사용자는 가상 네트워크에서 모든 리소스를 이동 하려면 않을 기존 가상 네트워크의 가상 머신.

Azure Backup으로 구성한 가상 머신을 이동하려면 다음 해결 방법을 사용합니다.

* 가상 머신의 위치를 찾습니다.
* 명명 패턴이 `AzureBackupRG_<location of your VM>_1`인 리소스 그룹을 찾습니다(예: AzureBackupRG_westus2_1).
* Azure Portal에서 작업하는 경우 “숨겨진 형식 표시”를 선택합니다.
* PowerShell에서 작업하는 경우에는 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet을 사용합니다.
* CLI에서 작업하는 경우에는 `az resource list -g AzureBackupRG_<location of your VM>_1`을 사용합니다.
* 종류가 `Microsoft.Compute/restorePointCollections`이고 명명 패턴이 `AzureBackup_<name of your VM that you're trying to move>_###########`인 리소스를 찾습니다.
* 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.
* 삭제가 완료되면 가상 머신을 이동할 수 있습니다. 자격 증명 모음 및 가상 머신을 대상 구독으로 이동할 수 있습니다. 이동 후 데이터 손실 없이 백업을 계속할 수 있습니다.
* 백업용 Recovery Service 자격 증명 모음 이동에 대한 내용은 [Recovery Services 제한 사항](#recovery-services-limitations)을 참조하세요.

### <a name="virtual-networks-limitations"></a>Virtual Networks 제한 사항

가상 네트워크를 이동할 때는 해당 종속 리소스도 함께 이동해야 합니다. VPN Gateway의 경우 IP 주소, 가상 네트워크 게이트웨이 및 모든 관련된 연결 리소스를 이동해야 합니다. 로컬 네트워크 게이트웨이는 다른 리소스 그룹에 있을 수 있습니다.

네트워크 인터페이스 카드를 사용 하 여 가상 컴퓨터를 이동 하려면 모든 종속 리소스를 이동 해야 합니다. 네트워크 인터페이스 카드에 대 한 가상 네트워크, 가상 네트워크 및 VPN gateway에 대 한 다른 모든 네트워크 인터페이스 카드를 이동 해야 합니다.

피어링된 가상 네트워크를 이동하려면 먼저 가상 네트워크 피어링을 사용하지 않도록 설정해야 합니다. 사용하지 않도록 설정되면 가상 네트워크를 이동할 수 있습니다. 이동 후에는 가상 네트워크 피어링을 사용하도록 다시 설정합니다.

리소스 탐색 링크가 있는 서브넷이 가상 네트워크에 있는 경우 가상 네트워크를 다른 구독으로 이동할 수 없습니다. 예를 들어 Azure Cache for Redis 리소스가 서브넷에 배포된 경우 해당 서브넷에는 리소스 탐색 링크가 있습니다.

### <a name="app-service-limitations"></a>App Service 제한

리소스를 구독 내에서 이동할지 또는 새 구독으로 이동할지에 따라 App Service 리소스 이동에 대한 제한 사항이 다릅니다. 웹앱이 App Service Certificate를 사용하는 경우 [App Service Certificate 제한 사항](#app-service-certificate-limitations)을 참조하세요.

#### <a name="moving-within-the-same-subscription"></a>동일한 구독 내에서 이동

‘동일한 구독 내’에서 웹앱을 이동할 경우 타사 SSL 인증서는 이동할 수 없습니다.  그러나 타사 인증서를 이동하지 않고 웹앱을 새 리소스 그룹으로 이동할 수 있으며 앱의 SSL 기능도 계속 작동합니다.

Web App을 사용하여 SSL 인증서를 이동하려면 다음 단계를 수행합니다.

1. 웹앱에서 타사 인증서를 삭제하지만 인증서 복사본 유지
2. Web App을 이동합니다.
3. 이동된 웹앱에 타사 인증서를 업로드합니다.

#### <a name="moving-across-subscriptions"></a>구독 간 이동

_구독 간에_ Web App을 이동할 때 적용되는 제한 사항은 다음과 같습니다.

- 대상 리소스 그룹에 기존 App Service 리소스가 없어야 합니다. App Service 리소스에는 다음이 포함됩니다.
    - Web Apps
    - App Service 계획
    - 업로드되거나 가져온 SSL 인증서
    - App Service Environment
- 리소스 그룹의 모든 App Service 리소스는 함께 이동해야 합니다.
- App Service 리소스는 처음 만들었던 리소스 그룹에서만 이동할 수 있습니다. App Service 리소스가 원래의 리소스 그룹에 더 이상 없으면 먼저 원래의 리소스 그룹으로 다시 이동해야 합니다. 그런 다음 구독 간에 App Service 리소스를 이동하면 됩니다.

원래 리소스 그룹을 기억 하지 못하는 경우에 진단을 통해 찾을 수 있습니다. 웹 앱 선택 **진단 및 문제 해결**합니다. 그런 다음 선택 **구성 및 관리**합니다.

![진단 선택](./media/resource-group-move-resources/select-diagnostics.png)

선택 **마이그레이션 옵션**합니다.

![마이그레이션 옵션을 선택 합니다.](./media/resource-group-move-resources/select-migration.png)

웹 앱을 이동 하는 권장된 단계에 대 한 옵션을 선택 합니다.

![권장된 단계를 선택 합니다.](./media/resource-group-move-resources/recommended-steps.png)

리소스를 이동 하기 전에 수행할 권장된 작업을 표시 합니다. 정보는 웹 앱에 대 한 원래 리소스 그룹을 포함합니다.

![권장 사항](./media/resource-group-move-resources/recommendations.png)

### <a name="app-service-certificate-limitations"></a>App Service Certificate 제한 사항

App Service Certificate를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다. App Service Certificate가 웹앱에 바인딩되어 있으면 리소스를 새 구독으로 이동하기 전에 몇 가지 단계를 수행해야 합니다. 리소스를 이동하기 전에 웹앱에서 SSL 바인딩 및 프라이빗 인증서를 삭제합니다. App Service Certificate를 삭제할 필요가 없고 웹앱의 프라이빗 인증서만 삭제하면 됩니다.

### <a name="classic-deployment-limitations"></a>클래식 배포 제한 사항

리소스를 구독 내에서 이동할지 또는 새 구독으로 이동할지에 따라 클래식 모델을 통해 배포된 리소스의 이동 옵션은 다릅니다.

#### <a name="same-subscription"></a>동일한 구독

한 리소스 그룹에서 같은 구독 내 다른 리소스 그룹으로 리소스를 이동할 경우 다음 제한 사항이 적용됩니다.

* 가상 네트워크(클래식)는 이동할 수 없습니다.
* 가상 머신(클래식)은 클라우드 서비스로 이동해야 합니다.
* 클라우드 서비스는 이동에 모든 가상 머신이 포함된 경우에만 이동할 수 있습니다.
* 한 번에 하나의 클라우드 서비스만 이동할 수 있습니다.
* 한 번에 하나의 저장소 계정(클래식)만 이동할 수 있습니다.
* Storage 계정(클래식)은 가상 머신 또는 클라우드 서비스와 같은 작업으로 이동할 수 없습니다.

클래식 리소스를 동일한 구독 내의 새 리소스 그룹으로 이동하려면 [포털](#use-portal), Azure PowerShell, Azure CLI 또는 REST API를 통해 표준 이동 작업을 사용합니다. Resource Manager 리소스 이동을 위해 사용하는 동일한 작업을 사용합니다.

#### <a name="new-subscription"></a>새 구독

새 구독으로 리소스 이동 시 다음 제한 사항이 적용됩니다.

* 구독의 모든 클래식 리소스는 동일한 작업에서 이동해야 합니다.
* 대상 구독은 다른 어떠한 클래식 리소스도 포함할 수 없습니다.
* 이동은 클래식 이동에 대한 별도의 REST API를 통해서만 요청할 수 있습니다. 클래식 리소스를 새 구독으로 이동할 경우 표준 Resource Manager 이동 명령은 작동하지 않습니다.

클래식 리소스를 새 구독으로 이동하려면 클래식 리소스와 관련된 REST 작업을 사용합니다. REST를 사용 하려면 다음 단계를 수행 합니다.

1. 원본 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     요청 본문에 다음을 포함합니다.

   ```json
   {
    "role": "source"
   }
   ```

     유효성 검사 작업에 대한 응답은 다음 형식입니다.

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

2. 대상 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     요청 본문에 다음을 포함합니다.

   ```json
   {
    "role": "target"
   }
   ```

     응답이 원본 구독 유효성 검사와 동일한 형식입니다.
3. 두 구독이 유효성 검사를 통과하면 다음 작업으로 한 구독에서 다른 구독으로 모든 클래식 리소스를 이동합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    요청 본문에 다음을 포함합니다.

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

이 작업은 몇 분 정도 실행될 수 있습니다.

### <a name="recovery-services-limitations"></a>Recovery Services 제한 사항

 Recovery Services 자격 증명 모음을 이동 하려면 다음이 단계를 수행 합니다. [새 리소스 그룹 또는 구독으로 리소스 이동](../backup/backup-azure-move-recovery-services-vault.md)합니다.

현재 지역별로 한 번에 하나의 Recovery Services 자격 증명 모음을 이동할 수 있습니다. Azure Files, Azure 파일 동기화 또는 IaaS 가상 머신의 SQL을 백업하는 자격 증명 모음은 이동할 수 없습니다.

가상 머신을 자격 증명 모음과 함께 이동하지 않으면 현재 가상 머신 복구 지점은 만료될 때까지 자격 증명 모음에 남아 있습니다. 가상 머신을 자격 증명 모음과 함께 이동했는지 여부에 관계없이 자격 증명 모음의 백업 기록에서 가상 머신을 복원할 수 있습니다.

Recovery Services 자격 증명 모음은 구독 간 백업을 지원하지 않습니다. 구독 간에 자격 증명 모음을 가상 머신 백업 데이터와 함께 이동하는 경우에는 가상 머신을 동일한 구독으로 이동하고 동일한 대상 리소스 그룹을 사용하여 백업을 계속해야 합니다.

자격 증명 모음에 대해 정의된 백업 정책은 자격 증명 모음이 이동한 후에도 유지됩니다. 이동 후에는 자격 증명 모음에 대한 보고 및 모니터링을 다시 설정해야 합니다.

Recovery Services 자격 증명 모음을 이동하지 않고 가상 머신을 새 구독으로 이동하려면:

 1. 일시적으로 백업 중지
 1. [복원 지점을 삭제합니다](#virtual-machines-limitations). 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.
 1. 가상 머신을 새 구독으로 이동
 1. 해당 구독의 새 자격 증명 모음으로 다시 보호

Azure Site Recovery로 재해 복구를 설정하는 데 사용된 Storage, Network 또는 Compute 리소스에 대해서는 이동이 사용되지 않습니다. 예를 들어, 온-프레미스 컴퓨터에서 스토리지 계정(Storage1)으로 복제를 설정했고 Azure에 장애 조치(failover) 후 가상 네트워크(Network1)에 연결된 가상 머신(VM1)로 보호되는 컴퓨터를 실행하려고 한다고 가정합니다. 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 이러한 Azure 리소스(Storage1, VM1, Network1) 작업 중 어떠한 것도 이동할 수 없습니다.

### <a name="hdinsight-limitations"></a>HDInsight 제한 사항

새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.

HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 저장소 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="checklist-before-moving-resources"></a>리소스를 이동하기 전의 검사 목록

리소스를 이동하기 전에 몇 가지 중요한 단계가 있습니다. 이러한 조건을 확인하면 오류를 방지할 수 있습니다.

1. 원본 및 대상 구독이 활성 상태여야 합니다. 사용하지 않도록 설정된 계정을 사용하도록 설정하는 과정에서 문제가 발생하면 [Azure 지원 요청을 작성](../azure-supportability/how-to-create-azure-support-request.md)하세요. 문제 유형으로 **구독 관리**를 선택합니다.

1. 원본 및 대상 구독은 동일한 [Azure Active Directory 테넌트](../active-directory/develop/quickstart-create-new-tenant.md) 내에 있어야 합니다. 두 구독이 모두 동일한 테넌트 ID를 갖는지 확인하려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

   Azure PowerShell의 경우 다음을 사용합니다.

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI의 경우

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   원본 및 대상 구독에 대한 테넌트 ID가 다른 경우 다음 메서드를 사용하여 테넌트 ID를 조정합니다.

   * [Azure 구독의 소유권을 다른 계정으로 이전](../billing/billing-subscription-transfer.md)
   * [Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 이동되는 리소스의 리소스 공급자가 대상 구독에 등록되어야 합니다. 그러지 않으면 **구독이 리소스 형식에 대해 등록되지 않았음**을 알리는 오류 메시지가 표시됩니다. 해당 리소스 종류와 함께 사용된 적이 없는 새 구독으로 리소스를 이동할 때 이 오류가 표시될 수 있습니다.

   PowerShell의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   리소스 공급자를 등록하려면 다음을 사용합니다.

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   리소스 공급자를 등록하려면 다음을 사용합니다.

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 리소스를 이동시키는 계정에는 적어도 다음과 같은 권한이 있어야 합니다.

   * 원본 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**
   * 대상 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/write**

1. 리소스를 이동하기 전에 리소스를 이동하려는 구독에 대한 구독 할당량을 확인합니다. 리소스 이동 시 구독이 해당 한계를 초과하는 경우 할당량 증가를 요청할 수 있는지 여부를 검토해야 합니다. 제한의 목록 및 증가 요청 방법은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

1. 가능한 경우 대용량 이동을 개별 이동 작업으로 나눕니다. 단일 작업에 800개가 넘는 리소스가 포함되면 Resource Manager가 즉시 오류를 반환합니다. 그러나 800개 미만의 리소스 이동도 시간 초과로 인해 실패할 수 있습니다.

1. 서비스는 리소스 이동 기능을 사용하도록 설정해야 합니다. 이동이 성공할지 여부를 확인하려면 [이동 요청 유효성 검사](#validate-move)를 수행합니다. 이 문서에서 [리소스 이동이 가능한 서비스](#services-that-can-be-moved)와 [리소스 이동이 가능하지 않은 서비스](#services-that-cannot-be-moved)에 대한 아래 섹션을 참조하세요.

## <a name="validate-move"></a>이동 유효성 검사

[이동 작업 유효성 검사](/rest/api/resources/resources/validatemoveresources)를 수행하면 실제로 리소스를 이동하지 않고 이동 시나리오를 테스트할 수 있습니다. 이 작업을 수행하여 이동이 성공할지 여부를 확인합니다. 이 작업을 실행하려면 다음이 필요합니다.

* 원본 리소스 그룹의 이름
* 대상 리소스 그룹의 리소스 ID
* 이동할 각 리소스의 리소스 ID
* 계정에 대한 [액세스 토큰](/rest/api/azure/#acquire-an-access-token)

다음 요청을 보냅니다.

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

다음 요청 본문을 사용합니다.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

요청 서식이 올바르게 지정되면 작업은 다음을 반환합니다.

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 상태 코드는 유효성 검사 요청이 수락되었음을 나타내지만 이동 작업의 성공 여부는 아직 확인하지 않은 것입니다. `location` 값에는 장기 실행 작업의 상태를 확인하는 데 사용하는 URL이 포함되어 있습니다.  

상태를 확인하려면 다음 요청을 보냅니다.

```
GET <location-url>
Authorization: Bearer <access-token>
```

작업이 실행되는 동안에는 202 상태 코드가 계속 수신됩니다. 다시 시도하기 전에 `retry-after` 값에 지정된 시간(초) 동안 대기합니다. 이동 작업 유효성 검사가 성공적으로 수행되면 204 상태 코드가 수신됩니다. 이동 유효성 검사가 실패할 경우 다음과 같은 오류 메시지가 수신됩니다.

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>리소스 이동

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />Azure Portal 사용

리소스를 이동하려면 해당 리소스가 포함된 리소스 그룹을 선택한 후 **이동** 단추를 선택합니다.

![리소스 이동](./media/resource-group-move-resources/select-move.png)

리소스를 새 리소스 그룹으로 이동할지 또는 새 구독으로 이동할지를 선택합니다.

이동할 리소스와 대상 리소스 그룹을 선택합니다. 이러한 리소스에 대해 스크립트를 업데이트해야 함을 승인하고 **확인**을 선택합니다. 이전 단계에서 구독 편집 아이콘을 선택한 경우 대상 구독도 선택해야 합니다.

![대상 선택](./media/resource-group-move-resources/select-destination.png)

**알림**에서 이동 작업이 실행 중임을 볼 수 있습니다.

![이동 상태 표시](./media/resource-group-move-resources/show-status.png)

완료되면 결과를 알려 줍니다.

![이동 결과 표시](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>Azure PowerShell 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 [Move-AzResource](/powershell/module/az.resources/move-azresource) 명령을 사용합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

새 구독으로 이동하려면 `DestinationSubscriptionId` 매개 변수 값을 포함합니다.

### <a name="by-using-azure-cli"></a>Azure CLI 사용

기존 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 명령을 사용합니다. 이동할 리소스에 대한 리소스 ID를 제공합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. `--ids` 매개 변수에서 이동할 리소스 ID를 쉼표로 구분한 목록을 제공합니다.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

### <a name="by-using-rest-api"></a>REST API 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 다음을 실행합니다.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

요청 본문에서 대상 리소스 그룹 및 이동할 리소스를 지정합니다. 이동 REST 작업에 대한 자세한 내용은 [리소스 이동](/rest/api/resources/Resources/MoveResources)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 리소스를 관리 하기 위한 PowerShell cmdlet에 대 한 자세한 내용은 참조 하세요 [Azure PowerShell를 사용 하 여 Resource Manager를 사용한](manage-resources-powershell.md)합니다.
* 리소스를 관리 하는 것에 대 한 Azure CLI 명령에 대 한 자세한 내용은 참조 하세요 [Resource Manager를 사용한 Azure CLI를 사용 하 여](manage-resources-cli.md)입니다.
* 구독을 관리하기 위한 포털 기능에 대한 자세한 내용은 [Azure 포털을 사용하여 리소스 관리](resource-group-portal.md)를 참조하세요.
* 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)을 참조하세요.
