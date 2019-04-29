---
title: Azure Site Recovery를 사용 하 여 VMware Vm 및 물리적 서버의 재해 복구 중 스케일 아웃 프로세스 서버 설정 | Microsoft Docs
description: 이 문서에서는 VMware Vm 및 물리적 서버의 재해 복구 중 스케일 아웃 프로세스 서버를 설정 하는 방법을 설명 합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 678f9aa60d4970540ded8ba0bb1a4ddaa6281a49
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101900"
---
# <a name="scale-with-additional-process-servers"></a>추가 프로세스 서버를 사용 하 여 크기 조정

기본적으로 [사이트 복구](site-recovery-overview.md)를 사용하여 VMware VM 또는 물리적 서버를 Azure에 복제하는 경우 프로세스 서버가 구성 서버 컴퓨터에 설치되어 사이트 복구 및 온-프레미스 인프라 간의 데이터 전송을 조정하는 데 사용됩니다. 용량을 늘리고 복제 배포를 확장하려면 추가 독립 실행형 프로세스 서버를 추가할 수 있습니다. 이 문서에서는 스케일 아웃 프로세스 서버를 설정 하는 방법을 설명 합니다.

## <a name="before-you-start"></a>시작하기 전에

### <a name="capacity-planning"></a>용량 계획

VMware 복제에 대해 [용량 계획](site-recovery-plan-capacity-vmware.md)을 수행했는지 확인합니다. 추가 프로세스 서버를 배포해야 시기와 방법을 식별하도록 도와줍니다.

9.24 버전에서 새 복제에 대 한 프로세스 서버를 선택 하는 동안 지침 추가 됩니다. 프로세스 서버는 정상, 경고 및 중요 특정 조건에 따라 표시 됩니다. 프로세스 서버의 상태에 영향을 줄 수 있는 다양 한 시나리오를 이해 하려면 방문 [프로세스 서버 선택 지침](vmware-azure-manage-process-server.md#process-server-selection-guidance)합니다.

> [!NOTE]
> 복제된 프로세스 서버 구성 요소를 사용하는 것은 지원되지 않습니다. 각 PS 스케일 아웃에서 이 문서의 단계를 수행할 수 있습니다.

### <a name="sizing-requirements"></a>크기 조정 요구 사항 

표에 요약된 크기 조정 요구 사항을 확인합니다. 일반적으로 배포 규모를 200대 초과 원본 컴퓨터로 확장해야 하거나 총 이탈률이 2TB를 초과하는 경우 트래픽 볼륨을 처리할 추가 프로세스가 필요합니다.

| **추가 프로세스 서버** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터** |
| --- | --- | --- | --- |
|4개 vCPU(2개 소켓 * 2코어 \@ 2.5GHz) 8GB 메모리 |300GB |250GB 이하 |85대 이하의 컴퓨터를 복제합니다. |
|8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz), 12GB 메모리 |600GB |250GB ~ 1TB |85-150대 컴퓨터를 복제합니다. |
|12개 vCPU(2개 소켓 * 6코어 \@ 2.5GHz) 24GB 메모리 |1TB |1TB ~ 2TB |150-225대 컴퓨터를 복제합니다. |

여기서 보호된 원본 머신 각각은 각 100GB의 디스크 3개로 구성됩니다.

### <a name="prerequisites"></a>필수 조건

추가 프로세스 서버에 대한 필수 구성 요소는 다음 표에 요약되어 있습니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>설치 파일 다운로드

다음과 같이 프로세스 서버에 대한 설치 파일을 다운로드합니다.

1. Azure portal에 로그인 하 고 Recovery Services 자격 증명 모음으로 이동 합니다.
2. **Site Recovery 인프라** > **VMware 및 물리적 컴퓨터** > **구성 서버**(VMware 및 물리적 컴퓨터용 아래에서)를 엽니다.
3. 구성 서버를 선택하여 서버의 세부 정보로 드릴다운합니다. 그럼 다음, **+ 프로세스 서버**를 클릭합니다.
4. **프로세스 서버 추가** >  **프로세스 서버 배포하려는 위치 선택**에서 **온-프레미스로 확장 프로세스 서버 배포를 선택합니다**.

   ![서버 페이지 추가](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. **Microsoft Azure Site Recovery 통합 설치 다운로드**를 클릭합니다. 최신 버전의 설치 파일을 다운로드합니다.

   > [!WARNING]
   > 프로세스 서버 설치 버전이 실행 중인 구성 서버 버전과 동일하거나 이전 버전이어야 합니다. 버전 호환성을 보장하는 간단한 방법은 구성 서버를 설치 또는 업데이트하는 데 최근에 사용한 동일한 설치 관리자를 사용하는 것입니다.

## <a name="install-from-the-ui"></a>UI에서 설치

다음과 같이 설치합니다. 서버를 설정한 후 이를 사용하도록 원본 컴퓨터를 마이그레이션할 수 있습니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>명령줄에서 설치

다음 명령을 실행하여 설치합니다.

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

여기에서 명령줄 매개 변수는 다음과 같습니다.

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

예를 들면 다음과 같습니다.

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>프록시 설정 파일 만들기

프록시 설정이 필요한 경우 ProxySettingsFilePath 매개 변수는 입력으로 파일을 사용합니다. 다음과 같이 파일을 만들어 입력 ProxySettingsFilePath 매개 변수로 전달할 수 있습니다.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>다음 단계
[프로세스 서버 설정 관리](vmware-azure-manage-process-server.md)에 대해 알아봅니다.
