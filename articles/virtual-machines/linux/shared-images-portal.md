---
title: 포털을 사용 하 여 Linux 용 공유 Azure VM 이미지 만들기
description: Azure Portal를 사용 하 여 가상 머신 이미지를 만들고 공유 하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 44df85a94ad26d3abcd48f01c31e7aa093c1123f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978699"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>포털을 사용 하 여 Azure 공유 이미지 갤러리 만들기

[공유 이미지 갤러리](shared-image-galleries.md) 는 조직 전체에서 사용자 지정 이미지 공유를 간소화 합니다. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. 사용자 지정 이미지는 미리 로드 응용 프로그램, 응용 프로그램 구성 및 기타 OS 구성과 같은 배포 작업을 부트스트랩 하는 데 사용할 수 있습니다. 

공유 이미지 갤러리를 사용 하면 AAD 테넌트 내에서 사용자 지정 VM 이미지를 조직의 다른 사용자와 공유할 수 있습니다. 공유할 이미지를 선택 하 고, 해당 이미지를 사용할 수 있도록 설정 하 고, 공유 하려는 대상 지역을 선택 합니다. 공유 이미지를 논리적으로 그룹화 할 수 있도록 여러 갤러리를 만들 수 있습니다. 

갤러리는 전체 RBAC (역할 기반 액세스 제어)를 제공 하는 최상위 수준 리소스입니다. 이미지의 버전을 지정할 수 있으며, 각 이미지 버전을 다른 Azure 지역 집합에 복제 하도록 선택할 수 있습니다. 갤러리는 관리 되는 이미지 에서만 작동 합니다.

공유 이미지 갤러리 기능에는 여러 리소스 종류가 있습니다. 이 문서에서 다음을 사용 하거나 빌드할 예정입니다.

