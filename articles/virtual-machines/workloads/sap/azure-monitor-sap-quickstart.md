---
title: Azure Portal를 사용 하 여 SAP 솔루션에 대 한 Azure Monitor 배포
description: Azure Portal를 사용 하 여 SAP 솔루션에 대 한 Azure Monitor 배포
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.date: 08/17/2020
ms.openlocfilehash: 2c3b11ca0c2bb916a5a3fcaf50eb99c7db3aa8e7
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88643004"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Azure Portal를 사용 하 여 SAP 솔루션에 대 한 Azure Monitor 배포

SAP 솔루션에 대 한 Azure Monitor 리소스는 [Azure Portal](https://azure.microsoft.com/features/azure-portal)를 통해 만들 수 있습니다. 이 메서드는 SAP 솔루션에 대 한 Azure Monitor를 배포 하 고 공급자를 구성 하기 위한 브라우저 기반 사용자 인터페이스를 제공 합니다.

## <a name="sign-in-to-azure-portal"></a>Azure Portal에 로그인

https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-monitoring-resource"></a>모니터링 리소스 만들기

1. **Azure Marketplace**에서 **SAP 솔루션에 대 한 Azure Monitor를** 선택 합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="이미지는 Azure Marketplace에서 SAP 솔루션 제품에 대 한 Azure Monitor를 선택 하는 방법을 보여 줍니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. **기본 사항** 탭에서 필요한 값을 입력 합니다. 해당 하는 경우 기존 Log Analytics 작업 영역을 사용할 수 있습니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Azure Portal 구성 옵션을 표시 합니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. 가상 네트워크를 선택 하는 경우 해당 VNET 내에서 모니터링할 시스템에 연결할 수 있는지 확인 합니다. 

   > [!IMPORTANT]
   > Microsoft에서 데이터 공유를 위해 **공유** 를 선택 하면 지원 팀에서 추가 지원을 제공할 수 있습니다.

## <a name="configure-providers"></a>공급자 구성

### <a name="sap-hana-provider"></a>SAP HANA 공급자 

1. 구성 하려는 공급자를 추가 하려면 **공급자** 탭을 선택 합니다. 다른 공급자를 하나씩 추가 하거나 모니터링 리소스를 배포한 후에 추가할 수 있습니다. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="SAP 솔루션에 대 한 Azure Monitor 공급자를 추가 하는 공급자 탭을 표시 합니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. **공급자 추가** 를 선택 하 고 드롭다운에서 **SAP HANA** 를 선택 합니다. 

3. HANA 서버에 대 한 개인 IP를 입력 합니다.

4. 사용 하려는 데이터베이스 테 넌 트의 이름을 입력 합니다. 그러나 모든 테 넌 트를 선택할 수 있지만, 더 광범위 한 모니터링 영역을 사용 하도록 설정 하기 위해 **Systemdb** 를 사용 하는 것이 좋습니다. 

5. HANA 데이터베이스와 연결 된 SQL 포트 번호를 입력 합니다. 포트 번호는 **[3]**  +  **[instance #]**  +  **[13]** 또는 **[3]**  +  **[instance #]**  +  **[15]** 형식 이어야 합니다. 예를 들면 30013 또는 30015입니다. 

6. 사용 하려는 데이터베이스 사용자 이름을 입력 합니다. 데이터베이스 사용자에 게 **모니터링** 및 **카탈로그 읽기** 역할이 할당 되어 있는지 확인 하십시오. 

7. 완료 되 면 **공급자 추가**를 선택 합니다. 필요에 따라 추가 공급자를 계속 추가 하거나 **검토 + 만들기** 를 선택 하 여 배포를 완료 합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="공급자 정보를 추가할 때의 구성 옵션 이미지입니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>고가용성 클러스터 (Pacemaker) 공급자

1. 드롭다운에서 **고가용성 클러스터 (Pacemaker)** 를 선택 합니다. 

   > [!IMPORTANT]
   > 고가용성 클러스터 (Pacemaker) 공급자를 구성 하려면 ha_cluster_provider 각 노드에 설치 되어 있는지 확인 합니다. 자세한 내용은 [HA 클러스터 내보내기](https://github.com/ClusterLabs/ha_cluster_exporter#installation) 를 참조 하세요.

2. 형식의 프로메테우스 끝점을 입력 http://IP:9664/metrics 합니다. 
 
3. 시스템 ID (SID), 호스트 이름 및 클러스터 이름을 입력 합니다.

4. 완료 되 면 **공급자 추가**를 선택 합니다. 필요에 따라 추가 공급자를 계속 추가 하거나 **검토 + 만들기** 를 선택 하 여 배포를 완료 합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="이미지 HA 클러스터 Pacemaker 공급자와 관련 된 옵션을 표시 합니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server 공급자

1. Microsoft SQL Server 공급자를 추가 하기 전에 SQL Server Management Studio에서 다음 스크립트를 실행 하 여 공급자를 구성 하는 데 필요한 적절 한 권한을 가진 사용자를 만들어야 합니다.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. **공급자 추가** 를 선택 하 고 드롭다운에서 **Microsoft SQL Server** 를 선택 합니다. 

3. Microsoft SQL Server 관련 된 정보를 사용 하 여 필드를 채웁니다. 

4. 완료 되 면 **공급자 추가**를 선택 합니다. 필요에 따라 추가 공급자를 계속 추가 하거나 **검토 + 만들기** 를 선택 하 여 배포를 완료 합니다.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="이미지 Microsoft SQL Server 공급자를 추가 하는 것과 관련 된 정보를 표시 합니다." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>다음 단계

[SAP 솔루션에 대 한 Azure Monitor](azure-monitor-overview.md) 에 대 한 자세한 정보
