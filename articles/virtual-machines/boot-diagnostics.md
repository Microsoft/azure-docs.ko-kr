---
title: Azure 부팅 진단
description: Azure 부팅 진단 및 관리 되는 부팅 진단 개요
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d425953b278a98af35a172d8777ab758db52709e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257758"
---
# <a name="azure-boot-diagnostics"></a>Azure 부팅 진단

부팅 진단은 VM 부팅 실패를 진단할 수 있도록 하는 Azure VM (가상 머신)에 대 한 디버깅 기능입니다. 부팅 진단을 통해 사용자는 직렬 로그 정보와 스크린샷을 수집 하 여 부팅 하는 VM의 상태를 관찰할 수 있습니다.

## <a name="boot-diagnostics-storage-account"></a>부트 진단 저장소 계정
Azure Portal에서 VM을 만들 때 부트 진단은 기본적으로 사용 하도록 설정 됩니다. 권장 되는 부팅 진단 환경은 Azure VM을 만들 때 상당한 성능 향상을 제공 하므로 관리 되는 저장소 계정을 사용 하는 것입니다. 이는 Azure 관리 저장소 계정이 사용 되므로 부팅 진단 데이터를 저장 하는 새 사용자 저장소 계정을 만드는 데 걸리는 시간이 제거 되기 때문입니다.

대체 부팅 진단 환경은 사용자 관리 저장소 계정을 사용 하는 것입니다. 사용자는 새 저장소 계정을 만들거나 기존 저장소 계정을 사용할 수 있습니다.

> [!IMPORTANT]
> Azure 고객은 10 월 2020 일까 지 관리 되는 저장소 계정을 사용 하는 부팅 진단으로 associted 저장소 비용에 대 한 요금이 부과 되지 않습니다.

## <a name="boot-diagnostics-view"></a>부트 진단 보기
가상 컴퓨터 블레이드에 있는 부팅 진단 옵션은 Azure Portal의 *지원 및 문제 해결* 섹션 아래에 있습니다. 부트 진단을 선택 하면 스크린샷 및 일련 로그 정보가 표시 됩니다. 직렬 로그는 커널 메시징을 포함 하 고 스크린샷은 Vm의 현재 상태에 대 한 스냅숏입니다. VM에서 Windows 또는 Linux를 실행 하 고 있는지 여부에 따라 예상 되는 스크린샷은 어떻게 나타나는지 결정 합니다. Windows의 경우 사용자에 게 데스크톱 배경 및 Linux가 표시 됩니다. 사용자에 게 로그인 프롬프트가 표시 됩니다.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 부팅 진단 스크린샷":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 부팅 진단 스크린샷":::


## <a name="limitations"></a>제한 사항
- 부트 진단은 Azure Resource Manager Vm에만 사용할 수 있습니다. 
- 부팅 진단은 premium storage 계정을 지원 하지 않습니다. premium storage 계정이 부팅 진단에 사용 되 면 `StorageAccountTypeNotSupported` VM을 시작할 때 오류가 발생 합니다. 
- 현재 Azure 직렬 콘솔은 부트 진단을 위해 관리 되는 저장소 계정과 호환 되지 않습니다. [Azure 직렬 콘솔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[Azure 직렬 콘솔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) 에 대 한 자세한 내용과 부팅 진단을 사용 하 여 [azure의 가상 컴퓨터 문제를 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)하는 방법을 알아보세요.
