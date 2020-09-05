---
title: 에이전트 없는 VMware VM 마이그레이션의 복제 문제 해결
description: 복제 주기 오류에 대 한 도움말 보기
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 6318f426e42612f21da7a43c9857894ae610f68e
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871186"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>에이전트 없는 VMware VM 마이그레이션의 복제 문제 해결

이 문서에서는 Azure Migrate: Server Migration 에이전트 없는 메서드를 사용 하 여 온-프레미스 VMware Vm을 복제할 때 발생할 수 있는 몇 가지 일반적인 문제 및 특정 오류에 대해 설명 합니다.

에이전트 없는 복제 방법을 사용 하 여 VMware 가상 컴퓨터를 복제 하는 경우 가상 컴퓨터&#39;s 디스크 (vmdk)의 데이터가 Azure 구독의 복제본 관리 디스크에 복제 됩니다. VM에 대 한 복제가 시작 되 면 디스크의 전체 복사본이 복제 되는 초기 복제 주기가 발생 합니다. 초기 복제가 완료 된 후에는 증분 복제 주기가 정기적으로 예약 되어 이전 복제 주기 이후에 발생 한 변경 내용을 전송 합니다.

VM에 대 한 복제 주기가 실패할 수 있습니다. 이러한 오류는 온-프레미스 네트워크 구성의 문제에서 Azure Migrate 클라우드 서비스 백 엔드에 대 한 문제로 인해 발생할 수 있습니다. 이 문서에서는 다음을 수행 합니다.

 - 복제 상태를 모니터링 하 고 오류를 해결 하는 방법을 보여 줍니다.
 - 일반적으로 발생 하는 복제 오류 중 일부를 나열 하 고 이러한 오류를 수정 하는 추가 단계를 제안 합니다.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Azure Portal를 사용 하 여 복제 상태 모니터링

다음 단계를 사용 하 여 가상 컴퓨터의 복제 상태를 모니터링 합니다.

  1. Azure Portal Azure Migrate의 서버 페이지로 이동 합니다.
  2. 서버 마이그레이션 타일에서 "서버 복제"를 클릭 하 여 "컴퓨터 복제" 페이지로 이동 합니다.
  3. 상태, 상태, 마지막 동기화 시간 등의 추가 정보와 함께 복제 서버 목록이 표시 됩니다. 상태 열은 VM의 현재 복제 상태를 나타냅니다. 상태 열의 ' 위험 ' 또는 ' 경고 ' 값은 일반적으로 VM에 대 한 이전 복제 주기가 실패 했음을 나타냅니다. 자세한 내용을 보려면 VM을 마우스 오른쪽 단추로 클릭 하 고 "오류 정보"를 선택 합니다. 오류 정보 페이지에는 오류에 대 한 정보 및 문제 해결 방법에 대 한 추가 정보가 포함 되어 있습니다. 또한 VM에 대 한 이벤트 페이지로 이동 하는 데 사용할 수 있는 "최근 이벤트" 링크도 표시 됩니다.
  4. "최근 이벤트"를 클릭 하 여 VM에 대 한 이전 복제 주기 실패를 확인 합니다. 이벤트 페이지에서 VM에 대 한 "복제 주기 실패" 또는 "디스크의 복제 주기 실패" 유형의 최신 이벤트를 찾습니다.
  5. 이벤트를 클릭 하 여 오류의 가능한 원인과 권장 수정 단계를 이해 합니다. 제공 된 정보를 사용 하 여 오류를 해결 하 고 해결 합니다.
    
## <a name="common-replication-errors"></a>일반적인 복제 오류

이 섹션에서는 몇 가지 일반적인 오류와 이러한 오류를 해결 하는 방법을 설명 합니다.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Vm을 복제 하는 동안 Key Vault 작업 실패 오류

**오류:** "Key Vault 작업이 실패 했습니다. 작업: 관리 되는 저장소 계정 구성, Key Vault: 키 자격 증명 모음 이름, 저장소 계정: 저장소 계정 이름 실패 (오류: ")

