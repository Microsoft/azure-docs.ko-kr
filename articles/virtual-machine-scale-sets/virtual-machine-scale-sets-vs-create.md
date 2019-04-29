---
title: Visual Studio를 사용하여 Virtual Machine Scale Set 배포 | Microsoft Docs
description: Visual Studio 및 Resource Manager 템플릿을 사용하여 Virtual Machine Scale Sets 배포 | Microsoft Azure
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108069"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Visual Studio에서 Virtual Machine Scale Set를 만드는 방법
이 문서는 Visual Studio 리소스 그룹 배포를 사용하여 Azure Virtual Machine Scale Set를 배포하는 방법을 보여줍니다.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)는 자동 크기 조정 및 부하 분산을 사용하여 유사한 가상 머신 컬렉션을 배포하고 관리하기 위한 Azure Compute 리소스입니다. [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)을 사용하여 Virtual Machine Scale Sets를 프로비전하고 배포할 수 있습니다. Azure Resource Manager 템플릿은 Azure CLI, PowerShell, REST를 사용하여 배포가 가능하고 Visual Studio에서 직접 배포할 수도 있습니다. Visual Studio는 Azure 리소스 그룹 배포 프로젝트의 일부로 배포될 수 있는 예제 템플릿 집합을 제공합니다.

Azure 리소스 그룹 배포는 단일 배포 작업을 통해 관련된 Azure 리소스 집합을 그룹화하여 게시하는 방법입니다. 에 대 한 자세한 내용은 여기 알아볼 수 있습니다. [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

## <a name="pre-requisites"></a>필수 조건
Visual Studio에서 Virtual Machine Scale Sets 배포를 시작하려면 다음 항목이 필요합니다.

* Visual Studio 2013 이상
* Azure SDK 2.7, 2.8 또는 2.9

>[!NOTE]
>이 지침은 사용자가 [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)과 Visual Studio를 사용한다고 가정합니다.

## <a name="creating-a-project"></a>프로젝트 만들기
1. Visual Studio에서 **파일 | 새로 만들기 | 프로젝트**를 선택하여 새 프로젝트를 만듭니다.
   
    ![파일 새로 만들기][file_new]

2. **Visual C# | 클라우드**에서 **Azure Resource Manager**를 선택하여 Azure Resource Manager 템플릿을 배포하는 프로젝트를 만듭니다.
   
    ![프로젝트 만들기][create_project]

3. 템플릿 목록에서 Linux 또는 Windows Virtual Machine Scale Set 템플릿을 선택합니다.
   
   ![템플릿 선택][select_Template]

4. 프로젝트가 만들어지면 PowerShell 배포 스크립트, Azure Resource Manager 템플릿, Virtual Machine Scale Set의 매개 변수 파일이 표시됩니다.
   
    ![Controllers\HomeController.cs][solution_explorer]

## <a name="customize-your-project"></a>프로젝트 사용자 지정
이제 템플릿을 편집하여 애플리케이션의 요구(예: VM 확장 속성 추가 또는 부하 분산 규칙 편집 등)에 맞게 사용자 지정할 수 있습니다. 기본적으로 Virtual Machine Scale Set 템플릿은 자동 크기 조정 규칙을 손쉽게 추가할 수 있게 AzureDiagnostics 확장을 배포하도록 구성됩니다. 또한 인바운드 NAT 규칙을 사용하여 구성된 공용 IP 주소를 가진 부하 분산 장치를 배포합니다. 

부하 분산 장치를 통해 SSH(Linux) 또는 RDP(Windows)를 사용하여 VM 인스턴스를 연결할 수 있습니다. 프런트 엔드 포트 범위는 50000에서 시작합니다. 즉, Linux의 경우 포트 50000에 SSH하는 경우 확장 집합에 있는 첫 번째 VM의 포트 22에 라우팅됩니다. 50001 포트로 연결하면 두 번째 VM의 22 포트로 라우팅되며 이런 방식으로 계속됩니다.

 Visual Studio에서 템플릿을 편집하는 좋은 방법은 JSON 개요를 사용하여 매개 변수, 변수, 리소스를 구성하는 것입니다. 스키마에 대한 이해를 바탕으로, Visual Studio는 템플릿을 배포하기 전에 거기에 포함된 오류를 알려줄 수 있습니다.

![JSON 탐색기][json_explorer]

## <a name="deploy-the-project"></a>프로젝트 배포
1. Azure Resource Manager 템플릿을 배포하여 Virtual Machine Scale Set 리소스를 만듭니다. 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **배포 | 새 배포**를 선택합니다.
   
    ![템플릿 배포][5deploy_Template]
    
2. “리소스 그룹에 배포” 대화 상자에서 구독을 선택합니다.
   
    ![템플릿 배포][6deploy_Template]

3. 여기에서 템플릿을 배포할 Azure 리소스 그룹을 만들 수 있습니다.
   
    ![새 리소스 그룹][new_resource]

4. 그런 다음 **매개 변수 편집**을 클릭하여 템플릿에 전달되는 매개 변수를 입력합니다. 배포하는 데 필요한 OS의 사용자 이름 및 암호를 입력합니다. PowerShell Tools for Visual Studio가 설치되지 않았다면 **암호 저장**을 확인하여 숨겨진 PowerShell 명령줄 프롬프트를 방지하하거나 [keyvault 지원](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)을 사용하는 것이 좋습니다.
   
    ![매개 변수 편집][edit_parameters]

5. 이제 **배포**를 클릭합니다. **출력** 창에 배포 진행률이 표시됩니다. 작업이 **Deploy-AzureResourceGroup.ps1** 스크립트를 실행 중입니다
   
   ![출력 창][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Virtual Machine Scale Set 탐색
배포가 완료되면 Visual Studio **클라우드 탐색기**에서(목록 새로 고침) 새로운 Virtual Machine Scale Set를 볼 수 있습니다. Cloud Explorer를 사용하면 애플리케이션을 배포하는 동안 Visual Studio에서 Azure 리소스를 관리할 수 있습니다. [Azure Portal](https://portal.azure.com) 및 [Azure 리소스 탐색기](https://resources.azure.com/)에서 Virtual Machine Scale Set를 볼 수도 있습니다.

![Cloud Explorer][cloud_explorer]

 포털은 웹 브라우저를 사용하여 Azure 인프라를 시각적으로 관리하는 최고의 방법을 제공하며, Azure 리소스 탐색기는 Azure 리소스를 탐색하고 디버그하는 손쉬운 방법을 제공하고, “인스턴스 뷰”에 대한 창을 제공하며 표시되는 리소스에 대한 PowerShell 명령을 보여 줍니다.

## <a name="next-steps"></a>다음 단계
Visual Studio를 통해 Virtual Machine Scale Sets를 성공적으로 배포하면 애플리케이션 요구 사항에 맞게 프로젝트를 추가로 사용자 지정할 수 있습니다. 예를 들어, **Insights** 리소스를 추가하거나, 독립 실행형 VM처럼 템플릿에 인프라를 추가하거나, 사용자 지정 스크립트 확장을 사용하는 애플리케이션을 배포하여 자동 크기 조정을 구성할 수 있습니다. [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates) GitHub 리포지토리에서 유용한 예제 템플릿을 찾을 수 있습니다("vmss" 검색).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
