---
title: VM 및 PaaS 테스트 환경에 Azure DevTest Labs 사용 | Microsoft Docs
description: VM 및 PaaS 테스트 환경 시나리오에 Azure DevTest Labs를 사용하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c6b458091a8e5e22cca55d401e89e5e13bcf9de9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623184"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>VM 및 PaaS 테스트 환경에 Azure DevTest Labs 사용

Azure DevTest Labs를 사용하여 여러 주요 시나리오를 구현할 수 있지만 기본 시나리오는 DevTest Labs를 사용하여 테스터를 위한 컴퓨터를 호스트하는 것입니다. 

이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- 테스터가 재사용 가능한 템플릿과 아티팩트를 사용하여 Windows와 Linux 환경을 빠르게 프로비전함으로써 최신 버전의 애플리케이션을 테스트할 수 있습니다.
- 테스터가 다수의 테스트 에이전트를 프로비전하여 부하 테스트를 강화할 수 있습니다.
- 관리자는 다음을 확인하여 비용을 제어할 수 있습니다.
  - 테스터는 VM을 필요 이상으로 가질 수 없습니다.
  - VM은 사용되지 않을 때 종료됩니다.

![학습에 DevTest Labs 사용](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

이 문서에서는 테스터 요구 사항을 충족하는 데 사용되는 다양한 Azure DevTest Labs 기능과 랩을 설정하기 위해 수행하는 자세한 단계에 대해 알아봅니다.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Azure DevTest Labs를 사용하여 테스트 환경 구현
1. **랩 만들기** 
   
    랩은 Azure DevTest Labs의 시작 지점입니다. 랩을 만든 후 랩에 사용자(테스터)를 추가하고, 비용을 제어하는 정책을 설정하고, 신속하게 만들 수 있는 VM 이미지를 정의하는 등의 작업을 수행할 수 있습니다.  
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure DevTest Labs에서 랩 만들기](devtest-lab-create-lab.md) |Azure Portal의 Azure DevTest Labs에서 랩을 만드는 방법을 알아봅니다. |
2. **바로 사용할 수 있는 마켓플레이스 이미지 및 사용자 지정 이미지를 사용하여 몇 분 만에 VM 만들기** 
   
    Azure Marketplace의 다양한 이미지에서 바로 사용할 수 있는 이미지를 선택하고 랩에서 사용하도록 할 수 있습니다. 바로 사용할 수 있는 이미지가 요구 사항을 충족하지 않을 경우 Azure Marketplace에서 바로 사용할 수 있는 이미지를 사용하여 랩 VM을 만들고, 필요한 모든 소프트웨어를 설치하고, 랩의 사용자 지정 이미지로 VM을 지정함으로써 사용자 지정 이미지를 만들 수 있습니다.

    사용자 지정 이미지를 사용하는 경우 이미지 팩터리를 사용하여 이미지를 만들고 배포하는 것이 좋습니다. 이미지 팩터리는 구성된 이미지를 자동으로 정기적으로 작성 및 배포하는 "코드로 구성" 솔루션입니다. 이를 사용하면 VM을 기본 OS로 만든 후에 시스템을 수동으로 구성하는 데 필요한 시간이 절감됩니다.
  
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure Marketplace 이미지 구성](devtest-lab-configure-marketplace-images.md) |테스터에게 필요한 이미지만 선택할 수 있도록 Azure Marketplace 이미지를 허용 목록에 추가할 수 있는 방법을 알아봅니다.|
   | [사용자 지정 이미지 만들기](devtest-lab-create-template.md) |테스터가 사용자 지정 이미지를 사용하여 신속하게 VM을 만들 수 있도록 필요한 소프트웨어를 미리 설치하여 사용자 지정 이미지를 만듭니다.|
   | [이미지 팩터리에 대한 자세한 정보](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |이미지 팩터리를 설정 및 사용하는 방법을 설명하는 비디오를 시청하세요.|

3. **테스트 컴퓨터에 재사용 가능한 템플릿 만들기** 
   
    Azure DevTest Labs의 수식은 VM을 만드는 데 사용되는 기본 속성 값의 목록입니다. 이미지, VM 크기(CPU와 RAM의 조합) 및 가상 네트워크를 선택하여 랩에서 수식을 만들 수 있습니다. 각 테스터는 랩에서 수식을 보고 수식을 사용하여 VM을 만들 수 있습니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [VM을 만드는 DevTest Labs 수식 관리](devtest-lab-manage-formulas.md) |이미지, VM 크기(CPU와 RAM의 조합) 및 가상 네트워크를 선택하여 수식을 만들 수 있는 방법을 알아봅니다.|

3. **다중 VM 테스트 환경 만들기** 
   
    Azure Resource Manager 템플릿을 사용하면 Azure 솔루션의 인프라와 구성을 정의하고 여러 VM을 일관된 상태로 반복적으로 배포할 수 있습니다.

    Azure PaaS 리소스는 Resource Manager 템플릿의 환경에서 프로비전할 수 있고 비용 추적에 표시됩니다. 하지만 VM 자동 종료는 PaaS 리소스에 적용되지 않습니다.

    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md) |테스트 환경에 일관된 상태로 여러 VM을 배포하는 방법에 대해 알아봅니다.|

