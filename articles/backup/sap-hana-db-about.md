---
title: Azure Vm에서 SAP HANA 데이터베이스 백업 정보
description: 이 문서에서는 Azure virtual machines에서 실행 되는 SAP HANA 데이터베이스를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: aad9e7e89c54100f460a7f348702d0a59e88f519
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479463"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Azure Vm에서 SAP HANA 데이터베이스 백업 정보

SAP HANA 데이터베이스는 낮은 RPO (복구 지점 목표)와 신속한 RTO (복구 시간 목표)를 필요로 하는 중요 업무용 워크 로드입니다. 이제 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)를 사용 하 여 [Azure vm에서 실행 되는 SAP HANA 데이터베이스를 백업할](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) 수 있습니다.

Azure Backup은 SAP에서 제공 하는 [Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) 로, SAP HANA의 기본 api를 활용 하 여 네이티브 백업 지원을 제공 합니다. Azure Backup에서 제공 하는이 제품은 Azure Backup의 **제로 인프라** 백업에 맞게 조정 되므로 백업 인프라를 배포 및 관리할 필요가 없습니다. 이제 Azure Vm에서 실행 되는 SAP HANA 데이터베이스 (이제는[M 시리즈 vm](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#m-series) 도 지원 됨)를 원활 하 게 백업 하 고 복원할 수 있으며 Azure Backup에서 제공 하는 엔터프라이즈 관리 기능을 활용할 수 있습니다.

## <a name="added-value"></a>추가 된 값

Azure Backup를 사용 하 여 SAP HANA 데이터베이스를 백업 및 복원 하면 다음과 같은 이점이 있습니다.

* **15 분 복구 지점 목표 (RPO)** : 최대 15 분의 중요 한 데이터를 복구할 수 있습니다.
* **한 번 클릭으로 특정 시점 복원**: 대체 HANA 서버로 프로덕션 데이터를 복원 하는 것이 쉽습니다. 복원을 수행 하는 백업 및 카탈로그 체인은 모두 내부적으로 Azure에서 관리 됩니다.
* **장기 보존**: 엄격한 규정 준수 및 감사 요구 사항 기본 제공 수명 주기 관리 기능을 통해 복구 지점이 자동으로 정리 되는 보존 기간을 기준으로 연도에 대한 백업을 보존 합니다.
* **Azure에서의 백업 관리**: 향상 된 관리 환경을 위해 Azure Backup의 관리 및 모니터링 기능을 사용 합니다. Azure CLI도 지원 됩니다.

현재 지원 되는 백업 및 복원 시나리오를 보려면 [SAP HANA 시나리오 지원 매트릭스](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)를 참조 하세요.

## <a name="backup-architecture"></a>백업 아키텍처

![백업 아키텍처 다이어그램](./media/sap-hana-db-about/backup-architecture.png)

* 백업 프로세스는 Azure에서 [Recovery services 자격 증명 모음을 만드는](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) 것부터 시작 됩니다. 이 자격 증명 모음은 시간에 따라 생성 된 백업 및 복구 위치를 저장 하는 데 사용 됩니다.
* SAP HANA server를 실행 하는 Azure VM이 자격 증명 모음에 등록 되 고 백업할 데이터베이스가 [검색](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)됩니다. Azure Backup 서비스에서 데이터베이스를 검색할 수 있도록 하려면 HANA 서버에서 루트 사용자로 [preregistration 스크립트](https://aka.ms/scriptforpermsonhana) 를 실행 해야 합니다.
* 이 스크립트는 **hdbuserstore**에 이름이 같은 **AZUREWLBACKUPHANAUSER** DB 사용자 및 해당 키를 만듭니다. 스크립트의 용도에 대한 자세한 내용은 [사용 권한 설정 섹션](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions) 을 참조 하세요.
* Azure Backup Service는 이제 등록 된 SAP HANA 서버에 **HANA 용 Azure Backup 플러그 인** 을 설치 합니다.
* Preregistration 스크립트에서 만든 **AZUREWLBACKUPHANAUSER** DB 사용자는 **HANA에 대한 Azure Backup 플러그 인** 에서 모든 백업 및 복원 작업을 수행 하는 데 사용 됩니다. 이 스크립트를 실행 하지 않고 SAP HANA Db에 대한 백업을 구성 하려고 하면 다음과 같은 오류가 표시 될 수 있습니다. **UserErrorHanaScriptNotRun**.
* 검색 된 데이터베이스에 [백업을 구성](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) 하려면 필요한 백업 정책을 선택 하 고 백업을 사용 하도록 설정 합니다.

* 백업이 구성 되 면 Azure Backup 서비스는 보호 된 SAP HANA 서버의 데이터베이스 수준에서 다음 Backint 매개 변수를 설정 합니다.
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>이러한 매개 변수가 호스트 수준에 존재 *하지* 않는지 확인 합니다. 호스트 수준 매개 변수는 이러한 매개 변수를 재정의 하므로 예기치 않은 동작이 발생할 수 있습니다.
>

* **HANA에 대한 Azure Backup 플러그 인** 은 모든 백업 일정 및 정책 세부 정보를 유지 관리 합니다. 예약 된 백업을 트리거하고 Backint Api를 통해 **HANA 백업 엔진과** 통신 합니다.
* **HANA 백업 엔진** 은 백업할 데이터가 포함 된 backint 스트림을 반환 합니다.
* 전체 또는 차등 인 모든 예약 된 백업 및 주문형 백업 (Azure Portal에서 트리거됨)은 **HANA 용 Azure Backup 플러그 인**에 의해 시작 됩니다. 그러나 **HANA 백업 엔진** 자체에서 로그 백업을 관리 하 고 트리거합니다.

## <a name="next-steps"></a>다음 단계

* [AZURE VM에서 실행 되는 SAP HANA 데이터베이스를 복원](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) 하는 방법을 알아봅니다.
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)을 알아봅니다.
