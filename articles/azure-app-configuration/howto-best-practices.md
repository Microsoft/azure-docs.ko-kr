---
title: Azure 앱 구성 모범 사례 | Microsoft Docs
description: Azure 앱 구성을 최대한 활용 하는 방법 알아보기
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 37f93099027f810e8089119536e089e07080d0bc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898640"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 앱 구성 모범 사례

이 문서에서는 Azure 앱 구성을 사용 하는 경우의 일반적인 패턴 및 모범 사례에 대해 설명 합니다.

## <a name="key-groupings"></a>키 그룹화

앱 구성은 키를 구성 하는 두 가지 옵션을 제공 합니다.

* 키 접두사
* 레이블

하나 또는 두 옵션 중 하나를 사용 하 여 키를 그룹화 할 수 있습니다.

*키 접두사* 는 키의 시작 부분입니다. 이름에 동일한 접두사를 사용 하 여 키 집합을 논리적으로 그룹화 할 수 있습니다. 접두사는 네임 스페이스를 형성 하는 URL 경로와 유사한 `/`같이 구분 기호로 연결 된 여러 구성 요소를 포함할 수 있습니다. 이러한 계층은 하나의 앱 구성 저장소에 많은 응용 프로그램, 구성 요소 서비스 및 환경에 대한 키를 저장 하는 경우에 유용 합니다.

중요 한 점은 키가 해당 설정의 값을 검색 하기 위해 응용 프로그램 코드에서 참조 하는 것 이라는 것입니다. 키가 변경 되 면 안 됩니다. 그렇지 않으면 발생 될 때마다 코드를 수정 해야 합니다.

*레이블은* 키에 대한 특성입니다. 키의 변형을 만드는 데 사용 됩니다. 예를 들어 여러 버전의 키에 레이블을 할당할 수 있습니다. 버전은 반복, 환경 또는 일부 다른 컨텍스트 정보 일 수 있습니다. 응용 프로그램은 다른 레이블을 지정 하 여 완전히 다른 키 값 집합을 요청할 수 있습니다. 따라서 모든 키 참조는 코드에서 변경 되지 않은 상태로 유지 됩니다.

## <a name="key-value-compositions"></a>키-값 컴퍼지션

앱 구성은이를 사용 하 여 저장 된 모든 키를 독립적인 엔터티로 처리 합니다. 앱 구성은 키 간의 관계를 유추 하거나 해당 계층 구조를 기반으로 키 값을 상속 하지 않습니다. 그러나 응용 프로그램 코드에서 적절 한 구성 스태킹와 결합 된 레이블을 사용 하 여 여러 키 집합을 집계할 수 있습니다.

예제를 살펴보겠습니다. **Asset1**라는 설정이 있는 경우 해당 값은 개발 환경에 따라 달라질 수 있습니다. 빈 레이블과 "Development" 라는 레이블이 있는 "Asset1" 라는 키를 만듭니다. 첫 번째 레이블에서 **Asset1**의 기본값을 입력 하 고, 후자에 "Development"에 대한 특정 값을 입력 합니다.

코드에서 먼저 레이블 없이 키 값을 검색 한 다음 "개발" 레이블을 사용 하 여 동일한 키 값 집합을 두 번 검색 합니다. 값을 두 번째로 검색할 때 키의 이전 값을 덮어씁니다. .NET Core 구성 시스템을 사용 하면 여러 구성 데이터 집합을 서로 "스택" 할 수 있습니다. 하나 이상의 집합에 키가 있는 경우 해당 키를 포함 하는 마지막 집합이 사용 됩니다. .NET Core와 같은 최신 프로그래밍 프레임 워크를 사용 하면 네이티브 구성 공급자를 사용 하 여 앱 구성에 액세스 하는 경우이 스택 기능을 무료로 이용할 수 있습니다. 다음 코드 조각에서는 .NET Core 응용 프로그램에서 스태킹를 구현할 수 있는 방법을 보여 줍니다.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>앱 구성 부트스트랩

앱 구성 저장소에 액세스 하려면 Azure Portal에서 사용할 수 있는 연결 문자열을 사용할 수 있습니다. 연결 문자열에는 자격 증명 정보가 포함 되어 있으므로 비밀으로 간주 됩니다. 이러한 암호는 Azure Key Vault에 저장 해야 하며, 코드를 검색 하려면 Key Vault에 인증 해야 합니다.

더 나은 옵션은 Azure Active Directory에서 관리 되는 id 기능을 사용 하는 것입니다. 관리 id를 사용 하는 경우 앱 구성 저장소에 대한 액세스를 부트스트랩 하기 위한 앱 구성 엔드포인트 URL만 필요 합니다. 응용 프로그램 코드 (예: *appsettings* 파일)에 URL을 포함할 수 있습니다. 자세한 내용은 [Azure 관리 id와 통합을](howto-integrate-azure-managed-service-identity.md) 참조 하세요.

## <a name="app-or-function-access-to-app-configuration"></a>앱 구성에 대한 앱 또는 기능 액세스

다음 방법 중 하나를 사용 하 여 웹 앱 또는 함수의 앱 구성에 대한 액세스를 제공할 수 있습니다.

* Azure Portal를 통해 App Service의 응용 프로그램 설정에서 앱 구성 저장소에 대한 연결 문자열을 입력 합니다.
* Key Vault에서 앱 구성 저장소에 연결 문자열을 저장 하 고 [App Service에서 참조](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)합니다.
* Azure 관리 되는 id를 사용 하 여 앱 구성 저장소에 액세스 합니다. 자세한 내용은 [Azure 관리 id와 통합](howto-integrate-azure-managed-service-identity.md)을 참조 하세요.
* 앱 구성에서 App Service로 구성을 푸시합니다. 앱 구성은 데이터를 App Service으로 직접 보내는 내보내기 함수 (Azure Portal 및 Azure CLI)를 제공 합니다. 이 방법을 사용 하면 응용 프로그램 코드를 변경할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

* [키 및 값](./concept-key-value.md)
