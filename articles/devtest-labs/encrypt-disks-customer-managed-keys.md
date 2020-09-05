---
title: Azure DevTest Labs에서 고객이 관리 하는 키를 사용 하 여 OS 디스크 암호화
description: Azure DevTest Labs에서 고객이 관리 하는 키를 사용 하 여 운영 체제 (OS) 디스크를 암호화 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 07/28/2020
ms.openlocfilehash: 241f53f0c8f289b43b8de465eb7509489345b955
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815924"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Azure DevTest Labs에서 고객 관리 키를 사용 하 여 운영 체제 (OS) 디스크 암호화
SSE(서버 쪽 암호화)는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다. SSE는 기본적으로 Azure (OS 및 데이터 디스크)의 관리 디스크에 저장 된 데이터를 클라우드로 유지할 때 자동으로 암호화 합니다. Azure의 [디스크 암호화](../virtual-machines/windows/disk-encryption.md) 에 대해 자세히 알아보세요. 

DevTest Labs 내에서 랩의 일부로 생성 된 모든 OS 디스크 및 데이터 디스크는 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 그러나 랩 소유자는 자신의 키를 사용 하 여 랩 가상 머신 OS 디스크를 암호화 하도록 선택할 수 있습니다. 사용자 고유의 키를 사용 하 여 암호화를 관리 하도록 선택 하는 경우 랩 OS 디스크의 데이터를 암호화 하는 데 사용할 **고객 관리 키** 를 지정할 수 있습니다. 고객 관리 키 및 기타 관리 되는 디스크 암호화 유형으로 SSE (서버 쪽 암호화)에 대 한 자세한 내용은 [고객 관리 키](../virtual-machines/windows/disk-encryption.md#customer-managed-keys)를 참조 하세요. 또한 [고객이 관리 하는 키 사용에 대 한 제한 사항](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions)을 참조 하세요.


> [!NOTE]
> - 고객 관리 키를 사용 하는 현재 디스크 암호화는 DevTest Labs의 OS 디스크에 대해서만 지원 됩니다. 
> 
> - 이 설정은 랩에서 새로 만든 OS 디스크에 적용 됩니다. 특정 시점에 디스크 암호화 집합을 변경 하도록 선택 하는 경우 랩의 이전 디스크는 이전 디스크 암호화 집합을 사용 하 여 계속 암호화 된 상태로 유지 됩니다. 

다음 섹션에서는 랩 소유자가 고객이 관리 하는 키를 사용 하 여 암호화를 설정할 수 있는 방법을 보여 줍니다.

## <a name="pre-requisites"></a>필수 구성 요소

1. 디스크 암호화가 설정 되지 않은 경우이 문서에 따라 [Key Vault 및 디스크 암호화](../virtual-machines/disks-enable-customer-managed-keys-portal.md)집합을 설정 합니다. 디스크 암호화 설정에 대 한 다음 요구 사항을 확인 하십시오. 

    - 디스크 암호화 집합은 **랩과 동일한 지역 및 구독에**있어야 합니다. 
    - 랩 OS 디스크를 암호화 하는 데 사용 되는 디스크 암호화 집합에 대해 최소한의 **판독기 수준 액세스** 권한이 (랩 소유자)에 게 있는지 확인 합니다. 
2. 8/1/2020 이전에 만든 labs의 경우 랩 소유자는 랩 시스템 할당 id가 사용 하도록 설정 되었는지 확인 해야 합니다. 이렇게 하려면 랩 소유자는 랩으로 이동 하 고, **구성 및 정책**을 클릭 하 고, **Id (미리 보기)** 블레이드를 클릭 하 고, 시스템 할당 id **상태** 를 **설정** 으로 변경 하 고, **저장**을 클릭 합니다. 8/1/2020 이후 생성 된 새 labs의 경우 랩의 시스템 할당 id는 기본적으로 사용 하도록 설정 됩니다. 
3. 랩에서 모든 랩 OS 디스크에 대 한 암호화를 처리 하려면 랩 소유자가 기본 Azure 구독의 가상 컴퓨터 참가자 역할 뿐만 아니라 디스크 암호화 집합에 대해 랩의 **시스템 할당 id** 판독기 역할을 명시적으로 부여 해야 합니다. 랩 소유자는 다음 단계를 완료 하 여이 작업을 수행할 수 있습니다.

   
    1. Azure 리소스에 대 한 사용자 액세스를 관리할 수 있도록 Azure 구독 수준에서 [사용자 액세스 관리자 역할](../role-based-access-control/built-in-roles.md#user-access-administrator) 의 구성원 인지 확인 합니다. 
    1. **디스크 암호화 집합** 페이지의 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택 합니다. 
    1. 도구 모음에서 **+ 추가** 를 선택 하 고 **역할 할당 추가**를 선택 합니다.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="역할 관리 추가-메뉴":::
    1. **역할 할당 추가** 페이지에서 **읽기 권한자** 역할 또는 추가 액세스를 허용 하는 역할을 선택 합니다. 
    1. 디스크 암호화 집합이 사용 될 랩 이름을 입력 하 고 드롭다운 목록에서 랩 이름 (랩에 대 한 시스템 할당 id)을 선택 합니다. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="랩의 시스템 관리 id를 선택 합니다.":::        
    1. 도구 모음에서 **저장**을 선택합니다. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="역할 할당 저장":::
3. **구독**액세스 제어 (IAM) 페이지를 사용 하 여 랩의 **시스템 할당 id** 를 **가상 머신 참가자** 역할에 추가  ->  **Access control (IAM)** 합니다. 단계는 이전 단계에서 설명한 것과 비슷합니다. 

    
    1. Azure Portal의 **구독** 페이지로 이동 합니다. 
    1. **액세스 제어(IAM)** 를 선택합니다. 
    1. 도구 모음에서 **+ 추가** 를 선택 하 고 **역할 할당 추가**를 선택 합니다. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="구독-> Access control (IAM) 페이지":::
    1. **역할 할당 추가** 페이지에서 역할에 대 한 **가상 컴퓨터 참가자** 를 선택 합니다.
    1. 랩 이름을 입력 하 고 드롭다운 목록에서 랩 **이름** (랩에 대 한 시스템 할당 id)을 선택 합니다. 
    1. 도구 모음에서 **저장**을 선택합니다. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>고객 관리 키를 사용 하 여 랩 OS 디스크 암호화 

1. Azure Portal의 랩 홈 페이지에서 왼쪽 메뉴의 **구성 및 정책** 을 선택 합니다. 
1. **구성 및 정책** 페이지의 **암호화** 섹션에서 **디스크 (미리 보기)** 를 선택 합니다. 기본적으로 **암호화 유형은** **플랫폼 관리 키를 사용 하 여 미사용 암호화**로 설정 됩니다.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="구성 및 정책 페이지의 디스크 탭":::
1. **암호화 유형**의 경우 드롭다운 목록에서 **고객 관리 키를 사용 하 여 미사용 암호화** 를 선택 합니다. 
1. **디스크 암호화 설정**에 대해 이전에 만든 디스크 암호화 집합을 선택 합니다. 시스템에 할당 된 랩에서 액세스할 수 있는 것과 동일한 디스크 암호화 집합입니다.
1. 도구 모음에서 **저장**을 선택합니다. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="고객이 관리 하는 키로 암호화 사용":::
1. 다음 텍스트를 포함 하는 메시지 상자: *이 설정은 랩에서 새로 만든 컴퓨터에 적용 됩니다. 이전 OS 디스크는 이전 디스크 암호화 집합으로 암호화 된 상태를 유지*하 고 **확인**을 선택 합니다. 

    구성 되 면 랩 OS 디스크는 디스크 암호화 집합을 사용 하 여 제공 되는 고객 관리 키를 사용 하 여 암호화 됩니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [Azure Disk Encryption](../virtual-machines/windows/disk-encryption.md). 
- [고객 관리형 키](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 
