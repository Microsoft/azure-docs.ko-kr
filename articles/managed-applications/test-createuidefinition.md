---
title: Azure Managed Application의 UI 정의 테스트 | Microsoft Docs
description: 포털을 통해 Azure Managed Application을 만들기 위해 사용자 환경을 테스트하는 방법을 설명합니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: b1392c29881a9077e26baafc8972148800d03d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746319"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>관리되는 애플리케이션에 대해 Azure Portal 인터페이스 테스트
Azure Managed Application에 대해 [createUiDefinition.json 파일을 만든 후](create-uidefinition-overview.md) 사용자 환경을 테스트해야 합니다. 간단한 테스트를 위해 파일을 포털에 로드하는 스크립트를 사용합니다. 실제로 관리되는 애플리케이션을 배포할 필요는 없습니다.

## <a name="prerequisites"></a>필수 조건

* **createUiDefinition.json** 파일. 이 파일이 없으면 [샘플 파일](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)을 복사하여 로컬에 저장합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="download-test-script"></a>테스트 스크립트 다운로드

포털에서 인터페이스를 테스트하려면 다음 스크립트 중 하나를 로컬 머신에 복사합니다.

* [PowerShell 테스트용으로 로드 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI 테스트용으로 로드 스크립트](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>스크립트 실행 

포털에서 인터페이스 파일을 보려면 다운로드한 스크립트를 실행합니다. 이 스크립트는 Azure 구독에 저장소 게정을 만들고 createUiDefinition.json 파일을 저장소 계정에 업로드합니다. 처음 스크립트를 실행하거나 저장소 계정이 삭제된 경우에만 저장소 계정이 생성됩니다. 저장소 계정이 Azure 구독에 이미 있는 경우 스크립트가 이를 다시 사용합니다. 스크립트에서 포털을 열고 저장소 계정에서 파일을 로드합니다.

저장소 계정의 위치를 제공하고 createUiDefinition.json 파일이 있는 폴더를 지정합니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI의 경우 

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

createUiDefinition.json 파일이 스크립트와 동일한 폴더에 있고 저장소 계정을 이미 만든 경우 해당 매개 변수를 제공할 필요가 없습니다.

PowerShell의 경우 다음을 사용합니다.

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Azure CLI의 경우 

```azurecli
./sideload-createuidef.sh
```

## <a name="test-your-interface"></a>인터페이스 테스트

이 스크립트는 브라우저에서 새 탭을 엽니다. 관리되는 애플리케이션을 만들기 위한 인터페이스가 있는 포털을 표시합니다.

![포털 보기](./media/test-createuidefinition/view-portal.png)

필드를 작성하기 전에 브라우저에서 Web Developer Tools를 엽니다. **콘솔**은 인터페이스에 관한 중요 메시지를 표시합니다.

![콘솔 선택](./media/test-createuidefinition/select-console.png)

인터페이스 정의에 오류가 있으면 콘솔에 설명이 표시됩니다.

![오류 표시](./media/test-createuidefinition/show-error.png)

필드의 값을 제공합니다. 완료되면 값이 템플릿으로 전달됩니다.

![값 표시](./media/test-createuidefinition/show-json.png)

배포 템플릿을 테스트하기 위해 이러한 값을 매개 변수 파일로 사용할 수 있습니다.

## <a name="troubleshooting-the-interface"></a>인터페이스 문제 해결

몇 가지 일반적인 오류가 표시될 수 있습니다.

* 포털에서 사용자 인터페이스가 로드되지 않습니다. 대신, 물방울이 있는 클라우드 아이콘이 표시됩니다. 일반적으로 파일에 구문 오류가 있을 때 이 아이콘이 표시됩니다. VS Code(또는 스키마 유효성 검사가 있는 다른 JSON 편집기)에서 파일을 열고 구문 오류를 확인하세요.

* 포털이 요약 화면에서 중지됩니다. 일반적으로 이 중단은 출력 섹션에 버그가 있을 때 발생합니다. 예를 들어, 존재하지 않는 컨트롤을 참조했을 수 있습니다.

* 출력에서 매개 변수가 비어 있습니다. 매개 변수가 존재하지 않는 속성을 참조할 수 있습니다. 예를 들어, 컨트롤에 대한 참조가 유효하지만 속성 참조는 유효하지 않습니다.

## <a name="test-your-solution-files"></a>솔루션 파일 테스트

이제 포털 인터페이스가 예상대로 작동하는 것을 확인했으므로 createUiDefinition 파일이 mainTemplate.json 파일과 제대로 통합되었는지 유효성을 검사할 시점입니다. 유효성 검사 스크립트 테스트를 실행하여 createUiDefinition 파일 등, 솔루션 파일의 콘텐츠를 테스트할 수 있습니다. 이 스크립트는 JSON 구문 유효성을 검사하고 텍스트 필드에서 Regex 식을 검사하며 포털 인터페이스의 출력 값이 템플릿 매개 변수와 ㅇ리치하는지 확인합니다. 이 스크립트 실행에 대한 자세한 내용은 [템플릿에 대해 정적 유효성 검사 실행](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)을 참조하세요.

## <a name="next-steps"></a>다음 단계

포털 인터페이스 유효성 검사 후 [Marketplace에서 Azure Managed Application](publish-marketplace-app.md)을 제공하는 방법을 알아보세요.
