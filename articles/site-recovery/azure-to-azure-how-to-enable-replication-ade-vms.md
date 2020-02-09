---
title: Azure Site Recovery에서 암호화 된 Azure Vm에 대해 복제를 사용 하도록 설정
description: 이 문서에서는 Site Recovery를 사용 하 여 한 Azure 지역에서 다른 지역으로 Azure Disk Encryption 지원 Vm에 대 한 복제를 구성 하는 방법을 설명 합니다.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 3a59f137240eff2a3a68fa5547be8c6c25d3e5fe
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772230"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Azure Disk Encryption 사용 가능한 가상 컴퓨터를 다른 Azure 지역에 복제

이 문서에서는 한 Azure 지역에서 다른 Azure 지역으로 Azure Vm (ADE)을 사용 하도록 설정 Azure Disk Encryption 된 Azure Vm을 복제 하는 방법을 설명 합니다.

>[!NOTE]
> Site Recovery는 현재 Windows 및 Linux 운영 체제를 실행 하는 Vm에 대해 AAD (Azure Active Directory)를 사용 하거나 사용 하지 않고 ADE를 지원 합니다.  AAD를 사용 하지 않고 ADE 1.1를 실행 하는 컴퓨터의 경우 Vm은 관리 디스크를 사용 해야 합니다. 관리 되지 않는 디스크가 있는 Vm은 지원 되지 않습니다. ADE 0.1 (AAD)에서 1.1로 전환 하는 경우에는 복제를 사용 하지 않도록 설정 하 고 1.1를 사용 하도록 설정한 후 VM에 대해 복제를 사용 하도록 설정 해야 합니다.


## <a id="required-user-permissions"></a>필요한 사용자 권한
Site Recovery를 사용 하려면 사용자에 게 대상 지역에서 키 자격 증명 모음을 만들고 원본 지역 key vault에서 대상 지역 key vault로 키를 복사할 수 있는 권한이 있어야 합니다.

Azure Portal에서 디스크 암호화 사용 Vm의 복제를 사용 하도록 설정 하려면 사용자에 게 **원본 지역과 대상 지역** 키 자격 증명 모음에 대 한 다음 권한이 있어야 합니다.

- 키 자격 증명 모음 권한
    - 목록, 만들기 및 가져오기
    
- 키 자격 증명 모음 비밀 권한
    - 비밀 관리 작업
        - Get, List 및 Set
    
- 키 자격 증명 모음 키 사용 권한 (Vm에서 키 암호화 키를 사용 하 여 디스크 암호화 키를 암호화 하는 경우에만 필요)
    - 키 관리 작업
        - 가져오기, 나열 및 만들기
    - 암호화 작업
        - 암호 해독 및 암호화

사용 권한을 관리 하려면 포털의 key vault 리소스로 이동 합니다. 사용자에 게 필요한 권한을 추가 합니다. 다음 예제에서는 소스 영역에 있는 key vault *ContosoWeb2Keyvault*에 대 한 사용 권한을 설정 하는 방법을 보여 줍니다.

