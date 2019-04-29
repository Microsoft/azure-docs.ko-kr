---
title: Machine Learning Studio용 PowerShell 모듈
titleSuffix: Azure Machine Learning Studio
description: PowerShell을 사용하여 Azure Machine Learning Studio 작업 영역, 실험, 웹 서비스 등을 만들고 관리하는 방법을 설명합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: e3c2587fceed265c9768b6ea6f2ecf3b9a8b7b1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094358"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Azure Machine Learning Studio용 PowerShell 모듈

PowerShell 모듈을 사용하면 Studio 리소스 및 작업 영역/데이터 세트/웹 서비스 등의 자산을 프로그래밍 방식으로 관리할 수 있습니다.

다음의 세 가지 PowerShell 모듈을 통해 Studio 리소스를 조작할 수 있습니다.

* [Azure PowerShell Az](#az-rm)(2018년 출시) - cmdlet 이름은 다르지만 AzureRM의 모든 기능이 포함되어 있습니다.
* [AzureRM](#az-rm) PowerShell Az로 대체 하는 2016 년에 출시
* [Azure Machine Learning PowerShell 클래식](#classic)(2016년 출시)

이러한 PowerShell 모듈 약간의 유사성이 있지만 각 특정 시나리오에 대 한 설계 되었습니다. 이 문서에서는 PowerShelll 모듈 간의 차이점을 설명하고 선택해야 하는 모듈을 결정하는 데 도움이 되는 정보를 제공합니다.  

아래의 [지원 표](#support-table)에서 각 모듈이 지원하는 리소스를 확인하세요. 

## <a name="az-rm"></a> Azure PowerShell Az 및 AzureRM

현재 Azure 조작용 PowerShell 모듈로 사용되고 있는 Az는 AzureRM의 모든 이전 기능을 포함합니다. AzureRM에도 버그 수정은 계속 제공되지만 신규 cmdlet이나 기능은 제공되지 않습니다.  Az와 AzureRM은 모두 **Azure Resource Manager** 배포 모델을 사용하여 배포된 솔루션을 관리합니다. 이러한 리소스는 Studio 작업 영역 및 Studio "New" 웹 서비스를 포함 합니다. 

PowerShell 클래식 Az 또는 AzureRM "new" 및 "클래식" 리소스 유형이 모두를 함께 설치할 수 있습니다. 그러나 Az와 AzureRM은 동시에 설치하지 않는 것이 좋습니다. Az와 AzureRM 중에 설치할 모듈을 결정해야 하는 경우 향후 모든 배포에 사용할 수 있는 Az를 선택하는 것이 좋습니다.  Az AzureRM과 마이그레이션 경로에 대해 자세히 알아보려면 [Azure PowerShell Az 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)합니다.

Az 사용을 시작하려면 [Azure Az 설치 지침](https://docs.microsoft.com/powershell/azure/install-az-ps)을 따르세요.

## <a name="classic"></a> PowerShell 클래식

Studio [PowerShell 클래식 모듈](https://aka.ms/amlps)에서는 **클래식 배포 모델**을 사용하여 배포된 리소스를 관리할 수 있습니다. 이러한 리소스는 Studio 사용자 자산, "클래식" 웹 서비스 및 "클래식" 웹 서비스 끝점을 포함 합니다.

그러나 모든 향후 리소스에 대 한 Resource Manager 배포 모델을 사용 하 여 배포 및 리소스 관리를 간소화 하는 것이 좋습니다. 배포 모델에 대해 자세히 알아보려면 [Azure Resource Manager와 클래식 배포 비교](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)를 참조하세요.

PowerShell 클래식 사용을 시작하려면 GitHub에서 [릴리스 패키지](https://github.com/hning86/azuremlps/releases)를 다운로드하고 [설치 지침](https://github.com/hning86/azuremlps/blob/master/README.md)을 따릅니다. 지침은 다운로드/압축 해제된 DLL의 차단을 해제한 다음 PowerShell 환경으로 가져오는 방법을 설명합니다.

PowerShell 클래식 Az 또는 AzureRM "new" 및 "클래식" 리소스 유형이 모두를 함께 설치할 수 있습니다.

## <a name="support-table"></a> PowerShell 지원 표


| | **Az** |  **PowerShell 클래식** |
| --- | --- | --- |
| 작업 영역 만들기/삭제 | [Resource Manager 템플릿](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| 작업 영역 약정 계획 관리 | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| 작업 영역 사용자 관리 |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 웹 서비스 관리 | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("new" 웹 서비스)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("클래식" 웹 서비스) |
| 웹 서비스 끝점/키 관리 |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| 사용자 데이터 집합/학습 모델 관리| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 사용자 실험 관리 |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 사용자 지정 모듈 관리 | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>다음 단계
이러한 PowerShell 모듈의 전체 설명서를 참조 합니다.
* [PowerShell 클래식](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
