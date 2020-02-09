---
title: 시험 드라이브 Azure Resource Manager | Azure Marketplace
description: Azure Resource Manager를 사용하여 Marketplace 시험 사용 빌드
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Patrick .Butler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8b2a24b6f2d7df92f1c8ea1b22432471aa432011
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644905"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 시험 사용

이 문서는 Azure Marketplace에 제품이 있는 게시자 또는 AppSource에 있지만 Azure 리소스만 사용해서 시험 사용을 빌드하려는 게시자를 위한 것입니다.

리소스 관리자 (Azure Resource Manager) 템플릿은 솔루션을 가장 잘 나타내도록 설계 된 Azure 리소스의 코딩 된 컨테이너입니다. 리소스 관리자 템플릿이 무엇 인지 잘 모르는 경우 [리소스 관리자 템플릿 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 및 템플릿 [리소스 관리자 작성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) 에 대 한 자세한 내용을 확인 하 여 고유한 템플릿을 빌드하고 테스트 하는 방법을 알아봅니다.

시험 사용은 제공된 Resource Manager 템플릿을 사용하고 해당 Resource Manager 템플릿에서 필요한 모든 리소스를 리소스 그룹에 배포하는 것입니다.

Azure Resource Manager 시험 사용을 빌드하도록 선택하는 경우 요구 사항은 다음과 같습니다.

- 시험 사용 Resource Manager 템플릿을 빌드, 테스트 및 업로드합니다.
- 시험 사용을 사용하는 데 필요한 모든 메타데이터 및 설정을 구성합니다.
- 시험 사용을 설정하여 제품을 다시 게시합니다.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Azure Resource Manager 시험 사용을 빌드하는 방법

Azure Resource Manager 테스트 드라이브를 빌드하기 위한 프로세스는 다음과 같습니다.

1. 흐름 다이어그램에서 고객이 수행할 작업을 디자인 합니다.
1. 고객이 빌드할 환경을 정의 합니다.
1. 위의 정의에 따라 고객에 게 이러한 환경을 수행 하는 데 필요한 부분과 리소스 (예: D365 instance 또는 데이터베이스가 포함 된 웹 사이트)를 결정 합니다.
1. 디자인을 로컬로 빌드하고 환경을 테스트 합니다.
1. ARM 템플릿 배포에서 환경을 패키지 하 고 여기에서 다음을 수행 합니다.
    1. 입력 매개 변수인 리소스의 부분을 정의 합니다.
    1. 변수는 무엇 인가요?
    1. 사용자 환경에 제공 되는 출력입니다.
1. 게시, 테스트 및 라이브 전환.

Azure Resource Manager 시험 사용 빌드 시 가장 중요한 부분은 고객이 경험할 시나리오를 정의하는 것입니다. 방화벽 제품 판매자이며 스크립트 삽입 공격을 얼마나 잘 처리하는지 시연하고 싶으신가요? 스토리지 제품 판매자이며 솔루션이 얼마나 빠르고 쉽게 파일을 압축하는지 시연하고 싶으신가요?

제품을 표시 하는 데 가장 적합 한 방법을 평가 하는 데 충분 한 시간을 사용 해야 합니다. 특히 필요한 모든 리소스를 사용 하 여 리소스 관리자 템플릿을 더 쉽게 패키지할 수 있습니다.

방화벽 예제를 계속하려면 아키텍처에 서비스에 대한 공용 IP URL과 방화벽으로 보호하는 웹 사이트에 대한 다른 공용 IP URL이 필요할 수 있습니다. 각 IP는 가상 머신에 배포되고 네트워크 보안 그룹 + 네트워크 인터페이스와 함께 연결됩니다.

원하는 리소스 패키지를 디자인 한 후에는 테스트 드라이브 리소스 관리자 템플릿을 작성 하 고 작성 하 게 됩니다.

## <a name="writing-test-drive-resource-manager-templates"></a>시험 사용 Resource Manager 템플릿 작성

시험 사용은 완전히 자동화된 모드로 배포를 실행하며, 이 때문에 시험 사용 템플릿에는 아래에 설명된 몇 가지 제한 사항이 있습니다.

### <a name="parameters"></a>매개 변수

대부분의 템플릿에는 매개 변수 집합이 있습니다. 매개 변수는 리소스 이름, 리소스 크기(예: 스토리지 계정 유형 또는 가상 머신 크기), 사용자 이름 및 암호, DNS 이름 등을 정의합니다. Azure Portal을 사용하여 솔루션을 배포할 때 이러한 매개 변수를 모두 수동으로 채우고, 사용 가능한 DNS 이름 또는 스토리지 계정 이름을 선택할 수 있습니다.

![Azure Resource Manager의 매개 변수 목록](./media/azure-resource-manager-test-drive/param1.png)

그러나 시험 사용은 사용자 상호 작용 없이 완전 자동 모드로 작동하므로 제한된 매개 변수 범주 집합만 지원합니다. 시험 사용 Resource Manager 템플릿의 매개 변수가 지원되는 범주 중 하나에 해당하지 않을 경우 **이 매개 변수를 변수 또는 상수 값으로 바꾸어야** 합니다.

매개 변수에 유효한 이름을 임의로 사용할 수 있으며, 시험 사용은 metadata-type 값을 사용하여 매개 변수 범주를 인식합니다. **모든 템플릿 매개 변수에 대해 metadata-type을 지정**해야 합니다. 그러지 않으면 템플릿이 유효성 검사를 통과하지 못합니다.

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

또한 **모든 매개 변수가 선택 사항**이므로, 원하지 않을 경우 매개 변수를 사용하지 않아도 됩니다.

### <a name="accepted-parameter-metadata-types"></a>허용되는 매개 변수 메타데이터 유형

| 메타데이터 유형   | 매개 변수 유형  | Description     | 샘플 값    |
|---|---|---|---|
| **baseuri**     | 문자열          | 배포 패키지의 기본 URI| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **username**    | 문자열          | 새 임의 사용자 이름입니다.| admin68876      |
| **password**    | 보안 문자열    | 새 임의 암호 | Lp!ACS\^2kh     |
| **세션 ID**   | 문자열          | 고유한 시험 사용 세션 ID(GUID)입니다.    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>사용자 이름

시험 사용은 이 매개 변수를 배포 패키지의 **기본 URI**로 초기화하므로 이 매개 변수를 사용하여 패키지에 포함된 파일의 URI를 구성할 수 있습니다.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

템플릿 내에서 이 매개 변수를 사용하여 시험 사용 배포 패키지에 있는 파일의 URI를 구성할 수 있습니다. 아래 예제는 연결된 템플릿의 URI를 구성하는 방법을 보여 줍니다.

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>사용자 이름

시험 사용은 이 매개 변수를 새 임의 사용자 이름으로 초기화합니다.

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

샘플 값:

    admin68876

솔루션에 임의 또는 상수 사용자 이름을 사용할 수 있습니다.

#### <a name="password"></a>password

시험 사용은 이 매개 변수를 새 임의 암호로 초기화합니다.

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

샘플 값:

    Lp!ACS^2kh

솔루션에 임의 또는 상수 암호를 사용할 수 있습니다.

#### <a name="session-id"></a>세션 ID

시험 사용은 이 매개 변수를 시험 사용 세션 ID에 해당하는 고유 GUID로 초기화합니다.

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

샘플 값:

    b8c8693e-5673-449c-badd-257a405a6dee

필요한 경우 이 매개 변수를 사용하여 시험 사용 세션을 고유하게 식별할 수 있습니다.

### <a name="unique-names"></a>고유 이름

스토리지 계정 또는 DNS 이름과 같은 일부 Azure 리소스에는 전역적으로 고유한 이름이 필요합니다.

즉, 시험 사용이 Resource Manager 템플릿을 배포할 때마다 모든\' 리소스에 대해 **고유 이름이 지정된 새 리소스 그룹**을 만듭니다. 따라서 리소스 그룹 ID의 변수 이름과 연결된 [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) 함수를 사용하여 임의의 고유 값을 생성해야 합니다.

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

매개 변수/변수 문자열(\'contosovm\')을 고유한 문자열 출력(\'resourceGroup().id\')과 연결해야 합니다. 이렇게 하면 각 변수의 고유성과 안정성이 보장됩니다.

예를 들어, 대부분의 리소스 이름은 숫자로 시작할 수 없지만, 고유한 문자열 함수는 숫자로 시작하는 문자열을 반환할 수 있습니다. 따라서 원시 고유 문자열 출력을 사용하는 경우 배포에 실패합니다. 

[이 문서](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)에서 리소스 명명 규칙 및 제한 사항에 대한 추가 정보를 확인할 수 있습니다.

### <a name="deployment-location"></a>배포 위치

여러 Azure 지역에서 시험 사용을 제공할 수 있습니다. 사용자가 최상의 사용자 경험을 제공하는 가장 가까운 지역을 선택하도록 허용하면 됩니다.

시험 사용에서 랩 인스턴스를 만드는 경우 항상 사용자가 선택한 지역에 리소스 그룹을 만든 다음, 이 그룹 컨텍스트에서 배포 템플릿을 실행합니다. 따라서 템플릿은 리소스 그룹에서 배포 위치를 선택해야 합니다.

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

그런 다음 이 위치를 특정 랩 인스턴스의 모든 리소스에 대해 사용합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

사용자 구독이 선택한 각 지역에 배포하려는 모든 리소스를 배포할 수 있는지 확인해야 합니다. 또한 사용하도록 설정하려는 모든 지역에서 가상 머신 이미지를 사용할 수 있는지 확인해야 합니다. 그러지 않으면 일부 지역에서 배포 템플릿이 작동하지 않습니다.

### <a name="outputs"></a>outputs

Resource Manager 템플릿을 사용하는 경우 일반적으로 출력을 생성하지 않고 배포할 수 있습니다. 이는 템플릿 매개 변수를 채우는 데 사용하는 모든 값을 알고 있으며 항상 리소스의 속성을 수동으로 검사할 수 있기 때문입니다.

그러나 시험 사용 Resource Manager 템플릿의 경우, 랩에 액세스하는 데 필요한 모든 정보(웹 사이트 URI, 가상 머신 호스트 이름, 사용자 이름 및 암호)를 시험 사용에 반환하는 것이 중요합니다. 이러한 변수는 고객에게 표시되므로 모든 출력 이름을 읽을 수 있는지 확인합니다.

템플릿 출력과 관련된 제한 사항은 없습니다. 단지, 시험 사용은 모든 출력 값을 **문자열**로 변환하므로 개체를 출력으로 보내는 경우 사용자에게 JSON 문자열이 표시됩니다.

예:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>구독 한도

한 가지 더 고려해야 하는 사항은 구독 및 서비스 한도입니다. 예를 들어, 최대 10개의 4 코어 가상 머신을 배포하려는 경우 랩에 사용하는 구독이 40개 코어 사용을 허용하는지 확인해야 합니다.

[이 문서](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)에서 Azure 구독 및 서비스 한도에 대한 자세한 정보를 확인할 수 있습니다. 동시에 여러 개의 시험 사용을 수행할 수 있으므로 구독이 수행할 수 있는 동시 시험 사용의 총수에 코어 수를 곱한 개수를 처리할 수 있는지 확인합니다.

### <a name="what-to-upload"></a>업로드할 사항

시험 사용 Resource Manager 템플릿은 zip 파일로 업로드되며, 다양한 배포 아티팩트를 포함할 수 있지만 **main-template.json**이라는 하나의 파일이 있어야 합니다. 이 파일은 Azure Resource Manager 배포 템플릿으로, 시험 사용은 이 파일을 사용하여 랩을 인스턴스화합니다.

이 파일 이외에 추가 리소스가 있는 경우 템플릿 내에서 외부 리소스로 참조하거나 zip 파일에 리소스를 포함할 수 있습니다.

게시 인증 중에 시험 사용은 배포 패키지의 압축을 풀고 해당 콘텐츠를 내부 시험 사용 Blob 컨테이너에 넣습니다. 컨테이너 구조는 배포 패키지의 구조를 반영합니다.

| package.zip                       | 시험 사용 Blob 컨테이너         |
|---|---|
| main-template.json                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| templates/solution.json           | https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json |
| scripts/warmup.ps1                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1  |


이 Blob 컨테이너의 URI를 기본 URI라고 합니다. 랩의 수정 버전마다 자체 Blob 컨테이너가 있으므로 랩의 수정 버전마다 자체 기본 URI가 있습니다. 시험 사용은 템플릿 매개 변수를 통해 압축을 푼 배포 패키지의 기본 URI를 템플릿에 전달할 수 있습니다.

## <a name="transforming-template-examples-for-test-drive"></a>시험 사용에 대한 템플릿 변환 예제

리소스의 아키텍처를 시험 사용 Resource Manager 템플릿으로 변환하는 프로세스는 까다로울 수 있습니다. 이 프로세스를 더 쉽게 진행할 수 있도록 [여기서 현재 배포 템플릿을 변환](./transforming-examples-for-test-drive.md)하는 가장 효율적인 방법에 대한 예제를 만들었습니다.

## <a name="how-to-publish-a-test-drive"></a>시험 사용 게시 방법

이 섹션에서는 시험 사용을 빌드한 후에 성공적으로 게시하기 위해 작성해야 하는 각 필드를 안내합니다.

![사용자 인터페이스에서 시험 사용 설정](./media/azure-resource-manager-test-drive/howtopub1.png)

첫 번째 필드이자 가장 중요한 필드는 사용자 제품에 시험 사용을 설정할지 여부를 토글하는 것입니다. **예를** 선택 하면 필요한 모든 필드가 포함 된 양식의 나머지 부분이 표시 되어 사용자가 작성할 수 있습니다. **아니요를** 선택 하면 양식을 사용할 수 없게 되 고 테스트 드라이브를 사용 하지 않도록 설정 하 여 다시 게시 하면 프로덕션에서 테스트 드라이브가 제거 됩니다.

참고: 사용자가 현재 사용 중인 시험 사용은 세션이 만료될 때까지 계속 실행됩니다.

### <a name="details"></a>세부 정보

다음 섹션에서는 시험 사용 제품에 대한 세부 정보를 작성합니다.

![시험 사용 세부 정보](./media/azure-resource-manager-test-drive/howtopub2.png)

**설명-** 테스트 드라이브의 용도에 대 한 기본 설명을 작성 하는 *데 필요* 합니다. 고객은 이 설명을 통해 제품과 관련해서 시험 사용에서 진행되는 시나리오를 확인할 수 있습니다. 

**사용자 설명서-** 테스트 드라이브 환경을 자세히 연습 하는 것이 *필요* 합니다. 고객은 이 매뉴얼을 열어 시험 사용 전반에 걸쳐 필요한 과정을 정확하게 진행할 수 있습니다. 따라서 쉽게 이해하고 진행할 수 있는 내용을 포함해야 합니다. 매뉴얼 파일은 .pdf 형식이어야 합니다.

**시험 사용 데모 비디오-** 사용자 설명서와 유사 하 게 사용 하는 *것이 좋습니다* . 테스트 드라이브 환경의 비디오 자습서를 포함 하는 것이 좋습니다. 고객은 Test Drive를 시작하기 전에나 진행하는 중에 이 비디오를 시청하고 Test Drive 전반에 걸쳐 필요한 과정을 정확하게 진행할 수 있습니다. 따라서 쉽게 이해하고 진행할 수 있는 내용을 포함해야 합니다.

- **이름** - 동영상의 제목입니다.
- **링크** - YouTube 또는 동영상의 포함 URL이어야 합니다. 포함 URL을 가져오는 방법의 예는 다음과 같습니다.
- **썸네일** - 고품질 이미지(533x324픽셀)여야 합니다. 여기서 일부 시험 사용 경험의 스크린샷을 만드는 것이 좋습니다.

아래 그림에서 시험 사용 경험 중에 고객에게 이러한 필드가 표시되는 방식을 확인할 수 있습니다.

![Marketplace 제품의 시험 사용 필드 위치](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>기술 정보 구성

다음 섹션에서는 시험 사용 Resource Manager 템플릿을 업로드하고 시험 사용 인스턴스의 구체적인 작동 방식을 정의합니다.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**인스턴스-** 원하는 인스턴스 수를 구성 하는 *데 필요한* 위치, 고객이 테스트 드라이브를 얻을 수 있는 속도

- **인스턴스** - 선택 지역에서 시험 사용 Resource Manager 템플릿이 배포되는 위치를 선택합니다. 고객이 있을 가능성이 가장 높은 지역 한 개만 선택하는 것이 좋습니다.
- **핫** - 선택한 지역당 이미 배포되었으며 액세스 대기 중인 시험 사용 인스턴스 수입니다. 고객은 배포를 기다리지 않고 이 시험 사용에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객은 전체 배포가 완료될 때까지 기다리고 싶어하지 않으며 고객 사용량이 감소하므로 **핫 인스턴스를 하나 이상** 사용하는 것이 좋습니다.
- **웜** - 배포된 후 VM이 중지되고 Azure Storage에 저장된 지역당 시험 사용 인스턴스 수입니다. 웜 인스턴스의 대기 시간은 핫 인스턴스보다 더 느리지만, 스토리지의 가동 시간 비용도 훨씬 저렴합니다.
- **콜드** - 배포할 수 있는 지역당 시험 사용 인스턴스 수입니다. 콜드 인스턴스를 사용하려면 고객이 시험 사용을 요청할 때 전체 시험 사용 Resource Manager 템플릿이 배포되어야 하므로 핫 인스턴스 또는 웜 인스턴스보다 더 느립니다. 그러나 장점은 시험 사용의 지속 기간 동안만 요금을 지불하면 된다는 것입니다.

이때 제공하려는 잠재적 동시 시험 사용의 총수를 계산하고, 구독의 할당량 한도가 해당 동시 사용량을 처리할 수 있는지 확인합니다.

**(선택한 지역 수 x 핫 인스턴스) + (선택한 지역 수 x 웜 인스턴스) + (선택한 지역 수 x 콜드 인스턴스)**

**테스트 드라이브 기간 (시간)-** 테스트 드라이브가 활성 상태로 유지 되는 기간 (시간 \#)에 대 한 *필수* 기간입니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다.

**테스트 드라이브 리소스 관리자 템플릿-** 여기에 리소스 관리자 템플릿을 업로드 *해야* 합니다. 이 파일은 위의 이전 섹션에서 빌드한 파일입니다. 기본 템플릿 파일의 이름을 “main-template.json”으로 지정하고, Resource Manager 템플릿에 필요한 키 변수에 대한 출력 매개 변수가 포함되어 있는지 확인합니다. 파일은 .zip 형식이어야 합니다.

**액세스 정보-** 고객이 테스트 드라이브를 가져온 후에 *필요한* 액세스 정보를 제공 합니다. 이러한 지침은 시험 사용 Resource Manager 템플릿의 유용한 출력 매개 변수를 공유하기 위한 것입니다. 출력 매개 변수를 포함하려면 **{{outputname}}** 과 같이 이중 중괄호를 사용합니다. 그러면 매개 변수가 해당 위치에 올바르게 삽입됩니다. 프런트 엔드에서 렌더링될 수 있도록 여기서는 HTML 문자열 서식을 사용하는 것이 좋습니다.

### <a name="test-drive-deployment-subscription-details"></a>시험 사용 배포 구독 세부 정보

마지막 섹션에서는 Azure 구독과 Azure AD(Active Directory)를 연결하여 시험 사용을 자동으로 배포하기 위한 필드를 작성합니다.

![시험 사용 배포 구독 세부 정보](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure 구독 ID-** 이는 azure 서비스 및 Azure Portal에 대 한 액세스 권한을 부여 하는 *데 필요* 합니다. 해당 구독에서 리소스 사용량이 보고되며 서비스 대금이 청구됩니다. **별도**의 시험 사용 전용 Azure 구독이 없으면 새로 만드세요. Azure Portal에 로그인하여 왼쪽 메뉴의 구독으로 이동하면 Azure 구독 ID를 확인할 수 있습니다(예: “a83645ac-1234-5ab6-6789-1h234g764ghty”).

![Azure 구독](./media/azure-resource-manager-test-drive/subdetails2.png)

**AZURE AD 테넌트 id-** *필수* 테넌트 id가 이미 있는 경우 속성-\> Directory id에서 찾을 수 있습니다.

![Azure Active Directory 속성](./media/azure-resource-manager-test-drive/subdetails3.png)

ID가 없으면 Azure Active Directory에서 새 테넌트를 만듭니다.

![Azure Active Directory 테넌트 목록](./media/azure-resource-manager-test-drive/subdetails4.png)

![Azure AD 테넌트의 조직, 도메인 및 국가/지역 정의](./media/azure-resource-manager-test-drive/subdetails5.png)

![선택 항목을 확인합니다.](./media/azure-resource-manager-test-drive/subdetails6.png)

**AZURE AD 앱 ID-** 새 응용 프로그램을 만들고 등록 하려면 다음 단계를 수행 *해야* 합니다. 이 애플리케이션을 사용하여 Test Drive 인스턴스에서 작업을 수행합니다.

1. 새로 만든 디렉터리 또는 이미 있는 디렉터리로 이동하여 필터 창에서 Azure Active Directory를 선택합니다.
2. “앱 등록”을 검색한 다음, “추가”를 클릭합니다.
3. 애플리케이션 이름을 입력합니다.
4. 유형으로 “웹앱/API”를 선택합니다.
5. 로그온 URL에는 아무 값이나 입력합니다. 해당 필드는 사용되지 않습니다.
6. 만들기를 클릭합니다.
7. 애플리케이션을 만든 후 속성 -\> 애플리케이션을 다중 테넌트로 설정으로 이동하여 저장을 클릭합니다.

[저장]을 클릭합니다. 마지막 단계에서는 등록된 이 앱의 애플리케이션 ID를 가져와 아래 그림에 표시된 시험 사용 필드에 붙여넣습니다.

![Azure AD 애플리케이션 ID 세부 정보](./media/azure-resource-manager-test-drive/subdetails7.png)

애플리케이션을 사용하여 구독을 배포할 것이므로 구독의 참가자로 애플리케이션을 추가해야 합니다. 이 작업을 수행하는 지침은 다음과 같습니다.

1. 구독 블레이드로 이동한 다음, 시험 사용 전용으로 사용할 적절한 구독을 선택합니다.
1. **액세스 제어(IAM)** 를 클릭합니다.
1. **역할 할당** 탭을 클릭 합니다.  ![새 Access Control 보안 주체를 추가](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. **역할 할당 추가**를 클릭합니다.
1. 역할을 **기여자**로 설정합니다.
1. Azure AD 애플리케이션의 이름을 입력하고 역할을 할당할 애플리케이션을 선택합니다.
    ![사용 권한 추가](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. **저장**을 클릭합니다.

**키 Azure AD 앱-** 마지막 필드는 인증 키를 생성 하는 *데 필요* 합니다. 키 아래에 키 설명을 추가하고 지속 기간을 사용 기간 제한 없음으로 설정한 다음, 저장을 선택합니다. 만료된 키를 사용해서는 **안 됩니다**. 만료된 키를 사용하면 프로덕션 환경에서 시험 사용이 중단됩니다. 이 값을 복사한 다음, 필요한 시험 사용 필드에 붙여넣습니다.

![Azure AD 애플리케이션에 대한 키를 표시합니다.](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>다음 단계

이제 모든 시험 사용 필드를 작성했으므로 제품을 **다시 게시**합니다. 시험 사용이 인증을 통과하면 제품 **미리 보기**에서 고객 경험을 포괄적으로 테스트해야 합니다. UI에서 시험 사용을 시작한 다음, Azure Portal 내에서 Azure 구독을 열고 시험 사용이 제대로 배포되는지 확인합니다.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

시험 사용 인스턴스는 고객용으로 프로비전되므로 삭제해서는 안 됩니다. 고객이 시험 사용을 완료하고 나면 시험 사용 서비스에서 이러한 리소스 그룹을 자동으로 정리하기 때문입니다.

미리 보기 제품에 문제가 없는 것으로 확인되면 제품을 **라이브로 제공**할 수 있습니다. 제품을 게시하고 나면 전체 종단 간 환경을 확인하기 위해 Microsoft에서 최종 검토 프로세스를 진행합니다. 제품이 거부되는 경우에는 엔지니어링 연락처에게 수정해야 하는 사항을 설명하는 제품 관련 알림이 전송됩니다.

추가 문의 사항이 있거나 문제 해결 관련 자문을 받거나 시험 사용의 효율성을 개선하려는 경우 [FAQ, 문제 해결 및 모범 사례](./marketing-and-best-practices.md)를 참조하세요.
