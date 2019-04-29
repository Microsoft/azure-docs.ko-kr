---
title: Azure Site Recovery를 사용한 Azure로의 VMware VM 및 물리적 서버 재해 복구를 위한 프로세스 서버 관리 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하는 Azure로의 VMware VM 및 물리적 서버 재해 복구를 위해 프로세스 서버 설정을 관리하는 방법을 설명합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0a0b6c83f800c0a479ba7a16c91b497d1a11da9e
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62732488"
---
# <a name="manage-process-servers"></a>프로세스 서버 관리

기본적으로 VMware VM 또는 실제 서버를 Azure에 복제할 때 사용되는 프로세스 서버는 온-프레미스 구성 서버 컴퓨터에 설치됩니다. 별도 프로세스 서버를 설정해야 하는 두 가지 인스턴스가 있습니다.

- 대규모 배포의 경우 용량 크기를 조정하기 위해 추가 온-프레미스 프로세스 서버가 필요할 수 있습니다.
- 장애 복구의 경우 Azure에서 임시 프로세스 서버 설정이 필요합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다. 

이 아티클에서는 이러한 추가 프로세스 서버에 대한 일반적인 관리 작업을 요약합니다.

## <a name="upgrade-a-process-server"></a>프로세스 서버 업그레이드

다음과 같이 온-프레미스 또는 Azure에서 실행 하는 프로세스 서버를 업그레이드합니다(장애 복구 목적).

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   일반적으로 장애 복구를 목적으로 Azure 갤러리 이미지를 사용하여 Azure에서 프로세스 서버를 만들 때 사용 가능한 최신 버전을 실행합니다. Site Recovery 팀은 정기적으로 해결책 및 향상된 기능을 릴리스합니다. 따라서 프로세스 서버를 최신 상태로 유지하는 것이 좋습니다.

## <a name="balance-the-load-on-process-server"></a>프로세스 서버에서 부하 분한

두 프로세스 서버 간에 부하를 분산하려면

1. **Recovery Services 자격 증명 모음** > **관리** > **Site Recovery 인프라** > **VMware 및 물리적 머신** > **구성 서버**로 이동합니다.
2. 프로세스 서버를 등록하려는 구성 서버를 클릭합니다.
3. 구성 서버에 등록된 프로세스 서버의 목록을 페이지에서 사용할 수 있습니다.
4. 워크로드를 수정하려는 프로세스 서버를 클릭합니다.

    ![부하 분산](media/vmware-azure-manage-process-server/LoadBalance.png)

5. 요구 사항에 따라 아래 설명된 대로 **부하 분산** 또는 **스위치** 옵션을 사용할 수 있습니다.

### <a name="load-balance"></a>부하 분산

이 옵션을 통해 하나 이상의 가상 머신을 선택하고 다른 프로세스 서버로 전송할 수 있습니다.

