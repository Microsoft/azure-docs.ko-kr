---
title: Azure Databricks (미리 보기) 가상 네트워크에 배포
description: 이 문서에서는 가상 네트워크에 VNet 주입 라고도 Azure Databricks를 배포 하는 방법을 설명 합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126684"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Azure Databricks (미리 보기) 가상 네트워크에 배포

Azure Databricks의 기본 배포는 Azure에서 완전히 관리 되는 서비스: 가상 네트워크 (VNet)를 포함 하 여 모든 데이터 평면 리소스와 잠긴된 리소스 그룹에 배포 됩니다. 그러나 네트워크 사용자 지정에 필요한 경우 리소스를 배포 하기 Azure Databricks (또한: 이라는 VNet 주입)를 사용자 고유의 가상 네트워크의 경우 수 있습니다.

* 서비스 끝점을 사용 하 여 더 안전한 방식으로 다른 Azure 서비스 (예: Azure Storage)를 Azure Databricks를 연결 합니다.
* 사용자 정의 경로 활용 하는 Azure Databricks를 사용 하 여 사용에 대 한 소스 온-프레미스 데이터에 연결 합니다.
* 모든 아웃 바운드 트래픽을 검사에 따라 작업을 수행 하는 네트워크 가상 어플라이언스를 Azure Databricks에 연결을 허용 및 거부 규칙입니다.
* 사용자 지정 DNS 사용 하 여 Azure Databricks를 구성 합니다.
* 송신 트래픽 제한을 지정 하려면 네트워크 보안 그룹 (NSG) 규칙을 구성 합니다.
* 기존 가상 네트워크에서 Azure Databricks 클러스터를 배포 합니다.

