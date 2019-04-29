---
title: Azure의 Linux VM에서 저장소 리소스 삭제 오류 문제 해결 | Microsoft Docs
description: 연결된 VHD가 포함된 저장소 리소스를 삭제할 때 발생하는 문제 해결 방법입니다.
keywords: ''
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: a1eb946d3f1b18aaa86735dedcfbaa1fd6a89621
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922648"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>저장소 리소스 삭제 오류 문제 해결

상황에 따라, 배포된 Azure Resource Manager에서 Azure 저장소 계정, 컨테이너 또는 BLOB을 삭제하려고 시도하면 다음 오류 중 하나가 발생할 수 있습니다.

> **스토리지 계정 'StorageAccountName'을 삭제하지 못했습니다. 오류: 스토리지 계정의 아티팩트가 사용 중이기 때문에 스토리지 계정을 삭제할 수 없습니다.**
> 
> **# 컨테이너에서 #을 삭제하지 못했습니다.<br>vhds: 현재 컨테이너에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다.**
> 
> **# Blob에서 #을 삭제하지 못했습니다.<br>BlobName.vhd: 현재 Blob에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다.**

Azure VM에 사용되는 VHD는 Azure의 표준 또는 Premium Storage 계정에 페이지 Blob으로 저장되는 .vhd 파일입니다. Azure 디스크에 대한 자세한 내용은 [관리 디스크 소개](../linux/managed-disks-overview.md)를 참조하세요.

Azure는 손상 방지를 위해 VM에 연결된 디스크 삭제를 차단합니다. 또한 VM에 연결된 페이지 Blob이 있는 저장소 계정 및 컨테이너의 삭제도 차단합니다. 

