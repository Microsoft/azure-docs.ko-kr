---
title: Azure Portal를 통한 Azure HANA Large Instances 제어 | Microsoft Docs
description: 포털을 통해 Azure HANA Large Instances를 식별 하 고 상호 작용 하는 방법에 대해 설명 합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099809"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어
이 문서에서는 [Azure Portal](https://portal.azure.com) 에 [hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 를 표시 하는 방법 및 사용자를 위해 배포 되는 hana large Instance 단위를 사용 하 여 Azure Portal를 통해 수행할 수 있는 작업을 설명 합니다. Azure Portal에서 HANA Large Instances의 표시 유형은 현재 공개 미리 보기로 제공 되는 HANA Large Instances 용 Azure 리소스 공급자를 통해 제공 됩니다.

## <a name="register-hana-large-instance-resource-provider"></a>HANA Large Instance 리소스 공급자 등록
일반적으로 HANA Large Instance 배포에 사용 하 던 Azure 구독은 HANA Large Instance 리소스 공급자에 대해 등록 됩니다. 그러나 HANA Large Instance 장치를 배포 하지 않은 경우 Azure 구독에 리소스 공급자를 등록 해야 합니다. HANA Large Instance 리소스 공급자를 등록 하는 방법에는 두 가지가 있습니다.

### <a name="register-through-cli-interface"></a>CLI 인터페이스를 통해 등록
Azure CLI 인터페이스를 통해 HANA Large Instance 배포에 사용 되는 Azure 구독에 로그인 해야 합니다. 다음 명령을 사용 하 여 HANA Large Instance 공급자를 다시 등록할 수 있습니다.
    
    az provider register --namespace Microsoft.HanaOnAzure

자세한 내용은 [Azure 리소스 공급자 및 형식](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli) 문서를 참조 하세요.


### <a name="register-through-azure-portal"></a>Azure Portal를 통해 등록
Azure Portal를 통해 HANA Large Instance 리소스 공급자를 다시 등록할 수 있습니다. Azure Portal에서 구독을 나열 하 고 HANA Large Instance 단위를 배포 하는 데 사용 된 구독을 두 번 클릭 해야 합니다. 구독의 개요 페이지에서 아래와 같이 "리소스 공급자"를 선택 하 고 검색 창에 "HANA"를 입력 합니다. 

![Azure Portal를 통해 HLI RP 등록](./media/hana-li-portal/portal-register-hli-rp.png)

표시 된 스크린샷에서 리소스 공급자가 이미 등록 되었습니다. 리소스 공급자가 아직 등록 되지 않은 경우 "재등록" 또는 "register"를 누릅니다.

자세한 내용은 [Azure 리소스 공급자 및 형식](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell) 문서를 참조 하세요.


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure Portal에서 HANA Large Instance 단위 표시
HANA Large Instance 배포 요청을 제출 하는 경우 HANA Large Instances에 연결 하는 Azure 구독을 지정 하 라는 메시지가 표시 됩니다. HANA Large Instance 장치에 대해 작동 하는 SAP 응용 프로그램 계층을 배포 하는 데 사용 하는 것과 동일한 구독을 사용 하는 것이 좋습니다.
첫 번째 HANA 대량 인스턴스가 배포 되 면 HANA Large Instance에 대 한 배포 요청에서 제출한 Azure 구독에 새 [azure 리소스 그룹이](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 생성 됩니다.  새 리소스 그룹은 특정 구독에 배포한 모든 HANA Large Instance 단위를 나열 합니다.

새 Azure 리소스 그룹을 찾기 위해 Azure Portal의 왼쪽 탐색 창에서 이동 하 여 구독에 리소스 그룹을 나열 합니다.

![Azure Portal의 탐색 창](./media/hana-li-portal/portal-resource-group.png)

리소스 그룹 목록에 나열 되는 경우 HANA 대량 인스턴스를 배포 하는 데 사용한 구독을 필터링 해야 할 수 있습니다.

![Azure Portal에서 리소스 그룹 필터링](./media/hana-li-portal/portal-filtering-subscription.png)

올바른 구독으로 필터링 한 후에도 긴 리소스 그룹 목록이 있을 수 있습니다. **-Txxx** 의 수정 후 "xxx"는 **-T050**와 같이 세 자리 숫자로 된 항목을 찾습니다. 

리소스 그룹을 찾았으면 세부 정보를 나열 합니다. 받은 목록은 다음과 같습니다.

![Azure Portal의 HLI 목록](./media/hana-li-portal/portal-hli-units-list.png)

나열 된 모든 단위는 구독에 배포 된 단일 HANA Large Instance 단위를 나타냅니다. 이 경우 구독에 배포 된 8 개의 다른 HANA 대량 인스턴스 단위를 확인 합니다.

동일한 Azure 구독에 여러 HANA Large Instance 테넌트를 배포한 경우 여러 Azure 리소스 그룹을 찾을 수 있습니다. 


## <a name="look-at-attributes-of-single-hli-unit"></a>단일 HLI 단위의 특성 살펴보기
HANA Large Instance 단위 목록에서 단일 단위를 클릭 하 고 단일 HANA Large Instance 유닛의 세부 정보를 가져올 수 있습니다. 

개요 화면에서 ' 자세히 표시 '를 클릭 하면 다음과 같은 단위의 프레젠테이션이 표시 됩니다.

![HLI unit의 개요 표시](./media/hana-li-portal/portal-show-overview.png)

표시 되는 다양 한 특성을 살펴보면 이러한 특성은 Azure VM 특성과 매우 다르게 보입니다. 왼쪽 머리글에서 리소스 그룹, Azure 지역, 구독 이름 및 ID 뿐만 아니라 추가한 일부 태그를 표시 합니다. 기본적으로 HANA Large Instance 장치에는 할당 된 태그가 없습니다. 헤더의 오른쪽에는 배포를 완료할 때 단위 이름이 할당 됨으로 나열 됩니다. 운영 체제가 IP 주소와 함께 표시 됩니다. Vm과 마찬가지로 CPU 스레드 및 메모리의 수를 포함 하는 HANA Large instance unit 형식이 표시 됩니다. 여러 HANA 대량 인스턴스 단위에 대 한 자세한 내용은 여기에 나와 있습니다.

- [HLI에 사용 가능한 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (대량 인스턴스) 저장소 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

오른쪽 아래에 있는 추가 데이터는 HANA Large Instance 스탬프의 수정 버전입니다. 가능한 값은

- 수정 버전 3
- 수정 버전 4

수정 버전 4는 HANA 큰 인스턴스에서 릴리스된 최신 아키텍처로, 수정 버전 4 스탬프 또는 행에 배포 된 Azure Vm과 HANA 큰 인스턴스 단위 간의 네트워크 대기 시간이 크게 향상 되었습니다.
개요의 오른쪽 아래에는 배포 된 각 HANA 큰 인스턴스 단위에 대해 자동으로 생성 되는 Azure 근접 배치 그룹의 이름이 포함 된 또 다른 매우 중요 한 정보가 있습니다. 이 근접 배치 그룹은 SAP 응용 프로그램 계층을 호스트 하는 Azure Vm을 배포할 때 참조 해야 합니다. HANA Large Instance 단위와 연결 된 [azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) 을 사용 하 여 azure VM이 Hana large instance 유닛에 가까운 곳에 배포 되는지 확인 합니다. [Sap 응용 프로그램의 최적의 네트워크 대기 시간을 위해 Azure 근접 배치 그룹에서 Azure 근접 배치 그룹에 설명 된 것 처럼 근접 배치 그룹을 사용 하 여 동일한 azure 데이터 센터에서 sap 응용 프로그램 계층을 찾을 수 있는 방법을 설명 합니다. ](sap-proximity-placement-scenarios.md).

헤더의 오른쪽 열에 있는 추가 필드는 HANA Large instance 유닛의 전원 상태에 대 한 정보를 알려 줍니다.

> [!NOTE]
> 전원 상태는 하드웨어 장치의 전원이 켜 졌는 지 여부를 나타냅니다. 운영 체제를 실행 중인 운영 체제에 대 한 정보는 제공 하지 않습니다. HANA Large Instance 단위를 다시 시작 하면 단위 상태가 **시작 됨**상태로 전환 되기 **시작** 하는 데 약간의 시간이 발생 합니다. **시작 됨** 상태에 있으면 os가 시작 되거나 os가 완전히 시작 된 것입니다. 따라서 단위를 다시 시작한 후 상태가 **시작 됨**으로 전환 되는 즉시 단위에 즉시 로그인 할 수 없습니다.
> 

' 자세히 보기 '를 누르면 추가 정보가 표시 됩니다. 추가 정보 중 하나는 HANA Large Instance 스탬프의 수정 버전을 표시 하는 것입니다. 단위는에서 배포 되었습니다. HANA Large Instance 스탬프의 여러 수정 사항에 대해서는 [Azure (Large Instances)의 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 문서를 참조 하세요.

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>단일 HANA Large Instance 유닛의 활동 확인 
HANA 큰 인스턴스 단위에 대 한 개요를 제공 하는 것 외에도 특정 단위의 활동을 확인할 수 있습니다. 활동 로그는 다음과 같습니다.

![Azure Portal의 탐색 창](./media/hana-li-portal/portal-activity-list.png)

기록 된 주요 작업 중 하나는 단위 다시 시작입니다. 나열 된 데이터에는 활동의 상태, 활동이 트리거된 타임 스탬프, 활동이 트리거된 구독 ID 및 활동을 트리거한 Azure 사용자가 포함 됩니다. 

기록 되는 다른 작업은 Azure 메타 데이터의 단위에 대 한 변경 내용입니다. 다시 시작을 시작 하는 것 외에도 **Write HANAInstances**의 작업을 볼 수 있습니다. 이 유형의 활동은 HANA Large Instance 단위 자체에서 변경 하지 않고 Azure에서 단위의 메타 데이터에 대 한 변경 내용을 문서화 합니다. 나열 된 경우 태그를 추가 하 고 삭제 했습니다 (다음 섹션 참조).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>HANA Large Instance 유닛에 Azure 태그 추가 및 삭제
HANA Large Instance 유닛에 [태그](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 를 추가할 수도 있습니다. 태그를 할당 하는 방법은 Vm에 태그를 할당 하는 것과 다릅니다. Vm과 마찬가지로 태그는 Azure 메타 데이터에 존재 하 고 HANA Large Instances의 경우 Vm에 대 한 태그와 동일한 제한을 가집니다.

태그 삭제는 Vm과 동일한 방식으로 작동 합니다. 두 활동, 즉 태그를 적용 하 고 삭제 하는 작업은 특정 HANA Large Instance 유닛의 활동 로그에 나열 됩니다.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>HANA Large Instance 유닛의 속성 확인
섹션 **속성** 에는 인스턴스가 사용자에 게 전달 될 때 얻을 수 있는 중요 한 정보가 포함 되어 있습니다. 지원 사례에서 요구 하거나 저장소 스냅숏 구성을 설정할 때 필요한 모든 정보를 얻을 수 있는 섹션입니다. 이 섹션은 인스턴스 주위의 데이터 모음으로, 인스턴스를 Azure에 연결 하 고 저장소 백 엔드에 연결 합니다. 섹션의 맨 위는 다음과 같습니다.


![Azure Portal에서 HLI 속성의 위쪽 부분](./media/hana-li-portal/portal-properties-top.png)

개요 화면에서 이미 보았던 처음 몇 개의 데이터 항목입니다. 하지만 데이터의 중요 한 부분은 첫 번째 배포 된 단위가 전달 될 때 가져온 Express 경로 회로 ID입니다. 일부 지원 사례에서는 해당 데이터를 묻는 메시지가 표시 될 수 있습니다. 중요 한 데이터 항목이 스크린샷 아래쪽에 표시 됩니다. 표시 되는 데이터는 저장소를 HANA Large Instance stack의 **테넌트** 로 격리 하는 NFS 저장소 헤드의 IP 주소입니다. 이 IP 주소는 [저장소 스냅숏 백업에 대 한 구성 파일](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)을 편집 하는 경우에도 필요 합니다. 

속성 창에서 아래로 스크롤하면 HANA 대량 인스턴스 단위에 대 한 고유 리소스 ID 또는 배포에 할당 된 구독 ID와 같은 추가 데이터를 얻게 됩니다.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Azure Portal를 통해 HANA Large Instance 단위 다시 시작
Linux 운영 체제를 다시 시작 하는 경우 OS에서 성공적으로 다시 시작을 완료할 수 없는 다양 한 상황이 있었습니다. 강제로 다시 시작 하려면 Microsoft 작업에서 HANA Large Instance unit의 전원 다시 시작을 수행 하도록 서비스 요청을 열어야 했습니다. HANA Large Instance 유닛의 전원 다시 시작 기능이 이제 Azure Portal에 통합 되었습니다. HANA Large Instance 유닛의 개요 부분에 있는 것 처럼 데이터 섹션 위에 다시 시작 단추가 표시 됩니다.

![Azure Portal 단계를 다시 시작 #1](./media/hana-li-portal/portal-restart-first-step.png)

다시 시작 단추를 누르면 단위를 다시 시작할지 여부를 묻는 메시지가 표시 됩니다. "예" 단추를 눌러 확인 하면 단위가 다시 시작 됩니다.

> [!NOTE]
> 다시 시작 프로세스에서 장치 상태가 **시작 됨**상태로 전환 되는 데 걸리는 시간을 약간의 시간이 발생 합니다. **시작 됨** 상태에 있으면 os가 시작 되거나 os가 완전히 시작 된 것입니다. 따라서 단위를 다시 시작한 후 상태가 **시작 됨**으로 전환 되는 즉시 단위에 즉시 로그인 할 수 없습니다.

> [!IMPORTANT]
> HANA Large Instance 유닛의 메모리 양에 따라 하드웨어를 다시 시작 하 고 다시 부팅 하 고 운영 체제를 최대 1 시간까지 걸릴 수 있습니다.


## <a name="open-a-support-request-for-hana-large-instances"></a>HANA large Instances에 대 한 지원 요청 열기
HANA Large Instance 유닛의 Azure Portal 표시 되지 않으므로 HANA large Instance 유닛에 대 한 지원 요청을 만들 수 있습니다. **새 지원 요청** 링크를 수행 하는 동안 

![Azure Portal에서 서비스 요청 단계 시작 #1](./media/hana-li-portal/portal-initiate-support-request.png)

다음 화면에 나열 된 SAP HANA(대규모 인스턴스) 서비스를 가져오려면 아래와 같이 ' 모든 서비스 '를 선택 해야 할 수 있습니다.

![Azure Portal의 모든 서비스 선택](./media/hana-li-portal/portal-create-service-request.png)

서비스 목록에서 서비스 **SAP HANA Large Instance**를 찾을 수 있습니다. 해당 서비스를 선택 하면 다음과 같이 특정 문제 유형을 선택할 수 있습니다.


![Azure Portal에서 문제 클래스를 선택 합니다.](./media/hana-li-portal/portal-select-problem-class.png)

각각의 문제 유형에 따라 문제에 대 한 특성을 추가로 제공 하기 위해 선택 해야 하는 문제 하위 유형 중에서 선택할 수 있습니다. 하위 유형을 선택한 후에는 제목 이름을 지정할 수 있습니다. 선택 프로세스를 완료 한 후에는 만들기의 다음 단계로 이동할 수 있습니다. **솔루션** 섹션에서 문제의 해결 방법에 대 한 포인터를 제공할 수 있는 HANA Large Instances에 대 한 설명서를 가리킵니다. 제안 된 설명서에서 문제에 대 한 해결 방법을 찾을 수 없으면 다음 단계로 이동 합니다. 다음 단계에서는 Vm과 관련 된 문제 인지 또는 HANA Large Instance 장치와 관련 된 문제 인지를 묻는 메시지가 표시 됩니다. 이 정보를 통해 지원 요청을 올바른 전문가에 게 지시할 수 있습니다. 

![Azure Portal 지원 사례의 세부 정보](./media/hana-li-portal/portal-support-request-details.png)

질문에 대 한 답변을 제공 하 고 추가 세부 정보를 제공 하면 지원 요청을 검토 하 고 제출 하는 다음 단계로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 SAP HANA (large instances)를 모니터링 하는 방법](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA 쪽에서 모니터링 및 문제 해결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

