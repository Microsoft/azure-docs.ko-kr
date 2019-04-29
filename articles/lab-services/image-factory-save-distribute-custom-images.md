---
title: Azure DevTest Labs에서 이미지를 배포 및 저장 | Microsoft Docs
description: Azure DevTest Labs에서 사용자 지정 이미지 팩터리를 만드는 방법에 알아봅니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622686"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>사용자 지정 이미지 저장 및 여러 랩에 배포
이 문서에서는 이미 만든된 virtual machines (Vm)에서 사용자 지정 이미지를 저장 하는 단계를 제공 합니다. 또한 조직에서 다른 DevTest Labs에 이러한 사용자 지정 이미지를 배포 하는 방법에 대해서도 설명 합니다.

## <a name="prerequisites"></a>필수 조건
다음 항목이 위치에 이미 있어야 합니다.

- Azure DevTest Labs에서 이미지 팩터리에 대 한 랩입니다.
- 이미지 팩터리를 자동화 하는 데 사용 되는 Azure DevOps 프로젝트입니다.
- 스크립트 및 구성 (이 예를 들어 이전 단계에서 언급 한 동일한 DevOps 프로젝트를) 포함 된 소스 코드 위치입니다.
- Azure Powershell 작업을 오케스트레이션 하는 정의 빌드하십시오.

단계를 따릅니다 필요한 경우는 [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md) 를 만들거나 이러한 항목을 설정 합니다. 

## <a name="save-vms-as-generalized-vhds"></a>일반화 된 Vhd로 Vm을 저장 합니다.
기존 Vm을 일반화 된 Vhd로 저장 합니다.  기존 Vm을 일반화 된 Vhd로 저장 하는 샘플 PowerShell 스크립트를 있습니다. 를 사용 하려면 먼저 다른 항목 추가 **Azure Powershell** 다음 그림과에서 같이 빌드 정의에 작업:

![Azure PowerShell 단계 추가](./media/save-distribute-custom-images/powershell-step.png)

목록에서 새 작업을 만든 후 다음 그림에 나와 있는 것 처럼에서는 모든 세부 정보 채울 수 있도록 항목을 선택 합니다. 

![PowerShell 설정](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>특수 한 사용자 지정 이미지 및 일반화
에 [Azure portal](https://portal.azure.com), 가상 컴퓨터에서 사용자 지정 이미지를 만들 때 일반화 된 또는 특수 한 사용자 지정 이미지를 선택할 수 있습니다.

- **특수 한 사용자 지정 이미지:** Sysprep/프로 비전 해제 된 컴퓨터에서 실행 되지 않았습니다. 이미지는 OS 디스크의 기존 가상 컴퓨터 (스냅숏)의 정확한 복사본을 의미 합니다.  동일한 파일, 응용 프로그램, 사용자 계정, 컴퓨터 이름 및 등이 사용자 지정 이미지에서 새 컴퓨터를 만들 때 모두 표시 됩니다.
- **사용자 지정 이미지를 일반화 합니다.** Sysprep/프로 비전 해제 된 컴퓨터에서 실행 됩니다.  이 프로세스가 실행 될 때 해당 사용자 계정을 제거, 컴퓨터 이름을 제거, 다른 가상 머신을 만들 때 지정할 수 있도록 이미지를 일반화의 목표를 사용 하 여 등을 사용자 레지스트리 하이브를 제거 합니다.  (Sysprep를 실행) 하 여 가상 컴퓨터를 일반화 하는 경우 프로세스는 현재 가상 머신을 제거 – 기능 더 이상.

이미지 팩터리에서 사용자 지정 이미지 맞춤에 대 한 스크립트는 이전 단계에서 만든 모든 가상 머신에 대 한 Vhd를 저장 합니다 (식별 된 Azure에서 리소스에 태그를 기준으로).

## <a name="update-configuration-for-distributing-images"></a>이미지 배포에 대 한 구성 업데이트
프로세스의 다음 단계를 필요로 하는 다른 모든 랩 아웃 이미지 팩터리 lab에서 사용자 지정 이미지를 푸시 방법은입니다. 이 프로세스의 핵심 부분은 합니다 **labs.json** 구성 파일입니다. 이 파일을 찾을 수 있습니다 합니다 **구성** 이미지 팩터리에 포함 된 폴더입니다.

두 가지 주요 labs.json 구성 파일에 나열 합니다.

- 랩 이름과 구독 ID를 사용 하 여 특정 대상 랩을 고유 하 게 식별 합니다.
- 구성 루트에 상대적인 경로로 랩에 푸시할 수 있는 이미지의 특정 집합. 전체 폴더 (해당 폴더의 모든 이미지를 가져오기)을 지정할 수 있습니다 너무 합니다.

나열 된 두 labs 사용 하 여 labs.json 파일의 예는 다음과 같습니다. 이 경우 두 개의 다른 랩에는 이미지를 배포 하는 합니다.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>빌드 작업 만들기
이 문서의 앞부분에서 살펴본 동일한 단계를 사용 하 여 추가 **Azure Powershell** 빌드 정의에 빌드 작업입니다. 다음 이미지와 같이 세부 정보를 입력 합니다. 

![빌드 이미지를 배포 하는 작업](./media/save-distribute-custom-images/second-build-task-powershell.png)

매개 변수는: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

이 작업 이미지 팩터리에 사용자 지정 이미지를 사용 하 고 Labs.json 파일에 정의 된 모든 랩에 푸시됩니다.

## <a name="queue-the-build"></a>큐 빌드
배포 빌드 작업이 완료 되 면 모든 항목이 작동 하는지 확인 하려면 새 빌드를 큐 대기 합니다. 빌드가 성공적으로 완료 된 후 새 사용자 지정 이미지 Labs.json 구성 파일에 입력 된 대상 랩에 표시 됩니다.

## <a name="next-steps"></a>다음 단계
시리즈의 다음 문서에서 보존 정책 및 정리 단계를 사용 하 여 이미지 팩터리를 업데이트 하면: [보존 정책을 설정 및 정리 스크립트 실행](image-factory-set-retention-policy-cleanup.md)합니다.
