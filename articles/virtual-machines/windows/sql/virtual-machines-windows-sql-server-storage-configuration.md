---
title: SQL Server VM에 대한 저장소 구성 | Microsoft Docs
description: 이 항목에서는 Azure가 프로비전하는 동안 SQL Server VM에 대한 저장소를 구성하는 방법을 설명합니다(Resource Manager 배포 모델). 또한 기존 SQL Server VM에 대한 저장소를 구성하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: da850b8ff9174fa310c5247cd7e99af69db28a8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477455"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM에 대한 저장소 구성

Azure에서 SQL Server 가상 머신 이미지를 구성하는 경우 포털에서는 저장소 구성을 자동화할 수 있습니다. 저장소를 VM에 연결하고 해당 저장소를 SQL Server에 액세스할 수 있도록 하고 구성하여 특정 성능 요구 사항에 최적화하는 작업을 포함합니다.

이 항목에서는 Azure에서 프로비전 중 저장소 SQL Server VM 및 기존 VM에 대한 저장소를 구성하는 방법을 설명합니다. 이 구성은 SQL Server를 실행하는 Azure VM의 [성능 모범 사례](virtual-machines-windows-sql-performance.md) 에 기반합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>필수 조건

자동화된 저장소 구성 설정을 사용하려면 가상 머신에는 다음과 같은 특성이 필요합니다.

* [SQL Server 갤러리 이미지](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)로 프로비전합니다.
* [Resource Manager 배포 모델](../../../azure-resource-manager/resource-manager-deployment-model.md)을 사용합니다.
* [프리미엄 SSD](../disks-types.md)를 사용합니다.

## <a name="new-vms"></a>새 VM

다음 섹션에서는 새 SQL Server 가상 머신에 대한 저장소를 구성하는 방법을 설명합니다.

### <a name="azure-portal"></a>Azure Portal

SQL Server 갤러리 이미지를 사용하여 Azure VM을 프로비전할 경우 새 VM에 대한 저장소를 자동으로 구성하도록 선택할 수 있습니다. 저장소 크기, 성능 제한 및 워크로드 유형을 지정합니다. 다음 스크린샷에서는 SQL VM을 프로비전하는 동안 사용된 저장소 구성 블레이드를 보여 줍니다.

