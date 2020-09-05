---
title: Azure 가상 머신 복원 프로세스 정보
description: Azure Backup 서비스에서 Azure virtual machines를 복원 하는 방법 알아보기
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 5458d02e241860a98d1ab5f64df141132813f8dd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011959"
---
# <a name="about-azure-vm-restore"></a>Azure VM 복원 정보

이 문서에서는 [Azure Backup 서비스](./backup-overview.md) 에서 Azure vm (가상 머신)을 복원 하는 방법을 설명 합니다. 복원 옵션에는 여러 가지가 있습니다. 지원 되는 다양 한 시나리오에 대해 설명 합니다.

## <a name="concepts"></a>개념

- **복구 지점** ( **복원 지점**이 라고도 함): 복구 지점은 백업 중인 원본 데이터의 복사본입니다.

- **계층 (스냅숏 및 자격 증명 모음)**: Azure VM 백업은 다음과 같은 두 단계로 수행 됩니다.

  - 1 단계에서 만든 스냅숏은 디스크와 함께 저장 됩니다. 이를 **스냅숏 계층**이라고 합니다. 스냅숏 계층 복원은 복원을 트리거하기 전에 자격 증명 모음에 스냅숏을 복사 하는 대기 시간을 없애기 때문에 더 빠릅니다 (자격 증명 모음에서 복원). 따라서 스냅숏 계층에서 복원은 [인스턴트 복원](./backup-instant-restore-capability.md)이 라고도 합니다.
  - 2 단계에서 스냅숏은 전송 되어 Azure Backup 서비스에서 관리 하는 자격 증명 모음에 저장 됩니다. 이를 **자격 증명 모음 계층**이라고 합니다.

- **OLR (원래 위치 복구)**: 복원 지점에서 백업이 수행 된 원본 Azure VM으로 복구를 수행 하 여 복구 지점에 저장 된 상태로 바꿉니다. 이는 원본 VM의 OS 디스크 및 데이터 디스크를 대체 합니다.

- **ALR (대체 위치 복구)**: 복구 지점에서 백업을 수행한 원래 서버가 아닌 서버에 대 한 복구 작업을 수행 합니다.

- **항목 수준 복원 (ILR):** 복구 지점에서 VM 내의 개별 파일 또는 폴더 복원

- **가용성 (복제 유형)**: Azure Backup는 저장소/데이터를 항상 사용 가능 하 게 유지 하는 두 가지 유형의 복제를 제공 합니다.
  - [LRS (로컬 중복 저장소)](../storage/common/storage-redundancy.md) 는 데이터 센터의 저장소 배율 단위에 데이터를 세 번 복제 합니다 (3 개의 데이터 복사본을 만듭니다). 모든 데이터 복사본은 동일한 지역 내에 있습니다. LRS는 로컬 하드웨어 오류로부터 데이터를 보호하기 위한 저비용 옵션입니다.
  - [GRS (지역 중복 저장소)](../storage/common/storage-redundancy.md) 는 기본 및 권장 되는 복제 옵션입니다. GRS는 데이터 원본의 기본 위치에서 수백 마일 떨어진 보조 지역으로 데이터를 복제합니다. GRS는 LRS보다 더 많은 비용이 들지만, 지역 가동 중단이 발생하는 경우에도 높은 수준의 데이터 내구성을 제공합니다.

- **Crr (영역 간 복원)**: [복원 옵션](./backup-azure-arm-restore-vms.md#restore-options)중 하나로, crr (지역 간 복원)을 사용 하면 azure [쌍을 이루는 지역](../best-practices-availability-paired-regions.md#what-are-paired-regions)에 있는 보조 지역에서 azure vm을 복원할 수 있습니다.

## <a name="restore-scenarios"></a>복원 시나리오

![복원 시나리오 ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **시나리오**                                                 | **완료 된 작업**                                             | **사용하는 경우**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [새 가상 컴퓨터를 만들기 위해 복원](./backup-azure-arm-restore-vms.md) | 전체 VM을 OLR로 복원 (원본 VM이 있는 경우) 또는 ALR | <li> 원본 VM이 손실 되거나 손상 된 경우에는 전체 VM을 복원할 수 있습니다.  <li> VM의 복사본을 만들 수 있습니다.  <li> 감사 또는 규정 준수를 위해 복원 드릴을 수행할 수 있습니다.  <li> 이 옵션은 Marketplace 이미지에서 만든 Azure Vm에 대해서는 작동 하지 않습니다 (즉, 라이선스가 만료 되었기 때문에 사용할 수 없는 경우). |
| [VM의 디스크 복원](./backup-azure-arm-restore-vms.md#restore-disks) | VM에 연결 된 디스크 복원                             |  모든 디스크:이 옵션은 템플릿을 만들고 디스크를 복원 합니다. 요구 사항에 맞게 특수 한 구성 (예: 가용성 집합)을 사용 하 여이 템플릿을 편집한 다음 템플릿을 사용 하 고 디스크를 복원 하 여 VM을 다시 만들 수 있습니다. |
| [VM 내의 특정 파일 복원](./backup-azure-restore-files-from-vm.md) | 복원 지점을 선택 하 고, 파일을 선택 하 고, 백업 된 VM과 동일한 (또는 호환) OS로 복원 합니다. |  복원할 특정 파일을 알고 있으면 전체 VM을 복원 하는 대신이 옵션을 사용 합니다. |
| [암호화 된 VM 복원](./backup-azure-vms-encryption.md) | 포털에서 디스크를 복원한 다음 PowerShell을 사용 하 여 VM을 만듭니다. | <li> [Azure Active Directory (AAD)를 사용 하 여 암호화 된 VM](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [AAD 없이 암호화 된 VM](../virtual-machines/windows/disk-encryption-windows.md) <li> [Aad *없이* aad로 마이그레이션된 암호화 *된* VM](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [지역 간 복원](./backup-azure-arm-restore-vms.md#cross-region-restore) | 새 VM 만들기 또는 보조 지역으로 디스크 복원 (Azure 쌍을 이루는 지역) | <li> **전체 중단**: 지역 간 복원 기능을 사용 하는 경우 보조 지역에서 데이터를 복구할 때 대기 시간이 없습니다. Azure가 가동 중단을 선언 하기 전에도 보조 지역에서 복원을 시작할 수 있습니다. <li> **부분적 중단**: Azure Backup 백업 된 데이터와 연결 된 Azure Backup 및 저장소 클러스터를 연결 하 여 백업 된 데이터를 저장 하는 특정 저장소 클러스터에서 가동 중지 시간이 발생할 수 있습니다. 지역 간 복원을 사용 하 여 보조 지역에서 백업 된 데이터의 복제본을 사용 하 여 보조 지역에서 복원을 수행할 수 있습니다. <li> **중단 없음**: 보조 지역 데이터를 사용 하 여 감사 또는 규정 준수를 위해 BCDR (비즈니스 연속성 및 재해 복구) 훈련을 수행할 수 있습니다. 이를 통해 비즈니스 연속성 및 재해 복구 드릴에 대 한 주 지역에 전체 또는 일부 중단이 없더라도 보조 지역에서 백업 된 데이터의 복원을 수행할 수 있습니다.  |

## <a name="next-steps"></a>다음 단계

- [VM 복원에 대 한 질문과 대답](./backup-azure-vm-backup-faq.md#restore)
- [지원되는 복원 방법](./backup-support-matrix-iaas.md#supported-restore-methods)
- [복원 문제 해결](./backup-azure-vms-troubleshoot.md#restore)
