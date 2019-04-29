---
title: Azure 가상 머신 확장 및 기능 | Microsoft Docs
description: Azure VM 확장 및 Azure 가상 머신에서 이 확장을 사용하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: a35cba0ab7df80596ba1403765980809635c0249
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617999"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 가상 머신 확장 및 기능
Azure VM(Azure 가상 머신) 확장은 Azure VM에서 배포 후 구성 및 자동화 작업을 제공하는 작은 애플리케이션이며, 기존 이미지를 사용하여 배포의 일부로 사용자 지정함으로써 사용자 지정 이미지를 작성하는 업무에서 벗어나게 할 수 있습니다.

Azure 플랫폼은 VM 구성, 모니터링, 보안 및 유틸리티 애플리케이션에 이르는 다양한 확장을 호스팅합니다. 게시자는 애플리케이션을 가져온 다음, 확장으로 래핑하고, 설치를 간소화하므로 필수 매개 변수를 제공하기만 하면 됩니다. 

 자체 및 타사 확장을 선택할 수 있습니다. 확장 리포지토리의 애플리케이션이 없는 경우 사용자 지정 스크립트 확장을 사용하고 자체 스크립트와 명령으로 VM을 구성할 수 있습니다.

확장이 사용되는 주요 시나리오의 예는 다음과 같습니다.
* VM 구성 - Powershell DSC(Desired State Configuration), Chef, Puppet 및 사용자 지정 스크립트 확장을 사용하여 VM 구성 에이전트를 설치하고 VM을 구성할 수 있습니다. 
* AV 제품(예: Symantec, ESET)
* VM 취약성 도구(예: Qualys, Rapid7, HPE)
* VM 및 앱 모니터링 도구(예: DynaTrace, Azure Network Watcher, Site24x7 및 Stackify)

확장은 새 VM 배포와 함께 번들로 묶을 수 있습니다. 예를 들어 대규모 배포의 일부이거나, VM 프로비전에서 애플리케이션을 구성하거나, 배포 후 지원되는 모든 확장 운영 체제에서 실행할 수 있습니다.

## <a name="how-can-i-find-what-extensions-are-available"></a>사용할 수 있는 확장을 찾으려면 어떻게 할까요?
포털의 확장 아래에 있는 VM 블레이드에서 사용 가능한 확장이 표시됩니다. 여기서는 약간의 확장만 표시되지만, CLI 도구를 사용하면 전체 목록을 확인할 수 있습니다. [Linux용 VM 확장 검색](features-linux.md) 및 [Windows용 VM 확장 검색](features-windows.md)을 참조하세요.

## <a name="how-can-i-install-an-extension"></a>확장을 설치하려면 어떻게 해야 할까요?
Azure VM 확장은 Azure CLI, Azure PowerShell, Azure Resource Manager 템플릿 및 Azure Portal 중 하나를 사용하여 관리할 수 있습니다. 확장을 사용하려면 Azure Portal로 이동하여 사용자 지정 스크립트 확장을 선택한 다음, 명령/스크립트를 전달하고, 확장을 실행합니다.

CLI 또는 Resource Manager 템플릿으로 포털에 추가한 것과 동일한 확장을 원하는 경우, [Windows 사용자 지정 스크립트 확장](custom-script-windows.md) 및 [Linux 사용자 지정 스크립트 확장](custom-script-linux.md)과 같은 다른 확장 설명서를 참조하세요.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>확장 애플리케이션 수명 주기를 관리하려면 어떻게 해야 할까요?
VM에 직접 연결하여 확장을 설치하거나 삭제할 필요가 없습니다. Azure 확장 애플리케이션 수명 주기는 VM 외부에서 관리되고 Azure 플랫폼에 통합되므로 확장에 대한 통합 상태도 얻을 수 있습니다.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>확장에 대해 고려해야 할 다른 사항은 무엇인가요?
확장은 몇 가지 요구 사항이 있는 애플리케이션과 같은 애플리케이션을 설치하고, 확장에 지원되는 Windows 및 Linux OS 목록이 있으며, Azure VM 에이전트를 설치해야 합니다. 일부 개별 VM 확장 애플리케이션에는 엔드포인트 액세스와 같은 자체 환경 필수 구성 요소가 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계
* Linux 에이전트 및 확장의 작동 방식에 대한 자세한 내용은 [Linux용 Azure VM 확장 및 기능](features-linux.md)을 참조하세요.
* Windows 게스트 에이전트 및 확장의 작동 방식에 대한 자세한 내용은 [Windows용 Azure VM 확장 및 기능](features-windows.md)을 참조하세요.  
* Windows 게스트 에이전트를 설치 하려면 [Azure Windows Virtual Machine 에이전트 개요](agent-windows.md)합니다.  
* Linux 에이전트를 설치 하려면 [Azure Linux Virtual Machine 에이전트 개요](agent-linux.md)합니다.  

