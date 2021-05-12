---
title: Azure(큰 인스턴스)에서 SAP HANA에 대해 재해 복구 사이트로 장애 조치(failover)를 수행하는 방법 | Microsoft Docs
description: Azure(큰 인스턴스)에서 SAP HANA에 대해 재해 복구 사이트로 장애 조치(failover)를 수행하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1113e4152d444dbb1d2d1fd3e7727ebff3fe1b86
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109736631"
---
# <a name="disaster-recovery-failover-procedure"></a>재해 복구 장애 조치(failover) 프로시저


>[!IMPORTANT]
>이 문서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체하지 않습니다. 이 문서에서는 독자가 SAP HANA 관리 및 작업 중에서도 특히 백업, 복원, 고가용성 및 DR(재해 복구)에 대해 확실히 이해하고 있고 전문 지식을 가졌다고 가정합니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 표시됩니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

DR 사이트로 장애 조치(failover)할 때 고려할 두 가지 경우가 있습니다.

- SAP HANA 데이터베이스를 최신 상태의 데이터로 되돌려야 합니다. 이 경우에 Microsoft에 문의할 필요 없이 장애 조치(failover)를 수행할 수 있는 셀프 서비스 스크립트가 있습니다. 장애 복구(failback)의 경우 Microsoft와 함께 작업해야 합니다.
- 최신 복제된 스냅샷이 아닌 스토리지 스냅샷으로 복원할 수도 있습니다. 이 경우에 Microsoft로 작업해야 합니다. 

>[!NOTE]
>다음 단계는 DR 단위를 나타내는 HANA 대규모 인스턴스 단위에서 실행되어야 합니다. 
 
최근에 복제된 스토리지 스냅샷으로 복원하려면 [Azure 기반 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)의 "전체 DR 장애 조치(failover) 수행 - azure_hana_dr_failover"의 단계를 따르세요. 

여러 SAP HANA 인스턴스를 장애 조치(failover)하려는 경우 azure_hana_dr_failover 명령을 여러 번 실행하세요. 요청된 경우 장애 조치(failover)하고 복원하려는 SAP HANA SID를 입력합니다. 


실제 복제 관계에 영향을 주지 않고 DR 장애 조치(failover)를 테스트할 수 있습니다. 테스트 장애 조치(failover)를 수행하려면 [Azure 기반 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)의 "테스트 DR 장애 조치(failover) 수행 - azure_hana_test_dr_failover"의 단계를 따르세요. 

>[!IMPORTANT]
>**장애 조치(failover) 테스트** 과정을 통해 DR 사이트에서 만든 인스턴스에서 프로덕션 트랜잭션을 실행하지 *않습니다*. azure_hana_test_dr_failover 명령은 기본 사이트와 아무 관계도 없는 볼륨 세트를 만듭니다. 따라서 기본 사이트로 다시 동기화할 수 *없습니다*. 

여러 SAP HANA 인스턴스를 테스트하려는 경우 스크립트를 여러 번 실행합니다. 요청된 경우 장애 조치(failover)를 위해 테스트하려는 인스턴스의 SAP HANA SID를 입력합니다. 

>[!NOTE]
>몇 시간 전에 삭제한 일부 데이터를 복구하기 위해 장애 조치(failover)하고 DR 볼륨을 이전 스냅샷으로 설정해야 하는 경우 이 프로시저가 적용됩니다. 

