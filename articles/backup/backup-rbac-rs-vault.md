---
title: 역할 기반 Access Control를 사용 하 여 백업 관리
description: 역할 기반 Access Control을 사용하여 Recovery Services 자격 증명 모음의 백업 관리 작업에 대한 액세스를 관리합니다.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 1a4bb617511f5d5ccf882ccb11e7d88eb7a848ea
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017807"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>역할 기반 Access Control을 사용하여 Azure Backup 복구 지점 관리

Azure RBAC (역할 기반 액세스 제어)를 통해 Azure에 대 한 세밀 한 액세스 관리가 가능 합니다. RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다.

> [!IMPORTANT]
> Azure Backup에서 제공 하는 역할은 Azure Portal 또는 REST API 또는 Recovery Services 자격 증명 모음 PowerShell 또는 CLI cmdlet을 통해 수행할 수 있는 작업으로 제한 됩니다. Azure Backup agent 클라이언트 UI 또는 System center Data Protection Manager UI 또는 Azure Backup Server UI에서 수행 되는 작업은 이러한 역할을 제어할 수 없습니다.

Azure Backup는 백업 관리 작업을 제어 하는 세 가지 기본 제공 역할을 제공 합니다. [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md) 에 대 한 자세한 정보

* [백업 참여자](../role-based-access-control/built-in-roles.md#backup-contributor) -이 역할에는 Recovery Services 자격 증명 모음을 삭제 하 고 다른 사용자에 게 액세스 권한을 부여 하는 경우를 제외 하 고 백업을 만들고 관리할 수 있는 모든 백업 관리 작업을 수행할 수 있는 백업 관리 관리자 역할로 생각하시면 됩니다.
* [Backup 운영자](../role-based-access-control/built-in-roles.md#backup-operator) - 이 역할은 백업을 제거하고 백업 정책을 관리하는 권한을 제외하고, 참여자가 할 수 있는 모든 일을 할 수 있습니다. 이 역할은 온-프레미스 리소스의 데이터 삭제나 등록 제거를 통해 백업을 중지하는 작업처럼 안전하지 않은 작업을 수행할 수 없다는 점만 빼면 참여자와 똑같습니다.
* [Backup 읽기 권한자](../role-based-access-control/built-in-roles.md#backup-reader) - 이 역할은 모든 백업 관리 작업을 볼 수 있는 권한을 갖습니다. 이 역할을 모니터링 요원으로 생각하시면 됩니다.

훨씬 더 많은 제어를 위해 사용자 고유의 역할을 정의 하려는 경우 Azure RBAC에서 [사용자 지정 역할을 빌드하](../role-based-access-control/custom-roles.md) 는 방법을 참조 하세요.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Backup 기본 제공 역할을 관리 작업에 매핑

다음 표에서는 백업 관리 작업 및 해당 작업을 수행 하는 데 필요한 최소 Azure 역할을 캡처합니다.

| 관리 작업 | 필요한 최소 Azure 역할 | 필요한 범위 |
| --- | --- | --- |
| Recovery Services 자격 증명 모음 만들기 | Backup 참가자 | 자격 증명 모음을 포함하는 리소스 그룹 |
| Azure VM의 백업 활성화 | Backup 운영자 | 자격 증명 모음을 포함하는 리소스 그룹 |
| | 가상 머신 참가자 | VM 리소스 |
| VM의 주문형 백업 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| VM 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 참가자 | VM이 배포될 리소스 그룹 |
| | 가상 머신 참가자 | 백업 된 원본 VM |
| 관리되지 않는 디스크 VM 백업 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 가상 머신 참가자 | 백업 된 원본 VM |
| | Storage 계정 참가자 | 디스크가 복원되는 Storage 계정 리소스 |
| VM 백업에서 관리 디스크 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 가상 머신 참가자 | 백업 된 원본 VM |
| | Storage 계정 참가자 | 복원 과정의 일부로 선택한 관리 디스크로 전환하기 전에 자격 증명 모음에서 데이터를 보관하도록 선택된 임시 Storage 계정 |
| | 참가자 | 관리 디스크가 복원될 리소스 그룹 |
| VM 백업에서 개별 파일 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | 가상 머신 참가자 | 백업 된 원본 VM |
| Azure VM 백업에 대한 백업 정책 만들기 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| Azure VM 백업의 백업 정책 수정 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| Azure VM 백업의 백업 정책 삭제 | Backup 참가자 | Recovery Services 자격 증명 모음 |
| VM 백업에서 백업 중지(데이터 보존 또는 데이터 삭제를 통해) | Backup 참가자 | Recovery Services 자격 증명 모음 |
| 온-프레미스 Windows 서버/클라이언트/SCDPM 또는 Azure Backup Server 등록 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| 등록된 온-프레미스 Windows 서버/클라이언트/SCDPM 또는 Azure Backup Server 삭제 | Backup 참가자 | Recovery Services 자격 증명 모음 |

> [!IMPORTANT]
> VM 리소스 범위에서 VM 기여자를 지정하고 VM 설정의 일환으로 백업을 클릭하면 VM이 이미 백업되었다 하더라도 ‘백업 사용’ 화면이 열립니다. 백업 상태를 확인하기 위한 호출은 구독 수준에서만 작동하기 때문입니다. 이를 방지하려면 자격 증명 모음으로 이동하여 VM의 백업 항목을 열거나, 구독 수준에서 VM 기여자 역할을 지정합니다.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure 파일 공유 백업에 대 한 최소 역할 요구 사항

다음 표에서는 Azure 파일 공유 작업을 수행 하는 데 필요한 백업 관리 작업 및 해당 역할을 캡처합니다.

| 관리 작업 | 역할 필요 | 리소스 |
| --- | --- | --- |
| Azure 파일 공유의 백업 사용 | Backup 참가자 |Recovery Services 자격 증명 모음 |
| |스토리지 계정 | 참가자 저장소 계정 리소스 |
| VM의 주문형 백업 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| 파일 공유 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| | Storage 계정 참가자 | 복원 원본 및 대상 파일 공유가 있는 저장소 계정 리소스 |
| 개별 파일 복원 | Backup 운영자 | Recovery Services 자격 증명 모음 |
| |Storage 계정 참가자|복원 원본 및 대상 파일 공유가 있는 저장소 계정 리소스 |
| 보호 중지 |Backup 참가자 | Recovery Services 자격 증명 모음 |
| 자격 증명 모음에서 저장소 계정 등록 취소 |Backup 참가자 | Recovery Services 자격 증명 모음 |
| |Storage 계정 참가자 | 스토리지 계정 리소스|

## <a name="next-steps"></a>다음 단계

* Azure [rbac (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md): AZURE PORTAL에서 RBAC를 시작 합니다.
* 다음을 사용하여 액세스를 관리하는 방법에 대해 알아봅니다.
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [역할 기반 Access Control 문제 해결](../role-based-access-control/troubleshooting.md): 일반적인 문제를 수정하기 위한 제안 사항을 봅니다.
