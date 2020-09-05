---
title: DevTest Labs 가상 머신에 대한 사용자 지정 아티팩트 만들기 | Microsoft Docs
description: 가상 컴퓨터를 프로 비전 한 후 응용 프로그램을 배포 하 고 설정 하기 위해 Azure DevTest Labs 사용할 아티팩트를 만드는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270685"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>DevTest Labs 가상 머신에 대한 사용자 지정 아티팩트 만들기

이 문서에 설명된 단계의 개요에 대한 다음 비디오 보기:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>개요
*아티팩트*를 사용하여 VM을 프로비전한 후 애플리케이션을 배포 및 설정할 수 있습니다. 아티팩트는 Git 리포지토리의 폴더에 저장되어 있는 아티팩트 정의 파일 및 다른 스크립트 파일로 구성됩니다. 아티팩트 정의 파일은 VM에 설치하려는 아티팩트를 지정하는 데 사용할 수 있는 JSON과 식으로 구성됩니다. 예를 들어 아티팩트의 이름, 실행할 명령, 명령이 실행되는 경우 사용할 수 있는 매개 변수를 정의할 수 있습니다. 이름으로 아티팩트 정의 파일 내의 다른 스크립트 파일을 참조할 수 있습니다.

## <a name="artifact-definition-file-format"></a>아티팩트 정의 파일 형식
다음 예제에서는 정의 파일의 기본 구조를 구성하는 섹션을 보여 줍니다.

```json
  {
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "",
    "description": "",
    "iconUri": "",
    "targetOsType": "",
    "parameters": {
      "<parameterName>": {
        "type": "",
        "displayName": "",
        "description": ""
      }
    },
    "runCommand": {
      "commandToExecute": ""
    }
  }
```

| 요소 이름 | 필수 여부 | 설명 |
| --- | --- | --- |
| $schema |예 |JSON 스키마 파일의 위치입니다. JSON 스키마 파일은 정의 파일의 유효성을 검사하는 데 도움이 될 수 있습니다. |
| title |예 |랩에 표시되는 아티팩트의 이름입니다. |
| description |예 |랩에 표시되는 아티팩트에 대한 설명입니다. |
| iconUri |예 |랩에 표시되는 아이콘의 URI입니다. |
| targetOsType |예 |아티팩트가 설치되는 VM의 운영 체제입니다. 지원되는 옵션은 Windows 및 Linux입니다. |
| 매개 변수 |예 |아티팩트 설치 명령이 컴퓨터에서 실행될 때 제공되는 값으로 아티팩트를 사용자 지정할 수 있습니다. |
| runCommand |예 |VM에서 실행되는 아티팩트 설치 명령입니다. |

### <a name="artifact-parameters"></a>아티팩트 매개 변수
정의 파일의 매개 변수 섹션에서 아티팩트를 설치할 때 사용자가 입력할 수 있는 값을 지정합니다. 아티팩트 설치 명령에서 다음 값을 참조할 수 있습니다.

매개 변수를 정의하려면 다음 구조를 사용합니다.

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| 요소 이름 | 필수 여부 | Description |
| --- | --- | --- |
| type |예 |매개 변수 값의 형식입니다. 허용되는 형식에 대해 다음 목록을 참조하세요. |
| displayName |예 |랩에서 사용자에게 표시되는 매개 변수의 이름입니다. |
| description |예 |랩에 표시되는 매개 변수의 설명입니다. |

허용 유형은 다음과 같습니다.

* 문자열(유효한 모든 JSON 문자열)
* int(유효한 모든 JSON 정수)
* bool(유효한 모든 JSON 부울)
* array(유효한 모든 JSON 배열)

## <a name="secrets-as-secure-strings"></a>보안 문자열로 된 비밀
비밀을 보안 문자열로 선언 합니다. 다음은 파일의artifactfile.js섹션에서 보안 문자열 매개 변수를 선언 하는 구문입니다 `parameters` . **artifactfile.json**

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

아티팩트 설치 명령의 경우 Convertto-html 명령을 사용 하 여 만든 보안 문자열을 사용 하는 PowerShell 스크립트를 실행 합니다. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

전체 예제 및 artifact.ps1 (PowerShell 스크립트) artifactfile.js[GitHub에서이 샘플](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)을 참조 하세요.

