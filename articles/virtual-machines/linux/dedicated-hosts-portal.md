---
title: Azure Portal를 사용 하 여 Azure 전용 호스트 배포
description: Azure Portal를 사용 하 여 전용 호스트에 Vm을 배포 합니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: a6191b0959345e6e69c16890d755407af289ef03
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047772"
---
# <a name="deploy-linux-vms-to-dedicated-hosts-using-the-portal"></a>포털을 사용 하 여 전용 호스트에 Linux Vm 배포 

이 문서에서는 VM(가상 머신)을 호스팅하는 Azure [전용 호스트](dedicated-hosts.md)를 만드는 방법을 안내합니다. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>VM 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
1. Azure Marketplace 리소스 목록 위에 있는 검색 상자에서 Canonical의 **Ubuntu Server 16.04 LTS**를 검색하고 선택한 다음, **만들기**를 선택합니다.
1. **기본** 정보 탭의 **프로젝트 세부 정보**에서 올바른 구독을 선택 했는지 확인 한 다음 **리소스 그룹**으로 *myDedicatedHostsRG* 을 선택 합니다. 
1. **인스턴스 세부 정보** 아래에서 **가상 머신 이름**에 *myVM*을 입력하고 **위치**에 *미국 동부*를 선택합니다.
1. **가용성 옵션** 에서 **가용성 영역**을 선택 하 고 드롭다운에서 *1* 을 선택 합니다.
1. 크기에 대해 **크기 변경**을 선택 합니다. 사용 가능한 크기 목록에서 **표준 E2s v3**과 같은 Esv3 시리즈 중 하나를 선택 합니다. 사용 가능한 모든 크기를 보려면 필터를 지워야 할 수 있습니다.
1. **관리자 계정** 아래에서 **SSH 공개 키**를 선택하고, 사용자 이름을 입력한 다음, 공개 키를 텍스트 상자에 붙여넣습니다. 공개 키에서 선행 또는 후행 공백을 모두 제거합니다.

    ![관리자 계정](./media/quick-create-portal/administrator-account.png)

1. **인바운드 포트 규칙**  >  **공용 인바운드 포트**에서 **선택한 포트 허용** 을 선택한 다음 드롭다운에서 **SSH (22)** 를 선택 합니다. 
1. 페이지 위쪽에서 **고급** 탭을 선택 하 고 **호스트** 섹션에서 호스트에 대해 *myHostGroup* **를 선택 하 고 호스트** 에 대해 *myhost* 를 선택 **합니다.** 
    ![호스트 그룹 및 호스트 선택](./media/dedicated-hosts-portal/advanced.png)
1. 나머지 기본값을 그대로 둔 다음, 페이지의 아래에서 **검토 + 만들기** 단추를 선택합니다.
1. 유효성 검사에 통과 되었다는 메시지가 표시 되 면 **만들기**를 선택 합니다.

VM 배포에는 몇 분 정도 걸립니다.

## <a name="add-an-existing-vm"></a>기존 VM 추가 

종료 된 VM을 전용 호스트에 추가할 수 있지만 VM은 먼저 Stop\Deallocated. VM을 전용 호스트로 이동 하기 전에 VM 구성이 지원 되는지 확인 합니다.

- VM 크기는 전용 호스트와 동일한 크기의 패밀리에 있어야 합니다. 예를 들어 전용 호스트가 DSv3 인 경우 VM 크기를 Standard_D4s_v3 수 있지만 Standard_A4_v2 수는 없습니다. 
- VM은 전용 호스트와 동일한 지역에 있어야 합니다.
- VM은 근접 배치 그룹에 포함 될 수 없습니다. 전용 호스트로 이동 하기 전에 근접 배치 그룹에서 VM을 제거 합니다. 자세한 내용은 [근접 배치 그룹에서 VM 이동](../windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group) 을 참조 하세요.
- VM은 가용성 집합에 있을 수 없습니다.
- VM이 가용성 영역에 있는 경우 호스트 그룹과 동일한 가용성 영역 이어야 합니다. VM 및 호스트 그룹의 가용성 영역 설정이 일치 해야 합니다.

[포털](https://portal.azure.com)을 사용 하 여 VM을 전용 호스트로 이동 합니다.

1. VM에 대 한 페이지를 엽니다.
1. 중지를 선택 하 여 VM을 **중지** 합니다.
1. 왼쪽 메뉴에서 **구성** 을 선택 합니다.
1. 드롭다운 메뉴에서 호스트 그룹 및 호스트를 선택 합니다.
1. 완료 되 면 페이지 위쪽에서 **저장** 을 선택 합니다.
1. VM이 호스트에 추가 된 후 왼쪽 메뉴에서 **개요** 를 선택 합니다.
1. 페이지 위쪽에서 **시작** 을 선택 하 여 VM을 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- 지역의 복원력을 극대화하기 위해 영역 및 장애 도메인을 모두 사용하는 샘플 템플릿을 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에서 확인할 수 있습니다.

- [Azure CLI](dedicated-hosts-cli.md)를 사용 하 여 전용 호스트를 배포할 수도 있습니다.
