---
title: Stream Analytics 도구를 사용하여 지속적으로 통합 및 개발
description: 이 문서에서는 Azure Stream Analytics용 Visual Studio 도구를 사용하여 지속적인 통합 및 배포 프로세스를 설정하는 방법에 대해 알아봅니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2017
ms.openlocfilehash: 641254be37ac0019ee6a256fc99f96fc3bfb75a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761508"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Stream Analytics 도구를 사용하여 지속적으로 통합 및 개발
이 문서에서는 Visual Studio용 Azure Stream Analytics 도구를 사용하여 지속적인 통합 및 배포 프로세스를 설정하는 방법에 대해 알아봅니다.

MSBuild에 대한 지원을 제공하기 위해 2.3.0000.0 이상 버전의 [Visual Studio용 Stream Analytics 도구](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)를 사용합니다.

NuGet 패키지를 사용할 수 있습니다. [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Stream Analytics Visual Studio 프로젝트의 지속적 통합과 배포를 지원하는 MSBuild, 로컬 실행 및 배포 도구를 제공합니다. 
> [!NOTE]
> NuGet 패키지는 Visual Studio용 Stream Analytics 도구 버전 2.3.0000.0 이상에서만 사용할 수 있습니다. 이전 버전의 Visual Studio 도구에서 만든 프로젝트가 있는 경우 2.3.0000.0 이상 버전에서 열어 저장하기만 하면 됩니다. 그런 다음 새 기능이 활성화됩니다. 

자세한 내용은 [Visual Studio용 Stream Analytics 도구](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)를 참조하세요.

## <a name="msbuild"></a>MSBuild
표준 Visual Studio MSBuild 환경과 마찬가지로 프로젝트를 빌드하는 두 가지 옵션이 있습니다. 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **빌드**를 선택할 수 있습니다. 또한 명령줄에서 NuGet 패키지의 **MSBuild**를 사용할 수도 있습니다.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Stream Analytics Visual Studio 프로젝트가 성공적으로 빌드되면 **bin/[Debug/Retail]/Deploy** 폴더 아래에 다음 두 Azure Resource Manager 템플릿 파일이 생성됩니다. 

*  Resource Manager 템플릿 파일

       [ProjectName].JobTemplate.json 

*  Resource Manager 매개 변수 파일

       [ProjectName].JobTemplate.parameters.json   

Parameters.json 파일의 기본 매개 변수는 Visual Studio 프로젝트의 설정에서 가져옵니다. 다른 환경에 배포하려면 해당 매개 변수를 적절하게 바꾸면 됩니다.

> [!NOTE]
> 모든 자격 증명에 대해 기본값은 null로 설정됩니다. 클라우드에 배포하기 전에 값을 *설정해야 합니다*.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
[Resource Manager 템플릿 파일 및 Azure PowerShell을 사용하여 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)하는 방법에 대해 자세히 알아보세요. [Resource Manager 템플릿에서 개체를 매개 변수로 사용](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)하는 방법에 대해 자세히 알아보세요.

Azure Data Lake Store Gen1용 관리 ID를 출력 싱크로 사용하려면 Azure에 배포하기 전에 PowerShell을 사용하여 서비스 주체에 액세스 권한을 제공해야 합니다. [Resource Manager 템플릿에서 관리 ID를 사용하여 ADLS Gen1을 배포](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)하는 방법을 자세히 알아봅니다.


## <a name="command-line-tool"></a>명령줄 도구 

### <a name="build-the-project"></a>프로젝트 빌드
NuGet 패키지에는 **SA.exe**라는 명령줄 도구가 있습니다. 이 도구는 연속 통합 및 지속적인 업데이트 프로세스에서 사용할 수 있는 임의의 컴퓨터에서의 프로젝트 빌드 및 로컬 테스트를 지원합니다. 

배포 파일은 기본적으로 현재 디렉터리에 배치됩니다. 다음 -OutputPath 매개 변수를 사용하여 출력 경로를 지정할 수 있습니다.

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>스크립트를 로컬로 테스트

프로젝트에 Visual Studio에서 지정한 로컬 입력 파일이 있는 경우 *localrun* 명령을 사용하여 자동화된 스크립트 테스트를 실행할 수 있습니다. 출력 결과는 현재 디렉터리 아래에 배치됩니다.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Stream Analytics PowerShell API에서 사용할 작업 정의 파일 생성

*arm* 명령은 빌드를 통해 생성된 작업 템플릿 매개 변수 파일과 작업 템플릿을 입력으로 취합니다. 그런 다음 Stream Analytics PowerShell API에서 사용할 수 있는 작업 정의 JSON 파일에 이를 결합합니다.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
예제:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


