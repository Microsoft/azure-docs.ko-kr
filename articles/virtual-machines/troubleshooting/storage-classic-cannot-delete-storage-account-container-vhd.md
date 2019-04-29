---
title: Azure 클래식 Storage 계정, 컨테이너 또는 VHD를 삭제하는 경우 발생하는 오류 문제 해결 | Microsoft Docs
description: 연결된 VHD가 포함된 저장소 리소스를 삭제할 때 발생하는 문제 해결 방법입니다.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 673101ad7f55969c216adf7e970402a2109f8254
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864284"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>클래식 스토리지 리소스 삭제 오류 문제 해결
이 문서에서는 Azure 클래식 Storage 계정, 컨테이너 또는 *.vhd 페이지 Blob 파일을 삭제하려고 할 때 다음 오류 중 하나가 발생할 경우의 문제 해결 지침을 제공합니다. 


이 문서에서는 클래식 스토리지 리소스의 문제만 다룹니다. Azure Portal, PowerShell 또는 CLI를 사용하여 클래식 가상 머신을 삭제하는 경우에는 디스크가 자동으로 삭제되지 않습니다. "디스크" 리소스를 삭제하는 옵션이 제공됩니다. 이 옵션을 선택하지 않으면 "디스크" 리소스는 스토리지 계정, 컨테이너 및 실제 *.vhd 페이지 blob 파일의 삭제를 방지합니다.

Azure 디스크에 관한 자세한 내용은 [여기](../../virtual-machines/windows/managed-disks-overview.md)에서 찾을 수 있습니다. Azure는 손상 방지를 위해 VM에 연결된 디스크 삭제를 차단합니다. 또한 VM에 연결된 페이지 Blob가 있는 스토리지 계정 및 컨테이너의 삭제도 차단합니다. 

## <a name="what-is-a-disk"></a>"Disk"란?
"Disk" 리소스는 *.vhd 페이지 blob 파일을 가상 머신에 OS 디스크 또는 데이터 디스크로 탑재하는 데 사용합니다. OS 디스크 또는 데이터 디스크 리소스는 삭제할 때까지 *.vhd 파일에 대한 임대를 계속 유지합니다. 아래 이미지에 표시된 경로의 모든 스토리지 리소스는 “디스크” 리소스가 가리키고 있으면 삭제할 수 없습니다.