위의 오류 중 하나가 수신되었을 때 저장소 계정, 컨테이너 또는 Blob을 삭제하는 프로세스는 다음과 같습니다. 
1. VM에 연결된 Blob 식별
2. [**OS 디스크**에 연결된 VM 삭제](#step-2-delete-vm-to-detach-os-disk)
3. [나머지 VM에서 모든 **데이터 디스크** 분리](#step-3-detach-data-disk-from-the-vm)

다음 단계를 완료한 후 저장소 계정, 컨테이너 또는 Blob 삭제를 다시 시도합니다.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>1단계: VM에 연결된 Blob 식별

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>시나리오 1: Blob 삭제 - 연결된 VM 식별
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **모든 리소스**를 선택합니다. 저장소 계정으로 이동하고 **Blob 서비스**에서 **컨테이너**를 선택하고 삭제할 Blob으로 이동합니다.
3. BLOB **임대 단계**가 **임대됨**이면 마우스 오른쪽 단추를 클릭하고 **메타데이터 편집**을 선택하여 Blob 메타데이터 창을 엽니다. 

    ![스토리지 계정 BLOB 및 마우스 오른쪽 단추 클릭 > "메타 데이터 편집"이 강조 표시된 포털의 스크린샷](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. Blob 메타데이터 창에서 **MicrosoftAzureCompute_VMName** 값을 확인하고 기록합니다. 이 값은 VHD가 연결된 VM의 이름입니다 (이 필드가 없는 경우 **중요** 참조).
5. Blob 메타데이터 창에서 **MicrosoftAzureCompute_DiskType** 값을 확인하고 기록합니다. 이 값은 연결된 디스크가 OS인지 또는 데이터 디스크인지를 식별합니다(이 필드가 없는 경우 **중요** 참조). 

     ![저장소 "Blob 메타 데이터" 창이 열린 포털 스크린샷](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Blob 디스크 형식이 **OSDisk**이면 [2단계: VM을 삭제하여 OS 디스크 분리](#step-2-delete-vm-to-detach-os-disk)를 따릅니다. 그렇지 않고 Blob 디스크 형식이 **DataDisk**이면 [3단계: VM에서 데이터 디스크 분리](#step-3-detach-data-disk-from-the-vm)의 단계를 따릅니다. 

> [!IMPORTANT]
> **MicrosoftAzureCompute_VMName** 및 **MicrosoftAzureCompute_DiskType**이 Blob 메타데이터에 표시되지 않으면 이 Blob가 명시적으로 임대 상태이며 VM에 연결되지 않았음을 의미합니다. 임대된 Blob는 먼저 임대를 중단해야 삭제할 수 있습니다. 임대를 차단하려면 BLOB을 마우스 오른쪽 단추로 클릭하여 **임대 차단**을 선택합니다. VM에 연결되지 않은 임대된 BLOB은 BLOB의 삭제를 방지하지만 컨테이너 또는 저장소 계정의 삭제는 방지하지 않습니다.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>시나리오 2: 컨테이너 삭제 - VM에 연결된 컨테이너 내 모든 Blob 식별
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **모든 리소스**를 선택합니다. 저장소 계정의 **Blob Service**에서 **컨테이너**를 선택하고 삭제할 컨테이너를 찾습니다.
3. 클릭하여 표시되는 Blob의 목록과 컨테이너를 엽니다. Blob 형식이 **페이지 Blob**이고 임대 상태 = **임대**인 모든 Blob를 식별합니다. 시나리오 1에 따라 각각의 이러한 Blob에 연결된 VM을 식별합니다.

    ![저장소 계정 Blob 및 "임대 상태"가 "임대"로 강조 표시된 포털의 스크린샷](./media/troubleshoot-vhds/utd-disks-sm.png)

4. [2단계](#step-2-delete-vm-to-detach-os-disk) 및 [3단계](#step-3-detach-data-disk-from-the-vm)에 따라**OSDisk**가 있는 VM을 삭제하고**DataDisk**를 분리합니다. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>시나리오 3: 스토리지 계정 삭제 - VM에 연결된 스토리지 계정 내 모든 Blob 식별
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **모든 리소스**를 선택합니다. 스토리지 계정으로 이동하고 **Blob 서비스**에서 **Blob**을 선택합니다.
3. **컨테이너** 창에서 **임대 상태**가 **임대됨**인 모든 컨테이너를 식별하고 **임대된** 컨테이너에 대해 [시나리오 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms)를 수행합니다.
4. [2단계](#step-2-delete-vm-to-detach-os-disk) 및 [3단계](#step-3-detach-data-disk-from-the-vm)에 따라**OSDisk**가 있는 VM을 삭제하고**DataDisk**를 분리합니다. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>2단계: VM을 삭제하여 OS 디스크 분리
VHD가 OS 디스크인 경우 먼저 VM을 삭제해야 연결된 VHD를 삭제할 수 있습니다. 이 단계를 완료한 후에는 동일한 VM에 연결된 데이터 디스크에 대해 추가적인 작업이 필요하지 않습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **Virtual Machines**를 선택합니다.
3. VHD가 연결된 VM을 선택합니다.
4. 실제로 가상 머신을 사용 중인 항목이 없고 가상 머신이 더 이상 필요하지 않음을 확인합니다.
5. **가상 머신 세부 정보** 창의 맨 위에서 **삭제**를 선택한 후 **예**를 클릭하여 확인합니다.
6. VM은 삭제되지만 VHD는 유지될 수 있습니다. 그러나 VHD는 더 이상 VM에 연결되거나 임대를 포함하지 않아야 합니다. 임대를 해제하는 데는 몇 분 정도 걸릴 수 있습니다. 임대 해제를 확인하려면 Blob 위치로 이동한 다음 **Blob 속성** 창에서 **임대 상태**가 **사용 가능**인지 확인합니다.

## <a name="step-3-detach-data-disk-from-the-vm"></a>3단계: VM에서 데이터 디스크 분리
VHD가 데이터 디스크인 경우 VM에서 VHD를 분리하여 임대를 제거합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **Virtual Machines**를 선택합니다.
3. VHD가 연결된 VM을 선택합니다.
4. **가상 머신 세부 정보** 창에서 **디스크**를 선택합니다.
5. VHD가 연결된 삭제할 데이터 디스크를 선택합니다. VHD의 URL을 확인하여 디스크에 연결된 Blob를 확인할 수 있습니다.
6. 디스크를 클릭하고 **VHD URI** 필드에서 경로를 확인하여 Blob 위치를 확인할 수 있습니다.
7. **디스크** 창 상단에서 **편집**을 선택합니다.
8. 삭제할 데이터 디스크의 **분리 아이콘**을 클릭합니다.

     ![저장소 "Blob 메타 데이터" 창이 열린 포털 스크린샷](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. **저장**을 선택합니다. 이제 디스크가 VM에서 분리되고 VHD가 더 이상 임대되지 않습니다. 임대를 해제하는 데는 몇 분 정도 걸릴 수 있습니다. 임대 해제를 확인하려면 Blob 위치로 이동한 다음 **Blob 속성** 창에서 **임대 상태**가 **잠금 해제** 또는 **사용 가능**인지 확인합니다.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