또한 사용자 디버깅을 위해 출력이 캡처될 때 콘솔에 비밀을 기록 하지 않는 것이 중요 합니다. 

## <a name="artifact-expressions-and-functions"></a>아티팩트 식 및 함수
식과 함수를 사용하여 아티팩트 설치 명령을 구성할 수 있습니다.
식은 대괄호([ 및 ]) 안에 들어 있고 아티팩트를 설치할 때 평가됩니다. 식은 JSON 문자열 값의 어디든지 표시될 수 있습니다. 식은 항상 다른 JSON 값을 반환합니다. 대괄호([)로 시작하는 리터럴 문자열을 사용해야 하는 경우 대괄호를 두 개([[) 사용해야 합니다.
일반적으로 함수가 포함된 식을 사용하여 값을 구성합니다. JavaScript에서와 마찬가지로 함수 호출은 **functionName(arg1,arg2,arg3)** 으로 형식이 지정됩니다.

다음 목록에는 일반 함수가 나와 있습니다.

* **매개 변수(parameterName)**: 아티팩트 명령이 실행될 때 제공되는 매개 변수 값을 반환합니다.
* **concat(arg1, arg2, arg3,...)**: 여러 문자열 값을 결합합니다. 이 함수는 다양한 인수를 사용할 수 있습니다.

다음 예제에서는 값을 구성하기 위해 식과 함수를 사용하는 방법을 보여 줍니다.

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>사용자 지정 아티팩트 만들기

1. JSON 편집기를 설치합니다. 아티팩트 정의 파일 작업을 수행하려면 JSON 편집기가 필요합니다. Windows, Linux 및 OS X에 사용할 수 있는 [Visual Studio Code](https://code.visualstudio.com/)를 사용 하는 것이 좋습니다.
2. 샘플 artifactfile.json 정의 파일을 가져옵니다. [GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)에서 DevTest Labs 팀에서 만든 아티팩트를 확인하세요. 사용자 고유의 아티팩트를 만들 수 있는 풍부한 라이브러리의 아티팩트를 만들었습니다. 아티팩트 정의 파일을 다운로드하고 변경하여 사용자 고유 아티팩트를 만들 수 있습니다.
3. IntelliSense를 사용합니다. 아티팩트 정의 파일을 구성하는 데 사용할 수 있는 유효한 요소를 보려면 IntelliSense를 사용합니다. 또한 요소 값에 대한 다양한 옵션을 볼 수 있습니다. 예를 들어 IntelliSense에 **targetOsType** 요소를 편집할 때 두 가지 선택 항목인 Windows 또는 Linux가 표시됩니다.
4. 아티팩트를 [DevTest Labs용 공용 Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) 또는 [사용자 고유의 Git 리포지토리](devtest-lab-add-artifact-repo.md)에 저장합니다. 공용 리포지토리에서 직접 사용하거나 필요에 맞게 사용자 지정할 수 있으며 다른 사용자가 공유한 아티팩트를 볼 수 있습니다.
   
   1. 각 아티팩트에 대한 별도 디렉터리를 만듭니다. 디렉터리 이름은 아티팩트 이름과 동일해야 합니다.
   2. 만든 디렉터리에 아티팩트 정의 파일(artifactfile.json)을 저장합니다.
   3. 아티팩트 설치 명령에서 참조된 스크립트를 저장합니다.
      
      다음은 아티팩트 폴더가 표시되는 모습의 예입니다.
      
      ![아티팩트 폴더 예제](./media/devtest-lab-artifact-author/git-repo.png)
5. 사용자 고유의 리포지토리를 사용하여 아티팩트를 저장하는 경우 [아티팩트 및 템플릿용 Git 리포지토리 추가](devtest-lab-add-artifact-repo.md) 문서의 지침에 따라 리포지토리를 랩에 추가합니다.

## <a name="related-articles"></a>관련 문서
* [DevTest Labs에서 아티팩트 실패를 진단하는 방법](devtest-lab-troubleshoot-artifact-failure.md)
* [DevTest Labs에서 Resource Manager 템플릿을 사용하여 기존 Active Directory 도메인에 VM 가입](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>다음 단계
* [랩에 Git 아티팩트 리포지토리를 추가](devtest-lab-add-artifact-repo.md)하는 방법에 대해 알아봅니다.
