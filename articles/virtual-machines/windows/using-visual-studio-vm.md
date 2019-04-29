---
title: Azure 가상 머신에서 Visual Studio 사용 | Microsoft Docs
description: Azure 가상 머신에서 Visual Studio를 사용합니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 04/02/2019
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: df91ba12c1ee74b8e28f75344a5cd55af018d3cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583339"
---
# <a name="visual-studio-images-on-azure"></a>Azure의 Visual Studio 이미지
미리 구성된 Azure VM(가상 머신)에서 Visual Studio를 사용하면 아무 작업도 하지 않고 실행 중인 개발 환경으로 빠르고 쉽게 이동할 수 있습니다. 다양한 Visual Studio 구성이 적용된 시스템 이미지를 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=visual%20studio&page=1)에서 사용할 수 있습니다.

Azure를 처음 사용하세요? [평가판 Azure 계정을 만듭니다](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>어떤 구성 및 버전을 사용할 수 있나요?
Azure Marketplace에서 가장 최근의 주 버전인 Visual Studio 2019, Visual Studio 2017 및 Visual Studio 2015에 대 한 이미지를 찾을 수 있습니다.  각 주 버전 출시에 대 한 참조를 원래 "웹 출시" (RTW) 버전과 최신 업데이트 된 버전입니다.  이러한 각 버전에는 Visual Studio Enterprise 및 Visual Studio Community Edition이 제공됩니다.  이 이미지는 최신 Visual Studio 및 Windows 업데이트가 포함되도록 최소한 매월 업데이트됩니다.  이미지의 이름은 그대로 유지되지만, 각 이미지의 설명에는 설치된 제품 버전 및 이미지의 '당시' 날짜가 포함됩니다.

| 릴리스 버전                                              | 에디션                     |     제품 버전      |
|:------------------------------------------------------------:|:----------------------------:|:------------------------:|
|     Visual Studio 2019: RTW                                  |    Enterprise, Community     |      16.0.0 버전      |
| Visual Studio 2017: 최신(버전 15.9)                    |    Enterprise, Community     |      15.9.10 버전     |
|         Visual Studio 2017: RTW                              |    Enterprise, Community     |      15.0.22 버전     |
|   Visual Studio 2015: 최신(Update 3)                      |    Enterprise, Community     |  버전 14.0.25431.01   |
|         Visual Studio 2015: RTW                              |             없음             | (서비스를 위해 만료됨)  |

> [!NOTE]
> Microsoft 서비스 정책에 따라 최초 릴리스(RTW) 버전의 Visual Studio 2015는 서비스 만료되었습니다. Visual Studio 2015 업데이트 3은 Visual Studio 2015 제품 라인에 대해 제공되는 유일하게 남은 버전입니다.

자세한 내용은 [Visual Studio 서비스 정책](https://www.visualstudio.com/productinfo/vs-servicing-vs)을 참조하세요.

## <a name="what-features-are-installed"></a>어떤 기능이 설치되나요?
각 이미지는 해당 Visual Studio 에디션에 대해 권장되는 기능 집합을 포함합니다. 일반적으로 설치에는 다음이 포함됩니다.

* 해당 워크로드의 권장되는 선택적 구성 요소를 포함하는 모든 사용 가능한 워크로드
* .NET 4.6.2 및 4.7.NET SDK, 대상 지정 팩 및 개발자 도구
* Visual F#
* Visual Studio용 GitHub 확장
* LINQ to SQL 도구

이미지를 빌드할 때 Visual Studio를 설치하는 데 사용되는 명령줄은 다음과 같습니다.

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

이미지에 필요한 Visual Studio 기능이 포함되어 있지 않으면 페이지의 오른쪽 위 모서리에 있는 피드백 도구를 통해 피드백을 주시기 바랍니다.

## <a name="what-size-vm-should-i-choose"></a>어떤 VM 크기를 선택해야 하나요?
Azure에는 모든 범위의 가상 머신 크기가 제공됩니다. Visual Studio는 강력한 다중 스레드 애플리케이션이므로, 2개 이상의 프로세서와 7GB의 메모리가 포함된 VM 크기가 필요합니다. Visual Studio 이미지에 대해 다음 VM 크기가 권장됩니다.

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
최신 컴퓨터 크기에 대한 자세한 내용은 [Azure에서 Windows 가상 컴퓨터에 대한 크기](/azure/virtual-machines/windows/sizes)를 참조하세요.

Azure를 사용하면 VM의 크기를 조정하여 처음에 선택한 내용을 다시 조정할 수 있습니다. 좀 더 적절한 크기를 사용하여 새 VM을 프로비전할 수도 있고, 기존 VM의 크기를 다른 기본 하드웨어에 맞게 조정할 수도 있습니다. 자세한 내용은 [Windows VM 크기 조정](/azure/virtual-machines/windows/resize-vm)을 참조하세요.

## <a name="after-the-vm-is-running-whats-next"></a>VM을 실행한 후 다음 단계는 무엇인가요?
Visual Studio는 Azure의 "BYOL(사용자 라이선스 필요)" 모델을 따릅니다. 전용 하드웨어를 설치하는 것과 마찬가지로, 첫 번째 단계 중 하나는 Visual Studio 설치의 사용을 허가하는 것입니다. Visual Studio의 잠금을 해제하려면 다음 중 하나를 수행합니다.
- Visual Studio 구독과 연결된 Microsoft 계정으로 로그인합니다. 
- 초기 구매 시 제공된 제품 키로 Visual Studio의 잠금을 해제합니다.

자세한 내용은 [Visual Studio에 로그인](/visualstudio/ide/signing-in-to-visual-studio) 및 [Visual Studio 잠금 해제 방법](/visualstudio/ide/how-to-unlock-visual-studio)을 참조하세요.

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>나중에 또는 팀에서 사용하도록 개발 VM을 저장하려면 어떻게 해야 하나요?

개발 환경의 스펙트럼은 방대하며, 좀 더 복잡한 환경을 구축하게 되면 실질적인 비용이 발생합니다. 환경 구성에 관계없이 나중에 사용할 수 있도록 또는 팀의 다른 멤버를 위해 구성된 VM을 "기본 이미지"로 저장하거나 캡처할 수 있습니다. 그런 다음, 새 VM을 부팅할 때, Azure Marketplace 이미지가 아닌 기본 이미지에서 프로비전하면 됩니다.

빠른 요약: 시스템 준비 도구(Sysprep)를 사용하여 실행 중인 VM을 종료한 다음, Azure Portal의 UI를 통해 VM을 이미지 *(그림 1)* 로 캡처합니다. Azure에서는 선택한 저장소 계정의 이미지를 포함하는 `.vhd` 파일을 저장합니다. 그러면 새 이미지가 구독의 리소스 목록에 이미지 리소스로 표시됩니다.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(그림 1) Azure Portal의 UI를 통해 이미지를 캡처합니다.*</center>

자세한 내용은 [Azure에서 일반화된 VM의 관리 이미지 만들기](/azure/virtual-machines/windows/capture-image-resource)를 참조하세요.

> [!IMPORTANT]
> VM을 준비하려면 Sysprep 사용을 잊지 마세요. 이 단계를 누락하면 Azure가 이미지에서 VM을 프로비전할 수 없습니다.

> [!NOTE]
> 이미지를 저장하는 데 약간의 비용이 발생하지만 이러한 추가 비용은 VM이 필요한 각 팀 멤버들이 VM을 처음부터 다시 구축하는 데 드는 오버헤드 비용에 비하면 미미할 수 있습니다. 예를 들어, 전체 팀에서 재사용할 수 있는 127GB 이미지를 만들고 1달 동안 저장하는 데는 몇 달러가 필요합니다. 그러나 이러한 비용은 각 직원이 개인적으로 사용하기 위해 적절히 구성된 개발 박스를 빌드하고 유효성을 검사하기 위해 투입하는 시간에 비하면 별로 크지 않습니다.

또한 개발 태스크나 기술에는 다양한 개발 구성 및 여러 컴퓨터 구성과 같은 더 큰 규모가 필요할 수 있습니다. Azure DevTest Labs를 사용하여 '골든 이미지'의 생성을 자동화하는 레시피를 만들 수 있습니다. DevTest Labs를 사용하여 팀의 실행 중인 VM에 대한 정책을 관리할 수도 있습니다. [개발자용 Azure DevTest Lab을 사용](/azure/devtest-lab/devtest-lab-developer-lab)하는 것이 DevTest Labs에 대한 자세한 내용을 확인하는 최선의 방법입니다.

## <a name="next-steps"></a>다음 단계
미리 구성된 Visual Studio 이미지에 대해 잘 알게 되었으므로 다음 단계는 새 VM을 만드는 것입니다.

* [Azure Portal을 통해 VM 만들기](quick-create-portal.md)
* [Windows 가상 머신 개요](overview.md)
