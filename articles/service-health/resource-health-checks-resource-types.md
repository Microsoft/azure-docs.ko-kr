---
title: Azure Resource Health를 통해 지원되는 리소스 유형 | Microsoft Docs
description: Azure Resource Health를 통해 지원되는 리소스 유형
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 3a61c8b90f4dc5e52a875488afb5374e3d6b7ce4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717077"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Azure Resource Health에서 리소스 유형 및 상태 검사
다음은 리소스 유형별 리소스 상태를 통해 실행되는 모든 검사 목록입니다.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|실행된 검사|
|---|
|<ul><li>서버가 작동 중인가?</li><li>서버에 메모리가 부족한가?</li><li>서버가 시작되었는가?</li><li>서버가 복구 중인가?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|실행된 검사|
|---|
|<ul><li>API Management 서비스가 가동되고 실행 중인가?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|실행된 검사|
|---|
|<ul><li>Batch 계정이 작동 하 고 실행 되 고 있나요?</li><li>이 배치 계정에 대 한 풀 할당량을 초과 했습니까?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|실행된 검사|
|---|
|<ul><li>모든 캐시 노드가 작동 중인가?</li><li>데이터 센터 내에서 캐시에 도달할 수 있는가?</li><li>캐시가 최대 연결 수에 도달했는가?</li><li> 캐시가 사용 가능한 메모리를 모두 소모했는가? </li><li>캐시에 많은 수의 페이지 폴트가 있는가?</li><li>캐시에 부하가 큰가?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|실행된 검사|
|---|
|<ul> <li>CDN 구성 작업을 위해 보조 포털에 액세스할 수 있는가?</li><li>CDN 엔드포인트에서 진행 중인 배달 문제가 있는가?</li><li>사용자가 CDN 리소스의 구성을 변경할 수 있는가?</li><li>구성 변경 내용이 예상된 속도로 전파되는가?</li><li>사용자가 Azure Portal, PowerShell 또는 API를 사용하여 CDN 구성을 관리할 수 있는가?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|실행된 검사|
|---|
|<ul><li>호스트 서버가 작동 중인가?</li><li>호스트 OS 부팅이 완료되었는가?</li><li>가상 컴퓨터 컨테이너가 프로비전되고 전원이 공급되는가?</li><li>호스트와 스토리지 계정 간에 네트워크 연결이 되어 있는가?</li><li>게스트 OS의 부팅이 완료되었는가?</li><li>진행 중인 계획된 유지 관리가 있는가?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|실행된 검사|
|---|
|<ul><li>데이터 센터 내에서 계정에 도달할 수 있는가?</li><li>Cognitive Services 리소스 공급자를 사용할 수 있는가?</li><li>해당 지역에서 Cognitive Service를 사용할 수 있는가?</li><li>리소스 메타데이터가 있는 스토리지 계정에서 읽기 작업을 수행할 수 있는가?</li><li>API 호출 할당량에 도달했는가?</li><li>API 호출 읽기 제한에 도달했는가?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|실행된 검사|
|---|
|<ul><li>가상 머신을 호스팅하는 서버가 작동 중인가?</li><li>호스트 OS 부팅이 완료되었는가?</li><li>가상 컴퓨터 컨테이너가 프로비전되고 전원이 공급되는가?</li><li>호스트와 스토리지 계정 간에 네트워크 연결이 되어 있는가?</li><li>게스트 OS의 부팅이 완료되었는가?</li><li>진행 중인 계획된 유지 관리가 있는가?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|실행된 검사|
|---|
|<ul><li>파이프라인 실행 실패가 있나요?</li><li>Data Factory를 호스팅하는 클러스터가 정상 상태 인가요?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|실행된 검사|
|---|
|<ul><li>사용자가 Data Lake Analytics 작업을 제출하거나 나열하는 데 문제가 있는가?</li><li>시스템 오류로 인해 작업을 완료할 수 Data Lake Analytics 있습니까?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|실행된 검사|
|---|
|<ul><li>사용자가 Data Lake Store에 데이터를 업로드하는 데 문제가 있는가?</li><li>사용자가 Data Lake Store에서 데이터를 다운로드하는 데 문제가 있는가?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|실행된 검사|
|---|
|<ul><li>데이터베이스 마이그레이션 서비스를 프로 비전 하지 못했습니다.</li><li>비활성 또는 사용자 요청으로 인해 데이터베이스 마이그레이션 서비스가 중지 되었습니까?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>DataShare/계정
|실행된 검사|
|---|
|<ul><li>데이터 공유 계정이 작동 하 고 실행 되 고 있나요?</li><li>데이터 공유를 호스팅하는 클러스터를 사용할 수 있나요?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|실행된 검사|
|---|
|<ul><li>유지 관리로 인해 서버를 사용할 수 없습니까?</li><li>재구성으로 인해 서버를 사용할 수 없습니까?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|실행된 검사|
|---|
|<ul><li>유지 관리로 인해 서버를 사용할 수 없습니까?</li><li>재구성으로 인해 서버를 사용할 수 없습니까?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|실행된 검사|
|---|
|<ul><li>유지 관리로 인해 서버를 사용할 수 없습니까?</li><li>재구성으로 인해 서버를 사용할 수 없습니까?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|실행된 검사|
|---|
|<ul><li>IoT Hub가 실행되고 있는가?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|실행된 검사|
|---|
|<ul><li>Azure Cosmos DB 서비스를 사용할 수 없어서 데이터베이스 또는 컬렉션 요청이 처리되지 않았는가?</li><li>Azure Cosmos DB 서비스를 사용할 수 없어서 문서 요청이 처리되지 않았는가?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|실행된 검사|
|---|
|<ul><li>사용자 생성 오류가 발생 한 Event Hubs 네임 스페이스 입니까?</li><li>현재 Event Hubs 네임 스페이스가 업그레이드 되 고 있나요?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|실행된 검사|
|---|
|<ul><li>HDInsight 클러스터에서 핵심 서비스를 사용할 수 있나요?</li><li>HDInsight 클러스터가 휴지 상태의 BYOK 암호화에 대 한 키에 액세스할 수 있나요?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|실행된 검사|
|---|
|<ul><li>Azure KeyVault 플랫폼 문제로 인해 Key Vault에 대한 요청이 실패하는가?</li><li>고객의 요청이 너무 많아서 Key Vault에 대한 요청이 스로틀되는가?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|실행된 검사|
|---|
|<ul><li>웹 서비스를 실행 하 고 있습니까?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/windowsazure.mediaservices
|실행된 검사|
|---|
|<ul><li>미디어 서비스를 실행 하 고 있습니까?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. network/applicationgateways
|실행된 검사|
|---|
|<ul><li>Application Gateway 성능이 저하 됩니까?</li><li>Application Gateway 사용 가능 여부</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|실행된 검사|
|---|
|<ul><li>VPN 터널이 연결되어 있는가?</li><li>연결에 구성 충돌이 있는가?</li><li>미리 공유한 키를 적절히 구성하였는가?</li><li>VPN 온-프레미스 디바이스에 연결할 수 있나요?</li><li>IPSec/IKE 보안 정책에 일치하지 않는 사항이 있는가?</li><li>S2S VPN 연결이 적절히 프로비전되었는가 아니면 실패한 상태인가?</li><li>VNET-VNET 연결이 적절히 프로비전되었는가 아니면 실패한 상태인가?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. network/expressroutecircuits
|실행된 검사|
|---|
|<ul><li>Express 경로 회로가 정상 입니까?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|실행된 검사|
|---|
|<ul><li>전면 도어 백 엔드가 상태 프로브에 대 한 오류와 함께 응답 하나요?</li><li>구성 변경이 지연 되었습니까?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. 네트워크/LoadBalancers 장치
|실행된 검사|
|---|
|<ul><li>부하 분산 엔드포인트을 사용할 수 있나요?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|실행된 검사|
|---|
|<ul><li>인터넷에서 VPN Gateway에 연결할 수 있는가?</li><li>VPN Gateway가 대기 모드인가?</li><li>VPN 서비스가 게이트웨이에서 실행 중인가?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|실행된 검사|
|---|
|<ul><li>네임스페이스에서 등록, 설치 또는 전송과 같은 런타임 작업을 수행 할 수 있는가?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|실행된 검사|
|---|
|<ul><li>작업 영역에 대 한 인덱싱 지연이 있나요?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|실행된 검사|
|---|
|<ul><li>용량 리소스가 실행 중인가요?</li><li>모든 워크노드가 실행 중인가요?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|실행된 검사|
|---|
|<ul><li>호스트 OS가 작동 중인가?</li><li>데이터 센터 외부에서 workspaceCollection에 연결할 수 있는가?</li><li>Power BI 리소스 공급자를 사용할 수 있나요?</li><li>Power BI 서비스를 해당 지역에서 사용할 수 있나요?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|실행된 검사|
|---|
|<ul><li>클러스터에서 진단 작업을 수행할 수 있는가?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|실행된 검사|
|---|
|<ul><li>고객에게 사용자가 생성한 Service Bus 오류가 발생하나요?</li><li>Service Bus 네임스페이스 업그레이드로 인해 사용자에게 일시적인 오류가 증가하고 있나요?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft .SQL/managedInstances/databases
|실행된 검사|
|---|
|<ul><li>데이터베이스를 실행 하 고 있습니까?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft .SQL/서버/데이터베이스
|실행된 검사|
|---|
|<ul><li>데이터베이스에 로그인했는가?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|실행된 검사|
|---|
|<ul><li>Azure Storage 플랫폼 문제로 인해 스토리지 계정에서 데이터를 읽는 요청이 실패하는가?</li><li>Azure Storage 플랫폼 문제로 인해 스토리지 계정에 데이터를 쓰는 요청이 실패하는가?</li><li>스토리지 계정이 있는 스토리지 클러스터를 사용할 수 없는가?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|실행된 검사|
|---|
|<ul><li>작업이 실행 중인 모든 호스트가 작동 중인가?</li><li>작업을 시작할 수 없는가?</li><li>진행 중인 런타임 업데이트가 있는가?</li><li>작업이 예상된 상태인가(예: 실행 중 또는 고객에 의해 중지)?</li><li>작업에 메모리 부족 예외가 발생했는가?</li><li>진행 중인 예약된 컴퓨팅 업데이트가 있나요?</li><li>실행 관리자(제어 계획)를 사용할 수 있는가?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|실행된 검사|
|---|
|<ul><li>호스트 서버가 작동 중인가?</li><li>인터넷 정보 서비스를 실행 중인가?</li><li>부하 분산 장치를 실행 중인가?</li><li>데이터 센터 내에서 App Service 계획에 도달할 수 있는가?</li><li>serverFarm에 대한 사이트 콘텐츠를 호스팅하는 스토리지 계정이 제공되는가?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|실행된 검사|
|---|
|<ul><li>호스트 서버가 작동 중인가?</li><li>인터넷 정보 서버를 실행 중인가?</li><li>부하 분산 장치를 실행 중인가?</li><li>데이터 센터 내에서 Web App에 도달할 수 있는가?</li><li>사이트 콘텐츠를 호스팅하는 스토리지 계정이 제공되는가?</li></ul>|

## <a name="next-steps"></a>다음 단계
-  [Azure Service Health 대시보드 소개](service-health-overview.md) 및 [Azure Resource Health 소개](resource-health-overview.md)를 참조하여 자세한 내용을 확인합니다. 
-  [Azure Resource Health에 대한 질문과 대답](resource-health-faq.md)
- 상태 문제 알림을 받도록 경고를 설정합니다. 자세한 내용은 [Service Health 이벤트에 대한 경고 구성](../azure-monitor/platform/alerts-activity-log-service-notifications.md)을 참조하세요. 
