---
title: Azure Resource Mover를 사용하여 지역 간에 암호화된 Azure VM 이동
description: Azure Resource Mover를 사용하여 다른 지역으로 암호화된 Azure VM을 이동하는 방법을 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361012"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>자습서: 지역 간에 암호화된 Azure VM 이동

이 문서에서는 [Azure Resource Mover](overview.md)를 사용하여 다른 Azure 지역으로 암호화된 Azure VM을 이동하는 방법을 알아봅니다. 암호화의 의미는 다음과 같습니다.

- 디스크에 Azure 디스크 암호화를 사용하도록 설정된 VM. [자세한 정보](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- 또는 저장소 데이터 암호화(서버 쪽 암호화)에 CMK(고객 관리형 키)를 사용하는 VM. [자세한 정보](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 필수 구성 요소 확인 
> * Azure 디스크 암호화를 사용하는 VM의 경우 원본 지역 키 자격 증명 모음에서 대상 지역 키 자격 증명 모음으로 키와 비밀 복사
> * 이동할 VM을 준비하고, 이동하려는 원본 지역에서 리소스 선택
> * 리소스 종속성 확인
> * Azure 디스크 암호화를 사용하는 VM의 경우 대상 키 자격 증명 모음을 수동으로 할당 고객 관리형 키를 통해 서버 쪽 암호화를 사용하는 VM의 경우 대상 지역에 설정된 디스크 암호화를 수동으로 할당
> * 키 자격 증명 모음 및/또는 디스크 암호화 세트를 이동
> * 원본 리소스 그룹 준비 및 이동 
> * 다른 리소스 준비 및 이동
> * 이동을 취소할지 아니면 커밋할지 결정 
> * 이동 후 필요에 따라 원본 지역의 리소스 제거

> [!NOTE]
> 이 자습서에서는 시나리오를 가장 빠르게 시도할 수 있는 경로를 보여주며, 기본 옵션을 사용합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다. 그런 다음 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="prerequisites"></a>필수 구성 요소

**요구 사항** |**세부 정보**
--- | ---
**구독 권한** | 이동하려는 리소스가 포함된 구독에 대한 *소유자* 액세스 권한이 있는지 확인합니다.<br/><br/> **소유자 액세스 권한이 필요한 이유는 무엇인가요?** Azure 구독에서 특정 원본 및 대상 쌍에 대한 리소스를 처음 추가하는 경우 Resource Mover를 통해 구독에서 신뢰할 수 있는 [시스템이 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)(이전에는 MSI(관리되는 서비스 ID)라고 함)를 만듭니다. ID를 만들고 필요한 역할(원본 구독의 기여자 및 사용자 액세스 관리자)을 할당하려면 리소스를 추가하는 데 사용하는 계정에 구독의 *소유자* 권한이 있어야 합니다. Azure 역할에 대해 [자세히 알아보세요](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).
**VM 지원** | 이동하려는 VM이 지원되는지 확인합니다.<br/><br/> - 지원되는 Windows VM을 [확인](support-matrix-move-region-azure-vm.md#windows-vm-support)합니다.<br/><br/> - 지원되는 Linux VM 및 커널 버전을 [확인](support-matrix-move-region-azure-vm.md#linux-vm-support)합니다.<br/><br/> - 지원되는 [컴퓨팅](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [스토리지](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) 및 [네트워킹](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) 설정을 확인합니다.
**키 자격 증명 모음 요구 사항(Azure 디스크 암호화)** | VM에 Azure 디스크 암호화를 사용하도록 설정한 경우 원본 지역의 키 자격 증명 모음 외에도 대상 지역에 키 자격 증명 모음이 필요합니다. [키 자격 증명 모음을 만듭니다](../key-vault/general/quick-create-portal.md).<br/><br/> 원본 지역과 대상 지역의 키 자격 증명 모음에 대해 다음 권한이 필요합니다.<br/><br/> - 키 권한: 키 관리 작업(가져오기, 나열), 암호화 작업(암호 해독 및 암호화)<br/><br/> - 비밀 권한: 비밀 관리 작업(가져오기, 나열, 설정)<br/><br/> - 인증서(나열, 가져오기)
**디스크 암호화 설정(CMK를 사용하는 서버 쪽 암호화)** | CMK를 사용하는 서버 쪽 암호화를 VM에 사용하는 경우 원본 지역의 디스크 암호화 세트 외에도 대상 지역에 디스크 암호화 세트가 필요합니다. [디스크 암호화 세트를 만듭니다](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> 고객 관리형 키에 HSM 키를 사용하는 경우에는 지역 간 이동이 지원되지 않습니다.
**대상 지역 할당량** | 구독의 할당량이 대상 지역에서 이동하려는 리소스를 만들기에 충분해야 합니다. 할당량이 없는 경우 [추가 제한](../azure-resource-manager/management/azure-subscription-service-limits.md)을 요청하세요.
**대상 지역 요금** | 리소스를 이동하는 대상 지역과 관련된 가격 책정 및 요금을 확인하세요. [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하면 도움이 됩니다.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>ADE(Azure Disk Encryption)를 사용하여 VMS용 키 자격 증명 모음에 대한 사용자 권한 확인

Azure 디스크 암호화가 활성화된 VM을 이동하는 경우 스크립트를 실행하는 사용자에게 적절한 사용 권한이 있어야 하는 [아래](#copy-the-keys-to-the-destination-key-vault)에 언급된 스크립트를 실행해야 합니다. 필요한 사용 권한에 대해 알아보려면 아래 표를 참조하세요. Azure Portal의 키 자격 증명 모음으로 이동하여 **설정** 아래에서 **액세스 정책** 을 선택하여 사용 권한을 변경하는 옵션을 찾을 수 있습니다.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="키 자격 증명 모음 액세스 정책을 여는 단추" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

사용자 권한이 없는 경우 **액세스 정책 추가** 를 선택하고 권한을 지정합니다. 사용자 계정에 이미 정책이 있는 경우 **사용자** 에서 아래 표에 따라 권한을 설정합니다.

ADE를 사용하는 Azure VM은 다음과 같이 변동될 수 있으며 관련 구성 요소에 따라 사용 권한을 적절하게 설정해야 합니다.
- 디스크가 비밀만 사용하여 암호화되는 기본 옵션
- [키 암호화 키](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)를 사용하여 보안 추가

### <a name="source-region-keyvault"></a>원본 지역 키 자격 증명 모음

스크립트를 실행하는 사용자에 대해 아래 권한을 설정해야 합니다. 

**구성 요소** | **권한이 필요함**
--- | ---
비밀|  권한 가져오기 <br> </br> **비밀 권한**>  **비밀 관리 작업** 에서 **가져오기** 를 선택합니다. 
구성 <br> </br> KEK(키 암호화 키)를 사용하는 경우 비밀 외에도 이 권한이 필요합니다.| 권한 가져오기 및 암호 해독 <br> </br> **키 권한** > **키 관리 작업** 에서 **가져오기** 를 선택합니다. **암호화 작업** 에서 **암호 해독** 을 선택합니다.

### <a name="destination-region-keyvault"></a>대상 지역 키 자격 증명 모음

**액세스 정책** 에서 **볼륨 암호화를 위한 Azure Disk Encryption** 을 사용하도록 설정합니다. 

스크립트를 실행하는 사용자에 대해 아래 권한을 설정해야 합니다. 

**구성 요소** | **권한이 필요함**
--- | ---
비밀|  권한 설정 <br> </br> **비밀 권한**>  **비밀 관리 작업** 에서 **설정** 을 선택합니다. 
구성 <br> </br> KEK(키 암호화 키)를 사용하는 경우 비밀 외에도 이 권한이 필요합니다.| 권한 가져오기, 만들기, 암호화 <br> </br> **키 권한** > **키 관리 작업** 에서 **가져오기** 및 **만들기** 를 선택합니다. **암호화 작업** 에서 **암호화** 를 선택합니다.

위의 권한 외에도 대상 키 자격 증명 모음에서 Resource Mover가 사용자 대신 Azure 리소스에 액세스하는 데 사용하는 [Managed System ID](./common-questions.md#how-is-managed-identity-used-in-resource-mover)에 대한 권한을 추가해야 합니다. 

1. **설정** 에서 **액세스 정책 추가** 를 선택합니다. 
2. **보안 주체 선택** 에서 MSI를 검색합니다. MSI 이름은 ```movecollection-<sourceregion>-<target-region>-<metadata-region>```입니다. 
3. MSI에 대한 아래 권한 추가

**구성 요소** | **권한이 필요함**
--- | ---
비밀|  권한 가져오기 및 나열 <br> </br> **비밀 권한**>  **비밀 관리 작업** 에서 **가져오기** 및 **나열** 을 선택합니다. 
구성 <br> </br> KEK(키 암호화 키)를 사용하는 경우 비밀 외에도 이 권한이 필요합니다.| Get, List 권한 <br> </br> **키 권한** > **키 관리 작업** 에서 **가져오기** 및 **나열** 을 선택합니다.



### <a name="copy-the-keys-to-the-destination-key-vault"></a>대상 키 자격 증명 모음에 키 복사

제공되는 스크립트를 사용하여 원본 키 자격 증명 모음에서 대상 키 자격 증명 모음으로 암호화 비밀 및 키를 복사해야 합니다.

- PowerShell에서 스크립트를 실행합니다. 최신 PowerShell 버전을 실행하는 것이 좋습니다.
- 특히 이 스크립트는 다음과 같은 모듈이 필요합니다.
    - Az.Compute
    - Az.KeyVault(버전 3.0.0)
    - Az.Accounts(버전 2.2.3)

다음과 같이 실행합니다.

1. GitHub에서 [스크립트](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1)로 이동합니다.
2. 스크립트의 내용을 로컬 파일에 복사하고, 파일 이름을 *Copy-keys.ps1* 로 지정합니다.
3. 스크립트를 실행합니다.
4. Azure에 로그인합니다.
5. **사용자 입력** 팝업에서 원본 구독, 리소스 그룹 및 원본 VM을 선택합니다. 그런 다음, 대상 위치를 선택하고 디스크 및 키 암호화에 사용할 대상 자격 증명 모음을 선택합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="스크립트 값을 입력하는 팝업" :::


6. 스크립트가 완료되면 CopyKeys가 성공했다는 출력이 화면에 표시됩니다.

## <a name="prepare-vms"></a>VM 준비

1. [VM이 요구 사항을 충족하는지 확인](#prerequisites)한 후에는 이동하려는 VM이 켜져 있는지 확인합니다. 대상 지역에서 사용하려는 모든 VM 디스크를 VM에서 연결하고 초기화해야 합니다.
3. VM에 신뢰할 수 있는 최신 루트 인증서와 업데이트된 CRL(인증서 해지 목록)이 있는지 확인합니다. 가상 하드 디스크 파일에 대한 중요 정보를 제공하려면
    - Windows VM에서 최신 Windows 업데이트를 설치합니다.
    - Linux VM에서 배포자 지침에 따라 머신에 최신 인증서와 CRL을 준비합니다. 
4. 다음과 같이 VM에서 아웃바운드 연결을 허용합니다.
    - [URL](support-matrix-move-region-azure-vm.md#url-access) 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음 URL에 대한 액세스를 허용합니다.
    - NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 [서비스 태그 규칙](support-matrix-move-region-azure-vm.md#nsg-rules)을 만듭니다.

## <a name="select-resources-to-move"></a>이동할 리소스 선택


- 선택한 원본 지역의 모든 리소스 그룹에서 지원되는 리소스 종류를 선택할 수 있습니다.  
- 리소스를 원본 지역과 동일한 구독의 대상 지역으로 이동합니다. 구독을 변경하려면 리소스를 이동한 후에 변경할 수 있습니다.

다음과 같이 리소스를 선택합니다.

1. Azure Portal에서 *resource mover* 를 검색합니다. 그런 다음, **서비스** 아래에서 **Azure Resource Mover** 를 선택합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Azure Portal에서 resource mover를 검색한 결과" :::

2. **개요** 에서 **지역 간 이동** 을 클릭합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="다른 지역으로 이동할 리소스를 추가하는 단추" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. **리소스 이동** > **원본 + 대상** 에서 원본 구독과 지역을 선택합니다.
4. **대상** 에서 VM을 이동하려는 지역을 선택합니다. 그런 다음 **다음** 을 클릭합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="원본 및 대상 지역을 선택하는 페이지" :::

5. **이동할 리소스** 에서 **리소스 선택** 을 클릭합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="이동할 리소스를 선택하는 단추" :::

6. **리소스 선택** 에서 VM을 선택합니다. [이동을 지원하는](#prepare-vms) 리소스만 추가할 수 있습니다. **완료** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="이동할 VM을 선택하는 페이지" :::

    > [!NOTE]
    >  이 자습서에서는 고객 관리형 키를 통해 서버 쪽 암호화를 사용하는 VM(rayne-vm)과 디스크 암호화를 사용하는 VM(rayne-vm-ade)을 선택합니다.

7.  **이동할 리소스** 에서 **다음** 을 클릭합니다.
8. **검토** 에서 원본 및 대상 설정을 확인합니다. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="설정을 검토하고 이동을 계속하는 페이지" :::

9. **계속** 을 클릭하여 리소스 추가를 시작합니다.
10. 알림 아이콘을 선택하여 진행 상황을 추적합니다. 추가 프로세스가 성공적으로 완료되면 알림에서 **이동할 리소스가 추가됨** 을 선택합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="리소스가 성공적으로 추가되었음을 확인하는 알림" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. 알림을 클릭한 후 **지역 간** 페이지에서 리소스를 검토합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="추가된 리소스가 준비 보류 중인 것을 보여주는 페이지" :::

> [!NOTE]
> - 추가하는 리소스는 *준비 보류 중* 상태가 됩니다.
> - VM에 대한 원본 그룹이 자동으로 추가됩니다.
> - 대상 지역에 이미 있는 리소스를 사용하도록 **대상 구성** 항목을 수정하면 이동을 시작할 필요가 없기 때문에 리소스 상태가 *커밋 보류 중* 으로 설정됩니다.
> - 추가된 리소스를 제거하려는 경우 이동 프로세스의 진행 상황에 따라 제거 방법이 달라집니다. [자세히 알아보기](remove-move-resources.md).


## <a name="resolve-dependencies"></a>종속성 오류 해결

1. 리소스의 **문제** 열에 *종속성 유효성 검사* 메시지가 표시되는 경우 **종속성 유효성 검사** 단추를 선택합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="종속성을 확인하는 단추" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    유효성 검사 프로세스가 시작됩니다.
2. 종속성이 발견되면 **종속성 추가** 를 클릭합니다.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="종속성을 추가하는 단추" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. **종속성 추가** 에서 기본 옵션인 **모든 종속성 표시** 를 그대로 둡니다.

    - **모든 종속성 표시** 는 리소스에 대한 모든 직접 및 간접 종속성을 반복합니다. 예를 들어 VM의 경우 NIC, 가상 네트워크, NSG(네트워크 보안 그룹) 등을 표시합니다.
    - **첫 번째 수준 종속성 표시** 는 직접 종속성만 표시합니다. 예를 들어 VM의 경우 NIC는 표시되지만 가상 네트워크는 표시되지 않습니다.
 
4. **종속성 추가** 에서 추가할 종속 리소스를 선택합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="종속성 목록에서 종속성 선택" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. 종속성 유효성 검사를 다시 수행합니다. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="유효성 검사를 다시 수행하는 페이지" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>대상 리소스 할당

암호화에 연결된 대상 리소스에는 수동 할당이 필요합니다.

- ADE(Azure 디스크 암호화)가 설정된 VM을 이동하려는 경우 대상 지역의 키 자격 증명 모음이 종속성으로 표시됩니다.
- CMK(고객 관리형 키)를 사용하는 서버 쪽 암호화가 설정된 VM을 이동하려는 경우 대상 지역의 디스크 암호화 세트가 종속성으로 표시됩니다. 
- 이 자습서에서는 ADE가 설정된 VM과 CMK를 사용하는 VM을 이동하므로 대상 키 자격 증명 모음과 디스크 암호화 세트가 모두 종속성으로 표시됩니다.

다음과 같이 수동으로 할당합니다.

1. 디스크 암호화 세트 항목의 **대상 구성** 열에서 **할당되지 않은 리소스** 를 선택합니다.
2. **구성 설정** 에서 대상 디스크 암호화 세트를 선택합니다. **변경 내용 저장** 을 선택합니다.
3. 수정하려는 리소스의 종속성을 저장하고 유효성을 검사할 수도 있고, 변경 내용을 그냥 저장한 후 수정하는 모든 항목의 유효성을 한 번에 검사할 수도 있습니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="대상 지역의 디스크 암호화 세트를 선택하는 페이지" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    대상 리소스를 추가한 후에는 디스크 암호화 세트의 상태가 *이동 커밋 보류 중* 으로 바뀝니다.
3. 키 자격 증명 모음 항목의 **대상 구성** 열에서 **할당되지 않은 리소스** 를 선택합니다. **구성 설정** 에서 대상 키 자격 증명 모음을 선택합니다. 변경 내용을 저장합니다. 

이 단계에서는 디스크 암호화 세트와 키 자격 증명 모음의 상태가 모두 *이동 커밋 보류 중* 으로 바뀝니다.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="준비하려는 다른 리소스를 선택하는 페이지" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

암호화 리소스의 이동 프로세스를 커밋하고 마칩니다.

1. **지역 간** 에서 리소스(디스크 암호화 세트 또는 키 자격 증명 모음) > **이동 커밋** 을 선택합니다.
2. **리소스 이동** 에서 **커밋** 을 클릭합니다.

> [!NOTE]
> 이동을 커밋한 후에는 리소스 상태가 *원본 삭제 보류 중* 으로 바뀝니다.


## <a name="move-the-source-resource-group"></a>원본 리소스 그룹 이동 

VM을 준비하고 이동하려면 대상 지역에 VM 리소스 그룹이 있어야 합니다. 

### <a name="prepare-to-move-the-source-resource-group"></a>원본 리소스 그룹 이동 준비

준비 과정에서 Resource Mover는 리소스 그룹 설정을 사용하여 ARM(Azure Resource Manager) 템플릿을 생성합니다. 리소스 그룹 내의 리소스는 영향을 받지 않습니다.

다음과 같이 준비합니다.

1. **지역 간** 에서 원본 리소스 그룹 > **준비** 를 차례로 선택합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="리소스 그룹 준비" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. **리소스 준비** 에서 **준비** 를 클릭합니다.

> [!NOTE]
> 리소스 그룹 준비가 끝나면 리소스 그룹이 *이동 시작 보류 중* 상태입니다. 

 
### <a name="move-the-source-resource-group"></a>원본 리소스 그룹 이동

다음과 같이 이동을 시작합니다.

1. **지역 간** 에서 리소스 그룹 > **이동 시작** 을 차례로 선택합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="이동을 시작하는 단추" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. **리소스 이동** 에서 **이동 시작** 을 클릭합니다. 리소스 그룹의 상태가 *이동 시작 진행 중* 으로 변경됩니다.   
3. 이동을 시작한 후, 생성된 ARM 템플릿에 따라 대상 리소스 그룹이 만들어집니다. 원본 리소스 그룹의 상태가 *이동 커밋 보류 중* 으로 변경됩니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="이동 커밋 보류 중 상태를 검토합니다." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

이동 프로세스를 커밋하고 완료하려면 다음을 수행합니다.

1. **지역 간** 에서 리소스 그룹 > **이동 커밋** 을 차례로 선택합니다.
2. **리소스 이동** 에서 **커밋** 을 클릭합니다.

> [!NOTE]
> 이동을 커밋한 후, 원본 리소스 그룹의 상태는 *원본 삭제 보류 중* 입니다.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="이동 삭제 보류 중 상태를 검토합니다." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>이동할 리소스 준비

암호화 리소스와 원본 리소스 그룹을 이동했으므로, 이제 *준비 보류 중* 상태인 다른 리소스를 이동하도록 준비할 수 있습니다.


1. **지역 간** 에서 유효성을 다시 검사하고 문제를 해결합니다.
2. 이동을 시작하기 전에 대상 설정을 편집하려면 리소스의 **대상 구성** 열에서 링크를 선택하고 설정을 편집합니다. 대상 VM 설정을 편집할 때 대상 VM이 원본 VM보다 작으면 안 됩니다.
3. 이동하려는 *준비 보류 중* 상태인 리소스에 대해 **준비** 를 선택합니다.
3. **리소스 준비** 에서 **준비** 를 선택합니다.

    - 준비 과정에서 VM을 복제하기 위해 Azure Site Recovery Mobility 에이전트가 VM에 설치됩니다.
    - VM 데이터가 주기적으로 대상 지역에 복제됩니다. 원본 VM에는 영향을 주지 않습니다.
    - 리소스를 이동하면 다른 원본 리소스에 대한 ARM 템플릿이 생성됩니다.

리소스 준비가 완료되면 리소스는 *이동 시작 보류 중* 상태가 됩니다.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="이동 시작 보류 중 상태인 리소스를 보여주는 페이지" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>이동 시작

리소스가 준비되었으면 이동을 시작할 수 있습니다. 

1. **지역 간** 에서 *이동 시작 보류 중* 상태인 리소스를 선택합니다. 그런 다음, **이동 시작** 을 클릭합니다.
2. **리소스 이동** 에서 **이동 시작** 을 클릭합니다.
3. 알림 표시줄에서 이동 진행률을 추적합니다.

    - VM의 경우 대상 지역에 복제본 VM이 만들어집니다. 원본 VM이 종료되고, 약간의 가동 중지 시간(일반적으로 분 단위)이 발생합니다.
    - Resource Mover가 준비된 ARM 템플릿을 사용하여 다른 리소스를 다시 만듭니다. 일반적으로 가동 중지 시간이 없습니다.
    - 리소스 이동이 완료되면 리소스는 *이동 커밋 보류 중* 상태가 됩니다.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="이동 커밋 보류 중 상태인 리소스를 보여주는 페이지" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>취소 또는 커밋

처음 이동한 후에는 이동을 커밋할지 아니면 취소할지 결정할 수 있습니다. 

- **취소**: 테스트하는 중에 원본 리소스를 실제로 이동하지 않으려는 경우 이동을 취소할 수 있습니다. 이동을 취소하면 리소스가 *이동 시작 보류 중* 상태로 돌아갑니다.
- **커밋**: 커밋은 대상 지역으로의 이동을 완료합니다. 커밋 후 원본 리소스는 *원본 삭제 보류 중* 상태가 되며 삭제 여부를 결정할 수 있습니다.


## <a name="discard-the-move"></a>이동 취소 

다음과 같이 이동을 취소할 수 있습니다.

1. **지역 간** 에서 *이동 커밋 보류 중* 상태인 리소스를 선택하고, **이동 취소** 를 클릭합니다.
2. **이동 취소** 에서 **취소** 를 클릭합니다.
3. 알림 표시줄에서 이동 진행률을 추적합니다.


> [!NOTE]
> 리소스 이동을 취소하면 VM이 *이동 시작 보류 중* 상태가 됩니다.

## <a name="commit-the-move"></a>이동 커밋

이동 프로세스를 완료하려면 이동을 커밋합니다. 

1. **지역 간** 에서 *이동 커밋 보류 중* 상태인 리소스를 선택하고, **이동 커밋** 을 클릭합니다.
2. **리소스 커밋** 에서 **커밋** 을 클릭합니다.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="리소스를 커밋하여 이동을 완료하는 페이지" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. 알림 표시줄에서 커밋 진행률을 추적합니다.

> [!NOTE]
> - 이동을 커밋하면 VM이 복제되지 않습니다. 원본 VM은 커밋의 영향을 받지 않습니다.
> - 커밋은 원본 네트워킹 리소스에 영향을 주지 않습니다.
> - 이동을 커밋한 후, 리소스 상태는 *원본 삭제 보류 중* 입니다.



## <a name="configure-settings-after-the-move"></a>이동 후 설정 구성

- Mobility Service는 VM에서 자동으로 제거되지 않습니다. 수동으로 제거하거나, 서버를 다시 이동할 생각이면 그대로 두세요.
- 이동 후 Azure RBAC(Azure 역할 기반 액세스 제어) 규칙을 수정합니다.

## <a name="delete-source-resources-after-commit"></a>커밋 후 원본 리소스 삭제

이동 후 필요에 따라 원본 지역에서 리소스를 삭제할 수 있습니다. 

1. **지역 간** 에서 삭제하려는 각 원본 리소스를 선택합니다. **원본 삭제** 를 선택합니다.
2. **원본 삭제** 에서 삭제할 항목을 검토하고, **삭제 확인** 에서 **예** 를 입력합니다. 이 작업은 취소할 수 없으므로 신중하게 확인하세요.
3. **예** 를 입력한 후에는 **원본 삭제** 를 선택합니다.

> [!NOTE]
>  리소스 이동 포털에서는 리소스 그룹, 키 자격 증명 모음 또는 SQL Server 서버를 삭제할 수 없습니다. 각 리소스의 속성 페이지에서 개별적으로 삭제해야 합니다.


## <a name="delete-additional-resources-created-for-move"></a>이동을 위해 만든 추가 리소스 삭제

이동 후에는 이동 컬렉션 및 생성된 Site Recovery 리소스를 수동으로 삭제할 수 있습니다.

- 이동 컬렉션은 기본적으로 숨겨져 있습니다. 이동 컬렉션을 표시하려면 숨겨진 리소스를 표시해야 합니다.
- 캐시 스토리지를 삭제하려면 먼저 잠금을 해제해야 합니다.

삭제하는 방법은 다음과 같습니다. 
1. 리소스 그룹 ```RegionMoveRG-<sourceregion>-<target-region>```의 리소스를 찾습니다.
2. 원본 지역의 모든 VM 및 기타 원본 리소스가 이동 또는 삭제되었는지 확인합니다. 이러한 리소스를 사용하는 보류 중인 리소스가 남아 있지 않도록 확인하기 위한 절차입니다.
2. 다음과 같이 리소스를 삭제합니다.

    - 이동 컬렉션 이름은 ```movecollection-<sourceregion>-<target-region>```입니다.
    - 캐시 스토리지 계정 이름은 ```resmovecache<guid>```입니다.
    - 자격 증명 모음 이름은 ```ResourceMove-<sourceregion>-<target-region>-GUID```입니다.
## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 암호화된 Azure VM 및 해당 종속 리소스를 다른 Azure 지역으로 이동했습니다.


이번에는 Azure SQL 데이터베이스 및 탄력적 풀을 다른 지역으로 이동해 보세요.

> [!div class="nextstepaction"]
> [Azure SQL 리소스 이동](./tutorial-move-region-sql.md)
