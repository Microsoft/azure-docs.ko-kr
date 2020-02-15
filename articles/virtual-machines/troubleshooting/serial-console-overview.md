---
title: Azure 직렬 콘솔 | Microsoft Docs
description: SSH 또는 RDP를 사용할 수 없는 경우 Azure 직렬 콘솔을 사용 하 여 VM에 연결할 수 있습니다.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153862"
---
# <a name="azure-serial-console"></a>Azure 직렬 콘솔

Azure Portal의 직렬 콘솔에서는 Linux 또는 Windows를 실행 하는 가상 머신 (Vm) 및 가상 머신 확장 집합 인스턴스의 텍스트 기반 콘솔에 대 한 액세스를 제공 합니다. 이 직렬 연결은 VM 또는 가상 머신 확장 집합 인스턴스의 ttyS0 또는 COM1 직렬 포트에 연결 하 여 네트워크 또는 운영 체제 상태에 관계 없이 액세스를 제공 합니다. 직렬 콘솔은 Azure Portal를 사용 하 여 액세스할 수 있으며, 참가자 이상의 액세스 역할이 있는 사용자 (VM 또는 가상 머신 확장 집합)에 대해서만 허용 됩니다.

직렬 콘솔은 Vm 및 가상 머신 확장 집합 인스턴스에 대해 동일한 방식으로 작동 합니다. 이 문서에서 Vm에 대 한 모든 멘 션은 별도로 명시 되지 않는 한 가상 머신 확장 집합 인스턴스를 암시적으로 포함 합니다.

> [!NOTE]
> 직렬 콘솔은 일반적으로 글로벌 Azure 지역 및 Azure Government의 공개 미리 보기에서 사용할 수 있습니다. Azure 중국 클라우드에서는 아직 사용할 수 없습니다.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Azure 직렬 콘솔에 액세스 하기 위한 필수 구성 요소
VM 또는 가상 머신 확장 집합 인스턴스에서 직렬 콘솔에 액세스 하려면 다음이 필요 합니다.

- VM에 대해 부트 진단을 사용 하도록 설정 해야 합니다.
- 암호 인증을 사용 하는 사용자 계정은 VM 내에 존재 해야 합니다. VM 액세스 확장의 [암호 재설정](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) 기능을 사용 하 여 암호 기반 사용자를 만들 수 있습니다. **지원 + 문제 해결** 섹션에서 **암호 재설정**을 선택합니다.
- 직렬 콘솔에 액세스 하는 Azure 계정에 VM 및 [부트 진단](boot-diagnostics.md) 저장소 계정 모두에 대 한 [가상 머신 참가자 역할이](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 있어야 합니다.

> [!NOTE]
> 클래식 배포는 지원되지 않습니다. VM 또는 가상 머신 확장 집합 인스턴스는 Azure Resource Manager 배포 모델을 사용 해야 합니다.

## <a name="get-started-with-the-serial-console"></a>직렬 콘솔 시작
Vm 및 가상 머신 확장 집합에 대 한 직렬 콘솔은 Azure Portal를 통해서만 액세스할 수 있습니다.

### <a name="serial-console-for-virtual-machines"></a>Virtual Machines 용 직렬 콘솔
Vm 용 직렬 콘솔은 Azure Portal의 **지원 + 문제 해결** 섹션 내에서 **직렬 콘솔** 를 클릭 하는 것 만큼 간단 합니다.
  1. [Azure Portal](https://portal.azure.com)을 엽니다.

  1. **모든 리소스** 로 이동 하 여 가상 컴퓨터를 선택 합니다. VM에 대한 개요 페이지가 열립니다.

  1. **지원 + 문제 해결** 섹션까지 아래로 스크롤하여 **직렬 콘솔**을 선택합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

     ![Linux 직렬 콘솔 창](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets 용 직렬 콘솔
직렬 콘솔은 확장 집합 내의 각 인스턴스에서 액세스할 수 있는 가상 머신 확장 집합에 사용할 수 있습니다. **직렬 콘솔** 단추를 표시 하기 전에 가상 머신 확장 집합의 개별 인스턴스로 이동 해야 합니다. 가상 머신 확장 집합에 부트 진단이 사용 하도록 설정 되어 있지 않으면 가상 머신 확장 집합 모델을 업데이트 하 여 부팅 진단을 사용 하도록 설정 하 고 직렬 콘솔에 액세스 하기 위해 모든 인스턴스를 새 모델로 업그레이드 해야 합니다.
  1. [Azure Portal](https://portal.azure.com)을 엽니다.

  1. **모든 리소스** 로 이동 하 여 가상 머신 확장 집합을 선택 합니다. 가상 머신 확장 집합에 대 한 개요 페이지가 열립니다.

  1. **인스턴스로** 이동

  1. 가상 머신 확장 집합 인스턴스를 선택 하십시오.

  1. **지원 + 문제 해결** 섹션에서 **직렬 콘솔**을 선택 합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

     ![Linux 가상 머신 확장 집합 직렬 콘솔](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>직렬 콘솔에 대 한 고급 사용
콘솔에서 VM에 액세스 하는 것 외에도 Azure 직렬 콘솔을 사용 하 여 다음을 수행할 수 있습니다.
* [VM에 시스템 요청 명령](./serial-console-nmi-sysrq.md) 보내기
* [마스크 불가능 인터럽트를 VM에](./serial-console-nmi-sysrq.md) 보내기
* [VM을 정상적으로 다시 부팅 하거나 강제로 전원 순환](./serial-console-power-options.md)


## <a name="next-steps"></a>다음 단계
추가 직렬 콘솔 설명서는 사이드바에서 다운로드할 수 있습니다.
- [Linux vm 용 직렬 콘솔](./serial-console-linux.md)에 대 한 자세한 정보를 확인할 수 있습니다.
- [Windows vm 용 직렬 콘솔](./serial-console-windows.md)에 대 한 자세한 정보를 확인할 수 있습니다.