1. **홈** > **Keyvaults** > **ContosoWeb2KeyVault > 액세스 정책**으로 이동 합니다.

   ![키 자격 증명 모음 권한 창](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 사용자 권한이 없다는 것을 볼 수 있습니다. **새로 추가**를 선택합니다. 사용자 및 사용 권한 정보를 입력 합니다.

   ![Keyvault 권한](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

DR (재해 복구)을 사용 하도록 설정 하는 사용자에 게 키를 복사할 권한이 없는 경우 적절 한 권한이 있는 보안 관리자는 다음 스크립트를 사용 하 여 암호화 암호와 키를 대상 지역에 복사할 수 있습니다.

권한 문제를 해결 하려면이 문서의 뒷부분에 나오는 [key vault 권한 문제](#trusted-root-certificates-error-code-151066) 를 참조 하세요.

>[!NOTE]
>포털에서 디스크 암호화 사용 Vm의 복제를 사용 하도록 설정 하려면 키 자격 증명 모음, 암호 및 키에 대 한 "목록" 권한이 있어야 합니다.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>PowerShell 스크립트를 사용 하 여 DR 지역에 디스크 암호화 키 복사

1. ["CopyKeys" 원시 스크립트 코드를 엽니다](https://aka.ms/ade-asr-copy-keys-code).
2. 스크립트를 파일에 복사 하 고 이름을 **Copy-keys**로 다시 만듭니다.
3. Windows PowerShell 응용 프로그램을 열고 파일을 저장 한 폴더로 이동 합니다.
4. Copy-keys를 실행 합니다.
5. 로그인 하려면 Azure 자격 증명을 제공 하세요.
6. VM의 **Azure 구독**을 선택합니다.
7. 리소스 그룹이 로드 될 때까지 기다린 후 Vm의 **리소스 그룹** 을 선택 합니다.
8. 표시 되는 목록에서 Vm을 선택 합니다. 디스크 암호화를 사용 하도록 설정 된 Vm만 목록에 있습니다.
9. **대상 위치**를 선택 합니다.

    - **디스크 암호화 키 자격 증명 모음**
    - **키 암호화 키 자격 증명 모음**

   기본적으로 Site Recovery는 대상 지역에 새 Key Vault를 만듭니다. 자격 증명 모음의 이름에는 원본 VM 디스크 암호화 키를 기반으로 하는 "asr" 접미사가 있습니다. Site Recovery에서 만든 키 자격 증명 모음이 이미 있는 경우 다시 사용 됩니다. 필요한 경우 목록에서 다른 키 자격 증명 모음을 선택 합니다.

## <a name="enable-replication"></a>복제 사용

이 예에서는 기본 Azure 지역이 동아시아 되며 보조 지역은 남부 동아시아입니다.

1. 자격 증명 모음에서 **+ 복제**를 선택 합니다.
2. 다음 필드에 유의 하십시오.
    - **원본**: VM의 원점이며 이 경우 **Azure**입니다.
    - **원본 위치**: 가상 머신을 보호 하려는 Azure 지역입니다. 이 예제에서 원본 위치는 "동아시아"입니다.
    - **배포 모델**: 원본 컴퓨터의 Azure 배포 모델입니다.
    - **원본 구독**: 원본 가상 머신이 속한 구독입니다. Recovery services 자격 증명 모음과 동일한 Azure Active Directory 테넌트에 있는 모든 구독 일 수 있습니다.
    - **리소스 그룹**: 원본 가상 머신이 속해 있는 리소스 그룹입니다. 선택한 리소스 그룹의 모든 Vm은 다음 단계에서 보호를 위해 나열 됩니다.

3. **가상 컴퓨터** > **Virtual Machines** 선택에서 복제 하려는 각 VM을 선택 합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 다음 **확인**을 선택합니다.

4. **설정**에서 다음과 같은 대상 사이트 설정을 구성할 수 있습니다.

    - **대상 위치**: 원본 가상 머신 데이터가 복제 될 위치입니다. Site Recovery 선택한 컴퓨터의 위치에 따라 적합 한 대상 영역 목록을 제공 합니다. Recovery Services 자격 증명 모음 위치와 동일한 위치를 사용 하는 것이 좋습니다.
    - **대상 구독**: 재해 복구에 사용 되는 대상 구독입니다. 기본적으로 대상 구독은 원본 구독과 동일합니다.
    - **대상 리소스 그룹**: 모든 복제된 가상 머신이 속하게 될 리소스 그룹입니다. 기본적으로 Site Recovery는 대상 지역에 새 리소스 그룹을 만듭니다. 이름은 "asr" 접미사를 가져옵니다. Azure Site Recovery에서 만든 리소스 그룹이 이미 있는 경우 다시 사용 됩니다. 다음 섹션과 같이 사용자 지정 하도록 선택할 수도 있습니다. 대상 리소스 그룹의 위치는 원본 가상 컴퓨터가 호스트 되는 지역을 제외한 모든 Azure 지역이 될 수 있습니다.
    - **대상 virtual network**: 기본적으로 Site Recovery는 대상 지역에 새 가상 네트워크를 만듭니다. 이름은 "asr" 접미사를 가져옵니다. 원본 네트워크에 매핑되고 향후 보호에 사용 됩니다. [자세히 알아봅니다](site-recovery-network-mapping-azure-to-azure.md) 를 확인해 보세요.
    - **대상 저장소 계정 (원본 vm이 관리 디스크를 사용 하지 않는 경우)** : 기본적으로 Site Recovery는 원본 vm 저장소 구성을 모방 하 여 새 대상 저장소 계정을 만듭니다. 저장소 계정이 이미 있는 경우 다시 사용 됩니다.
    - **복제본 관리 디스크 (원본 vm이 관리 디스크를 사용 하는 경우)** : Site Recovery 대상 지역에 새 복제본 관리 디스크를 만들어서 원본 vm의 관리 디스크와 동일한 저장소 유형 (standard 또는 premium)의 원본 vm 관리 디스크를 미러링합니다.
    - **캐시 저장소 계정**: Site Recovery에는 원본 지역에서 *캐시 저장소* 라는 추가 저장소 계정이 필요 합니다. 원본 Vm의 모든 변경 내용이 추적 되 고 캐시 저장소 계정으로 전송 됩니다. 그런 다음 대상 위치에 복제 됩니다.
    - **가용성 집합**: 기본적으로 Site Recovery는 대상 지역에 새 가용성 집합을 만듭니다. 이름에 "asr" 접미사가 있습니다. Site Recovery에서 만든 가용성 집합이 이미 있는 경우 다시 사용 됩니다.
    - **디스크 암호화 키 자격 증명 모음**: 기본적으로 Site Recovery는 대상 지역에 새 키 자격 증명 모음을 만듭니다. 원본 VM 디스크 암호화 키를 기반으로 하는 "asr" 접미사가 있습니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있는 경우 다시 사용 됩니다.
    - **키 암호화 키 자격 증명 모음**: 기본적으로 Site Recovery는 대상 지역에 새 키 자격 증명 모음을 만듭니다. 이름에는 원본 VM 키 암호화 키를 기반으로 하는 "asr" 접미사가 있습니다. Azure Site Recovery에서 만든 키 자격 증명 모음이 이미 있는 경우 다시 사용 됩니다.
    - **복제 정책**: 복구 지점 보존 기록 및 앱 일치 스냅숏 빈도에 대 한 설정을 정의 합니다. 기본적으로 Site Recovery는 복구 지점 보존을 위한 *24 시간* 및 앱 일치 스냅숏 빈도에 대 한 *60 분* 의 기본 설정을 사용 하 여 새 복제 정책을 만듭니다.

## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

Site Recovery 기본 대상 설정을 수정 하려면 다음 단계를 수행 합니다.

1. "대상 구독" 옆의 **사용자 지정** 을 선택 하 여 기본 대상 구독을 수정 합니다. Azure AD 테넌트에서 사용할 수 있는 구독 목록에서 구독을 선택 합니다.

2. "리소스 그룹, 네트워크, 저장소 및 가용성 집합" 옆의 **사용자 지정** 을 선택 하 여 다음 기본 설정을 수정 합니다.
    - **대상 리소스 그룹**의 경우 구독 대상 위치의 리소스 그룹 목록에서 리소스 그룹을 선택 합니다.
    - **대상 가상 네트워크**의 경우 대상 위치의 가상 네트워크 목록에서 네트워크를 선택 합니다.
    - 가용성 **집합의 경우**가용성 집합 설정이 원본 지역의 가용성 집합의 일부인 경우 VM에 해당 설정을 추가할 수 있습니다.
    - **대상 저장소 계정**에 대해 사용할 계정을 선택 합니다.

2. "암호화 설정" 옆의 **사용자 지정** 을 선택 하 여 다음 기본 설정을 수정 합니다.
   - **대상 디스크 암호화 키 자격 증명 모음**의 경우 구독 대상 위치의 키 자격 증명 모음 목록에서 대상 디스크 암호화 키 자격 증명 모음을 선택 합니다.
   - **대상 키 암호화 키 자격 증명 모음의**경우 구독 대상 위치의 키 자격 증명 모음 목록에서 대상 키 암호화 키 자격 증명 모음을 선택 합니다.

3. **대상 리소스 만들기** 를 선택 하 > **복제를 사용 하도록 설정**합니다.
4. Vm이 복제를 사용 하도록 설정 된 후 복제 된 **항목**에서 vm의 상태를 확인할 수 있습니다.

>[!NOTE]
>초기 복제 중에는 상태를 명확 하 게 진행 하지 않고 새로 고치는 데 시간이 걸릴 수 있습니다. 최신 상태를 가져오려면 **새로 고침** 을 클릭 합니다.

## <a name="update-target-vm-encryption-settings"></a>대상 VM 암호화 설정 업데이트
다음 시나리오에서는 대상 VM 암호화 설정을 업데이트 해야 합니다.
  - VM에서 Site Recovery 복제를 사용 하도록 설정 했습니다. 나중에 원본 VM에서 디스크 암호화를 사용 하도록 설정 했습니다.
  - VM에서 Site Recovery 복제를 사용 하도록 설정 했습니다. 나중에 원본 VM에서 디스크 암호화 키 또는 키 암호화 키를 변경 했습니다.

[스크립트를](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) 사용 하 여 암호화 키를 대상 영역에 복사한 다음, **Recovery services 자격 증명 모음** > *복제 된 항목* > **속성** > **Compute 및 네트워크**에 대 한 대상 암호화 설정을 업데이트할 수 있습니다.

![ADE 설정 업데이트 대화 상자 창](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Azure에서 Azure로 VM을 복제 하는 동안 주요 자격 증명 모음 권한 문제 해결

비밀을 읽고 대상 지역 키 자격 증명 모음에 복사 하려면 소스 지역 key vault에 대 한 읽기 권한이 있어야 하 고 대상 지역 key vault에 대 한 쓰기 권한이 있어야 Azure Site Recovery. 

**원인 1:** 키를 읽을 **원본 지역 Key vault** 에 대 한 "GET" 권한이 없습니다. </br>
**해결 방법:** 구독 관리자 인지 여부에 관계 없이 주요 자격 증명 모음에 대 한 get 권한이 있어야 합니다.

1. 이 예에서 "ContososourceKeyvault" > **액세스 정책** 인 원본 영역 키 자격 증명 모음으로 이동 합니다. 
2. **보안 주체 선택** 에서 사용자 이름 추가 예: "dradmin@contoso.com"
3. **키 사용 권한** 에서 가져오기를 선택 합니다. 
4. **비밀 권한** 아래에서 가져오기를 선택 합니다. 
5. 액세스 정책 저장

**원인 2:** 키를 쓸 수 있는 **대상 지역 키 자격 증명 모음** 에 대 한 필수 권한이 없습니다. </br>

*예를 들어*원본 지역에 Key vault *CONTOSOSOURCEKEYVAULT* 가 있는 VM을 복제 하려고 합니다.
원본 지역 키 자격 증명 모음에 대 한 모든 권한이 있습니다. 하지만 보호 하는 동안 권한이 없는 이미 생성 된 key vault ContosotargetKeyvault를 선택 합니다. 오류가 발생 합니다.

[대상 키 자격 증명 모음](#required-user-permissions) 에 필요한 권한

**해결 방법:** **홈** > **Keyvaults** > **ContosotargetKeyvault** > **액세스 정책** 으로 이동 하 여 적절 한 사용 권한을 추가 합니다.

## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