1. 실행 중인 HANA 대규모 인스턴스의 재해 복구 단위에서 HANA의 비프로덕션 인스턴스를 종료합니다. 유휴 상태의 HANA 프로덕션 인스턴스가 미리 설치되어 있습니다.
1. 실행 중인 SAP HANA 프로세스가 없는지 확인해야 합니다. 이 확인에는 다음 명령을 사용합니다.

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      출력에는 **hdbdaemon** 프로세스가 중지된 상태로 표시되고, 실행 중이거나 시작된 상태의 다른 HANA 프로세스는 표시되지 않습니다.
1. 재해 복구 사이트를 복원하려는 스냅샷 이름 또는 SAP HANA 백업 ID를 결정합니다. 실제 재해 복구의 경우 이 스냅샷이 일반적으로 최신 스냅샷입니다. 손실된 데이터를 복구해야 하는 경우 이전 스냅샷을 선택하십시오.
1. 우선 순위가 높은 지원 요청을 통해 Azure 지원에 문의합니다. DR 사이트에서 해당 스냅샷(스냅샷의 이름 및 날짜를 포함) 또는 HANA 백업 ID의 복원을 요청합니다. 기본값은 작업 쪽에서 /hana/data 볼륨만 복원하는 것입니다. /hana/logbackups 볼륨도 필요하면 구체적으로 명시해야 합니다. */hana/shared 볼륨을 복원하지 마십시오.* 대신 PRD에서 /hana/shared 볼륨을 다시 탑재한 후에 **.snapshot** 디렉터리 및 해당 하위 디렉터리에서 특정 파일(예: global.ini)을 선택합니다. 

   작업 쪽에서 다음 단계가 수행됩니다.

   a. 프로덕션 볼륨에서 재해 복구 볼륨으로의 스냅샷 복제는 중지됩니다. 프로덕션 사이트의 중단으로 인해 재해 복구 절차를 수행해야 하는 경우 이러한 중단이 이미 발생했을 수 있습니다.
   
   b. 선택한 백업 ID에 해당하는 스냅샷 또는 스토리지 스냅샷 이름이 재해 복구 볼륨에 복원됩니다.
   
   다. 복원 후에는 재해 복구 볼륨을 재해 복구 지역의 HANA 대규모 인스턴스 단위에 탑재할 수 있습니다.
      
1. 재해 복구 볼륨을 재해 복구 사이트의 HANA 대규모 인스턴스 단위에 탑재하십시오. 
1. 유휴 SAP HANA 프로덕션 인스턴스를 시작합니다.
1. RPO 시간을 줄이기 위해 트랜잭션 로그 백업 로그를 복사하도록 선택한 경우 해당 트랜잭션 로그 백업을 새로 탑재된 DR/hana/logbackups 디렉터리에 병합합니다. 기존 백업을 덮어쓰지 마십시오. 스토리지 스냅샷의 최신 복제를 사용하여 복제되지 않은 최신 백업만 복사합니다.
1. DR Azure 지역의 /hana/shared/PRD 볼륨에 복제된 스냅샷에서 단일 파일을 복원할 수도 있습니다.

다음 단계에서는 복원된 스토리지 스냅샷 및 사용 가능한 트랜잭션 로그 백업에 따라 SAP HANA 프로덕션 인스턴스를 복구하는 방법을 보여줍니다.

