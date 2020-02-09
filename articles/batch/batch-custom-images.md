---
title: 관리 되는 이미지에서 사용자 지정 풀 프로 비전-Azure Batch | Microsoft Docs
description: 응용 프로그램에 대한 소프트웨어 및 데이터를 사용 하 여 계산 노드를 프로 비전 하기 위해 관리 되는 이미지 리소스에서 Batch 풀을 만듭니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020151"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>관리 되는 이미지를 사용 하 여 가상 머신 풀 만들기

Batch 풀의 Vm (가상 머신)에 대한 사용자 지정 이미지를 만들려면 [공유 이미지 갤러리](batch-sig-images.md)또는 *관리 되는 이미지* 리소스를 사용할 수 있습니다.

> [!TIP]
> 대부분의 경우 공유 이미지 갤러리를 사용 하 여 사용자 지정 이미지를 만들어야 합니다. 공유 이미지 갤러리를 사용 하면 풀을 더 빠르게 프로 비전 하 고, 더 많은 수의 Vm을 확장 하 고, Vm을 프로 비전 할 때 안정성을 향상 시킬 수 있습니다. 자세히 알아보려면 [공유 이미지 갤러리를 사용 하 여 사용자 지정 풀 만들기](batch-sig-images.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

- **관리되는 이미지 리소스**. 사용자 지정 이미지를 사용하여 가상 머신 풀을 만들려면 Batch 계정과 동일한 Azure 구독 및 지역에 관리되는 이미지 리소스가 있거나 해당 리소스를 만들어야 합니다. VM OS 디스크 및 연결된 데이터 디스크(선택 사항)의 스냅샷에서 이미지를 만들어야 합니다. 자세한 내용 및 관리되는 이미지를 준비하는 단계는 다음 섹션을 참조하세요.
  - 작성하는 각 풀에 대해 고유한 사용자 지정 이미지를 사용합니다.
  - Batch API를 사용하여 이미지로 풀을 만들려면 이미지의 **리소스 ID**를 지정합니다. 리소스 ID의 형식은 다음과 같습니다. `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` 포털을 사용하려면 이미지의 **이름**을 사용합니다.  
  - 관리되는 이미지 리소스는 강화에 사용할 수 있도록 풀 수명 동안 유지되어야 하며, 풀을 삭제한 후에 제거할 수 있습니다.

- **AAD(Azure Active Directory) 인증**. Batch 클라이언트 API가 AAD 인증을 사용해야 합니다. AAD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다.

## <a name="prepare-a-custom-image"></a>사용자 지정 이미지 준비

Azure에서 관리 되는 이미지를 준비 하려면 다음을 수행 합니다.

- Azure VM의 OS 및 데이터 디스크의 스냅숏
- 관리 디스크를 사용 하는 일반화 된 Azure VM
- 클라우드로 업로드 된 일반화 된 온-프레미스 VHD

사용자 지정 이미지를 사용하여 안정적으로 Batch 풀 크기를 조정하려는 경우 VM 디스크의 스냅샷을 사용하는 *첫 번째 방법만* 사용하여 관리되는 이미지를 만드는 것이 좋습니다. VM을 준비하고 스냅샷을 생성하고 스냅샷에서 이미지를 만들려면 다음 단계를 참조하세요.

### <a name="prepare-a-vm"></a>VM 준비

이미지에 대한 새 VM을 만드는 경우 Batch에서 지원 되는 첫 번째 파티 Azure Marketplace 이미지를 관리 되는 이미지의 기본 이미지로 사용 합니다. 첫 번째 파티 이미지만 기본 이미지로 사용할 수 있습니다. Azure Batch에서 지 원하는 Azure Marketplace 이미지 참조의 전체 목록을 얻으려면 [노드 에이전트 Sku 나열](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 작업을 참조 하세요.

> [!NOTE]
> 추가 라이선스 및 구매 약관이 있는 타사 이미지는 기본 이미지로 사용할 수 없습니다. Marketplace 이미지에 대한 자세한 내용은 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) 또는 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM을 참조하세요.

- VM이 관리 디스크를 사용 하 여 만들어졌는지 확인 합니다. VM을 만들 때 기본 스토리지 설정입니다.
- 사용자 지정 스크립트 확장 등의 Azure 확장을 VM에 설치해서는 안 됩니다. 이미지에 미리 설치된 확장이포함되어 있으면 Batch 풀을 배포할 때 Azure에서 문제가 발생할 수 있습니다.
- 연결 된 데이터 디스크를 사용 하는 경우 VM 내에서 디스크를 탑재 하 고 포맷 하 여 사용 해야 합니다.
- 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하도록 해야 합니다. Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상합니다.
- VM이 실행되면 RDP(Windows용) 또는 SSH(Linux용)를 통해 연결합니다. 필요한 소프트웨어를 설치하거나 원하는 데이터를 복사합니다.  

### <a name="create-a-vm-snapshot"></a>VM 스냅샷 만들기

스냅샷은 VHD의 전체 읽기 전용 복사본입니다. VM OS 또는 데이터 디스크의 스냅샷을 만들려는 경우 Azure Portal 또는 명령줄 도구를 사용할 수 있습니다. 스냅샷을 만드는 단계와 옵션은 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 또는 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM용 지침을 참조하세요.

### <a name="create-an-image-from-one-or-more-snapshots"></a>스냅샷 하나 이상에서 이미지 만들기

스냅샷에서 관리되는 이미지를 만들려면 [az image create](/cli/azure/image) 명령과 같은 Azure 명령줄 도구를 사용합니다. OS 디스크 스냅샷을 지정하고 필요에 따라 데이터 디스크 스냅샷을 하나 이상 지정하여 이미지를 만들 수 있습니다.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>포털의 사용자 지정 이미지에서 풀 만들기

사용자 지정 이미지를 저장했으며 리소스 ID 또는 이름을 알고 있다면 해당 이미지에서 Batch 풀을 만듭니다. 다음 단계는 Azure Portal에서 풀을 만드는 방법을 보여줍니다.

> [!NOTE]
> Batch API 중 하나를 사용하여 풀을 만드는 경우 AAD 인증에 사용하는 ID가 이미지 리소스에 대한 권한을 가지고 있는지 확인합니다. [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)을 참조하세요.
>
> 관리 되는 이미지에 대한 리소스는 풀의 수명 동안 존재 해야 합니다. 기본 리소스를 삭제 하면 풀 크기를 조정할 수 없습니다.

1. Azure Portal에서 Batch 계정으로 이동합니다. 이 계정은 사용자 지정 이미지가 포함된 리소스 그룹과 동일한 구독 및 지역에 있어야 합니다.
2. 왼쪽의 **설정** 창에서 **풀** 메뉴 항목을 선택합니다.
3. **풀** 창에서 **추가** 명령을 선택합니다.
4. **풀 추가** 창의 **이미지 형식** 드롭다운에서 **사용자 지정 이미지(Linux/Windows)** 를 선택합니다. **사용자 지정 VM 이미지** 드롭다운에서 이미지 이름(리소스 ID의 약식)을 선택합니다.
5. 사용자 지정 이미지에 대해 올바른 **게시자/제품/Sku**를 선택합니다.
6. **노드 크기**, **대상 전용 노드**및 **우선 순위가 낮은 노드**를 포함 하 여 나머지 필수 설정을 지정 하 고 원하는 선택적 설정도 지정 합니다.

    예를 들어 Microsoft Windows Server Datacenter 2016 사용자 지정 이미지에 대해서는 아래 표시된 것처럼 **풀 추가** 창이 나타납니다.

    ![사용자 지정 Windows 이미지에서 풀 추가](media/batch-custom-images/add-pool-custom-image.png)
  
기존 풀이 사용자 지정 이미지를 기반으로 하는지 확인하려면 **풀** 창의 리소스 요약 섹션에 있는 **운영 체제** 속성을 참조하세요. 사용자 지정 이미지에서 풀을 만든 경우 **사용자 지정 VM 이미지**로 설정합니다.

풀과 연결된 모든 사용자 지정 이미지가 해당 풀의 **속성** 창에 표시됩니다.

## <a name="considerations-for-large-pools"></a>대형 풀 관련 고려 사항

사용자 지정 이미지를 사용하여 VM이 수백 개인 풀을 만들려는 경우에는 위의 지침에 따라 VM 스냅샷에서 만든 이미지를 사용해야 합니다.

다음 사항에 유의 해야 합니다.

- **크기 제한** - 사용자 지정 이미지 사용 시에는 Batch의 풀 크기가 전용 컴퓨팅 노드 2,500개 또는 낮은 우선 순위 노드 1,000개로 제한됩니다.

  같은 이미지 또는 같은 기본 스냅샷을 기반으로 하는 여러 이미지를 사용해 여러 풀을 만드는 경우에는 풀의 총 컴퓨팅 노드 수가 위의 제한을 초과할 수 없습니다. 하나의 이미지나 해당 기본 스냅샷을 둘 이상의 풀에 사용하지 않는 것이 좋습니다.

  [인바운드 NAT 풀](pool-endpoint-configuration.md)을 사용하여 풀을 구성하는 경우에는 제한을 낮출 수 있습니다.

- **크기 조정 시간 제한** - 풀에 고정된 수의 노드가 포함되는 경우(풀 크기가 자동으로 조정되지 않음) 풀의 resizeTimeout 속성을 20~30분 등의 값으로 늘립니다. 풀이 제한 시간 내에 대상 크기에 도달하지 않으면 다른 [크기 조정 작업](/rest/api/batchservice/pool/resize)을 수행합니다.

  풀에 컴퓨팅 노드를 300개보다 많이 포함하려는 경우에는 풀이 대상 크기에 도달하도록 크기를 여러 번 조정해야 할 수 있습니다.
  
[공유 이미지 갤러리](batch-sig-images.md)를 사용 하 여 더 많은 공유 이미지 복제본과 함께 사용자 지정 된 이미지를 사용 하 여 더 큰 풀을 만들 수 있습니다. 공유 이미지를 사용 하는 경우 풀이 안정 된 상태에 도달 하는 데 걸리는 시간은 최대 25% 더 빠르며 VM 유휴 대기 시간은 최대 30% 더 짧습니다.

## <a name="considerations-for-using-packer"></a>패키지 사용에 대한 고려 사항

패키지를 직접 사용 하 여 관리 되는 이미지 리소스를 만드는 작업은 사용자 구독 모드 Batch 계정 으로만 수행할 수 있습니다. Batch 서비스 모드 계정의 경우 먼저 VHD를 만든 다음, VHD를 관리 되는 이미지 리소스로 가져와야 합니다. 풀 할당 모드 (사용자 구독 또는 Batch 서비스)에 따라 관리 되는 이미지 리소스를 만드는 단계는 다양 합니다.

사용자 지정 이미지를 참조 하는 모든 풀의 수명 동안 관리 되는 이미지를 만드는 데 사용 되는 리소스가 있는지 확인 합니다. 이렇게 하지 않으면 풀 할당 오류 및/또는 크기 조정 오류가 발생할 수 있습니다.

이미지 또는 기본 리소스가 제거 되 면 다음과 유사한 오류 메시지가 표시 될 수 있습니다. `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. 이 오류가 발생 하는 경우 기본 리소스가 제거 되지 않았는지 확인 합니다.

패키지를 사용 하 여 VM을 만드는 방법에 대한 자세한 내용은 패키지를 사용 하 여 [Linux 이미지 빌드](../virtual-machines/linux/build-image-with-packer.md) 또는 패키지를 [사용 하 여 Windows 이미지 빌드](../virtual-machines/windows/build-image-with-packer.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 컴퓨팅 솔루션 개발](batch-api-basics.md)을 참조하세요.
