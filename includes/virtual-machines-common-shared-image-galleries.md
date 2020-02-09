---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: ef0eed330dd7a5b338cdbf36a159d1f046d3939d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021170"
---
공유 이미지 갤러리는 관리 되는 이미지에 대 한 구조와 조직을 구축 하는 데 도움이 되는 서비스입니다. 공유 이미지 갤러리는 다음을 제공 합니다.

- 이미지의 관리 되는 전역 복제
- 보다 쉽게 관리할 수 있도록 이미지의 버전 관리 및 그룹화
- 가용성 영역를 지 원하는 지역에서 ZRS (영역 중복 저장소) 계정을 포함 하는 항상 사용 가능한 이미지 ZRS는 영역 오류에 대해 향상된 복원력을 제공합니다.
- RBAC를 사용 하 여 구독 간 및 AD (Active Directory) 테넌트 간에 공유
- 각 지역에서 이미지 복제본으로 배포를 확장 합니다.

공유 이미지 갤러리를 사용하면 조직 내의 여러 사용자, 서비스 주체 또는 AD 그룹에게 이미지를 공유할 수 있습니다. 배포의 크기를 더 빠르게 조정하기 위해 여러 지역에 공유 이미지를 복제할 수 있습니다.

관리되는 이미지는 이미지를 만드는 방법에 따라 전체 VM(연결된 데이터 디스크 포함) 또는 OS 디스크의 사본입니다. 이미지에서 VM을 만드는 경우 이미지의 VHD 복사본을 사용하여 새 VM의 디스크를 만듭니다. 관리되는 이미지는 스토리지에 유지되며 새 VM을 만들 때 계속 반복해서 사용할 수 있습니다.

유지 관리 해야 하는 관리 되는 이미지가 많고 회사 전체에서 사용할 수 있게 하려는 경우 공유 이미지 갤러리를 리포지토리로 사용 하 여 이미지를 쉽게 공유할 수 있도록 합니다. 

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다.

