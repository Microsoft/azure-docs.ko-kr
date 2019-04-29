---
title: Azure Site Recovery에서 Azure Disk Encryption을 사용 하도록 설정 하는 Vm에 대 한 복제 구성 | Microsoft Docs
description: 이 문서에서는 Site Recovery를 사용 하 여 다른 Azure 지역 간에 Azure 디스크 암호화 지원 Vm에 대 한 복제를 구성 하는 방법을 설명 합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791017"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>다른 Azure 지역에 Azure Disk Encryption을 사용 하도록 설정 하는 가상 머신 복제

이 문서에서는 다른 Azure 지역 간에 Azure Disk Encryption을 사용 하도록 설정한 Vm을 복제 하는 방법을 설명 합니다.

>[!NOTE]
>Azure Site Recovery는 현재 Azure Vm Windows OS를 실행 하 고 있는을 지원 [Azure Active Directory (Azure AD)를 사용 하 여 암호화가 활성화 된](https://aka.ms/ade-aad-app)합니다.

## <a name="required-user-permissions"></a>필요한 사용자 권한
Site Recovery에는 사용자에 게 지역으로 대상 지역 및 복사 키에서 key vault를 만들 수 있는 권한이 필요 합니다.

Azure portal에서 디스크 암호화 지원 Vm의 복제를 사용 하려면 사용자는 다음 권한이 필요 합니다.

- 키 자격 증명 모음 권한
    - 나열
    - 생성
    - 가져오기

-   키 자격 증명 모음 비밀 권한
    - 나열
    - 생성
    - 가져오기

- 키 자격 증명 모음 키 사용 권한 (Vm 디스크 암호화 키를 암호화 하려면 키 암호화 키를 사용 하는 경우에 필요)
    - 나열
    - 가져오기
    - 생성
    - 암호화
    - 암호 해독

을 권한을 관리 하려면 포털에서 key vault 리소스를 이동 합니다. 사용자에 게 필요한 권한을 추가 합니다. 다음 예에서는 key vault에 대 한 권한을 사용 하도록 설정 하는 방법을 보여 줍니다 *ContosoWeb2Keyvault*, 원본 지역에 있습니다.

1. 로 이동 **Home** > **Keyvaults** > **ContosoWeb2KeyVault > 액세스 정책**합니다.

   ![키 자격 증명 모음 사용 권한 창](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 사용자 권한 없음 있는지 확인할 수 있습니다. **새로 추가**를 선택합니다. 사용자 및 사용 권한 정보를 입력 합니다.

   ![key vault 사용 권한](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

재해 복구 (DR)를 사용 하는 사용자 키를 복사할 권한이 없는 경우 적절 한 권한이 있는 보안 관리자는 암호화 암호 및 키를 대상 지역에 복사할 다음 스크립트를 사용할 수 있습니다.

사용 권한 문제를 해결 하려면 참조 [키 자격 증명 모음 사용 권한 문제](#trusted-root-certificates-error-code-151066) 이 문서의 뒷부분에 나오는.

>[!NOTE]
>포털에서 디스크 암호화 지원 Vm의 복제를 사용 하려면 있습니다 필요 이상 "List" 키 자격 증명 모음, 암호 및 키에 대 한 권한.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>PowerShell 스크립트를 사용 하 여 디스크 암호화 키를 DR 지역으로 복사

1. ["CopyKeys" 원시 스크립트 코드를 열고](https://aka.ms/ade-asr-copy-keys-code)합니다.
2. 파일에 스크립트를 복사 하 고 이름을 **복사 keys.ps1**합니다.
3. Windows PowerShell 응용 프로그램을 열고 파일을 저장 한 폴더로 이동 합니다.
4. 복사 keys.ps1를 실행 합니다.
5. 로그인 하려면 Azure 자격 증명을 제공 합니다.
6. VM의 **Azure 구독**을 선택합니다.
7. 를 로드 하려면 리소스 그룹에 대 한 대기를 선택한 합니다 **리소스 그룹** vm.
8. 표시 되는 목록에서 Vm을 선택 합니다. 디스크 암호화에 사용할 수 있는 Vm만 목록에 있습니다.
9. 선택 된 **대상 위치**합니다.

    - **디스크 암호화 키 자격 증명 모음**
    - **키 암호화 키 자격 증명 모음**

   기본적으로 Site Recovery는 대상 지역에 새 Key Vault를 만듭니다. 자격 증명 모음의 이름에 원본 VM 디스크 암호화 키를 기반으로 하는 "asr" 접미사가 있습니다. 키 자격 증명 모음을 이미 있는 경우 Site Recovery에서 만든 다시 사용 됩니다. 필요한 경우 목록에서 다른 키 자격 증명 모음을 선택 합니다.

## <a name="enable-replication"></a>복제 사용

예를 들어 주 Azure 지역 동남 아시아 이며 보조 지역을 동남 아시아 라고 합니다.

1. 자격 증명 모음에서 선택 **+ 복제**합니다.
2. 다음 필드를 note 합니다.
    - **원본**: VM의 원점이며 이 경우 **Azure**입니다.
    - **원본 위치**: 가상 컴퓨터를 보호 하려는 Azure 지역입니다. 예를 들어 소스 위치가 "East Asia"입니다.
    - **배포 모델**: 원본 컴퓨터의 Azure 배포 모델입니다.
    - **원본 구독**: 원본 가상 머신이 속한 구독입니다. Recovery services 자격 증명 모음을 동일한 Azure Active Directory 테 넌 트에 있는 모든 구독 수 있습니다.
    - **리소스 그룹**: 원본 가상 머신이 속한 리소스 그룹입니다. 선택한 리소스 그룹의 모든 Vm은 다음 단계에서 보호를 위해 나열 됩니다.

3. **Virtual Machines** > **가상 머신 선택**, 복제 하려는 각 VM을 선택 합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 다음 **확인**을 선택합니다.

4. **설정을**, 다음 대상 사이트 설정을 구성할 수 있습니다.

    - **대상 위치**: 원본 가상 머신 데이터가 복제될 위치입니다. Site Recovery는 선택한 컴퓨터의 위치에 따라 적합 한 대상 지역 목록을 제공 합니다. Recovery Services 자격 증명 모음의 위치와 동일한 위치를 사용 하는 것이 좋습니다.
    - **대상 구독**: 재해 복구에 사용 되는 대상 구독입니다. 기본적으로 대상 구독은 원본 구독과 동일합니다.
    - **대상 리소스 그룹**: 모든 복제된 가상 머신이 속하는 리소스 그룹입니다. 기본적으로 Site Recovery는 대상 지역에 새 리소스 그룹을 만듭니다. 이름에 "asr" 접미사를 가져옵니다. 리소스 그룹에 이미 있으면 Azure Site Recovery에서 만든 다시 사용 됩니다. 다음 섹션에 나와 있는 것 처럼, 사용자 지정 하 선택할 수도 있습니다. 대상 리소스 그룹의 위치는 원본 가상 머신이 호스트 되는 위치 지역 제외한 모든 Azure 지역 수 있습니다.
    - **대상 가상 네트워크**: 기본적으로 Site Recovery는 대상 지역에 새 가상 네트워크를 만듭니다. 이름에 "asr" 접미사를 가져옵니다. 원본 네트워크에 매핑된 고 이후의 모든 보호에 사용 합니다. [자세히 알아봅니다](site-recovery-network-mapping-azure-to-azure.md) 를 확인해 보세요.
    - **대상 저장소 계정 (관리 디스크를 VM에서 사용 하지 않는 소스) 하는 경우**: 기본적으로 Site Recovery는 원본 VM 저장소 구성을 모방 하 여 새 대상 저장소 계정을 만듭니다. 저장소 계정이 이미 있는 경우 다시 사용 됩니다.
    - **복제본 관리 디스크 (원본 VM에 managed disks를 사용) 하는 경우**: Site Recovery는 원본 VM의 관리 되는 디스크와 동일한 저장소 유형 (표준 또는 프리미엄)의 원본 VM의 관리 디스크로 미러링합니다 대상 지역에 새로운 복제본 관리 디스크를 만듭니다.
    - **스토리지 계정 캐시**: Site Recovery 라는 추가 저장소 계정이 필요 *캐시할 storage* 원본 지역에 있습니다. 원본 Vm의 모든 변경 내용은 추적 하 고 캐시 저장소 계정으로 전송 됩니다. 이러한 문제는 다음에 대상 위치로 복제 됩니다.
    - **가용성 집합**: 기본적으로 Site Recovery는 대상 지역에 설정 하는 새 가용성을 만듭니다. 이름에 "asr" 접미사가 있습니다. 이미 Site Recovery에서 만든 가용성 집합에 있는 경우 다시 사용 됩니다.
    - **디스크 암호화 Key Vault**: 기본적으로 Site Recovery는 대상 지역에 새 Key Vault를 만듭니다. 원본 VM 디스크 암호화 키를 기반으로 하는 "asr" 접미사가 있습니다. 이미 Azure Site Recovery에서 만든 키 자격 증명 모음에 있는 경우 다시 사용 됩니다.
    - **키 암호화 Key Vault**: 기본적으로 Site Recovery는 대상 지역에 새 Key Vault를 만듭니다. 이름에 원본 VM 키 암호화 키를 기반으로 하는 "asr" 접미사. 이미 Azure Site Recovery에서 만든 키 자격 증명 모음에 있는 경우 다시 사용 됩니다.
    - **복제 정책**: 복구 지점 보존 기록 및 앱 일치 스냅숏 빈도 대 한 설정을 정의합니다. 기본적으로 Site Recovery의 기본 설정을 사용 하 여 새 복제 정책을 만듭니다 *24 시간* 복구 지점 보존 하 고 *60 분* 앱 일치 스냅숏 빈도 대 한 합니다.

## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

Site Recovery 기본 대상 설정을 수정 하려면 다음이 단계를 수행 합니다.

1. 선택 **사용자 지정** "대상 구독" 옆에 있는 기본 대상 구독을 수정 합니다. Azure AD 테 넌 트에서 사용할 수 있는 구독 목록에서 구독을 선택 합니다.

2. 선택 **사용자 지정** 옆에 "리소스 그룹, 네트워크, 저장소 및 가용성 집합" 다음 기본 설정을 수정 하려면:
    - 에 대 한 **대상 리소스 그룹**, 구독의 대상 위치에 리소스 그룹 목록에서 리소스 그룹을 선택 합니다.
    - 에 대 한 **대상 가상 네트워크**, 대상 위치에서 가상 네트워크 목록에서 네트워크를 선택 합니다.
    - 에 대 한 **가용성 집합**, 가용성 원본 영역의 집합의 일부인 경우에 VM을 가용성 집합 설정을 추가할 수 있습니다.
    - 에 대 한 **대상 저장소 계정**를 사용 하 여 계정을 선택 합니다.

2. 선택 **사용자 지정** 암호화 설정 옆에 있는 다음 기본 설정을 수정 하려면:
   - 에 대 한 **대상 디스크 암호화 키 자격 증명 모음**, 구독의 대상 위치에 키 자격 증명 모음 목록에서 대상 디스크 암호화 키 자격 증명 모음을 선택 합니다.
   - 에 대 한 **대상 키 암호화 키 자격 증명 모음**, 구독의 대상 위치에 키 자격 증명 모음 목록에서 대상 키 암호화 키 자격 증명 모음을 선택 합니다.

3. 선택 **대상 리소스 만들기** > **복제 사용**합니다.
4. Vm은 복제를 사용 하도록 설정 아래에서 Vm의 상태를 확인할 수 있습니다 **복제 된 항목**합니다.

>[!NOTE]
>초기 복제 중 상태가 명백한 진행 하지 않고 새로 고침 하는 데 시간이 걸릴 수 있습니다. 클릭 **새로 고침** 최신 상태를 가져옵니다.

## <a name="update-target-vm-encryption-settings"></a>대상 VM 암호화 설정 업데이트
다음 시나리오에서는 해야 대상 VM 암호화 설정을 업데이트 합니다.
  - VM에서 Site Recovery 복제를 사용 하도록 설정 합니다. 나중에 원본 VM에서 디스크 암호화 사용 하도록 설정 합니다.
  - VM에서 Site Recovery 복제를 사용 하도록 설정 합니다. 나중에 원본 VM에서 키 암호화 키를 디스크 암호화 키 변경 했습니다.

사용할 수 있습니다 [스크립트](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) 대상 영역에 암호화 키를 복사 하 여 다음에 대상 암호화 설정을 업데이트 하 **Recovery services 자격 증명 모음** > *복제 된항목*  >  **속성** > **Compute 및 네트워크**합니다.

![ADE 설정 대화 상자 창 업데이트](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Azure 간 VM 복제 하는 동안 키 자격 증명 모음 권한 문제 해결

**원인 1:** 수 없으면 선택한 대상 지역에서 이미 만든 키 자격 증명 모음을 만들어야 하는 Site Recovery는 대신 필요한 사용 권한이 없는 합니다. 키 자격 증명 모음에는 앞에서 설명한 대로 권한이 필요 합니다.

*예*: 키 자격 증명 모음에 있는 VM을 복제 하려고 *ContososourceKeyvault* 원본 지역에 있습니다.
원본 지역 키 자격 증명 모음에 모든 권한을 해야합니다. 이지만 보호 하는 동안 이미 만든 key vault는 권한이 없는 ContosotargetKeyvault을 선택 합니다. 오류가 발생 했습니다.

**해결 방법:** 로 이동 **Home** > **Keyvaults** > **ContososourceKeyvault** > **액세스 정책** 및 적절 한 사용 권한을 추가 합니다.

**원인 2:** 수 없으면 선택한 대상 지역에서 이미 만든 키 자격 증명 모음을 없는 권한 대신 만들어야 하는 Site Recovery를 암호 해독 암호화 합니다. 했는지를 암호 해독-암호화 권한 원본 지역에서 키를 암호화 하는 경우.</br>

*예*: Key vault가 있는 VM을 복제 하려고 *ContososourceKeyvault* 원본 지역에 있습니다. 권한이 있는 모든 필요한 원본 지역 키 자격 증명 모음입니다. 이지만 보호 하는 동안 이미 만든 key vault 암호 해독 및 암호화에 대 한 권한이 없을 ContosotargetKeyvault을 선택 합니다. 오류가 발생 했습니다.</br>

**해결 방법:** 로 이동 **Home** > **Keyvaults** > **ContososourceKeyvault** > **액세스 정책**합니다. 아래의 사용 권한 추가 **키 권한** > **암호화 작업**합니다.

## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
