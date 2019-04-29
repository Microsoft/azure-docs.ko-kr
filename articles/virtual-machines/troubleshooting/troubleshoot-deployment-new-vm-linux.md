---
title: Linux VM 배포 문제 해결 | Microsoft Docs
description: Azure에서 새 Linux 가상 머신 생성 시 Resource Manager 배포 문제 해결
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 9fea914fdf9b025fd5d38219a6bfc81b4a9cc584
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125619"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Azure에서 새 Linux 가상 머신 생성 관련 Resource Manager 배포 문제 해결
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>주요 문제
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

VM 배포 문제 및 질문은 [Azure에서 Linux 가상 머신 배포 문제 해결](troubleshoot-deploy-vm-linux.md)을 참조하세요.

## <a name="collect-activity-logs"></a>활동 로그 선택
문제 해결을 시작하려면 문제와 관련된 오류를 파악하기 위해 활동 로그를 수집합니다. 다음 링크에는 수행할 프로세스에 대한 자세한 내용이 포함되어 있습니다.

[배포 작업 보기](../../azure-resource-manager/resource-manager-deployment-operations.md)

[활동 로그를 보고 Azure 리소스 관리](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** OS가 일반화 된 Linux 업로드 되거나 일반화 된 설정을 사용 하 여 캡처할 경우 다음 없습니다 오류. 마찬가지로 OS가 특수화된 Linux인 경우, 특수화된 설정을 사용하여 업로드되거나 캡처되고, 오류는 발생하지 않습니다.

**업로드 오류:**

**N<sup>1</sup>:** OS가 일반화 된 Linux 및 것으로 업로드 하는 경우 특수화 된 하면 프로 비전 시간 초과 오류가 있으므로 VM 프로 비전 단계에서 중단 됩니다.

**N<sup>2</sup>:** OS가 특수화 된 Linux이 고 일반화 된 것으로 업로드 됩니다, 경우 원래 컴퓨터 이름, 사용자 이름 및 암호를 사용 하 여 새 VM이 실행 되기 때문에 프로 비전 실패 오류가 받습니다.

**해결 방법:**

모두 이러한 오류를 해결 하려면 온-프레미스와 같은 설정 (일반화/특수화) OS로 사용할 수 있는 원본 VHD를 업로드 합니다. 일반화된 것으로 업로드하려면, 먼저 프로비전 해제를 실행해야 합니다.

**캡처 오류:**

**N<sup>3</sup>:** OS가 일반화 된 Linux이 고 것으로 캡처된다 면 특수화 된 하면 프로 비전 시간 초과 오류가 있으므로 원래 VM을 일반화 된 것으로 표시 되어 있으므로 사용할 수 없습니다.

**N<sup>4</sup>:** OS가 특수화 된 Linux이 고 것 일반화 된 것으로 캡처된다 면, 하는 경우 원래 컴퓨터 이름, 사용자 이름 및 암호를 사용 하 여 새 VM이 실행 되기 때문에 프로 비전 실패 오류가 받습니다. 또한, 원본 VM이 일반화된 것으로 표시되기 때문에 사용할 수 없습니다.

**해결 방법:**

이 두 가지 오류를 모두 해결하려면, 현재 이미지를 포털에서 제거하고, OS와 같은 설정(일반화/특수화)으로 [현재 VHD에서 다시 캡처](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다.

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>문제: 사용자 지정 / 갤러리 / 마켓플레이스 이미지; 할당 오류입니다.
이 오류는 요청되는 VM 크기를 지원할 수 없거나 요청을 수용할 여유 공간이 없는 클러스터에 새 VM 요청이 고정된 상황에서 발생합니다.

**원인 1:** 클러스터가 요청된 VM 크기를 지원할 수 없습니다.

**해결 방법 1:**

* 더 작은 VM 크기를 사용하여 요청을 다시 시도합니다.
* 요청한 VM의 크기를 변경할 수 없으면:
  * 가용성 집합의 VM을 모두 중지합니다.
    **리소스 그룹** > *사용자의 리소스 그룹* > **리소스** > *사용자의 가용성 집합* > **Virtual Machines** > *사용자의 가상 머신* > **중지**를 클릭합니다.
  * VM을 모두 중지한 후에, 원하는 크기로 VM을 새로 만듭니다.
  * 먼저 VM을 시작한 후에 중지된 각각의 VM을 선택하고 **시작**을 클릭합니다.

**원인 2:** 클러스터에 여유 리소스가 없습니다.

**해결 방법 2:**

* 요청을 나중에 다시 시도합니다.
* 새 VM이 다른 가용성 집합의 일부가 될 수 있다면
  * 다른 가용성 집합(동일한 지역의)에 새 VM을 만듭니다.
  * 새 VM을 동일한 가상 네트워크에 추가합니다.

## <a name="next-steps"></a>다음 단계
중지된 Linux VM을 시작하거나 Azure에서 기존 Linux VM의 크기를 조정할 때 문제가 발생하면 [Azure의 기존 Linux Virtual Machine 재시작 또는 크기 조정 관련 Resource Manager 배포 문제 해결](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

