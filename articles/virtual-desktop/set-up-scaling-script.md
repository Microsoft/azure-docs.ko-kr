---
title: Windows 가상 데스크톱 미리 보기 세션 호스트-Azure에 자동으로 조정
description: Windows 가상 데스크톱 미리 보기 세션 호스트에 대 한 자동 크기 조정 스크립트를 설정 하는 방법에 설명 합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 379e73c33aa4570c3e56f902b011d75944c94a8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870726"
---
# <a name="automatically-scale-session-hosts"></a>세션 호스트 자동 크기 조정

Azure에서 많은 가상 데스크톱 미리 보기 Windows 배포의 경우 가상 머신 비용은 Windows 가상 데스크톱 배포 비용의 상당 부분을 나타냅니다. 비용을 줄이기 위해 종료 사용량이 시간에 가상 컴퓨터를 호스트 하는 중 세션 (Vm)를 할당 취소 하 고 사용량이 다시 시작 하는 것이 좋습니다.

이 문서에서는 간단한 크기 조정 스크립트를 사용 하 여 Windows 가상 데스크톱 환경에서 가상 컴퓨터를 호스트 하는 세션을 자동으로 조정. 크기 조정 스크립트의 작동 원리에 대 한 자세한 내용은 참조는 [크기 조정 스크립트의 작동 방법을](#how-the-scaling-script-works) 섹션입니다.

## <a name="prerequisites"></a>필수 조건

스크립트를 실행 하는 환경에는 다음 작업을 있어야 합니다.

- Windows 가상 데스크톱 테 넌 트 및 계정 또는 해당 테 넌 트 (예: RDS 참가자)을 쿼리할 수 있는 권한이 있는 서비스 주체.
- 세션 호스트 풀 Vm 구성 및 가상 데스크톱 Windows 서비스를 등록 합니다.
- 추가 scaler 하 고 작업 일정을 통해 예약된 된 작업을 실행 하는 VM에 네트워크 액세스 세션 호스트 합니다.
- 예약된 된 작업을 실행 하는 VM에 설치 된 Microsoft Azure Resource Manager PowerShell 모듈입니다.
- 예약된 된 작업을 실행 하는 VM에 설치 된 Windows 가상 데스크톱 PowerShell 모듈입니다.

## <a name="recommendations-and-limitations"></a>권장 사항 및 제한 사항

크기 조정 스크립트를 실행 하는 경우 다음 사항에 유의 하십시오 유지 합니다.

- 이 크기 조정 하는 스크립트 크기 조정 스크립트를 실행 하는 예약된 된 작업의 인스턴스당 하나의 호스트 풀을 처리할 수 있습니다.
- 크기 조정 스크립트를 실행 하는 예약된 된 작업이 항상 켜져 있는 VM에 있어야 합니다.
- 크기 조정 스크립트 및 해당 구성의 각 인스턴스에 대해 별도 폴더를 만듭니다.
- 이 스크립트는 multi-factor authentication 계정을 지원 하지 않습니다. 서비스 주체를 사용 하 여 Windows 가상 데스크톱 서비스 및 Azure에 액세스 하는 것이 좋습니다.
- Azure의 SLA를 보장 가용성 집합의 Vm에만 적용 됩니다. 현재 버전의 문서에는 가용성 요구 사항을 충족 하지 않을 수 있습니다는 단일 vm 확장을 수행 하는 환경을 설명 합니다.

## <a name="deploy-the-scaling-script"></a>크기 조정 스크립트 배포

다음 절차에서는 크기 조정 스크립트를 배포 하는 방법을 알려줍니다.

### <a name="prepare-your-environment-for-the-scaling-script"></a>크기 조정 스크립트에 대 한 환경 준비

먼저 크기 조정 스크립트에 대 한 환경 준비:

1. VM에 로그인 (**VM 크기 조정**) 도메인 관리 계정을 사용 하 여 예약 된 작업이 실행 되는 합니다.
2. 크기 조정 vm을 크기 조정 스크립트와 해당 구성을 저장할 폴더를 만듭니다 (예를 들어 **c:\\크기 조정 HostPool1**).
3. 다운로드 합니다 **basicScaler.ps1**를 **Config.xml**, 및 **함수 PSStoredCredentials.ps1** 파일 및 **PowershellModules** 폴더를 [스크립트 리포지토리를 크기 조정](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) 2 단계에서 만든 폴더에 복사 합니다.

### <a name="create-securely-stored-credentials"></a>안전 하 게 저장 된 자격 증명 만들기

다음으로, 안전 하 게 저장된 된 자격 증명을 만들려면 해야 합니다.

1. 관리자 권한으로 PowerShell ISE를 엽니다.
2. 편집 창을 열고 부하 합니다 **함수 PSStoredCredentials.ps1** 파일입니다.
3. 다음 cmdlet을 실행합니다.
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    예를 들어 **Set-variable-KeyPath 이름-범위 전역-값 "c:\\확장 HostPool1"**
4. 실행 합니다 **새로 만들기-StoredCredential KeyPath \$KeyPath** cmdlet. 메시지가 표시 되 면 호스트 풀을 쿼리할 수 있는 권한이 있는 Windows 가상 데스크톱 자격 증명을 입력 합니다. (호스트 풀에 지정 된 된 **config.xml**).
    - 표준 계정 또는 다른 서비스 주체를 사용 하는 경우 실행 합니다 **새로 만들기-StoredCredential KeyPath \$KeyPath** 로컬 만들려는 각 계정에 대 한 자격 증명을 저장 한 후 cmdlet.
5. 실행 **Get StoredCredentials-목록** 자격 증명 생성 되었는지 확인 합니다.

### <a name="configure-the-configxml-file"></a>Config.xml 파일 구성

Config.xml에서 크기 조정 스크립트 설정을 업데이트 하려면 다음 필드에 관련 값을 입력 합니다.

| 필드                     | 설명                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | 세션 호스트 Vm이 실행 되는 구독을 연결 하는 azure AD 테 넌 트 ID     |
| AADApplicationId              | 서비스 주체 응용 프로그램 ID                                                       |
| AADServicePrincipalSecret     | 테스트 단계에서 입력할 수 있지만 자격 증명을 만든 후에 빈 유지 해야 하는이 **PSStoredCredentials.ps1 함수**    |
| currentAzureSubscriptionId    | 세션 호스트 Vm이 실행 되는 Azure 구독의 ID                        |
| tenantName                    | Windows 가상 데스크톱 테 넌 트 이름                                                    |
| hostPoolName                  | Windows 가상 데스크톱 호스트 풀 이름                                                 |
| RDBroker                      | WVD 서비스 URL을 기본 값 https:\//rdbroker.wvd.microsoft.com             |
| 사용자 이름                      | 서비스 주체 응용 프로그램 ID (AADApplicationId 에서처럼에서 동일한 서비스 주체를 가질 수는) 또는 multi-factor authentication 없이 표준 사용자 |
| isServicePrincipal            | 허용 되는 값은 **true** 하거나 **false**합니다. 자격 증명을 사용 하 고 두 번째 집합의 서비스 주체 인지 또는 표준 계정을 나타냅니다. |
| BeginPeakTime                 | 최대 사용량 시간 시작 될 때                                                            |
| EndPeakTime                   | 최대 사용 시간이 종료 될 때                                                              |
| TimeDifferenceInHours         | 시간에서 현지 시간과 UTC 사이의 시간 차이                                   |
| SessionThresholdPerCPU        | 새 RDSH 서버를 최대 사용 시간 동안 시작 해야 하는 경우를 결정 하는 데 사용 된 CPU 임계값 당 세션의 최대 수입니다.  |
| MinimumNumberOfRDSH           | 호스트 풀 사용량이 시간 동안 계속 실행 하는 Vm의 최소 수             |
| LimitSecondsToForceLogOffUser | 사용자가 로그 아웃을 강제 적용 하기 전에 대기할 시간 (초) 수입니다. 사용자가 0으로 설정 로그 아웃을 강제 적용 되지 않습니다.  |
| LogOffMessageTitle            | 로그 아웃 해야 하는 전에 사용자에 게 보내는 메시지의 제목                  |
| LogOffMessageBody             | 사용자에 게 전송 중인 아웃 하기 전에 경고 메시지의 본문입니다. 예를 들어, "이이 컴퓨터는 종료 X 분에 다운 합니다. 작업을 저장 후 로그 아웃 합니다. " |

### <a name="configure-the-task-scheduler"></a>작업 Scheduler를 구성 합니다.

구성.xml 파일을 구성한 후 일정 한 간격으로 basicScaler.ps1 파일을 실행 하려면 작업 Scheduler를 구성 해야 합니다.

1. 시작 **작업 스케줄러**합니다.
2. 에 **작업 스케줄러** 창에서 **작업 만들기...**
3. **작업 만들기** 대화 상자에서 선택 합니다 **일반** 탭을 입력을 **이름** (예: "동적 RDSH")을 선택 **사용자의 로그온 여부 실행** 하 고 **가장 높은 권한으로 실행**합니다.
4. 로 이동 합니다 **트리거** 탭을 선택한 다음 선택 **새로 만들기...**
5. 에 **새 트리거** 대화 상자 아래에 있는 **고급 설정**, 확인 **작업 반복 모든** 적절 한 기간 및 기간을 선택 하 고 (예를 들어 **15 분** 나 **무기한**).
6. 선택 된 **동작** 탭 및 **새로 만들기...**
7. 에 **새 작업** 대화 상자에서 입력 **powershell.exe** 에 **프로그램/스크립트** 필드를 입력 합니다 **c:\\확장\\ RDSScaler.ps1** 에 **인수 추가 (선택 사항)** 필드입니다.
8. 로 이동 합니다 **조건을** 및 **설정** 탭을 선택 **확인** 각각에 대 한 기본 설정을 그대로 적용 합니다.
9. 크기 조정 스크립트를 실행 하려는 관리자 계정의 암호를 입력 합니다.

## <a name="how-the-scaling-script-works"></a>크기 조정 스크립트의 작동 원리

이 크기 조정 스크립트 시작 및 끝 하루 동안 최대 사용 기간을 포함 하 여 config.xml 파일에서 설정을 읽습니다.

최대 사용량 시간 동안 스크립트를 현재 세션 수와 각 컬렉션에 대해 현재 실행 중인 RDSH 용량을 확인합니다. 실행 중인 RDSH 서버 config.xml 파일에 정의 된 SessionThresholdPerCPU 매개 변수에 따라 기존 세션을 지원 하도록 충분 한 용량이 있으면 계산 합니다. 그렇지 않은 경우 스크립트 컬렉션에 RDSH 서버 추가 시작 합니다.

사용량이 시간 동안 스크립트는 config.xml 파일에서 MinimumNumberOfRDSH 매개 변수를 기반으로 하는 RDSH 서버를 종료 해야 확인 합니다. 스크립트를 호스트에 연결 하는 새 세션을 방지 하는 모드를 비우기 위해 RDSH 서버를 설정 합니다. 설정한 경우에 **LimitSecondsToForceLogOffUser** 0이 아닌 양수 값으로 config.xml 파일에서 매개 변수를 스크립트에 그 사실을 알리는 작업을 저장 하 고 구성 된 기간 동안 대기 합니다. 그런 다음 적용할에 현재 로그인 된는 사용자가 로그 아웃 합니다. 모든 사용자 세션 RDSH 서버에서 서명 된, 일단 스크립트 서버가 종료 됩니다.

설정 하는 경우는 **LimitSecondsToForceLogOffUser** 0 config.xml 파일의 매개 변수를 스크립트 하면 세션 구성 설정의 컬렉션 속성을 사용자 세션을 승인 처리 합니다. RDSH 서버에서 모든 세션이 있는 경우 실행 RDSH 서버를 남습니다. 모든 세션 없으면 스크립트 RDSH 서버 종료 됩니다.

스크립트는 작업 스케줄러를 사용 하 여 scaler VM 서버의 주기적으로 실행 되도록 설계 되었습니다. 원격 데스크톱 서비스 환경의 크기에 따라 적절 한 시간 간격을 선택 하 고 시작 하 고 가상 컴퓨터를 종료 시간이 걸릴 수 합니다. 15 분 마다 크기 조정 스크립트를 실행 하는 것이 좋습니다.

## <a name="log-files"></a>로그 파일

크기 조정 스크립트는 두 개의 로그 파일을 만듭니다 **WVDTenantScale.log** 하 고 **WVDTenantUsage.log**합니다. 합니다 **WVDTenantScale.log** 파일은 로그 이벤트 및 오류 (있는 경우) 크기 조정 스크립트의 각 실행 중입니다.

합니다 **WVDTenantUsage.log** 파일은 기록 active 코어 수 및 가상 컴퓨터의 현재 수 크기 조정 스크립트를 실행할 때마다 합니다. Microsoft Azure Vm 및 비용의 실제 사용량을 예측 하려면이 정보를 사용할 수 있습니다. 다음 정보를 포함 하는 각 항목을 사용 하 여 파일을 쉼표로 구분 된 값으로 형식이:

>시간, 컬렉션, 코어, Vm

파일 이름 확장명이.csv, Microsoft Excel에 로드 하 고 분석을 수정할 수도 있습니다.