1. **부하 분산**을 클릭하고, 드롭다운에서 대상 프로세스 서버를 선택합니다. **확인**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **머신 선택**을 클릭하고, 현재 프로세스 서버에서 대상 프로세스 서버로 이동하려는 가상 머신을 선택합니다. 평균 데이터 변경의 세부 정보는 각 가상 머신에 대해 표시됩니다.
3. **확인**을 클릭합니다. **Recovery Services 자격 증명 모음** > **모니터링** > **Site Recovery 작업**에서 작업의 진행률을 모니터링합니다.
4. 이 작업의 성공적인 완료 후에 변경 내용을 반영하거나 즉시 적용을 위해 [구성 서버를 새로 고치](vmware-azure-manage-configuration-server.md#refresh-configuration-server)는 데 15분이 걸립니다.

### <a name="switch"></a>Switch

이 옵션을 통해 프로세스 서버의 보호를 받는 전체 워크로드가 다른 프로세스 서버로 이동됩니다.

1. **스위치**를 클릭하고, 대상 프로세스 서버를 선택하고, **확인**을 클릭합니다.

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. **Recovery Services 자격 증명 모음** > **모니터링** > **Site Recovery 작업**에서 작업의 진행률을 모니터링합니다.
3. 이 작업의 성공적인 완료 후에 변경 내용을 반영하거나 즉시 적용을 위해 [구성 서버를 새로 고치](vmware-azure-manage-configuration-server.md#refresh-configuration-server)는 데 15분이 걸립니다.

## <a name="process-server-selection-guidance"></a>프로세스 서버 선택 지침

Azure Site Recovery 프로세스 서버에 도달 하 고 사용량 제한 하는 경우에 자동으로 식별 합니다. 프로세스 서버 확장을 설정 하는 경우 지침은 제공 됩니다.

|상태  |설명  | 리소스 가용성  | 권장 사항|
|---------|---------|---------|---------|
| 정상 (녹색)    |   프로세스 서버에 연결 되 고 정상 상태      |CPU 및 메모리 사용률은 80%; 미만 사용 가능한 공간 가용성을 30% 초과| 추가 서버를 보호 하기 위해이 프로세스 서버를 사용할 수 있습니다. 새 작업 이내 인지 확인 합니다 [프로세스 서버 제한 정의](vmware-azure-set-up-process-server-scale.md#sizing-requirements)합니다.
|경고 (주황색)    |   프로세스 서버가 연결 되어 있지만 특정 리소스가 최대 한도 도달 하려고 합니다.  |   CPU 및 메모리 사용률은 80%-95% 사이의; 사용 가능한 공간 가용성은 25%-30% 사이의       | 프로세스 서버의 사용량 임계값에 가깝습니다. 새 서버 추가 프로세스 서버를 동일한 임계값을 통과 시킬를 기존 보호 된 항목에 영향을 줄 수 있습니다. 하는 것이 좋습니다 [스케일 아웃 프로세스 서버 설정](vmware-azure-set-up-process-server-scale.md#before-you-start) 새 복제에 대 한 합니다.
|경고 (주황색)   |   프로세스 서버가 연결 되어 있지만 데이터는 지난 30 분에서 Azure에 업로드 되지 않았습니다.  |   임계값 제한 내에서 리소스 사용률은       | 문제 해결 [데이터 업로드 실패](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) 새 워크 로드를 추가 하기 전에 **하거나** [스케일 아웃 프로세스 서버 설정](vmware-azure-set-up-process-server-scale.md#before-you-start) 새 복제에 대 한 합니다.
|위험 (빨강)    |     프로세스 서버 연결이 끊어졌을 수 있습니다.  |  임계값 제한 내에서 리소스 사용률은      | 문제 해결 [서버 간의 연결 문제를 처리할](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) 또는 [스케일 아웃 프로세스 서버 설정](vmware-azure-set-up-process-server-scale.md#before-you-start) 새 복제에 대 한 합니다.
|위험 (빨강)    |     리소스 사용률 임계값 제한에 도달한 |  CPU 및 메모리 사용률을 95%; 초과 공간 가용성은 25% 미만입니다.   | 새 워크 로드 같은 프로세스 서버를 추가 제한 사항이 이미 충족 하는 리소스 임계값으로 사용할 수 없습니다. 따라서 [스케일 아웃 프로세스 서버 설정](vmware-azure-set-up-process-server-scale.md#before-you-start) 새 복제에 대 한 합니다.
위험 (빨강)    |     데이터 지난 45 분에 Azure로 업로드 되지 않았습니다. |  임계값 제한 내에서 리소스 사용률은      | 문제 해결 [데이터 업로드 실패](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) 동일한 프로세스 서버를 새 워크 로드를 추가 하기 전에 또는 [스케일 아웃 프로세스 서버 설정](vmware-azure-set-up-process-server-scale.md#before-you-start)

## <a name="reregister-a-process-server"></a>프로세스 서버 등록

온-프레미스에서 실행하는 프로세스 서버를 등록하거나 Azure의 구성 서버에서 등록해야 하는 경우 다음을 수행합니다.

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

설정을 저장한 후에 다음을 수행합니다.

1. 프로세스 서버에서 관리자 명령 프롬프트를 엽니다.
2. **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 다음 명령을 실행합니다.

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>온-프레미스 프로세스 서버에 대한 프록시 설정 수정

프로세스 서버가 프록시를 사용하여 Azure의 Site Recovery에 연결할 때 기존 프록시 설정을 수정해야 하는 경우 이 절차를 사용합니다.

1. 프로세스 서버 컴퓨터에 로그온합니다. 
2. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 다음 명령을 실행합니다.
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>프로세스 서버 제거

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>프로세스 서버에서 바이러스 백신 소프트웨어 관리

바이러스 백신 소프트웨어가 독립 실행형 프로세스 서버 또는 마스터 대상 서버에서 활성화되어 있으면 다음 폴더를 바이러스 백신 작업에서 제외합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 프로세스 서버 설치 디렉터리, 예제: C:\Program Files (x86)\Microsoft Azure Site Recovery