유연한 CIDR 범위를 활용할 수 있습니다 Azure Databricks 리소스 사용자 고유의 가상 네트워크를 배포할 (/16-사이의 아무 곳 이나 / 24 및 /18-사이 가상 네트워크 / 26 서브넷에 대 한).

  > [!NOTE]
  > 기존 작업 영역에 대 한 가상 네트워크를 바꿀 수 없습니다. 현재 작업 영역 수 없습니다. 필요한 활성 클러스터 노드 수를 수용할 수 있도록 하는 경우 더 큰 가상 네트워크에서 다른 작업 영역을 만듭니다. 따릅니다 [이러한 자세한 마이그레이션 단계](howto-regional-disaster-recovery.md#detailed-migration-steps) 복사할 리소스 (예: 노트북, 클러스터 구성, 작업) 이전에서 새 작업 영역입니다.

## <a name="virtual-network-requirements"></a>Virtual network 요구 사항

Azure portal에서 Azure Databricks 작업 영역 배포 인터페이스를 사용 하 여 자동으로 필요한 서브넷, 네트워크 보안 그룹 및 허용 목록에 추가 설정을 사용 하 여 기존 가상 네트워크를 구성 하려면 또는 Azure Resource Manager를 사용할 수 있습니다. 가상 네트워크를 구성 하 고 작업 영역을 배포 하는 템플릿.

Azure Databricks 작업 영역을 배포 하는 가상 네트워크에는 다음 요구 사항을 충족 해야 합니다.

### <a name="location"></a>Location

가상 네트워크는 Azure Databricks 작업 영역으로 동일한 위치에 있어야 합니다.

### <a name="subnets"></a>서브넷

가상 네트워크는 Azure Databricks에 전용 되는 두 개의 서브넷을 포함 해야 합니다.

   1. 클러스터 내부 통신을 허용 하는 구성 된 네트워크 보안 그룹을 사용 하 여 개인 서브넷

   2. Azure Databricks 제어 평면으로 통신할 수 있도록 구성 된 네트워크 보안 그룹을 통해 공개 서브넷입니다.

### <a name="address-space"></a>주소 공간

/ 16 인 가상 네트워크에 대해/24 CIDR 블록을 사설 및 공용 서브넷에 대 한 /18/26 사이 사이의 CIDR 블록입니다.

### <a name="whitelisting"></a>허용 목록

서브넷 및 Azure Databricks 제어 평면 간의 모든 아웃 바운드 및 인바운드 트래픽을 허용 목록에 추가 해야 합니다.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

이 섹션에서는 Azure portal에서 Azure Databricks 작업 영역을 만들고 자신의 기존 가상 네트워크에 배포 하는 방법을 설명 합니다. 두 개의 새 서브넷 및 사용자가 제공 하는 CIDR 범위를 사용 하 여 네트워크 보안 그룹을 사용 하 여 가상 네트워크를 업데이트 하는 azure Databricks, 허용 목록에 인바운드 및 아웃 바운드 서브넷 트래픽을 업데이트 된 가상 네트워크에 작업 영역을 배포 합니다.

## <a name="prerequisites"></a>필수 조건

가상 네트워크를 Azure Databricks 작업 영역 배포 해야 합니다. 기존 가상 네트워크를 사용 하거나 새 대시보드를 만들 수 있습니다 하지만 가상 네트워크와 Azure Databricks 작업 영역을 만들려는 동일한 지역에 있어야 합니다. / 16/24 사이의 CIDR 범위는 가상 네트워크가 필요 합니다.

  > [!Warning]
  > 더 작은 가상 네트워크를 사용 하 여 작업 영역을 – 즉, 더 낮은 CIDR 범위 – 수 부족 IP 주소 (네트워크 공간) 보다 더 큰 가상 네트워크를 사용 하 여 작업 영역을 보다 신속 하 게 합니다. 예를 들어/24 사용 하 여 작업 영역 가상 네트워크 및/26 서브넷 반면 한 번에 활성 노드를 64 개까지 있을 수 있습니다는 /20을 사용 하 여 작업 영역을 가상 네트워크 및 롤아웃의 경우/22 서브넷에는 최대 1024 노드가 포함 될 수 있습니다.

  서브넷을 작업 영역을 구성 하 고 기회를 구성 하는 동안 서브넷의 CIDR 범위를 제공 해야 하는 경우 자동으로 생성 됩니다.

## <a name="configure-the-virtual-network"></a>가상 네트워크 구성

1. Azure portal에서 선택 **+ 리소스 만들기 > Analytics > Azure Databricks** Azure Databricks 서비스 대화 상자를 엽니다.

2. 2 단계에서에서 설명 하는 구성 단계를 수행 합니다. 시작 하는 시작 가이드에서는 Azure Databricks 작업 영역 만들기 및 배포 하는 Azure Databricks 작업 영역에 가상 네트워크 옵션을 선택 합니다.

   ![Azure Databricks 서비스 만들기](./media/vnet-injection/create-databricks-service.png)

3. 사용 하려는 가상 네트워크를 선택 합니다.

   ![가상 네트워크 옵션](./media/vnet-injection/select-vnet.png)

4. 두 서브넷의 경우 Azure Databricks에 전용 /18/26 사이 블록에서 CIDR 범위를 제공 합니다.

   * 공용 서브넷을 Azure Databricks 제어 평면으로 통신할 수 있도록 연결 된 네트워크 보안 그룹을 사용 하 여 생성 됩니다.
   * 클러스터 내부 통신을 허용 하는 연결 된 네트워크 보안 그룹을 사용 하 여 개인 서브넷 만들어질 수 있습니다.

5. 클릭 **만들기** 가상 네트워크에 Azure Databricks 작업 영역을 배포 합니다.

## <a name="advanced-resource-manager-configurations"></a>고급 리소스 관리자 구성

사용자 고유의 보안 규칙 – 만들거나 기존 서브넷을 사용 하 여, 기존 네트워크 보안 그룹을 사용 하려면 예를 들어, 가상 네트워크의 구성을 통해 제어 하려는 경우 대신 다음 Azure Resource Manager 템플릿을 사용할 수 있습니다는 포털의 가상 네트워크 구성 및 작업 영역 배포 합니다.

### <a name="all-in-one"></a>통합형

하나에서 모든 가상 네트워크, 네트워크 보안 그룹 및 Azure Databricks 작업 영역을 만들려면 사용 합니다 [Databricks VNet 삽입 작업 영역에 대 한 모든 통합 된 단일 템플릿을](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)합니다.

이 템플릿을 사용 하는 경우 모든 수동 허용 목록에 추가 서브넷 트래픽 할 필요가 없습니다.

### <a name="network-security-groups"></a>네트워크 보안 그룹

기존 가상 네트워크에 대 한 필요한 규칙을 사용 하 여 네트워크 보안 그룹을 만들기를 사용 합니다 [Databricks VNet 삽입에 대 한 네트워크 보안 그룹 템플릿](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection)합니다.

이 템플릿을 사용 하는 경우 모든 수동 허용 목록에 추가 서브넷 트래픽 할 필요가 없습니다.

### <a name="virtual-network"></a>가상 네트워크

적절 한 공용 및 개인 서브넷과 가상 네트워크를 만들려면 사용 합니다 [Databricks VNet 주입을 위한 가상 네트워크 템플릿을](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection)합니다.

또한 네트워크 보안 그룹 템플릿을 사용 하지 않고이 서식 파일을 사용 하는 경우 가상 네트워크를 사용 하는 네트워크 보안 그룹에 허용 목록 규칙을 수동으로 추가 해야 합니다.

### <a name="azure-databricks-workspace"></a>Azure Databricks 작업 영역

Azure Databricks 작업 영역을 공용 및 개인 서브넷 및 올바르게 구성 된 네트워크 보안 그룹을 이미 설정 된 기존 가상 네트워크를 배포 하려면 사용 합니다 [Databricks VNet 주입을 위한 작업 영역 템플릿은](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection)합니다.

또한 네트워크 보안 그룹 템플릿을 사용 하지 않고이 서식 파일을 사용 하는 경우 가상 네트워크를 사용 하는 네트워크 보안 그룹에 허용 목록 규칙을 수동으로 추가 해야 합니다.

## <a name="whitelisting-subnet-traffic"></a>허용 목록 서브넷 트래픽

사용 하지 않는 경우는 [Azure portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) 또는 [Azure Resource Manager 템플릿](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) 네트워크 보안 그룹을 만들려면 수동으로 해야 다음 트래픽 허용 목록을 서브넷에 있습니다.

|Direction|프로토콜|원본|원본 포트|대상|대상 포트|
|---------|--------|------|-----------|-----------|----------------|
|인바운드|\*|VirtualNetwork|\*|\*|\*|
|인바운드|\*|제어 평면 NAT IP|\*|\*|22|
|인바운드|\*|제어 평면 NAT IP|\*|\*|5557|
|아웃바운드|\*|\*|\*|Webapp IP|\*|
|아웃바운드|\*|\*|\*|SQL (서비스 태그)|\*|
|아웃바운드|\*|\*|\*|저장소 (서비스 태그)|\*|
|아웃바운드|\*|\*|\*|VirtualNetwork|\*|

다음 IP를 사용 하 여 허용 목록에 추가 서브넷 트래픽 해결 합니다. SQL (metastore)와 Storage (아티팩트 및 로그 저장소)에 대 한 Sql 및 저장소를 사용 해야 [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)합니다.

|Azure Databricks 지역|서비스|공용 IP|
|-----------------------|-------|---------|
|미국 동부|제어 평면 NAT </br></br>웹 앱|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 동부 2|제어 평면 NAT </br></br>웹 앱|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 중북부|제어 평면 NAT </br></br>웹 앱|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 중부|제어 평면 NAT </br></br>웹 앱|23.101.152.95/32 </br></br>40.70.58.221/32|
|미국 중남부|제어 평면 NAT </br></br>웹 앱|40.83.178.242/32 </br></br>40.118.174.12/32|
|미국 서부|제어 평면 NAT </br></br>웹 앱|40.83.178.242/32 </br></br>40.118.174.12/32|
|미국 서부 2|제어 평면 NAT </br></br>웹 앱|40.83.178.242/32 </br></br>40.118.174.12/32|
|캐나다 중부|제어 평면 NAT </br></br>웹 앱|40.85.223.25/32 </br></br>13.71.184.74/32|
|캐나다 동부|제어 평면 NAT </br></br>웹 앱|40.85.223.25/32 </br></br>13.71.184.74/32|
|영국 서부|제어 평면 NAT </br></br>웹 앱|51.140.203.27/32 </br></br>51.140.204.4/32|
|영국 남부|제어 평면 NAT </br></br>웹 앱|51.140.203.27/32 </br></br>51.140.204.4/32|
|서유럽|제어 평면 NAT </br></br>웹 앱|23.100.0.135/32 </br></br>52.232.19.246/32|
|유럽 북부|제어 평면 NAT </br></br>웹 앱|23.100.0.135/32 </br></br>52.232.19.246/32|
|중앙 인도|제어 평면 NAT </br></br>웹 앱|104.211.89.81/32 </br></br>104.211.101.14/32|
|인도 남부|제어 평면 NAT </br></br>웹 앱|104.211.89.81/32 </br></br>104.211.101.14/32|
|인도 서부|제어 평면 NAT </br></br>웹 앱|104.211.89.81/32 </br></br>104.211.101.14/32|
|동남아시아|제어 평면 NAT </br></br>웹 앱|52.187.0.85/32 </br></br>52.187.145.107/32|
|동아시아|제어 평면 NAT </br></br>웹 앱|52.187.0.85/32 </br></br>52.187.145.107/32|
|오스트레일리아 동부|제어 평면 NAT </br></br>웹 앱|13.70.105.50/32 </br></br>13.75.218.172/32|
|오스트레일리아 남동부|제어 평면 NAT </br></br>웹 앱|13.70.105.50/32 </br></br>13.75.218.172/32|
|오스트레일리아 중부|제어 평면 NAT </br></br>웹 앱|13.70.105.50/32 </br></br>13.75.218.172/32|
|오스트레일리아 중부 2|제어 평면 NAT </br></br>웹 앱|13.70.105.50/32 </br></br>13.75.218.172/32|
|일본 동부|제어 평면 NAT </br></br>웹 앱|13.78.19.235/32 </br></br>52.246.160.72/32|
|일본 서부|제어 평면 NAT </br></br>웹 앱|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>문제 해결

### <a name="workspace-launch-errors"></a>작업 영역 시작 오류

사용자 지정 가상 네트워크에서 작업 영역 시작 실패 Azure Databricks에서 다음 오류를 사용 하 여 화면에 로그인 합니다. **"작업 영역을 만드는 오류가 발생 했습니다. 사용자 지정 네트워크 구성이 올바른지 확인 하 고 다시 시도 하십시오. "**

이 오류는 네트워크 구성 요구 사항에 맞지 않음으로 발생 합니다. 작업 영역을 만들 때이 항목의 지침을 수행 했는지 확인 합니다.

### <a name="cluster-creation-errors"></a>클러스터 만들기 오류

**인스턴스에 연결할 수 없습니다. 리소스는 SSH를 통해 연결할 수 없습니다.**

가능한 원인: 작업자 제어 평면에서 트래픽이 차단 됩니다. 인바운드 보안 규칙 요구 사항을 충족 하는지 확인 하 여 수정 합니다. 온-프레미스 네트워크에 연결 된 기존 가상 네트워크에 배포 하는 경우 온-프레미스 네트워크에 연결 하는 Azure Databricks 작업 영역에서 제공 되는 정보를 사용 하 여 설치를 검토 합니다.

**예기치 않은 시작 실패: 클러스터를 설정 하는 동안 예기치 않은 오류가 발생 했습니다. 다시 시도 하 고 문제가 계속 되 면 Azure Databricks에 게 문의 합니다. 내부 오류 메시지: 노드를 배치 하는 동안 시간이 초과 되었습니다.**

가능한 원인: Azure Storage 끝점에 작업자에서 트래픽이 차단 됩니다. 아웃 바운드 보안 규칙 요구 사항을 충족 하는지 확인 하 여 수정 합니다. 사용자 지정 DNS 서버를 사용 하는 경우에 가상 네트워크에 DNS 서버의 상태를 확인할 수도 있습니다.

**클라우드 공급자 시작 실패: 클러스터를 설정 하는 동안 클라우드 공급자 오류가 발생 했습니다. 자세한 내용은 Azure Databricks 가이드를 참조 하십시오. Azure 오류 코드: AuthorizationFailed/InvalidResourceReference.**

가능한 원인: 가상 네트워크 또는 서브넷 더 이상 존재 하지 않습니다. 가상 네트워크 및 서브넷이 존재 하는지 확인 합니다.

**클러스터를 종료 합니다. 원인: Spark 시작 오류: Spark가 시간에 시작 하지 못했습니다. Hive metastore를 제대로 작동 하지 않는, 잘못 된 Spark 구성 또는 오작동 init 스크립트에서이 문제를 발생할 수 있습니다. 문제가 지속 되 면 Databricks 문의 및이 문제를 해결 하려면 Spark 드라이버 로그를 참조 합니다. 내부 오류 메시지: Spark를 시작 하지 못했습니다. 드라이버 시간에 시작 하지 못했습니다.**

가능한 원인: 컨테이너 호스팅 인스턴스 또는 storage 계정을 DBFS와 통신할 수 없습니다. 다음 홉 인터넷 되를 사용 하 여 DBFS 저장소 계정에 대 한 서브넷에 사용자 지정 경로 추가 하 여 수정 합니다.

### <a name="notebook-command-errors"></a>Notebook 명령 오류

**명령 응답 하지 않습니다.**

가능한 원인: 작업자-작업자 통신이 차단 됩니다. 인바운드 보안 규칙 요구 사항을 충족 하는지 확인 하 여 수정 합니다.

**전자 필기장 워크플로 예외와 함께 실패: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

가능한 원인: Azure Databricks Webapp 작업자에서 트래픽이 차단 됩니다. 아웃 바운드 보안 규칙 요구 사항을 충족 하는지 확인 하 여 수정 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Databricks를 사용하여 데이터 추출, 변환 및 로드](databricks-extract-load-sql-data-warehouse.md)
