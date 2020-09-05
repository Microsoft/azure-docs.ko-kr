---
title: Azure Portal - Private Links를 사용하여 관리 디스크에 대한 가져오기/내보내기 액세스 제한
description: 현재 미리 보기로 제공되는 Azure Portal을 사용하여 관리 디스크에 대한 Private Links를 사용하도록 설정합니다. 가상 네트워크 내에서 디스크를 안전하게 내보내고 가져올 수 있습니다.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab861b3ed265da9060e2367bdfdeeeee7047c584
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815864"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure Portal을 사용하여 Private Links로 관리 디스크에 대한 가져오기/내보내기 액세스를 제한합니다.

관리 디스크에 대한 Private Links 지원은 현재 미리 보기로 제공되며 Azure 가상 네트워크 내에서만 발생하도록 관리 디스크의 내보내기 및 가져오기를 제한할 수 있습니다. 지역 확장을 위해 데이터를 다른 지역으로 내보내고 재해 복구를 수행하고 포렌식 분석을 위해 데이터를 읽을 수 있도록 연결되지 않은 관리 디스크 및 스냅샷에 대한 시간 제한 SAS(공유 액세스 서명) URI를 생성할 수 있습니다. 또한 SAS URI를 사용하여 VHD를 온-프레미스에서 빈 디스크로 직접 업로드할 수 있습니다. 가상 네트워크의 클라이언트와 관리 디스크 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크만 통과하여 공용 인터넷으로부터의 노출을 제거합니다.

프라이빗 엔드포인트를 만들어 동일한 구독에서 디스크 액세스 리소스를 만들고 이를 가상 네트워크에 연결할 수 있습니다. Private Link를 통해 데이터를 내보내고 가져오려면 디스크 또는 스냅샷을 디스크 액세스에 연결해야 합니다. 또한 디스크 또는 스냅샷의 NetworkAccessPolicy 속성을 `AllowPrivate`으로 설정해야 합니다. 

다른 사용자가 디스크 또는 스냅샷에 대한 SAS URI를 생성하지 못하도록 NetworkAccessPolicy 속성을 `DenyAll`로 설정할 수 있습니다. NetworkAccessPolicy 속성의 기본값은 `AllowAll`입니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [virtual-machines-disks-private-links-regions](../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="prerequisites"></a>필수 구성 요소

관리 디스크를 내보내고 가져오는 데 프라이빗 엔드포인트를 사용하려면 구독에서 이 기능을 사용하도록 설정해야 합니다. 구독에 사용하도록 설정된 기능을 가져오려면 구독 ID를 사용하여 이메일을 mdprivatelinks@microsoft .com에 보냅니다.

디스크가 연결된 VM의 가상 네트워크를 적어 두어야 합니다. 가상 네트워크는 프라이빗 엔드포인트를 구성할 때 필요합니다.

## <a name="create-a-disk-access-resource"></a>디스크 액세스 리소스 만들기

1. Azure Portal에 로그인하고, [이 링크](https://aka.ms/disksprivatelinks)를 사용하여 **디스크 액세스**로 이동합니다.

    > [!IMPORTANT]
    > [제공된 링크](https://aka.ms/disksprivatelinks)를 사용하여 [디스크 액세스] 블레이드로 이동해야 합니다. 링크를 사용하지 않으면 현재 퍼블릭 포털에 표시되지 않습니다.

1. **+ 추가**를 선택하여 새 디스크 액세스 리소스를 만듭니다.
1. 만들기 블레이드에서 구독, 리소스 그룹을 선택하고, 이름을 입력하고, 지역을 선택합니다.
1. **검토 + 만들기**를 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="디스크 액세스 만들기 블레이드의 스크린샷. 원하는 이름을 입력하고, 지역을 선택하고, 리소스 그룹을 선택하고, 계속 진행합니다.":::

리소스가 만들어지면 해당 리소스로 직접 이동합니다.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="포털의 리소스로 이동 단추에 대한 스크린샷":::

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

이제 디스크 액세스 리소스가 있으므로 이를 사용하여 디스크 내보내기/가져오기에 대한 액세스를 처리할 수 있습니다. 이 작업은 프라이빗 엔드포인트를 통해 수행됩니다. 따라서 프라이빗 엔드포인트를 만들어 디스크 액세스에 맞도록 구성해야 합니다.

1. 디스크 액세스 리소스에서 **프라이빗 엔드포인트 연결**을 선택합니다.
1. **+ 프라이빗 엔드포인트**를 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="디스크 액세스 리소스에 대한 개요 블레이드의 스크린샷. 프라이빗 엔드포인트 연결이 강조 표시되어 있습니다.":::

1. 리소스 그룹 선택
1. 이름을 입력하고, 디스크 액세스 리소스를 만든 지역과 동일한 지역을 선택합니다.
1. 완료되면 **다음: 리소스 >** 를 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="프라이빗 엔드포인트 만들기 워크플로(첫 번째 블레이드)의 스크린샷. 적절한 지역을 선택하지 않으면 나중에 문제가 발생할 수 있습니다.":::

1. **리소스** 블레이드에서 **내 디렉터리에서 Azure 리소스에 연결합니다.** 를 선택합니다.
1. **리소스 종류**에 대해 **Microsoft.Compute/diskAccesses**를 선택합니다.
1. **리소스**에 대해 이전에 만든 디스크 액세스 리소스를 선택합니다.
1. **대상 하위 리소스**를 **디스크**로 유지합니다.
1. **다음: 구성 >** 을 클릭합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="프라이빗 엔드포인트 만들기 워크플로(두 번째 블레이드)의 스크린샷. 모든 값(리소스 종류, 리소스, 대상 하위 리소스)이 강조 표시되어 있습니다.":::

1. 디스크 내보내기를 제한하려는 가상 네트워크를 선택합니다. 그러면 다른 가상 네트워크에서 디스크를 내보낼 수 없습니다.

    > [!NOTE]
    > 선택한 서브넷에 사용하도록 설정된 NSG(네트워크 보안 그룹)가 있는 경우 이 서브넷의 프라이빗 엔드포인트에만 NSG를 사용하지 않도록 설정됩니다. 이 서브넷의 다른 리소스에는 여전히 NSG가 적용됩니다.

1. 적절한 서브넷을 선택합니다.
1. **검토 + 만들기**를 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="프라이빗 엔드포인트 만들기 워크플로(세 번째 블레이드)의 스크린샷. 가상 네트워크 및 서브넷이 강조 표시되어 있습니다.":::

## <a name="enable-private-endpoint-on-your-disk"></a>디스크에서 프라이빗 엔드포인트 사용

1. 구성하려는 디스크로 이동합니다.
1. **네트워킹**을 선택합니다.
1. **프라이빗 엔드포인트(디스크 액세스를 통해)** 를 선택하고, 이전에 만든 디스크 액세스를 선택합니다.
1. **저장**을 선택합니다.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="관리 디스크 네트워킹 블레이드의 스크린샷. 프라이빗 엔드포인트 선택 항목 및 선택한 디스크 액세스가 강조 표시되어 있습니다. 이를 저장하면 이 액세스에 대한 디스크가 구성됩니다.":::

이제 관리 디스크를 가져오거나 내보낼 때 사용할 수 있는 Private Link를 구성하는 작업이 완료되었습니다.

## <a name="next-steps"></a>다음 단계

- [Private Link에 대한 FAQ](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [PowerShell을 사용하여 관리 스냅샷을 VHD로 다른 지역의 스토리지 계정에 내보내기/복사](scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md)