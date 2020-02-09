---
title: SAP HANA Backup 지원 매트릭스
description: 이 문서에서는 azure backup을 사용 하 여 Azure Vm에 SAP HANA 데이터베이스를 백업할 때 지원 되는 시나리오 및 제한 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 82d844385290ab0dc2953537c1f9a3387dd7b2b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842634"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM의 SAP HANA 데이터베이스 백업에 대한 지원 매트릭스

Azure Backup는 Azure에 대한 SAP HANA 데이터베이스의 백업을 지원 합니다. 이 문서에서는 Azure Backup를 사용 하 여 Azure Vm에서 SAP HANA 데이터베이스를 백업 하는 경우 제공 되는 시나리오 및 제한 사항을 요약 합니다.

## <a name="onboard-to-the-public-preview"></a>공개 미리 보기에 온보딩

다음과 같이 공개 미리 보기에 온보딩합니다.

* 포털에서 [이 문서에 따라](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal) 구독 ID를 Recovery Services 공급자에 등록합니다.
* PowerShell의 경우 다음 cmdlet을 실행합니다. 그러면 "등록됨"으로 완료됩니다.

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> 이제 로그 백업 빈도를 최소 15 분으로 설정할 수 있습니다. 로그 백업은 데이터베이스에 대한 전체 백업이 성공적으로 완료 된 후에만 흐름을 시작 합니다.

## <a name="scenario-support"></a>시나리오 지원

| **시나리오**               | **지원 되는 구성**                                | **지원 되지 않는 구성**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **토폴로지**               | Azure Linux Vm 에서만 실행 되는 SAP HANA                    | HANA Large Instances (HLI)                                   |
| **지역**                   | **GA**<br />**유럽** – 유럽 서부, 유럽, 프랑스 중부, 영국 남부, 영국 서부, 독일 북부, 독일 중서부, 스위스 북부, 스위스 서부<br />**아시아 태평양** – 오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 동부, 오스트레일리아 남동쪽, 일본 동부, 일본 서 부, 대한민국 중부, 한국 남부<br /><br>**미리 보기:**<br />**미주** – 미국 중부, 미국 동부 2, 미국 동부, 미국 중 북부, 미국 중 북부, 미국 서 부 2, 미국 서 부, 미국 서 부, 캐나다 중부, 캐나다 동부 <br />**아시아 태평양** – 동아시아, 동남 아시아, 인도 중부, 인도 남부 | 중국 동부, 중국 북부, 중국 동부 2,, 중국 북부 2, 인도 서 부, 중앙 스위스 북부, 남아프리카 공화국 북부, 남아프리카 공화국 서 부, 아랍에미리트 북부, 아랍에미리트 중부, Azure Government 지역, 프랑스 남부, 브라질 남부 |
| **OS 버전**            | SLES 12 SP2, SP3 또는 SP4                                | SLES 15, RHEL                                                |
| **HANA 버전**          | HANA 1.x의 SDC, HANA 2.x의 MDC < = SPS04 Rev 44            | -                                                            |
| **HANA 배포**       | 단일 Azure VM에 SAP HANA-확장만               | 확장                                                    |
| **HANA 인스턴스**         | 단일 Azure VM의 단일 SAP HANA 인스턴스 – 수직 확장만 | 단일 VM의 여러 SAP HANA 인스턴스                  |
| **HANA 데이터베이스 형식**    | 1\.x의 1.x (Single Database 컨테이너 (SDC), 2.x의 다중 데이터베이스 컨테이너 (MDC) | HANA 1.x의 MDC                                              |
| **HANA 데이터베이스 크기**     | 2tb 전체 백업 크기가 HANA에 의해 보고 됨)                   |                                                              |
| **백업 유형**           | 전체, 차등 및 로그 백업                          | 증분, 스냅숏                                       |
| **복원 유형**          | 지원 되는 복원 유형에 대한 자세한 내용은 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) 을 참조 하세요. |                                                              |
| **백업 제한**          | SAP HANA 인스턴스당 최대 2tb의 전체 백업 크기         |                                                              |
| **특수 구성** |                                                              | SAP HANA + 동적 계층화 <br>  LaMa를 통한 복제        |

------

> [!NOTE]
> SAP HANA native client에서의 백업 및 복원 작업 (SAP HANA Studio/op/DBA 환경)은 현재 지원 되지 않습니다.



## <a name="next-steps"></a>다음 단계

* [Azure vm에서 실행 되는 SAP HANA 데이터베이스를 백업](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) 하는 방법 알아보기
* [Azure vm에서 실행 되는 SAP HANA 데이터베이스를 복원](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) 하는 방법 알아보기
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](sap-hana-db-manage.md)을 알아봅니다.
* [SAP HANA 데이터베이스를 백업할 때 발생하는 일반적인 문제를 해결하는 방법](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)을 알아봅니다.
