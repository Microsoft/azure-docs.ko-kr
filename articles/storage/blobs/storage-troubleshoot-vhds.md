---
title: Azure VM에 연결된 디스크 문제 해결 | Microsoft Docs
description: Azure Virtual Machine VHD(가상 하드 디스크)에 대한 리소스 문제를 해결하기 위한 링크를 제공합니다.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 10/31/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098102"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure VM에 연결된 디스크 문제 해결 

Azure VM(Virtual Machines)은 OS 디스크 및 연결된 모든 데이터 디스크에 VHD(가상 하드 디스크)를 사용합니다. VHD는 하나 이상의 Azure Storage 계정에 페이지 Blob으로 저장됩니다. 이 문서에서는 VHD에서 발생할 수 있는 일반적인 문제 해결 방법을 설명합니다. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>VM의 저장소 리소스 삭제 오류 문제 해결

경우에 따라 연결된 VHD가 Resource Manager 배포의 VM에 들어 있을 때 저장소 리소스를 삭제하면 오류가 발생할 수 있습니다. 이 문제를 해결하기 위한 도움말은 다음 문서 중 하나를 참조하세요. 

  * Linux vm: [Resource Manager 배포의 저장소 삭제 오류](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows vm: [Resource Manager 배포의 저장소 삭제 오류](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>VHD가 연결된 VM이 예기치 않게 다시 부팅되는 문제 해결

많은 VHD가 연결된 VM이 예기치 않게 재부팅되는 경우 다음 문서 중 하나를 참조하세요.

  * Linux vm: [VHD가 연결된 VM이 예기치 않게 다시 부팅되는 경우](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows vm: [VHD가 연결된 VM이 예기치 않게 다시 부팅되는 경우](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