**오류:** "Key Vault 작업이 실패 했습니다. 작업: 공유 액세스 서명 정의를 생성 Key Vault: 키 자격 증명 모음 이름, 저장소 계정: 저장소 계정 이름이 실패 했습니다. 오류: "

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

이 오류는 일반적으로 Key Vault에 대 한 사용자 액세스 정책에서 저장소 계정을 Key Vault 관리 하도록 구성 하는 데 필요한 권한을 현재 로그인 한 사용자에 게 제공 하지 않기 때문에 발생 합니다. 키 자격 증명 모음에 대 한 사용자 액세스 정책을 확인 하려면 키 자격 증명 모음에 대 한 포털의 키 자격 증명 모음 페이지로 이동 하 여 액세스 정책을 선택 합니다. 

포털에서 키 자격 증명 모음을 만드는 경우에는 현재 로그인 한 사용자에 게 관리 권한을 부여 하는 사용자 액세스 정책도 추가 하 여 Key Vault 관리 되도록 저장소 계정을 구성할 수 있습니다. 이는 두 가지 이유로 실패할 수 있습니다.

- 로그인 한 사용자는 고객 Azure 테 넌 트의 원격 사용자 (CSP 구독 및 로그인 한 사용자가 파트너 관리자)입니다. 이 경우의 해결 방법은 주요 자격 증명 모음을 삭제 하 고, 포털에서 로그 아웃 한 다음, 고객 테 넌 트 (원격 주 서버가 아님)에서 사용자 계정으로 로그인 하 여 작업을 다시 시도 하는 것입니다. CSP 파트너는 일반적으로 고객 Azure Active Directory 테 넌 트에 사용할 수 있는 사용자 계정을 가집니다. 그렇지 않은 경우 고객 Azure Active Directory 테 넌 트에 새 사용자 계정을 만들 수 없으며 포털에 새 사용자로 로그인 한 후 복제 작업을 다시 시도 합니다. 사용 되는 계정에는 리소스 그룹의 계정에 부여 된 소유자 또는 참가자 + 사용자 액세스 관리자 권한이 있어야 합니다 (프로젝트 리소스 그룹 마이그레이션).

- 이 문제가 발생할 수 있는 다른 경우는 한 사용자 (user1)가 처음에 복제를 설정 하 고 오류가 발생 했지만 key vault가 이미 생성 된 경우 (그리고 사용자 액세스 정책이이 사용자에 게 적절 하 게 할당 된 경우)입니다. 이제 나중에 다른 사용자 (사용자 2)가 복제를 설정 하려고 하지만, 키 자격 증명 모음에 사용자 지정에 해당 하는 사용자 액세스 정책이 없으므로 관리 저장소 계정 구성 또는 SAS 정의 생성 작업이 실패 합니다.

**해결**방법:이 문제를 해결 하려면 관리 되는 저장소 계정을 구성 하 고 SAS 정의를 생성 하는 keyvault에서 사용자 2에 대 한 사용자 액세스 정책을 만듭니다. 다음 cmdlet을 사용 하 여 Azure PowerShell에서이 작업을 수행할 수 있습니다.

$userPrincipalId = $ (Get-azurermaduser-UserPrincipalName "user2_email_address"). A-id

Set-azurermkeyvaultaccesspolicy-VaultName "keyvaultname"-ObjectId $userPrincipalId-Stostorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, 복구, 백업, 복원, 제거


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**오류 ID:** 181008

**오류 메시지:** VM: VMName. 오류: & ' [' SnapshotReplication SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle ' (' WaitingForArtefactsDisposalPreCycle ')] ' 상태에서 시간 제한 이벤트 ' DisposeArtefactsTimeout '이 (가) 발생 했습니다.

**가능한 원인:**

Azure에 데이터를 복제 하려는 구성 요소가 다운 되었거나 응답 하지 않습니다. 가능한 원인은 다음과 같습니다.

- Azure Migrate 어플라이언스에서 실행 중인 게이트웨이 서비스가 다운 되었습니다.
- 게이트웨이 서비스에 Service Bus/이벤트 허브/어플라이언스 저장소 계정에 대 한 연결 문제가 발생 했습니다.

**DisposeArtefactsTimedOut에 대 한 정확한 원인과 해당 해결 방법 확인:**