![프로비전하는 동안 SQL Server VM 저장소 구성](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

선택을 기반으로 Azure는 VM을 만든 후에 다음과 같은 저장소 구성 작업을 수행합니다.

* 프리미엄 SSD를 만들고 가상 머신에 연결합니다.
* SQL Server에 액세스할 수 있도록 데이터 디스크를 구성합니다.
* 지정된 크기와 성능(IOPS 및 처리량) 요구 사항에 따라 저장소 풀에 데이터 디스크를 구성합니다.
* 가상 컴퓨터에 새 드라이브와 저장소 풀을 연결합니다.
* 지정한 워크로드 유형(데이터 웨어하우징, 트랜잭션 처리 또는 일반)에 따라 새 드라이브를 최적화합니다.

Azure에서 저장소 설정을 구성하는 방법에 대한 자세한 내용은 [저장소 구성 섹션](#storage-configuration)을 참조하세요. Azure Portal에서 SQL Server VM을 만드는 방법의 전체 연습은 [프로비전 자습서](virtual-machines-windows-portal-sql-server-provision.md)를 참조하세요.

### <a name="resource-manage-templates"></a>Resource Manager 템플릿

다음 Resource Manager 템플릿을 사용하는 경우 두 개의 프리미엄 데이터 디스크는 저장소 풀 구성 없이 기본적으로 연결됩니다. 그러나 이러한 템플릿을 사용자 지정하여 가상 머신에 연결된 프리미엄 데이터 디스크의 수를 변경할 수 있습니다.

* [자동화된 Backup을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [자동화된 패치를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [AKV 통합을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>기존 VM

기존 SQL Server VM의 경우 Azure 포털에서 일부 저장소 설정을 수정할 수 있습니다. VM을 선택하고 설정 영역으로 이동하며 SQL Server 구성을 선택합니다. SQL Server 구성 블레이드에서는 VM의 현재 저장소 사용을 보여 줍니다. VM에 있는 모든 드라이브는 이 차트에 표시됩니다. 각 드라이브의 경우 저장 공간은 네 개의 섹션에 표시됩니다.

* SQL 데이터
* SQL 로그
* 기타(비 SQL 저장소)
* 사용 가능

![기존 SQL Server VM에 대한 저장소 구성](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

새 드라이브를 추가하거나 기존 드라이브를 확장하도록 저장소를 구성하려면 차트 위의 편집 링크를 클릭합니다.

표시된 구성 옵션은 전에 이 기능을 사용했는지 여부에 따라 달라 집니다. 처음으로 사용하는 경우 새 드라이브에 대한 저장소 요구 사항을 지정할 수 있습니다. 이전에 이 기능을 사용하여 드라이브를 만든 경우 해당 드라이브의 저장소를 확장할 수 있습니다.

### <a name="use-for-the-first-time"></a>처음으로 사용

이 기능을 처음으로 사용하는 경우 새 드라이브에 대한 저장소 크기와 성능 제한을 지정할 수 있습니다. 이러한 환경은 프로비전 시 표시되는 것과 비슷합니다. 주요 차이점은 워크로드 유형을 지정하도록 허용되지 않았다는 점입니다. 이 제한은 가상 머신에서 기존 SQL Server 구성을 중단하지 않도록 방지합니다.

![SQL Server 저장소 슬라이더 구성](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure는 사양에 따라 새 드라이브를 만듭니다. 이 시나리오에서 Azure는 다음과 같은 저장소 구성 작업을 수행합니다.

* Premium Storage 데이터 디스크를 만들고 가상 머신에 연결합니다.
* SQL Server에 액세스할 수 있도록 데이터 디스크를 구성합니다.
* 지정된 크기와 성능(IOPS 및 처리량) 요구 사항에 따라 저장소 풀에 데이터 디스크를 구성합니다.
* 가상 컴퓨터에 새 드라이브와 저장소 풀을 연결합니다.

Azure에서 저장소 설정을 구성하는 방법에 대한 자세한 내용은 [저장소 구성 섹션](#storage-configuration)을 참조하세요.

### <a name="add-a-new-drive"></a>새 드라이브 추가

SQL Server VM에 이미 저장소를 구성한 경우 저장소를 확장하면 두 가지 새로운 옵션을 표시합니다. 첫 번째는 새 드라이브를 추가하는 옵션으로 VM의 성능 수준을 높일 수 있습니다.

![SQL VM에 새 드라이브 추가](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

그러나 드라이브를 추가한 후에 성능 향상을 달성하기 위해 몇 가지 추가 수동 구성을 수행해야 합니다.

### <a name="extend-the-drive"></a>드라이브 확장

저장소 확장을 위한 다른 옵션은 기존 드라이브를 확장하는 것입니다. 이 옵션으로 사용자의 드라이브에 사용 가능한 저장소가 증가하지만 성능은 증가하지 않습니다. 저장소 풀을 만든 후에 저장소 풀을 사용해서 열 수를 변경할 수 없습니다. 열 수는 병렬 쓰기 수를 결정하며 이는 데이터 디스크에 걸쳐 스트라이프될 수 있습니다. 따라서 추가 데이터 디스크는 성능을 향상시킬 수 없습니다. 쓰여지는 데이터에 더 많은 저장 공간을 제공할 수 있습니다. 즉, 이 제한으로 드라이브를 확장할 때 열 수는 추가할 수 있는 데이터 디스크의 최소 수를 결정합니다. 따라서 4개의 데이터 디스크가 있는 저장소 풀을 만드는 경우 열 수도 4개가 됩니다. 저장소를 확장하면 4개 이상의 데이터 디스크도 추가해야 합니다.

![SQL VM에 대한 드라이브 확장](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Storage 구성

이 섹션에서는 Azure가 SQL VM을 프로비전하거나 Azure Portal에서 구성하는 동안 자동으로 수행하는 저장소 구성 변경에 대한 참조를 제공합니다.

* VM에 대한 2TB 미만의 저장소를 선택한 경우 Azure는 저장소 풀을 만들지 않습니다.
* VM에 대한 2TB 이상의 저장소를 선택한 경우 Azure는 저장소 풀을 구성합니다. 이 항목의 다음 섹션에서는 저장소 풀 구성의 세부 정보를 제공합니다.
* 자동 스토리지 구성은 항상 [프리미엄 SSD](../disks-types.md) P30 데이터 디스크를 사용합니다. 따라서 선택한 테라바이트 수와 VM에 연결된 데이터 디스크의 수 간에 1:1 매핑이 됩니다.

가격 책정 정보는 [디스크 저장소](https://azure.microsoft.com/pricing/details/storage) 탭의 **저장소 가격 책정** 페이지를 참조하세요.

### <a name="creation-of-the-storage-pool"></a>저장소 풀 만들기

Azure는 다음 설정을 사용하여 SQL Server VM에 저장소 풀을 만듭니다.

| 설정 | 값 |
| --- | --- |
| 스트라이프 크기 |256KB(데이터 웨어하우징); 64KB(트랜잭션) |
| 디스크 크기 |각각 1TB |
| 캐시 |읽기 |
| 할당 크기 |64KB NTFS 할당 단위 크기 |
| 즉시 파일 초기화 |Enabled |
| 메모리 내 페이지 잠금 |Enabled |
| 복구 |단순 복구(복원력 없음) |
| 열 수 |데이터 디스크 수<sup>1</sup> |
| TempDB 위치 |데이터 디스크에 저장됨<sup>2</sup> |

<sup>1</sup> 저장소 풀을 만든 후에 저장소 풀에서 열 수를 변경할 수 없습니다.

<sup>2</sup> 이 설정은 저장소 구성 기능을 사용하는 첫 번째 드라이브에만 적용됩니다.

## <a name="workload-optimization-settings"></a>워크로드 최적화 설정

다음 테이블에서는 사용할 수 있는 세 가지 워크로드 유형 옵션 및 이에 해당하는 최적화를 설명합니다.

| 워크로드 유형 | 설명 | 최적화 |
| --- | --- | --- |
| **일반** |대부분의 워크로드를 지원하는 기본 설정 |없음 |
| **트랜잭션 처리** |기존의 데이터베이스 OLTP 워크로드용으로 저장소를 최적화합니다. |추적 플래그 1117<br/>추적 플래그 1118 |
| **데이터 웨어하우징** |분석 및 보고 워크로드용으로 저장소를 최적화합니다. |추적 플래그 610<br/>추적 플래그 1117 |

> [!NOTE]
> SQL 가상 컴퓨터를 프로비전할 때 저장소 구성 단계에서 워크로드 유형을 선택하여 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure Virtual Machines의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.