4. **아티팩트를 만들어 유연한 VM 사용자 지정 사용**

   VM이 프로비전된 후 애플리케이션을 배포하고 구성하기 위해 아티팩트가 사용됩니다. 아티팩트는 다음과 같을 수 있습니다.

   - VM에 설치하려는 도구(예: 에이전트, Fiddler 및 Visual Studio)
   - VM에서 실행하려는 작업(예: 리포지토리 복제)
   - 테스트하려는 애플리케이션

   많은 아티팩트는 이미 기본 제공되어 있습니다. 특정 요구에 맞게 추가로 사용자 지정하려는 경우 자시만의 사용자 지정 아티팩트를 만들 수 있습니다.

   다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [DevTest Lab VM에 대한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md) |랩에서 가상 머신에 대한 사용자 고유의 사용자 지정 아티팩트를 만듭니다.|
   | [Azure DevTest Labs에서 사용하기 위한 사용자 지정 아티팩트 및 Azure Resource Manager 템플릿을 저장할 Git 리포지토리 추가](devtest-lab-add-artifact-repo.md) |사용자 지정 아티팩트를 자체의 개인 Git 리포지토리에 저장하는 방법을 알아봅니다.|

5. **비용 제어**
   
    Azure DevTest Labs를 통해 랩에서 테스터가 만들 수 있는 VM의 최대 수를 지정하는 정책을 랩에 설정할 수 있습니다. 
   
    테스트 팀에 설정된 작업 일정이 있고 하루 중 특정 시간에 모든 VM을 중지했다가 다음 날 자동으로 다시 시작하도록 하려면 랩에서 자동 종료 및 자동 시작 정책을 설정하여 쉽게 수행할 수 있습니다. 
   
    마지막으로 앱 개발이 완료되면 단일 PowerShell 스크립트를 실행하여 모든 VM을 한 번에 삭제할 수 있습니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [랩 정책 정의](devtest-lab-set-lab-policy.md) |랩에 정책을 설정하여 비용을 제어합니다. |
   | [PowerShell 스크립트를 사용하여 모든 랩 VM 삭제](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |테스트가 완료되면 한 번에 모든 랩을 삭제합니다.|

1. **랩에 가상 네트워크 추가** 
   
    DevTest Labs는 랩이 생성될 때마다 새 VNET(가상 네트워크)를 만듭니다. 예를 들어 ExpressRoute 또는 사이트 간 VPN을 사용하여 사용자 고유의 VNET을 구성한 경우 VM을 만들 때 사용할 수 있도록 랩의 가상 네트워크 설정에 이 VNET을 추가할 수 있습니다.

    또한 VM이 만들어질 때 VM을 도메인에 가입시키는 Azure Active Directory 도메인 가입 아티팩트를 사용할 수 있습니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md) |Azure Portal을 사용하여 Azure DevTest Labs에서 가상 네트워크를 구성하는 방법을 알아봅니다.|

6. **각 테스터와 랩 공유**
   
    테스터와 공유하는 링크를 사용하여 랩에 직접 액세스할 수 있습니다. [Microsoft 계정](devtest-lab-faq.md#what-is-a-microsoft-account)이 있으면 Azure 계정이 없어도 됩니다. 테스터는 다른 테스터가 만든 VM을 볼 수 없습니다.  
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure DevTest Labs에서 랩에 테스터 추가](devtest-lab-add-devtest-user.md) |랩에 테스터를 추가하려면 Azure Portal을 사용합니다.|
   | [PowerShell 스크립트를 사용하여 랩에 테스터 추가](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |PowerShell을 사용하여 랩에 테스터를 자동으로 추가합니다. |
   | [랩에 대한 링크 가져오기](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |테스터가 하이퍼링크를 통해 랩에 직접 액세스하는 방법을 알아봅니다.|

7. **추가 팀을 위한 랩 생성 자동화** 
   
    Resource Manager 템플릿을 만들어 동일한 랩을 반복해서 다시 만드는 데 사용하면 사용자 지정 설정을 포함하여 랩 만들기를 자동화할 수 있습니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Resource Manager 템플릿을 사용하여 랩 만들기](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Resource Manager 템플릿을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