1. Azure Migrate 어플라이언스가 실행 중인지 확인합니다.
2. 어플라이언스에서 게이트웨이 서비스가 실행 되 고 있는지 확인 합니다.
   1.  원격 데스크톱을 사용 하 여 Azure Migrate 어플라이언스에 로그인 하 고 다음을 수행 합니다.

   2.  Microsoft 서비스 MMC 스냅인 (> services.msc 실행)을 열고 "Microsoft Azure Gateway Service"가 실행 중인지 확인 합니다. 서비스가 중지 되었거나 실행 되 고 있지 않으면 서비스를 시작 합니다. 또는 명령 프롬프트 또는 PowerShell을 열고 "Net Start asrgwy"를 수행할 수 있습니다.

3. Azure Migrate 어플라이언스와 어플라이언스 저장소 계정 간의 연결 문제를 확인 합니다. 

    Azure Migrate 어플라이언스에서 azcopy를 다운로드 한 후 다음 명령을 실행 합니다.
    
    _azcopy 도구 https://[계정]. blob. SAS_
    
    **성능 벤치 마크 테스트를 실행 하는 단계:**
    
      1. [다운로드](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy
        
      2. 리소스 그룹에서 어플라이언스 저장소 계정을 찾습니다. 저장소 계정의 이름은 migrategwsa와 유사 \* \* \* \* \* \* \* \* \* \* 합니다. 위의 명령에서 [account] 매개 변수의 값입니다.
        
      3. Azure Portal에서 저장소 계정을 검색 합니다. 검색 하는 데 사용 하는 구독이 저장소 계정이 만들어진 것과 동일한 구독 (대상 구독) 인지 확인 합니다. Blob Service 섹션의 컨테이너로 이동 합니다. + 컨테이너를 클릭 하 고 컨테이너를 만듭니다. 공용 액세스 수준을 기본 선택 값으로 그대로 둡니다.
        
      4. 설정에서 공유 액세스 서명으로 이동 합니다. "허용 되는 리소스 종류"에서 컨테이너를 선택 합니다. SAS 및 연결 문자열 생성을 클릭 합니다. SAS 값을 복사 합니다.
        
      5. 계정, 컨테이너, SAS를 각각 2, 3 및 4 단계에서 얻은 값으로 바꿔 명령 프롬프트에서 위의 명령을 실행 합니다.
        
      또는 Azure Storage [다운로드](https://go.microsoft.com/fwlink/?linkid=2138967) 하 여 어플라이언스를 탐색 하 고 저장소 계정에 64 개의 blob을 업로드 하려고 시도 합니다. 문제가 없는 경우 업로드가 성공적으로 수행 됩니다.
        
    **해결 방법:** 이 테스트에 실패 하면 네트워킹 문제가&#39;. 연결 문제를 확인 하기 위해 로컬 네트워킹 팀에 참여 합니다. 일반적으로 오류를 발생 시키는 일부 방화벽 설정이 있을 수 있습니다.
    
4.  Azure Migrate 어플라이언스와 Service Bus 간의 연결 문제를 확인 합니다.

    이 테스트는 Azure Migrate 기기가 Azure Migrate 클라우드 서비스 백 엔드와 통신할 수 있는지 확인 합니다. 어플라이언스는 Service Bus 및 이벤트 허브 메시지 큐를 통해 서비스 백 엔드와 통신 합니다. 어플라이언스에서 Service Bus 연결의 유효성을 검사 하려면 Service Bus 탐색기를 [다운로드](https://go.microsoft.com/fwlink/?linkid=2139104) 하 고 어플라이언스 Service Bus 연결을 시도한 후 메시지 보내기/받기 메시지를 수행 합니다. 문제가 없으면 성공 해야 합니다.

    **테스트를 실행 하는 단계:**

    1. 마이그레이션 프로젝트에서 만든 Service Bus 연결 문자열을 복사 합니다.
    2. Service Bus 탐색기를 엽니다.
    3. 파일로 이동 하 여 연결
    4. 연결 문자열을 붙여넣고 연결을 클릭 합니다.
    5. 그러면 Service Bus 이름 공간이 열립니다.
    6. 항목에서 Snapshot Manager를 선택 합니다. Snapshot Manager를 마우스 오른쪽 단추로 클릭 하 고 "메시지 받기"를 선택 > "peek"를 선택 하 고 확인을 클릭 합니다.
    7. 성공적으로 연결 되 면 콘솔 출력에 "[x] 메시지가 수신 되었습니다."가 표시 됩니다. 연결이 실패 하면 연결에 실패 했다는 메시지가 표시 됩니다.
    
    **해결 방법:** 이 테스트에 실패 하면 네트워킹 문제가 발생 합니다. 연결 문제를 확인 하기 위해 로컬 네트워킹 팀에 참여 합니다. 일반적으로 오류를 발생 시키는 일부 방화벽 설정이 있을 수 있습니다.

5. Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제:

    이 테스트는 Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제를 확인 합니다. Key Vault는 복제에 사용 되는 저장소 계정 액세스를 관리 하는 데 사용 됩니다.
    
    **연결을 확인 하는 단계:**
    
    1. Azure Migrate 프로젝트에 해당 하는 리소스 그룹의 리소스 목록에서 Key Vault URI를 가져옵니다.
    
    1. Azure Migrate 어플라이언스에서 PowerShell을 열고 다음 명령을 실행 합니다.
    
    _테스트 netconnection Key Vault URI-P 443_
    
    이 명령은 TCP 연결을 시도 하 고 출력을 반환 합니다.
    
     - 출력에서 "_Tcptestsucceeded_" 필드를 확인 합니다. 값이 "_True_" 이면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 없습니다. 값이 "False" 이면 연결 문제가 있는 것입니다.
    
    **해결 방법:** 이 테스트에 실패 하면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 있습니다. 연결 문제를 확인 하기 위해 로컬 네트워킹 팀에 참여 합니다. 일반적으로 오류를 발생 시키는 일부 방화벽 설정이 있을 수 있습니다.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**오류 ID:** 1011

**오류 메시지:** 가상 컴퓨터 VMName의 디스크가 디스크 DiskPath에 대 한 데이터 업로드 VMId가 예상 된 시간 내에 완료 되지 않았습니다.

이 오류는 일반적으로 복제를 수행 하는 Azure Migrate 어플라이언스를 Azure Cloud Services에 연결할 수 없거나 복제가 느리게 진행 되어 복제 주기가 시간 초과 됨을 나타냅니다.

가능한 원인은 다음과 같습니다.

- Azure Migrate 어플라이언스의 작동이 중단 되었습니다.
- 어플라이언스의 복제 게이트웨이 서비스가 실행 되 고 있지 않습니다.
- 복제 게이트웨이 서비스에서 복제에 사용 되는 다음 Azure 서비스 구성 요소 중 하나에 대 한 연결 문제가 발생 하 고 있습니다. Service Bus/이벤트 허브/Azure cache Storage 계정/Azure Key Vault.
- 디스크를 읽으려고 할 때 게이트웨이 서비스가 vCenter 수준에서 제한 됩니다.

**근본 원인을 파악 하 고 문제를 해결 합니다.**

1. Azure Migrate 어플라이언스가 실행 중인지 확인합니다.
2. 어플라이언스에서 게이트웨이 서비스가 실행 되 고 있는지 확인 합니다.
   1.  원격 데스크톱을 사용 하 여 Azure Migrate 어플라이언스에 로그인 하 고 다음을 수행 합니다.

   2.  Microsoft 서비스 MMC 스냅인 (> services.msc 실행)을 열고 "Microsoft Azure Gateway Service"가 실행 중인지 확인 합니다. 서비스가 중지 되었거나 실행 되 고 있지 않으면 서비스를 시작 합니다. 또는 명령 프롬프트 또는 PowerShell을 열고 "Net Start asrgwy"를 수행할 수 있습니다.


3. **Azure Migrate 어플라이언스와 캐시 저장소 계정 간의 연결 문제를 확인 합니다.** 

    Azure Migrate 어플라이언스에서 azcopy를 다운로드 한 후 다음 명령을 실행 합니다.
    
    _azcopy 도구 https://[계정]. blob. SAS_
    
    **성능 벤치 마크 테스트를 실행 하는 단계:**
    
      1. [다운로드](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy
        
      2. 리소스 그룹에서 어플라이언스 저장소 계정을 찾습니다. 저장소 계정의 이름은 migratelsa와 유사 \* \* \* \* \* \* \* \* \* \* 합니다. 위의 명령에서 [account] 매개 변수의 값입니다.
        
      3. Azure Portal에서 저장소 계정을 검색 합니다. 검색 하는 데 사용 하는 구독이 저장소 계정이 만들어진 것과 동일한 구독 (대상 구독) 인지 확인 합니다. Blob Service 섹션의 컨테이너로 이동 합니다. + 컨테이너를 클릭 하 고 컨테이너를 만듭니다. 공용 액세스 수준을 기본 선택 값으로 그대로 둡니다.
        
      4. 설정에서 공유 액세스 서명으로 이동 합니다. "허용 되는 리소스 종류"에서 컨테이너를 선택 합니다. SAS 및 연결 문자열 생성을 클릭 합니다. SAS 값을 복사 합니다.
        
      5. 계정, 컨테이너, SAS를 각각 2, 3 및 4 단계에서 얻은 값으로 바꿔 명령 프롬프트에서 위의 명령을 실행 합니다.
        
      또는 Azure Storage [다운로드](https://go.microsoft.com/fwlink/?linkid=2138967) 하 여 어플라이언스를 탐색 하 고 저장소 계정에 64 개의 blob을 업로드 하려고 시도 합니다. 문제가 없는 경우 업로드가 성공적으로 수행 됩니다.
        
    **해결 방법:** 이 테스트에 실패 하면 네트워킹 문제가&#39;. 연결 문제를 확인 하기 위해 로컬 네트워킹 팀에 참여 합니다. 일반적으로 오류를 발생 시키는 일부 방화벽 설정이 있을 수 있습니다.
                
4.  **Azure Migrate 어플라이언스와 Azure Service Bus 간의 연결 문제:**

    이 테스트는 Azure Migrate 기기가 Azure Migrate 클라우드 서비스 백 엔드와 통신할 수 있는지 여부를 확인 합니다. 어플라이언스는 Service Bus 및 이벤트 허브 메시지 큐를 통해 서비스 백 엔드와 통신 합니다. 어플라이언스에서 Service Bus 연결의 유효성을 검사 하려면 Service Bus 탐색기를 [다운로드](https://go.microsoft.com/fwlink/?linkid=2139104) 하 고 어플라이언스 Service Bus 연결을 시도한 후 메시지 보내기/받기 메시지를 수행 합니다. 문제가 없으면 성공 해야 합니다.

    **테스트를 실행 하는 단계:**
    
    1. Azure Migrate 프로젝트에 해당 하는 리소스 그룹에서 만든 Service Bus 연결 문자열을 복사 합니다.
    
    1. Service Bus 탐색기 열기
    
    1. 파일 > 연결로 이동 합니다.
    
    1. 1 단계에서 복사한 연결 문자열을 붙여넣고 연결을 클릭 합니다.
    
    1. 이렇게 하면 Service Bus 네임 스페이스가 열립니다.
    
    1. 네임 스페이스의 항목에서 Snapshot Manager를 선택 합니다. Snapshot Manager를 마우스 오른쪽 단추로 클릭 하 고 "메시지 받기"를 선택 > "피킹 (peeking)"을 선택 하 고 확인을 클릭 합니다.
    
    성공적으로 연결 되 면 콘솔 출력에 "[x] 메시지가 수신 되었습니다."가 표시 됩니다. 연결이 실패 하면 연결에 실패 했다는 메시지가 표시 됩니다.
    
    **해결 방법:** 이 테스트에 실패 하면 Azure Migrate 어플라이언스와 Service Bus 간에 연결 문제가 있습니다. 이러한 연결 문제를 확인 하기 위해 로컬 네트워킹 팀에 참여 하세요. 일반적으로 오류를 발생 시키는 일부 방화벽 설정이 있을 수 있습니다.
    
 5. **Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제:**

    이 테스트는 Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제를 확인 합니다. Key Vault는 복제에 사용 되는 저장소 계정 액세스를 관리 하는 데 사용 됩니다.
    
    **연결을 확인 하는 단계:**
    
    1. Azure Migrate 프로젝트에 해당 하는 리소스 그룹의 리소스 목록에서 Key Vault URI를 가져옵니다.
    
    1. Azure Migrate 어플라이언스에서 PowerShell을 열고 다음 명령을 실행 합니다.
    
    _테스트 netconnection Key Vault URI-P 443_
    
    이 명령은 TCP 연결을 시도 하 고 출력을 반환 합니다.
    
    1. 출력에서 "_Tcptestsucceeded_" 필드를 확인 합니다. 값이 "_True_" 이면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 없습니다. 값이 "False" 이면 연결 문제가 있는 것입니다.
    
    **해결 방법:** 이 테스트에 실패 하면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 있습니다. 연결 문제를 확인 하기 위해 로컬 네트워킹 팀에 참여 합니다. 일반적으로 오류를 발생 시키는 일부 방화벽 설정이 있을 수 있습니다.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>변경 된 블록을 인출 하는 동안 오류가 발생 했습니다.

오류 메시지: ' 변경 블록을 인출 하는 동안 오류가 발생 했습니다. '

에이전트 없는 복제 방법은 VMware의 변경 된 CBT (블록 추적 기술)를 사용 하 여 Azure에 데이터를 복제 합니다. CBT를 사용 하면 서버 마이그레이션 도구가 마지막 복제 주기 이후 변경 된 블록만 추적 하 고 복제할 수 있습니다. 복제 가상 머신에 대한 변경된 블록 추적이 다시 설정되거나 변경된 블록 추적 파일이 손상된 경우 이 오류가 발생합니다.

이 오류는 다음과 같은 두 가지 방법으로 해결할 수 있습니다.

- VM의 복제를 트리거할 때 "예"를 선택 하 여 "자동으로 복제 복구"를 옵트인 한 경우 도구에서 자동으로 복구를 시도 합니다. VM을 마우스 오른쪽 단추로 클릭 하 고 "복제 복구"를 선택 합니다.
- "자동으로 복제 복구"를 선택 하지 않았거나 위의 단계가 작동 하지 않는 경우 가상 머신에 대 한 복제를 중지 하 고, 가상 머신에서 [변경 된 블록 추적을 다시 설정](https://go.microsoft.com/fwlink/?linkid=2139203) 하 고, 복제를 다시 구성 합니다.

VMware vSphere 5.5에서 가상 머신의 CBT 재설정을 발생 시킬 수 있는 이러한 알려진 문제 중 하나는 [VMWARE KB 2048201:](https://go.microsoft.com/fwlink/?linkid=2138888) vsphere 5. x의 저장소 vMotion 작업 후 변경 된 블록 추적이 다시 설정 되는 것입니다. VMware vSphere 5.5를 사용하는 경우 이 KB에 설명된 업데이트를 적용합니다.

또는 VMware PowerCLI를 사용 하 여 가상 머신에서 VMware 변경 된 블록 추적을 다시 설정할 수 있습니다.

## <a name="an-internal-error-occurred"></a>내부 오류가 발생 했습니다.

경우에 따라 VMware 환경/a p i의 문제로 인해 발생 하는 오류가 발생할 수 있습니다. 다음 오류 집합을 VMware 환경 관련 오류로 식별 했습니다. 이러한 오류의 형식은 고정 되어 있습니다.

_오류 메시지: 내부 오류가 발생 했습니다. [오류 메시지]_

예: 오류 메시지: 내부 오류가 발생 했습니다. [잘못 된 스냅숏 구성이 검색 되었습니다.]

다음 섹션에는 일반적으로 표시 되는 몇 가지 VMware 오류와 이러한 오류를 완화 하는 방법이 나열 되어 있습니다.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>오류 메시지: 내부 오류가 발생 했습니다. [서버 연결 거부]

문제는 알려진 VMware 문제 이며 VDDK 6.7에서 발생 합니다. Azure Migrate 어플라이언스에서 실행 되는 게이트웨이 서비스를 중지 하 고 [VMWARE KB에서 업데이트를 다운로드](https://go.microsoft.com/fwlink/?linkid=2138889)한 후 게이트웨이 서비스를 다시 시작 해야 합니다.

게이트웨이 서비스를 중지 하는 단계:

1. Windows + R을 누르고 services.msc를 엽니다. "Microsoft Azure Gateway Service"를 클릭 하 고 중지 합니다.
2. 또는 명령 프롬프트 또는 PowerShell을 열고 Net Stop asrgwy을 수행할 수 있습니다. 서비스가 더 이상 실행 되지 않는 메시지가 나타날 때까지 기다려야 합니다.

게이트웨이 서비스를 시작 하는 단계:

1. Windows + R을 누르고 services.msc를 엽니다. "Microsoft Azure Gateway Service"를 마우스 오른쪽 단추로 클릭 하 고 시작 합니다.
2. 또는 명령 프롬프트 또는 PowerShell을 열고 Net Start asrgwy를 수행할 수 있습니다.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>오류 메시지: 내부 오류가 발생 했습니다. [' 잘못 된 스냅숏 구성이 검색 되었습니다. ']

여러 디스크를 포함 하는 가상 컴퓨터가 있는 경우 가상 컴퓨터에서 디스크를 제거 하면이 오류가 발생할 수 있습니다. 이 문제를 해결 하려면 [이 VMware 문서의](https://go.microsoft.com/fwlink/?linkid=2138890)단계를 참조 하세요.

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>오류 메시지: 내부 오류가 발생 했습니다. [스냅숏 생성 중지 됨]

이 문제는 스냅숏 생성이 응답 하지 않을 때 발생 합니다. 이 문제가 발생 하면 95% 또는 99%에서 스냅숏 만들기 작업 중지를 확인할 수 있습니다. 이 문제를 해결 하려면이 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138969) 를 참조 하십시오.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>오류 메시지: 내부 오류가 발생 했습니다. [VM에서 디스크를 통합 하지 못했습니다. _[이유]_]

복제 주기 끝에 디스크를 통합 하면 작업이 실패 합니다. 적절 한 _이유_ 를 선택 하 여 문제를 해결 하는 [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) 의 지침을 따르세요.

VMware 스냅숏 관련 작업 (create, delete 또는 create disk)이 실패 하면 다음과 같은 오류가 발생 합니다. 다음 섹션의 지침에 따라 오류를 수정 합니다.

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>오류 메시지: 내부 오류가 발생 했습니다. [다른 태스크가 이미 진행 중입니다.]

백그라운드에서 실행 중인 충돌 하는 가상 컴퓨터 작업이 있거나 vCenter Server 내의 태스크가 시간 초과 되는 경우이 문제가 발생 합니다. 다음 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138891)에 제공 된 해상도를 따릅니다.

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>오류 메시지: 내부 오류가 발생 했습니다. [현재 상태에서 작업이 허용 되지 않음]

VCenter Server management agent의 작동이 중지 되는 경우이 문제가 발생 합니다. 이 문제를 해결 하려면 다음 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138971)의 해상도를 참조 하십시오.

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>오류 메시지: 내부 오류가 발생 했습니다. [스냅숏 디스크 크기가 잘못 되었습니다.]

이는 스냅숏에서 나타내는 디스크 크기가 0이 되는 알려진 VMware 문제입니다. [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138972)에 제공 된 해상도를 따릅니다.

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>오류 메시지: 내부 오류가 발생 했습니다. [메모리 할당에 실패 했습니다. 메모리가 부족 합니다.]

이는 NFC 호스트 버퍼의 메모리가 부족 한 경우에 발생 합니다. 이 문제를 해결 하려면 VM (compute vMotion)을 사용 가능한 리소스가 있는 다른 호스트로 이동 해야 합니다.

## <a name="next-steps"></a>다음 단계

VM 복제를 계속 하 고 [테스트 마이그레이션을](https://go.microsoft.com/fwlink/?linkid=2139333)수행 합니다.