1. SAP HANA Studio를 사용하여 백업 위치를 **/hana/logbackups** 로 변경합니다.

   ![DR 복구의 백업 위치 변경](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA는 백업 파일 위치를 검색하고, 가장 최근 트랜잭션 로그 백업을 복원할 것을 제안합니다. 아래와 같은 화면이 표시될 때까지 검사하는 데 몇 분 정도가 소요될 수 있습니다.

   ![DR 복구를 위한 트랜잭션 로그 백업 목록](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 일부 기본 설정을 조정합니다.

      - **델타 백업 사용** 선택을 취소합니다.
      - **로그 영역 초기화** 를 선택합니다.

   ![로그 영역 초기화 설정](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. **마침** 을 선택합니다.

   ![DR 복원 완료](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

여기에 표시된 것처럼 진행률 창이 표시됩니다. 해당 예제는 3노드 스케일 아웃 SAP HANA 구성의 재해 복구 복원에 대한 것입니다.

![복원 진행률](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

복원이 **마침** 화면에서 더 이상 응답하지 않고 진행률 화면이 표시되지 않는 경우 작업자 노드의 모든 SAP HANA 인스턴스가 실행되고 있는지 확인합니다. 필요한 경우 SAP HANA 인스턴스를 수동으로 시작합니다.


## <a name="failback-from-a-dr-to-a-production-site"></a>DR에서 프로덕션 사이트로 장애 복구(Failback)
DR에서 프로덕션 사이트로 장애 복구(Failback)할 수 있습니다. 재해 복구 사이트로의 장애 조치가 손실된 데이터를 복구할 필요가 아닌 프로덕션 Azure 지역의 문제로 인해 발생한 시나리오를 살펴보겠습니다. 

재해 복구 사이트에서 한동안 SAP 프로덕션 워크로드를 실행했습니다. 프로덕션 사이트의 문제가 해결되면 프로덕션 사이트로 장애 복구(Failback)하려고 할 수 있습니다. 데이터가 손실되도록 할 수는 없으므로 프로덕션 사이트로 돌아가는 단계는 Azure 운영 팀의 SAP HANA와의 몇 가지 단계 및 긴밀한 협조가 필요합니다. 문제가 해결되었을 때 운영 팀이 프로덕션 사이트로 다시 동기화를 시작하도록 트리거하는 것은 사용자의 몫입니다.

다음 단계를 수행하세요.

1. Azure 운영 팀의 SAP HANA는 프로덕션 상태를 나타내는 재해 복구 스토리지 볼륨에서 프로덕션 스토리지 볼륨을 동기화하는 트리거를 가져옵니다. 이 상태에서 프로덕션 사이트에 있는 HANA 큰 인스턴스 단위가 종료됩니다.
1. Azure 운영 팀의 SAP HANA는 복제를 모니터링하고 사용자에게 알리기 전에 인식해야 합니다.
1. 재해 복구 사이트에서 프로덕션 HANA 인스턴스를 사용하는 애플리케이션을 종료합니다. 그런 다음, HANA 트랜잭션 로그 백업을 수행합니다. 다음으로 재해 복구 사이트의 HANA 대규모 인스턴스 단위에서 실행되는 HANA 인스턴스를 중지합니다.
1. 재해 복구 사이트의 HANA 대규모 인스턴스 단위에서 실행 중인 HANA 인스턴스가 종료된 후에 운영 팀은 수동으로 디스크 볼륨을 다시 동기화합니다.
1. Azure 기반 SAP HANA 운영 팀이 프로덕션 사이트에서 HANA 대규모 인스턴스 단위를 다시 시작합니다. 그리고 개발자에게 전달합니다. 개발자는 HANA 대규모 인스턴스 단위의 시작 시간에 SAP HANA 인스턴스가 종료된 상태인지 확인해야 합니다.
1. 이전에 재해 복구 사이트로 장애 조치(failover)를 수행할 때와 동일한 데이터베이스 복원 단계를 수행합니다.

## <a name="monitor-disaster-recovery-replication"></a>재해 복구 복제 모니터링

스토리지 복제 진행 상태를 모니터링하려면 `azure_hana_replication_status` 스크립트를 실행합니다. 이 명령은 재해 복구 위치에서 실행되는 단위에서 실행해야 올바르게 작동합니다. 이 명령은 복제가 활성 상태인지 여부에 관계없이 작동합니다. 이 명령은 재해 복구 위치에서 테넌트의 모든 HANA 대규모 인스턴스 단위에 대해 실행할 수 있습니다. 부팅 볼륨에 대한 세부 정보를 가져오는 데는 사용할 수 없습니다. 

명령 및 해당 출력에 대한 자세한 내용은 [Azure 기반 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)의 "DR 복제 상태 가져오기 - azure_hana_replication_status"를 참조하세요.


## <a name="next-steps"></a>다음 단계
- [HANA 쪽에서 모니터링 및 문제 해결](hana-monitor-troubleshoot.md)을 참조하세요.