| 리소스 | Description|
|----------|------------|
| **관리되는 이미지** | 단독으로 사용 하거나 이미지 갤러리에서 **이미지 버전** 을 만드는 데 사용할 수 있는 기본 이미지입니다. 관리 되는 이미지는 [일반화](#generalized-and-specialized-images) 된 vm에서 만들어집니다. 관리되는 이미지는 여러 VM을 만드는 데 사용할 수 있는 특수한 유형의 VHD로, 이제 공유 이미지 버전을 만드는 데 사용할 수 있습니다. |
| **스냅샷** | **이미지 버전**을 만드는 데 사용할 수 있는 VHD의 복사본입니다. 스냅숏은 [특수 한 VM (](#generalized-and-specialized-images) 일반화 되지 않은 VM)에서 가져올 수 있으며 단독으로 사용 하거나 데이터 디스크의 스냅숏으로 사용 하 여 특수 이미지 버전을 만들 수 있습니다.
| **이미지 갤러리** | Azure Marketplace와 마찬가지로 **이미지 갤러리**는 이미지를 관리하고 공유하는 데 사용되는 리포지토리이지만 액세스할 수 있는 사람을 제어할 수 있습니다. |
| **이미지 정의** | 이미지는 갤러리 내에서 정의 되며, 조직 내에서 이미지를 사용 하기 위한 요구 사항 및 이미지에 대 한 정보를 전달 합니다. 이미지를 일반화 하거나 특수화 하 든, 운영 체제, 최소 및 최대 메모리 요구 사항, 릴리스 정보 등의 정보를 포함할 수 있습니다. 이미지의 형식 정의입니다. |
| **이미지 버전** | **이미지 버전**은 갤러리를 사용하는 경우 VM을 만들 때 사용합니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 관리되는 이미지와 마찬가지로 **이미지 버전**을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다. |

<br>

![갤러리에서 한 이미지의 여러 버전을 보유하는 방법을 보여주는 그래픽](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>이미지 정의

이미지 정의는 이미지 버전의 논리적 그룹화입니다. 이미지 정의는 이미지를 만든 이유에 대 한 정보, 사용 중인 OS 및 이미지 사용에 대 한 정보를 포함 합니다. 이미지 정의는 특정 이미지 만들기에 대 한 모든 세부 정보에 대 한 계획과 비슷합니다. 이미지 정의에서 VM을 배포 하는 것이 아니라 정의에서 만든 이미지 버전에서 VM을 배포 하지 않습니다.

조합- **게시자**, **제품** 및 **SKU**에 사용 되는 각 이미지 정의에 대해 3 개의 매개 변수가 있습니다. 특정 이미지 정의를 찾는 데 사용 됩니다. 3개 값을 모두 공유하지는 않으면서 1개 또는 2개의 값을 공유하는 이미지 버전을 사용할 수 있습니다.  예를 들어 다음은 3개의 이미지 정의와 해당 값입니다.

|이미지 정의|게시자|제품|SKU|
|---|---|---|---|
|myImage1|Contoso|금융|백 엔드|
|myImage2|Contoso|금융|프런트 엔드|
|myImage3|테스트|금융|프런트 엔드|

이러한 세 가지 정의는 모두 고유한 값 세트를 갖습니다. 형식은 최신 버전의 Marketplace 이미지를 가져오기 위해 현재 Azure PowerShell의 [Azure Marketplace 이미지](../articles/virtual-machines/windows/cli-ps-findimage.md) 에 대 한 게시자, 제품 및 SKU를 지정 하는 방법과 비슷합니다. 각 이미지 정의에는 이러한 값의 고유한 집합이 있어야 합니다.

다음은 리소스를 더 쉽게 추적할 수 있도록 이미지 정의에 대해 설정할 수 있는 다른 매개 변수입니다.

* 운영 체제 상태-OS 상태를 [일반화 또는 특수](#generalized-and-specialized-images)로 설정할 수 있습니다.
* 운영 체제-Windows 또는 Linux 일 수 있습니다.
* 설명-이미지 정의가 존재 하는 이유에 대 한 자세한 정보를 제공 하기 위해 설명을 사용 합니다. 예를 들어 응용 프로그램이 미리 설치 되어 있는 프런트 엔드 서버에 대 한 이미지 정의가 있을 수 있습니다.
* Eula-이미지 정의와 관련 된 최종 사용자 사용권 계약을 가리키는 데 사용할 수 있습니다.
* 개인정보 취급 방침 및 릴리스 정보-Azure storage에 릴리스 정보 및 개인 정보 취급 방침을 저장 하 고 이미지 정의의 일부로 액세스 하기 위한 URI를 제공 합니다.
* 수명 종료 날짜-자동화를 사용 하 여 이전 이미지 정의를 삭제할 수 있도록 이미지 정의에 수명 종료 날짜를 연결 합니다.
* 태그-이미지 정의를 만들 때 태그를 추가할 수 있습니다. 태그에 대 한 자세한 내용은 [태그를 사용 하 여 리소스 구성](../articles/azure-resource-manager/management/tag-resources.md) 을 참조 하세요.
* 최소 및 최대 vCPU 및 메모리 권장 사항-이미지에 vCPU 및 메모리 권장 사항이 있는 경우 해당 정보를 이미지 정의에 연결할 수 있습니다.
* 허용 되지 않는 디스크 유형-VM의 저장소 요구 사항에 대 한 정보를 제공할 수 있습니다. 예를 들어 이미지가 표준 HDD 디스크에 적합 하지 않은 경우 허용 안 함 목록에 추가 합니다.

## <a name="generalized-and-specialized-images"></a>일반화 되 고 특수 이미지

공유 이미지 갤러리에서 지 원하는 두 가지 운영 체제 상태가 있습니다. 일반적으로 이미지는 이미지를 만들기 전에 이미지를 만드는 데 사용 되는 VM을 일반화 해야 합니다. 일반화는 VM에서 컴퓨터 및 사용자 관련 정보를 제거 하는 프로세스입니다. Windows의 경우 Sysprep도 사용 됩니다. Linux의 경우 [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` 또는 `-deprovision+user` 매개 변수를 사용할 수 있습니다.

특수 한 Vm은 컴퓨터의 특정 정보 및 계정을 제거 하는 프로세스를 거치지 않았습니다. 또한 특수 이미지에서 만든 Vm에는 연결 된 `osProfile` 없습니다. 즉, 특수 이미지에 몇 가지 제한 사항이 있습니다.

- Vm에 로그인 하는 데 사용할 수 있는 계정은 해당 VM에서 만든 특수 이미지를 사용 하 여 만든 모든 VM 에서도 사용할 수 있습니다.
- Vm에는 이미지를 만든 VM의 **컴퓨터 이름이** 포함 됩니다. 충돌을 방지 하려면 컴퓨터 이름을 변경 해야 합니다.
- `osProfile`은 `secrets`를 사용 하 여 일부 중요 한 정보를 VM에 전달 하는 방법입니다. 이로 인해 KeyVault, WinRM 및 `osProfile`에서 `secrets`를 사용 하는 기타 기능을 사용 하는 문제가 발생할 수 있습니다. 일부 경우에는 MSI (관리 서비스 id)를 사용 하 여 이러한 제한 문제를 해결할 수 있습니다.

> [!IMPORTANT]
> 특수 이미지는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> **알려진 미리 보기 제한 사항** Vm은 포털 또는 API를 사용 하는 특수 이미지 에서만 만들 수 있습니다. 는 미리 보기에 대해 CLI 또는 PowerShell을 지원 하지 않습니다.


## <a name="regional-support"></a>국가별 지원

원본 지역은 아래 표에 나와 있습니다. 모든 공용 지역은 대상 지역이 될 수 있지만 오스트레일리아 중부 및 오스트레일리아 중부 2로 복제 하려면 구독 허용 목록 있어야 합니다. 허용 목록 포함을 요청하려면 https://azure.microsoft.com/global-infrastructure/australia/contact/ 로 이동합니다.


| 원본 영역        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| 오스트레일리아 중부     | 중국 동부        | 인도 남부        | 서유럽        |
| 오스트레일리아 중부 2   | 중국 동부 2      | 동남아시아     | 영국 남부           |
| 오스트레일리아 동부        | 중국 북부       | 일본 동부         | 영국 서부            |
| 오스트레일리아 남동부   | 중국 북부 2     | 일본 서부         | US DoD 중부     |
| 브라질 남부          | 동아시아         | 한국 중부      | US DoD 동부        |
| 캐나다 중부        | 미국 동부           | 한국 남부        | US Gov 애리조나     |
| 캐나다 동부           | 미국 동부 2         | 미국 중북부   | US Gov 텍사스       |
| 인도 중부         | 미국 동부 2 EUAP    | 북유럽       | US Gov 버지니아    |
| 미국 중부            | 프랑스 중부    | 미국 중남부   | 인도 서부         |
| 미국 중부 EUAP       | 프랑스 남부      | 미국 중서부    | 미국 서부            |
|                       |                   |                    | 미국 서부 2          |



## <a name="limits"></a>제한 

공유 이미지 갤러리를 사용 하 여 리소스를 배포 하기 위해 구독 당 제한이 있습니다.
- 100 공유 이미지 갤러리, 구독 당, 지역별
- 1000 이미지 정의 (구독 당, 지역별)
- 1만 이미지 버전 (구독 당, 지역별)
- 이미지에 연결 된 모든 디스크 크기는 1TB 미만 이어야 합니다.

자세한 내용은 현재 사용량을 확인 하는 방법에 대 한 예제는 [제한에 대 한 리소스 사용 확인](https://docs.microsoft.com/azure/networking/check-usage-against-limits) 을 참조 하세요.
 
## <a name="scaling"></a>크기 조정
공유 이미지 갤러리를 사용하면 Azure에서 이미지를 보관하도록 하려는 복제본 수를 지정할 수 있습니다. 이렇게 하면 VM 배포를 여러 복제본으로 확대함으로써 단일 복제본 과부하로 인해 인스턴스 생성 처리가 제한될 가능성을 줄일 수 있으므로 다중 VM 배포 시나리오에 도움이 됩니다.

이제 공유 이미지 갤러리를 사용 하 여 가상 머신 확장 집합에 1000 VM 인스턴스를 배포할 수 있습니다 (관리 되는 이미지가 있는 600부터). 이미지 복제본은 더 나은 배포 성능, 안정성 및 일관성을 제공 합니다.  영역에 대 한 크기 조정 요구 사항에 따라 각 대상 지역에서 다른 복제본 수를 설정할 수 있습니다. 각 복제본은 이미지의 전체 복사본이 기 때문에 각 추가 복제본으로 배포를 선형으로 확장 하는 데 도움이 됩니다. 두 개의 이미지나 지역이 동일 하지는 않지만, 지역에서 복제본을 사용 하는 방법에 대 한 일반적인 지침은 다음과 같습니다.

- VMSS (가상 머신 확장 집합) 배포의 경우-동시에 만드는 20 개의 Vm 마다 하나의 복제본을 유지 하는 것이 좋습니다. 예를 들어 한 지역의 동일한 이미지를 사용 하 여 동시에 120 Vm을 만드는 경우 이미지의 복제본을 6 개 이상 유지 하는 것이 좋습니다. 
- VMSS (가상 머신 확장 집합) 배포의 경우-최대 600 인스턴스를 포함 하는 모든 확장 집합 배포에 대해 하나 이상의 복제본을 유지 하는 것이 좋습니다. 예를 들어, 각각 단일 지역의 동일한 이미지를 사용 하는 600 VM 인스턴스가 있는 5 개의 크기 집합을 동시에 만드는 경우 이미지의 복제본을 5 개 이상 유지 하는 것이 좋습니다. 

이미지 크기, 콘텐츠 및 OS 유형 같은 요소로 인해 복제본 수를 과도 하 게 프로 비전 하는 것이 좋습니다.

![이미지를 확장하는 방법을 보여 주는 그래픽](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>이미지를 항상 사용 가능 하 게 설정

[AZURE ZRS (영역 중복 저장소)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) 는 지역에서 가용성 영역 오류에 대 한 복원 력을 제공 합니다. 공유 이미지 갤러리의 일반 공급을 사용 하면 가용성 영역를 사용 하 여 지역에서 ZRS 계정에 이미지를 저장 하도록 선택할 수 있습니다. 

각 대상 지역에 대 한 계정 유형을 선택할 수도 있습니다. 기본 저장소 계정 유형은 Standard_LRS 이지만 가용성 영역 지역에 대해 Standard_ZRS를 선택할 수 있습니다. [여기](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)에서 ZRS의 지역별 가용성을 확인 하세요.

![ZRS를 보여 주는 그래픽](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>복제
공유 이미지 갤러리를 사용하여 다른 Azure 지역에 이미지를 자동으로 복제할 수도 있습니다. 조직에 적합한 방식으로 각 공유 이미지 버전을 여러 지역에 복제할 수 있습니다. 한 가지 예는 다중 지역에서 최신 이미지를 항상 복제하는 것이지만 이전 버전은 모두 1개 지역에서만 사용 가능합니다. 공유 이미지 버전의 스토리지 비용을 절약하는 데 도움이 될 수 있습니다. 

공유 이미지 버전이 복제되는 지역은 생성 시간 후 업데이트할 수 있습니다. 여러 지역에 복제하는 데 걸리는 시간은 복사되는 데이터의 양과 버전이 복제되는 지역 수에 따라 다릅니다. 경우에 따라 몇 시간이 걸릴 수 있습니다. 복제가 진행 중일 때 지역별 복제 상태를 볼 수 있습니다. 이미지 복제가 한 지역에서 완료 되 면 해당 지역에서 해당 이미지 버전을 사용 하 여 VM 또는 확장 집합을 배포할 수 있습니다.

![이미지를 복제하는 방법을 보여 주는 그래픽](./media/shared-image-galleries/replication.png)

## <a name="access"></a>액세스

공유 이미지 갤러리, 이미지 정의 및 이미지 버전은 모든 리소스 이므로 기본 제공 되는 네이티브 Azure RBAC 컨트롤을 사용 하 여 공유할 수 있습니다. RBAC를 사용 하 여 이러한 리소스를 다른 사용자, 서비스 사용자 및 그룹과 공유할 수 있습니다. 내에서 만든 테넌트 외부의 개별 사용자에 대 한 액세스를 공유할 수도 있습니다. 사용자가 공유 이미지 버전에 액세스할 수 있게 되 면 VM 또는 가상 머신 확장 집합을 배포할 수 있습니다.  사용자가 액세스할 수 있는 항목을 이해하는 데 도움이 되는 공유 행렬은 다음과 같습니다.

| 공유한 항목     | 공유 이미지 갤러리 | 이미지 정의 | 이미지 버전 |
|----------------------|----------------------|--------------|----------------------|
| 공유 이미지 갤러리 | 예                  | 예          | 예                  |
| 이미지 정의     | 아닙니다.                   | 예          | 예                  |

최상의 환경을 위해 갤러리 수준에서 공유 하는 것이 좋습니다. 개별 이미지 버전을 공유 하지 않는 것이 좋습니다. RBAC에 대 한 자세한 내용은 [rbac를 사용 하 여 Azure 리소스에 대 한 액세스 관리](../articles/role-based-access-control/role-assignments-portal.md)를 참조 하세요.

다중 테넌트 앱 등록을 사용 하 여 테넌트 간에도 이미지를 대규모로 공유할 수 있습니다. 테넌트 간에 이미지를 공유 하는 방법에 대 한 자세한 내용은 [Azure 테넌트 간에 갤러리 VM 이미지 공유](../articles/virtual-machines/linux/share-images-across-tenants.md)를 참조 하세요.

## <a name="billing"></a>청구
추가 비용 없이 공유 이미지 갤러리 서비스를 이용할 수 있습니다. 다음 리소스에 대한 비용이 청구됩니다.
- 공유 이미지 버전 저장 시 스토리지 비용. 비용은 이미지 버전의 복제본 수와 버전이 복제 된 지역 수에 따라 달라 집니다. 예를 들어 2 개 이미지가 있고 두 개의 지역에 복제 되는 경우 크기에 따라 관리 디스크 6 개에 대해 변경 됩니다. 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조 하세요.
- 원본 지역에서 복제 된 지역으로의 첫 번째 이미지 버전 복제에 대 한 네트워크 송신 요금입니다. 후속 복제본은 지역 내에서 처리 되므로 추가 비용이 없습니다. 

## <a name="updating-resources"></a>리소스 업데이트

만든 후에는 이미지 갤러리 리소스를 변경할 수 있습니다. 이러한 제한 사항은 다음과 같습니다.
 
공유 이미지 갤러리:
- Description

이미지 정의:
- 권장 vCPU
- 권장 메모리
- Description
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

## <a name="sdk-support"></a>SDK 지원

다음 SDK를 사용하면 공유 이미지 갤러리를 만들 수 있습니다.

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.JS](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>템플릿

템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답) 

* [구독 간에 공유 이미지 갤러리 리소스를 모두 나열 하려면 어떻게 해야 하나요?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [기존 이미지를 공유 이미지 갤러리로 이동할 수 있나요?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [특수 한 디스크에서 이미지 버전을 만들 수 있나요?](#can-i-create-an-image-version-from-a-specialized-disk)
* [공유 이미지 갤러리 리소스를 만든 후 다른 구독으로 이동할 수 있나요?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Azure 중국 21Vianet 또는 Azure 독일 또는 Azure Government Cloud와 같은 클라우드 간에 이미지 버전을 복제할 수 있나요?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [구독 간에 이미지 버전을 복제할 수 있나요?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Azure AD 테넌트 간에 이미지 버전을 공유할 수 있나요?](#can-i-share-image-versions-across-azure-ad-tenants)
* [대상 지역에서 이미지 버전을 복제 하는 데 소요 되는 시간](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [원본 지역과 대상 지역 간의 차이점은 무엇 인가요?](#what-is-the-difference-between-source-region-and-target-region)
* [이미지 버전을 만드는 동안 원본 지역을 지정 어떻게 할까요??](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [각 지역에 만들 이미지 버전 복제본의 수를 지정 어떻게 할까요??](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [이미지 정의와 이미지 버전에 대 한 것과 다른 위치에서 공유 이미지 갤러리를 만들 수 있나요?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [공유 이미지 갤러리 사용에 대 한 요금은 얼마 인가요?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [공유 이미지 갤러리 및 이미지 정의와 이미지 버전을 만드는 데 사용 해야 하는 API 버전은 무엇 인가요?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [이미지 버전에서 공유 VM 또는 가상 머신 확장 집합을 만드는 데 사용 해야 하는 API 버전은 무엇 인가요?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>구독 간에 모든 공유 이미지 갤러리 리소스를 나열하는 방법은 무엇인가요?

Azure Portal에 대 한 액세스 권한이 있는 모든 구독에서 공유 이미지 갤러리 리소스를 모두 나열 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. **모든 리소스**로 이동합니다.
1. 모든 리소스를 나열하려는 모든 구독을 선택합니다.
1. **프라이빗 갤러리** 형식의 리소스를 찾습니다.
 
   이미지 정의 및 이미지 버전을 확인하려면 **숨겨진 형식 표시**도 선택해야 합니다.
 
   사용 권한이 있는 구독 간에 공유 이미지 갤러리 리소스를 나열하려면 Azure CLI에서 다음 명령을 사용하세요.

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>기존 이미지를 공유 이미지 갤러리로 이동할 수 있나요?
 
예. 가지고 있는 이미지의 유형에 따라 3가지 시나리오가 있습니다.

 시나리오 1: SIG와 동일한 구독에 관리 되는 이미지가 있는 경우 여기에서 이미지 정의와 이미지 버전을 만들 수 있습니다.

 시나리오 2: SIG와 동일한 구독에 관리 되지 않는 이미지가 있는 경우 해당 이미지에서 관리 되는 이미지를 만든 다음 여기에서 이미지 정의와 이미지 버전을 만들 수 있습니다. 

 시나리오 3: 로컬 파일 시스템에 VHD가 있는 경우 관리 되는 이미지에 VHD를 업로드 해야 합니다. 그런 다음 이미지 정의 및 이미지 버전을 만들 수 있습니다.

- VHD가 Windows VM 인 경우 [Vhd 업로드](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)를 참조 하세요.
- Linux VM용 VHD일 경우 [VHD 업로드](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)를 참조하세요.

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>특수한 디스크에서 이미지 버전을 만들 수 있나요?

예, 특수 한 디스크에 대 한 지원은 미리 보기 상태입니다. 포털 ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) 또는 [LINUX](../articles/virtual-machines/linux/shared-images-portal.md)) 및 API를 사용 하 여 특수 이미지에서 VM을 만들 수 있습니다. 미리 보기에 대 한 PowerShell은 지원 되지 않습니다.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>공유 이미지 갤러리 리소스를 만든 후 다른 구독으로 이동할 수 있나요?

아니요. 공유 이미지 갤러리 리소스를 다른 구독으로 이동할 수는 없습니다. 그러나 필요에 따라 갤러리의 이미지 버전을 다른 지역으로 복제할 수는 있습니다.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Azure 중국 21Vianet 또는 Azure 독일 또는 Azure Government Cloud와 같은 클라우드 간에 이미지 버전을 복제할 수 있나요?

아니요, 클라우드 간에 이미지 버전을 복제할 수는 없습니다.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>내 이미지 버전을 구독 간에 복제할 수 있나요?

아니요. 구독에서 지역 간에 이미지 버전을 복제하고 RBAC를 통해 다른 구독에서 사용할 수 있습니다.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Azure AD 테넌트 간에 이미지 버전을 공유할 수 있나요? 

예, RBAC를 사용 하 여 테넌트의 개인에 게 공유할 수 있습니다. 그러나 대규모에서 공유 하려면 [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) 또는 [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)를 사용 하 여 "Azure 테넌트에서 갤러리 이미지 공유"를 참조 하세요.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>대상 지역 간에 이미지 버전을 복제하려면 얼마나 걸리나요?

이미지 버전 복제 시간은 이미지의 크기와 이미지가 복제되는 지역 수에 따라 크게 달라집니다. 그러나 최상의 결과를 얻으려면 이미지를 작게 유지하고 원본 지역과 대상 지역을 가깝게 유지하는 것이 가장 좋습니다. -ReplicationStatus 플래그를 사용하여 복제의 상태를 확인할 수 있습니다.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>원본 지역과 대상 지역 사이의 차이점은 무엇인가요?

원본 지역은 이미지 버전이 생성되는 지역이며, 대상 지역은 이미지 버전의 복사본이 저장되는 지역입니다. 각 이미지 버전에 하나의 원본 지역만 사용할 수 있습니다. 또한 이미지 버전을 만들 때 원본 지역 위치를 대상 지역 중 하나로 전달하세요.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>이미지 버전을 만드는 동안 원본 영역을 지정하는 방법은 무엇인가요?

이미지 버전을 만들 때 CLI의 **--location** 태그와 PowerShell의 **-Location** 태그를 사용하여 원본 지역을 지정할 수 있습니다. 이미지 버전을 만들려면 기본 이미지로 사용 중인 관리되는 이미지가 이미지 버전을 만들려는 위치와 동일한 위치에 있는지 확인하세요. 또한 이미지 버전을 만들 때 원본 지역 위치를 대상 지역 중 하나로 전달하세요.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 무엇인가요?

각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 두 가지가 있습니다.
 
1. 지역당 만들려는 복제본의 수를 지정하는 지역당 복제본 수. 
2. 지역당 복제본 수가 지정되지 않은 경우 지역당 기본 개수인 일반적인 복제본 수입니다. 

지역 복제본 수를 지정 하려면 해당 지역에 만들 복제본 수와 함께 위치를 전달 합니다. "남 Central US = 2". 

각 위치에 지역당 복제본 수가 지정되지 않은 경우 기본 복제본 수가 일반적인 복제본 수로 지정됩니다. 

CLI에서 일반적인 복제본 수를 지정하려면 `az sig image-version create` 명령에 **--replica-count** 인수를 사용합니다.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>이미지 정의와 이미지 버전에 대 한 것과 다른 위치에서 공유 이미지 갤러리를 만들 수 있나요?

예, 구성할 수 있습니다. 그러나 최상의 방법으로 리소스 그룹, 공유 이미지 갤러리, 이미지 정의 및 이미지 버전을 동일한 위치에 유지 하는 것이 좋습니다.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>공유 이미지 갤러리 사용 요금은 얼마인가요?

공유 이미지 갤러리 서비스 사용 요금은 없습니다(단, 이미지 버전을 저장할 때 발생하는 스토리지 요금과 원본 지역에서 대상 지역으로 이미지 버전을 복제할 때 발생하는 네트워크 송신 요금 제외).

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>공유 이미지 갤러리 및 이미지 정의와 이미지 버전을 만드는 데 사용 해야 하는 API 버전은 무엇 인가요?

공유 이미지 갤러리, 이미지 정의 및 이미지 버전으로 작업하려면 API 버전 2018-06-01을 사용하는 것이 좋습니다. ZRS (영역 중복 저장소)에는 버전 2019-03-01 이상이 필요 합니다.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>이미지 버전에서 공유 VM 또는 가상 머신 확장 집합을 만드는 데 사용 해야 하는 API 버전은 무엇 인가요?

이미지 버전을 사용한 VM 및 Virtual Machine Scale Set 배포의 경우 API 버전 2018-04-01 이상을 사용하는 것이 좋습니다.
