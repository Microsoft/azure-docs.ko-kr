---
title: Azure portal을 통해 azure HANA 큰 인스턴스 제어 | Microsoft Docs
description: 식별 하 고 포털을 통해 Azure HANA Large Instances와 상호 작용 방식에 설명 합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482230"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어
이 문서에서는 방식 [HANA 큰 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 사항은 [Azure portal](https://portal.azure.com) 및 수에 대 한 배포 된 HANA 큰 인스턴스 단위를 사용 하 여 Azure portal을 통해 어떤 작업을 수행할 수 있습니다. Azure portal에서 HANA 큰 인스턴스 표시 여부는 현재 공개 미리 보기로 제공 되는 HANA 큰 인스턴스에 대 한 Azure 리소스 공급자를 통해 제공 됩니다.

## <a name="register-hana-large-instance-resource-provider"></a>HANA 큰 인스턴스 리소스 공급자 등록
일반적으로 HANA 큰 인스턴스 배포에 사용할 Azure 구독에는 HANA 큰 인스턴스 리소스 공급자에 등록 됩니다. 그러나 HANA 큰 인스턴스 단위를 배포 하는 것이 보이지, Azure 구독에서 리소스 공급자를 등록 해야 합니다. HANA 큰 인스턴스 리소스 공급자를 등록 하는 중에 두 가지가 있습니다.

### <a name="register-through-cli-interface"></a>CLI 인터페이스를 통해 등록
Azure CLI 인터페이스를 통해 HANA 큰 인스턴스 배포에 사용 되는 Azure 구독에 로그인 해야 합니다. (다시) 등록을 HANA 큰 인스턴스 공급자이 명령 사용 하 여 수행할 수 있습니다.
    
    az provider register --namespace Microsoft.HanaOnAzure

자세한 내용은 문서를 참조 하세요. [Azure 리소스 공급자 및 형식](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Azure portal을 통해 등록
(다시) 등록을 Azure portal 통해 HANA 큰 인스턴스 리소스 공급자 수 있습니다. Azure portal에서 구독을 나열 하 고 HANA 큰 인스턴스 단위에 배포 하는 데 사용 된 구독에서 두 번 클릭 해야 합니다. 구독 개요 페이지에서 하나 아래와 같이 "리소스 공급자"를 선택 하 고 검색 창에 "HANA"를 입력 합니다. 

![Azure portal을 통해 HLI RP 등록](./media/hana-li-portal/portal-register-hli-rp.png)

표시 된 스크린샷에서 리소스 공급자를 이미 등록 되었습니다. 리소스 공급자를 아직 등록 되지 않은 경우에 "re-register" 또는 "register"를 누릅니다.

자세한 내용은 문서를 참조 하세요. [Azure 리소스 공급자 및 형식](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure portal에서 HANA 큰 인스턴스 단위의 표시
HANA 큰 인스턴스 배포 요청을 제출할 때도 HANA 큰 인스턴스에 연결 하는 Azure 구독을 지정 하 라는 메시지가 표시 합니다. 것이 좋습니다, HANA 큰 인스턴스 단위에 대해 작동 하는 SAP 응용 프로그램 계층을 배포 하는 데 사용할 동일한 구독을 사용 합니다.
처음으로 HANA 큰 인스턴스 배포를 새 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 에 HANA 큰 인스턴스에 대 한 배포 요청에 제출 된 Azure 구독에 만들어집니다.  새 리소스 그룹에는 모든 HANA 큰 인스턴스 단위 사용 하 여 특정 구독에 배포한 경우 나열 됩니다.

새 Azure 리소스 그룹을 찾기 위해 나열할 수는 리소스 그룹 구독에서 탐색을 통해 Azure portal의 왼쪽된 탐색 창

![Azure portal의 탐색 창](./media/hana-li-portal/portal-resource-group.png)

리소스 그룹의 목록에서 나와 가져오기, HANA 큰 인스턴스 배포를 사용 하 여 구독에 필터를 적용 해야 할 수 있습니다.

![Azure portal에서 리소스 그룹을 필터링](./media/hana-li-portal/portal-filtering-subscription.png)

올바른 구독을 필터링 한 후 계속 해야 리소스 그룹 목록이 긴 합니다. 사후 수정 개 찾습니다 **-Txxx** "xxx" 인 세 자리 위치와 같은 **-T050**합니다. 

리소스 그룹으로의 세부 정보를 나열 합니다. 수신은 다음과 같을 수 있습니다.

![Azure portal에서 HLI 목록](./media/hana-li-portal/portal-hli-units-list.png)

나열 된 모든 장치를 구독에 배포 된 단일 HANA 큰 인스턴스 단위를 나타내는입니다. 이 경우 보면 8 명의 서로 다른 HANA 큰 인스턴스 단위를 구독에 배포 된 합니다.

동일한 Azure 구독에서 여러 HANA 큰 인스턴스 테 넌 트를 배포한 경우에 여러 Azure 리소스 그룹을 찾을 수 있습니다. 


## <a name="look-at-attributes-of-single-hli-unit"></a>단일 HLI 단위의 특성 확인
HANA 큰 인스턴스 단위의 목록에서 단일 장치를 클릭 하 고 단일 HANA 큰 인스턴스 단위의 세부 정보를 가져올 수 있습니다. 

개요 화면에서 유사 단위의 프레젠테이션을 얻게 됩니다.

![HLI 단위의 개요를 보여 줍니다.](./media/hana-li-portal/portal-show-overview.png)

해당 특성이 표시 된 다른 특성을 보고 Azure VM 특성 보다 거의 다르게 보입니다. 왼쪽에 있는 머리글을 추가 하는 몇 가지 태그 뿐만 아니라 리소스 그룹, Azure 지역, 구독 이름 및 ID 표시 됩니다. 기본적으로 HANA 큰 인스턴스 단위 경우 할당 된 태그가 없습니다. 헤더의 오른쪽에 배포가 완료 된 경우 할당 단위의 이름을 나열 됩니다. 운영 체제와 함께 IP 주소가 표시 됩니다. 로 CPU의 수를 사용 하 여 HANA 큰 인스턴스 단위를 입력 하는 Vm을 사용 하 여 스레드 및 메모리 같습니다도 합니다. 서로 다른 HANA 큰 인스턴스 단위에 대 한 자세한 내용은 다음과 같습니다.

- [HLI에 사용 가능한 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (큰 인스턴스) 저장소 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

헤더의 오른쪽 열에서 추가 필드를 HANA 큰 인스턴스 단위의 전원 상태를 알립니다.

> [!NOTE]
> 전원 상태는 하드웨어 장치 전원이 켜져 있거나 꺼져 설명 합니다. 운영 시스템 실행 되 고 실행 하는 방법에 대 한 정보를 제공 하지 않습니다. HANA 큰 인스턴스 단위를 다시 시작 하는 대로 단위의 상태가으로 변경 하는 위치를 잠시 발생할 수 있습니다 **시작** 의 상태로 이동할 **시작**합니다. 상태의 되 **시작** OS 시작 되는 OS가 시작 된 완전히 의미 합니다. 결과적으로, 단위, 다시 시작 된 후 기대할 수는 없지만 상태 전환 하는 즉시 단위를 즉시 로그인 **Started**합니다.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>단일 HANA 큰 인스턴스 단위의 활동 확인 
HANA 큰 인스턴스 단위에 간략하게 그치지 단위의 특정 활동을 확인할 수 있습니다. 활동 로그는 다음과 같을 수 있습니다.

![Azure portal의 탐색 창](./media/hana-li-portal/portal-activity-list.png)

기록 하는 주요 작업 중 하나는 단위를 다시 시작 합니다. 활동을 활동 가져온 트리거되면 타임 스탬프의 구독 ID의 상태를 포함 하는 나열 된 데이터 작업을 트리거한 가져온 및 작업을 트리거한 Azure 사용자입니다. 

기록 시작 되는 다른 작업은 Azure 메타 데이터의 단위 변경. 작업을 시작한 다시 시작한 것 외에 보면 **HANAInstances 작성**합니다. 이 유형의 작업 자체를 HANA 큰 인스턴스 단위에서 변경 하지 않고를 수행 하지만 Azure에서 장치 메타 데이터의 변경 내용을 문서화 됩니다. 나열 된 경우에서 추가 하 고 태그를 삭제 (다음 섹션 참조).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>추가 하 고 HANA 큰 인스턴스 단위에는 Azure 태그 삭제
추가 해야 하는 또 다른 가능성은는 [태그](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) HANA 큰 인스턴스 단위에 있습니다. 태그 할당 된 방식으로 Vm에 태그를 할당에서 다르지 않습니다. Vm을 사용 하 여 태그 Azure 메타 데이터의 존재와, HANA 큰 인스턴스에 대 한 동일한 제한이 태그로 Vm에 대 한 합니다.

태그를 삭제 하면 Vm과 마찬가지로 동일한 방식으로 작동 합니다. 두 활동 모두에 적용 하 고 태그를 삭제 하는 특정 HANA 큰 인스턴스 단위에 대 한 작업 로그에 나열 됩니다.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>HANA 큰 인스턴스 단위의 속성 확인
섹션 **속성** 여러분에 게 전달 되는 인스턴스 때 얻을 수 있는 중요 한 정보가 포함 되어 있습니다. 섹션 지원이 필요할 수 있는 모든 정보는 어디서 얻을 수는 경우 또는 저장소 스냅숏 구성을 설정할 때 필요 합니다. 따라서이 섹션은 인스턴스를 연결 하 고 Azure 저장소 백 엔드 인스턴스 데이터의 컬렉션입니다. 섹션의 맨 위에 다음과 같습니다.


![Azure portal에서 HLI 속성 위쪽](./media/hana-li-portal/portal-properties-top.png)

첫 번째 몇 가지 데이터 항목 살펴보았습니다 개요 화면에 이미 있습니다. 하지만 시 중요 한 부분은 데이터는 첫 번째 배포 단위를 통해 전달 된 대로 얻은 ExpressRoute 회로 ID입니다. 일부 지원의 경우에서 해당 데이터에 대해 해야 가져올 수도 있습니다. 중요 한 데이터 항목은 스크린샷에서 맨 아래에 표시 됩니다. 표시 되는 데이터 저장소를 격리 하는 NFS 저장소 헤드의 IP 주소인 하 **테 넌 트** HANA 큰 인스턴스 스택의 합니다. 이 IP 주소를 편집할 때 필요 합니다 [저장소에 대 한 구성 파일 스냅숏 백업은](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)합니다. 

속성 창에서 아래로 스크롤하면에 HANA 큰 인스턴스 단위에 대해 고유한 리소스 ID 또는 배포에 할당 된 구독 ID와 같은 추가 데이터를 가져옵니다.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Azure portal 통해 HANA 큰 인스턴스 단위를 다시 시작
여기서 OS를 완료할 수 없습니다를 다시 시작 했습니다. 다양 한 경우가 확인 되었습니다 Linux 운영 체제를 다시 시작을 시작 합니다. 강제로 다시 시작을 위해 HANA 큰 인스턴스 단위의 power 다시 시작을 수행 하는 Microsoft 작업을 할 서비스 요청을 열 필요 합니다. HANA 큰 인스턴스 단위를 다시 시작 전원 기능의 Azure portal에 통합 되었습니다. HANA 큰 인스턴스 단위의 개요에는 표시 데이터 섹션을 기반으로 다시 시작 하는 단추

![Azure portal에서 1 단계를 다시 시작](./media/hana-li-portal/portal-restart-first-step.png)

다시 시작 단추를 누르면 하는 대로 단위를 다시 시작 하려는 지 여부를 묻는 메시지가 표시 됩니다. "Yes" 단추를 눌러 확인 하는 동안 단위 다시 시작 됩니다.

> [!NOTE]
> 다시 시작 과정에서 장치의 상태에 변경 되는 짧은 시간 경험할 **시작** 의 상태로 이동할 **시작**합니다. 상태의 되 **시작** OS 시작 되는 OS가 시작 된 완전히 의미 합니다. 결과적으로, 단위, 다시 시작 된 후 기대할 수는 없지만 상태 전환 하는 즉시 단위를 즉시 로그인 **Started**합니다.

> [!IMPORTANT]
> HANA 큰 인스턴스 단위에는 메모리의 양에 따라, 하드웨어와 운영 체제의 다시 부팅을 다시 시작 수 최대 1 시간이 소요


## <a name="open-a-support-request-for-hana-large-instances"></a>HANA 큰 인스턴스에 대 한 지원 요청을 여세요.
HANA 큰 인스턴스 단위의 Azure 포털 표시에서 HANA 큰 인스턴스 단위에도 특히에 대 한 지원 요청을 만들 수 있습니다. 링크에 따라 **새 지원 요청** 

![Azure portal에서 서비스 요청 단계 # 1를 시작 합니다.](./media/hana-li-portal/portal-initiate-support-request.png)

다음 화면에 나열 된 SAP HANA 큰 인스턴스 서비스에 가져오기 위해 선택 해야 ' 모든 서비스 "아래와 같이

![Azure 포털에서 모든 서비스를 선택 합니다.](./media/hana-li-portal/portal-create-service-request.png)

서비스 목록에서 서비스를 찾을 수 있습니다 **SAP HANA 큰 인스턴스**합니다. 해당 서비스를 선택 하 여 표시 된 것 처럼 특정 문제 유형을 선택할 수 있습니다.


![Azure 포털에서 문제 클래스를 선택 합니다.](./media/hana-li-portal/portal-select-problem-class.png)

다른 문제 유형 각각에서 다양 한 문제가 하위 문제를 더 자세히 특징을 결정 하기 위해 선택 해야 제공 됩니다. 하위 형식을 선택한 후 이제 주체를 이름을 수 있습니다. 선택 프로세스를 사용 하 여 완료 되 면 생성의 다음 단계로 이동할 수 있습니다. 에 **솔루션** 지정 하려는 섹션에서는 HANA 큰 인스턴스에 대 한 설명서를 문제의 솔루션에 대 한 포인터를 지정할 수 있습니다는. 제안 된 설명서에서 문제에 대 한 솔루션을 찾을 수 없으면, 다음 단계로 이동 합니다. 다음 단계에서는 HANA 큰 인스턴스 단위 또는 Vm을 사용 하 여 문제 인지 여부를 묻는 하려고 합니다. 이 정보는 올바른 전문가 지원 요청을 보낼 수 있습니다. 

![Azure portal에서 지원 사례의 세부 정보](./media/hana-li-portal/portal-support-request-details.png)

질문에 답변 하 고 추가 세부 정보를 제공 하는 대로 지원 요청을 제출 하는 검토 하려면 다음 단계를 이동할 수 있습니다 것입니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 SAP HANA (큰 인스턴스)를 모니터링 하는 방법](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA 쪽에서 모니터링 및 문제 해결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