| Erőforrás | Leírás|
|----------|------------|
| **관리 이미지** | 단독으로 사용 하거나 이미지 갤러리에서 **이미지 버전** 을 만드는 데 사용할 수 있는 기본 이미지입니다. 관리 되는 이미지는 [일반화](shared-image-galleries.md#generalized-and-specialized-images) 된 vm에서 만들어집니다. 관리 이미지는 여러 Vm을 만드는 데 사용할 수 있는 특수 한 유형의 VHD 이며 이제 공유 이미지 버전을 만드는 데 사용할 수 있습니다. |
| **스냅숏** | **이미지 버전**을 만드는 데 사용할 수 있는 VHD의 복사본입니다. 스냅숏은 [특수 한 VM (](shared-image-galleries.md#generalized-and-specialized-images) 일반화 되지 않은 VM)에서 가져올 수 있으며 단독으로 사용 하거나 데이터 디스크의 스냅숏으로 사용 하 여 특수 이미지 버전을 만들 수 있습니다.
| **이미지 갤러리** | Azure Marketplace와 마찬가지로 **이미지 갤러리** 는 이미지를 관리 하 고 공유 하기 위한 리포지토리입니다 하지만 액세스 권한이 있는 사용자를 제어 합니다. |
| **이미지 정의** | 이미지는 갤러리 내에서 정의 되며, 조직 내에서 이미지를 사용 하기 위한 요구 사항 및 이미지에 대 한 정보를 전달 합니다. 이미지를 일반화 하거나 특수화 하 든, 운영 체제, 최소 및 최대 메모리 요구 사항, 릴리스 정보 등의 정보를 포함할 수 있습니다. 이미지 형식의 정의입니다. |
| **이미지 버전** | **이미지 버전** 은 갤러리를 사용할 때 VM을 만드는 데 사용 하는 것입니다. 사용자 환경에 맞게 여러 버전의 이미지를 사용할 수 있습니다. 관리 이미지와 마찬가지로, **이미지 버전** 을 사용 하 여 vm을 만들 때 이미지 버전은 vm에 대 한 새 디스크를 만드는 데 사용 됩니다. 이미지 버전은 여러 번 사용할 수 있습니다. |

<br>

> [!IMPORTANT]
> 특수 이미지는 현재 공개 미리 보기로 제공 됩니다.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **알려진 미리 보기 제한 사항** Vm은 포털 또는 API를 사용 하는 특수 이미지 에서만 만들 수 있습니다. 는 미리 보기에 대해 CLI 또는 PowerShell을 지원 하지 않습니다.


## <a name="before-you-begin"></a>Előzetes teendők

이 문서의 예제를 완료 하려면 일반화 된 VM의 관리 되는 기존 이미지 또는 특수 한 VM의 스냅숏을 만들어야 합니다. [자습서: Azure PowerShell을 사용 하 여 AZURE VM의 사용자 지정 이미지 만들기](tutorial-custom-images.md) 또는 특수 한 VM에 대 한 [스냅숏 만들기](../windows/snapshot-copy-managed-disk.md) 를 수행할 수 있습니다. 관리 되는 이미지와 스냅숏의 경우 데이터 디스크 크기는 1TB 이상일 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Virtuális gépek létrehozása 

이제 하나 이상의 새 Vm을 만들 수 있습니다. 이 예제에서는 *미국 동부* 데이터 센터의 *Myresourcegroup* 에 *MYVMFROMIMAGE*라는 VM을 만듭니다.

1. 이미지 정의로 이동 합니다. 리소스 필터를 사용 하 여 사용 가능한 모든 이미지 정의를 표시할 수 있습니다.
1. 이미지 정의 페이지의 페이지 맨 위에 있는 메뉴에서 **VM 만들기** 를 선택 합니다.
1. **리소스 그룹**에 대해 **새로 만들기** 를 선택 하 고 이름에 *myresourcegroup* 을 입력 합니다.
1. **가상 컴퓨터 이름**에 *myvm*을 입력 합니다.
1. **지역**에서 *미국 동부*를 선택 합니다.
1. **가용성 옵션**의 경우에는 기본 *인프라 중복성 필요 없음*을 그대로 둡니다.
1. 이미지 **에 대 한 값** 은 이미지 정의에 대해 페이지에서 시작한 경우 자동으로 `latest` 이미지 버전으로 채워집니다.
1. **크기**의 경우 사용 가능한 크기 목록에서 VM 크기를 선택한 다음 **선택**을 선택 합니다.
1. **관리자 계정**에서 원본 VM이 일반화 된 경우 **사용자 이름** 및 **SSH 공개 키**를 입력 합니다. 원본 VM이 특수화 된 경우 원본 VM의 정보가 사용 되므로 이러한 옵션은 회색으로 표시 됩니다.
1. VM에 대 한 원격 액세스를 허용 하려면 **공용 인바운드 포트**에서 **선택한 포트 허용** 을 선택한 다음 드롭다운에서 **SSH (22)** 를 선택 합니다. VM에 대 한 원격 액세스를 허용 하지 않으려면 **공용 인바운드 포트**에 대해 선택 **안 함** 을 선택 하지 않습니다.
1. 작업이 완료 되 면 페이지 맨 아래에 있는 **검토 + 만들기** 단추를 선택 합니다.
1. VM에서 유효성 검사를 통과 한 후 페이지 맨 아래에서 **만들기** 를 선택 하 여 배포를 시작 합니다.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a **Törlés** elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

개별 리소스를 삭제 하려면 해당 리소스를 역순으로 삭제 해야 합니다. 예를 들어 이미지 정의를 삭제 하려면 해당 이미지에서 만든 모든 이미지 버전을 삭제 해야 합니다.

## <a name="next-steps"></a>Következő lépések

템플릿을 사용 하 여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 사용할 수 있는 몇 가지 Azure 빠른 시작 템플릿이 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

공유 이미지 갤러리에 대 한 자세한 내용은 [개요](shared-image-galleries.md)를 참조 하세요. 문제가 발생 하는 경우 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 참조 하세요.