![디스크(클래식) “속성” 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>클래식 가상 머신 삭제 동안의 단계 
1. 클래식 가상 머신을 삭제합니다.
2. "디스크" 확인란을 선택한 경우 페이지 Blob *.vhd와 연결된 **디스크 임대**(위의 그림에 표시)가 중단됩니다. 실제 페이지 Blob *.vhd 파일은 스토리지 계정에 여전히 존재합니다.
![가상 머신(클래식) "삭제" 오류 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. 디스크 임대가 중단되면 페이지 Blob 자체를 삭제할 수 있습니다. 스토리지 계정 또는 컨테이너는 포함된 모든 "디스크" 리소스가 삭제되면 삭제할 수 있습니다.

>[!NOTE] 
>사용자가 VM은 삭제하고 VHD는 삭제하지 않으면 페이지 blob *.vhd 파일에 대해 스토리지 요금이 계속 부과됩니다. 요금은 스토리지 계정 유형에 따라 좌우됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/en-us/pricing/details/storage/)를 참조하세요. 사용자가 더 이상 VHD를 사용하지 않을 예정이면 향후에 요금이 부과되지 않도록 삭제합니다. 

## <a name="unable-to-delete-storage-account"></a>스토리지 계정을 삭제할 수 없음 

사용자가 더 이상 필요하지 않은 클래식 스토리지 계정을 삭제하려고 하면 다음과 같은 동작이 나타날 수 있습니다.

#### <a name="azure-portal"></a>Azure portal 
[Azure Portal](https://portal.azure.com)에서 클래식 스토리지 계정으로 이동한 후 **삭제**를 클릭하면 다음 메시지가 표시됩니다. 

디스크가 가상 머신에 “연결된”된 경우

![가상 머신(클래식) "삭제" 오류 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


디스크가 가상 머신에 “연결되지 않은” 경우

![가상 머신(클래식) "삭제" 비오류 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
사용자는 클래식 PowerShell cmdlet을 사용하여 더 이상 사용되고 있지 않은 스토리지 계정을 삭제하려고 합니다. 다음과 같은 메시지가 표시됩니다.

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: 스토리지 계정 myclassicaccount에 활성 이미지 및/또는 디스크(예:  
> myclassicaccount)가 있습니다. 이 저장소 계정을 삭제하려면 이러한 이미지 및/또는 디스크를 제거해야 합니다.</span>

## <a name="unable-to-delete-storage-container"></a>스토리지 컨테이너를 삭제할 수 없음

사용자가 더 이상 필요하지 않은 클래식 스토리지 Blob 컨테이너를 삭제하려고 하면 다음과 같은 동작이 나타날 수 있습니다.

#### <a name="azure-portal"></a>Azure portal 
Azure Portal은 컨테이너의 *.vhd 페이지 blob 파일을 가리키는 "디스크" 임대가 있는 경우 컨테이너를 삭제할 수 없도록 합니다. 기본적으로 디스크 임대가 있는 vhd 파일을 실수로 삭제하지 않도록 합니다. 

![스토리지 컨테이너 "목록" 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
사용자가 PowerShell을 사용하여 삭제하도록 선택하면 다음 오류가 발생합니다. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : 원격 서버에서 오류를 반환했습니다. (412) 현재 컨테이너에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다. HTTP 상태 코드: 412 - HTTP 오류 메시지: 현재 컨테이너에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다.</span>

## <a name="unable-to-delete-a-vhd"></a>vhd를 삭제할 수 없음 

Azure Virtual Machine을 삭제한 후 vhd 파일(페이지 blob)을 삭제하려고 하면 아래 메시지가 수신됩니다.

#### <a name="azure-portal"></a>Azure portal 
포털에서 삭제용으로 선택한 blob 목록에 따라 두 가지 환경이 있을 수 있습니다.

1. "임대한" blob만 선택하면 삭제 단추가 표시되지 않습니다.
![컨테이너 blob "목록" 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. "임대" 및 "사용 가능" blob을 혼합해서 선택하면 "삭제" 단추가 표시됩니다. 하지만 "삭제" 작업을 수행한 후에 디스크 임대가 있는 페이지 blob이 남아 있습니다. 
![컨테이너 blob "목록" 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![선택한 blob “삭제” 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
사용자가 PowerShell을 사용하여 삭제하도록 선택하면 다음 오류가 발생합니다. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : 원격 서버에서 오류를 반환했습니다. (412) 현재 Blob에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다. HTTP 상태 코드: 412 - HTTP 오류 메시지: 현재 Blob에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다.</span>


## <a name="resolution-steps"></a>해결 단계:

### <a name="to-remove-classic-disks"></a>클래식 디스크를 제거하려면
Azure Portal에서 다음 단계를 따릅니다.
1.  [Azure Portal](https://portal.azure.com)로 이동합니다.
2.  디스크(클래식)로 이동합니다. 
3.  디스크 탭을 클릭합니다. ![컨테이너 blob "목록" 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  데이터 디스크를 선택한 다음 디스크 삭제를 클릭합니다.
 ![컨테이너 blob "목록" 창이 열려 있는 포털 스크린샷](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  이전에 실패한 삭제 작업을 다시 시도하세요.
6.  단일 디스크를 포함하는 경우 스토리지 계정 또는 컨테이너를 삭제할 수 없습니다.

### <a name="to-remove-classic-images"></a>클래식 이미지를 제거하려면   
Azure Portal에서 다음 단계를 따릅니다.
1.  [Azure Portal](https://portal.azure.com)로 이동합니다.
2.  OS 이미지(클래식)로 이동합니다.
3.  이미지를 삭제합니다.
4.  이전에 실패한 삭제 작업을 다시 시도하세요.
5.  단일 이미지를 포함하는 경우 스토리지 계정 또는 컨테이너를 삭제할 수 없습니다.
