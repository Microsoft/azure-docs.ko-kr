---
title: System Center Data Protection Manager 문제 해결
description: 이 문서에서는 System Center Data Protection Manager를 사용 하는 동안 발생할 수 있는 문제에 대한 해결 방법을 알아봅니다.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2020
ms.locfileid: "75664757"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>System Center Data Protection Manager 문제 해결

이 문서에서는 Data Protection Manager를 사용하는 동안 발생할 수 있는 문제 해결 방법을 설명합니다.

System Center Data Protection Manager의 최신 릴리스 정보는 [System Center 설명서](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)를 참조하세요. Data Protection Manager 지원에 대한 내용은 [이 지원표](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)에서 확인할 수 있습니다.

## <a name="error-replica-is-inconsistent"></a>오류: 복제본이 일치하지 않습니다.

다음과 같은 이유로 복제본이 일치하지 않을 수 있습니다.

- 복제본 만들기 작업이 실패합니다.
- 변경 저널에 문제가 있습니다.
- 볼륨 수준 필터 비트맵에 오류가 있습니다.
- 원본 컴퓨터가 예기치 않게 종료됩니다.
- 동기화 로그가 오버플로됩니다.
- 복제본이 일치하지 않습니다.

이 문제를 해결하려면 다음 작업을 수행합니다.

- 불일치 상태를 제거하려면 일관성 검사를 수동으로 실행하거나 일일 일관성 검사를 예약합니다.
- 최신 버전의 Microsoft Azure Backup Server와 Data Protection Manager를 사용 중인지 확인합니다.
- **자동 일관성** 설정을 사용합니다.
- 명령 프롬프트에서 서비스를 다시 시작합니다. `net stop dpmra` 명령을 사용한 후 `net start dpmra` 명령을 사용해 봅니다.
- 네트워크 연결 및 대역폭 요구 사항에 부합하는지 확인합니다.
- 원본 컴퓨터가 예기치 않게 종료되었는지 확인합니다.
- 디스크가 정상이고 복제본을 저장하기에 충분한 공간이 있는지 확인합니다.
- 중복된 백업 작업이 동시에 실행되고 있지는 않은지 확인합니다.

## <a name="error-online-recovery-point-creation-failed"></a>오류: 온라인 복구 지점을 만들지 못했습니다.

이 문제를 해결하려면 다음 작업을 수행합니다.

- 최신 버전의 Azure Backup 에이전트를 사용 중인지 확인합니다.
- 수동으로 보호 작업 영역에서 복구 지점을 만들어 봅니다.
- 데이터 원본에 대해 일관성 검사를 실행합니다.
- 네트워크 연결 및 대역폭 요구 사항에 부합하는지 확인합니다.
- 복제본 데이터가 불일치 상태이면 이 데이터 원본의 디스크 복구 지점을 만듭니다.
- 복제본이 있고, 누락되지 않았는지 확인합니다.
- 복제본에 USN(업데이트 시퀀스 번호) 저널을 만들 공간이 충분한지 확인합니다.

## <a name="error-unable-to-configure-protection"></a>오류: 보호를 구성할 수 없습니다.

이 오류는 Data Protection Manager 서버가 보호되는 서버에 연결할 수 없는 경우에 발생합니다.

이 문제를 해결하려면 다음 작업을 수행합니다.

- 최신 버전의 Azure Backup 에이전트를 사용 중인지 확인합니다.
- Data Protection Manager 서버와 보호되는 서버 사이에 연결(네트워크/방화벽/프록시)이 있는지 확인합니다.
- SQL 서버를 보호 중인 경우 **로그인 속성** > **NT AUTHORITY\SYSTEM** 속성에서 **sysadmin** 설정이 활성화되었는지 확인합니다.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>오류: 서버가 자격 증명 모음 자격 증명 파일에 지정된 대로 등록되지 않았습니다.

이 오류는 Data Protection Manager/Azure Backup 서버 데이터의 복구 프로세스 과정에서 발생합니다. 복구 프로세스에 사용되는 자격 증명 모음 자격 증명 파일은 Data Protection Manager/Azure Backup 서버에 대한 Recovery Services 자격 증명 모음에 속하지 않습니다.

이 문제를 해결하려면 다음 단계를 수행합니다.

1. Data Protection Manager/Azure Backup 서버가 등록된 Recovery Services 자격 증명 모음에서 자격 증명 모음 자격 증명 파일을 다운로드합니다.
2. 가장 최근에 다운로드한 자격 증명 모음 자격 증명 파일을 사용하여 서버를 자격 증명 모음에 등록해 봅니다.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>오류: 복구 가능한 데이터가 없거나 선택한 서버는 Data Protection Manager 서버가 아닙니다.

이 오류는 다음과 같은 이유로 발생합니다.

- 다른 Data Protection Manager/Azure Backup 서버가 Recovery Services 자격 증명 모음에 등록되지 않았습니다.
- 서버에서 아직 메타데이터를 업로드하지 않았습니다.
- 선택한 서버는 Data Protection Manager/Azure Backup 서버가 아닙니다.

다른 Data Protection Manager/Azure Backup 서버가 Recovery Services 자격 증명 모음에 등록되지 않은 경우 문제를 해결하려면 다음 단계를 수행합니다.

1. 최신 Azure Backup 에이전트가 설치되어 있는지 확인합니다.
2. 최신 에이전트가 설치된 것을 확인했으면 하루를 기다린 후 복구 프로세스를 시작합니다. 야간 백업 작업을 통해 보호되는 모든 백업의 메타데이터가 클라우드로 업로드됩니다. 그러면 백업 데이터를 복구에 사용할 수 있습니다.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>오류: 입력된 암호화 암호가 서버 암호와 일치하지 않습니다.

이 오류는 Data Protection Manager/Azure Backup 서버 데이터를 복구할 때 암호화 과정에서 발생합니다. 복구 프로세스에 사용되는 암호화 암호가 서버의 암호화 암호와 일치하지 않습니다. 결과적으로, 에이전트가 데이터를 암호 해독할 수 없으므로 복구가 실패합니다.

> [!IMPORTANT]
> 암호화 암호를 잊어버리거나 분실한 경우 데이터를 복구할 수 있는 다른 방법이 없습니다. 유일한 옵션은 암호를 다시 생성하는 것입니다. 향후 백업 데이터를 암호화하려면 새 암호를 사용합니다.
>
> 데이터를 복구할 때 항상 Data Protection Manager/Azure Backup 서버와 연결된 것과 동일한 암호화 암호를 입력해야 합니다.
>
