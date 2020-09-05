---
title: Azure 앱 구성 모범 사례 | Microsoft Docs
description: Azure 앱 구성을 사용 하는 동안 모범 사례를 알아봅니다. 이 항목에서는 키 그룹화, 키-값 컴퍼지션, 앱 구성 부트스트랩 등을 다룹니다.
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
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d532b8aab87840f4b6ad90daedba743597f4fe43
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588061"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 앱 구성 모범 사례

이 문서에서는 Azure 앱 구성을 사용 하는 경우의 일반적인 패턴 및 모범 사례에 대해 설명 합니다.

## <a name="key-groupings"></a>키 그룹화

앱 구성은 키를 구성 하는 두 가지 옵션을 제공 합니다.

* 키 접두사
* 레이블

하나 또는 두 옵션 중 하나를 사용 하 여 키를 그룹화 할 수 있습니다.

*키 접두사* 는 키의 시작 부분입니다. 이름에 동일한 접두사를 사용 하 여 키 집합을 논리적으로 그룹화 할 수 있습니다. 접두사는 `/` 네임 스페이스를 구성 하기 위해 URL 경로와 유사한 구분 기호로 연결 된 여러 구성 요소를 포함할 수 있습니다. 이러한 계층은 하나의 앱 구성 저장소에 많은 응용 프로그램, 구성 요소 서비스 및 환경에 대 한 키를 저장 하는 경우에 유용 합니다.

중요 한 점은 키가 해당 설정의 값을 검색 하기 위해 응용 프로그램 코드에서 참조 하는 것 이라는 것입니다. 키가 변경 되 면 안 됩니다. 그렇지 않으면 발생 될 때마다 코드를 수정 해야 합니다.

*레이블은* 키에 대 한 특성입니다. 키의 변형을 만드는 데 사용 됩니다. 예를 들어 여러 버전의 키에 레이블을 할당할 수 있습니다. 버전은 반복, 환경 또는 일부 다른 컨텍스트 정보 일 수 있습니다. 응용 프로그램은 다른 레이블을 지정 하 여 완전히 다른 키 값 집합을 요청할 수 있습니다. 따라서 모든 키 참조는 코드에서 변경 되지 않은 상태로 유지 됩니다.

## <a name="key-value-compositions"></a>키-값 컴퍼지션

앱 구성은이를 사용 하 여 저장 된 모든 키를 독립적인 엔터티로 처리 합니다. 앱 구성은 키 간의 관계를 유추 하거나 해당 계층 구조를 기반으로 키 값을 상속 하지 않습니다. 그러나 응용 프로그램 코드에서 적절 한 구성 스태킹와 결합 된 레이블을 사용 하 여 여러 키 집합을 집계할 수 있습니다.

예제를 살펴보겠습니다. **Asset1**라는 설정이 있는 경우 해당 값은 개발 환경에 따라 달라질 수 있습니다. 빈 레이블과 "Development" 라는 레이블이 있는 "Asset1" 라는 키를 만듭니다. 첫 번째 레이블에서 **Asset1**의 기본값을 입력 하 고, 후자에 "Development"에 대 한 특정 값을 입력 합니다.

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

[레이블을 사용 하 여 환경 마다 다른 구성을 사용 하도록 설정 하면](./howto-labels-aspnet-core.md) 전체 예제를 제공 합니다.

## <a name="app-configuration-bootstrap"></a>앱 구성 부트스트랩

앱 구성 저장소에 액세스 하려면 Azure Portal에서 사용할 수 있는 연결 문자열을 사용할 수 있습니다. 연결 문자열에는 자격 증명 정보가 포함 되어 있으므로 비밀으로 간주 됩니다. 이러한 암호는 Azure Key Vault에 저장 해야 하며, 코드를 검색 하려면 Key Vault에 인증 해야 합니다.

더 나은 옵션은 Azure Active Directory에서 관리 되는 id 기능을 사용 하는 것입니다. 관리 id를 사용 하는 경우 앱 구성 저장소에 대 한 액세스를 부트스트랩 하기 위한 앱 구성 끝점 URL만 필요 합니다. 응용 프로그램 코드 (예: 파일 * 에appsettings.js* )에 URL을 포함할 수 있습니다. 자세한 내용은 [Azure 관리 id와 통합을](howto-integrate-azure-managed-service-identity.md) 참조 하세요.

## <a name="app-or-function-access-to-app-configuration"></a>앱 구성에 대 한 앱 또는 기능 액세스

다음 방법 중 하나를 사용 하 여 웹 앱 또는 함수의 앱 구성에 대 한 액세스를 제공할 수 있습니다.

* Azure Portal를 통해 App Service의 응용 프로그램 설정에서 앱 구성 저장소에 대 한 연결 문자열을 입력 합니다.
* Key Vault에서 앱 구성 저장소에 연결 문자열을 저장 하 고 [App Service에서 참조](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)합니다.
* Azure 관리 되는 id를 사용 하 여 앱 구성 저장소에 액세스 합니다. 자세한 내용은 [Azure 관리 id와 통합](howto-integrate-azure-managed-service-identity.md)을 참조 하세요.
* 앱 구성에서 App Service로 구성을 푸시합니다. 앱 구성은 데이터를 App Service으로 직접 보내는 내보내기 함수 (Azure Portal 및 Azure CLI)를 제공 합니다. 이 방법을 사용 하면 응용 프로그램 코드를 변경할 필요가 없습니다.

## <a name="reduce-requests-made-to-app-configuration"></a>앱 구성에 대 한 요청 줄이기

앱 구성에 대 한 과도 한 요청은 제한 또는 초과분 요금이 발생할 수 있습니다. 요청 수를 줄이려면 다음을 수행 합니다.

* 특히 구성 값이 자주 변경 되지 않는 경우 새로 고침 제한 시간을 늘립니다. [ `SetCacheExpiration` 메서드](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)를 사용 하 여 새 새로 고침 제한 시간을 지정 합니다.

* 개별 키를 감시 하지 않고 단일 *센티널 키*를 시청 합니다. 센티널 키가 변경 되는 경우에만 모든 구성을 새로 고칩니다. 예는 [ASP.NET Core 앱에서 동적 구성 사용](enable-dynamic-configuration-aspnet-core.md) 을 참조 하세요.

* 변경을 지속적으로 폴링하는 대신 Azure Event Grid를 사용 하 여 구성이 변경 될 때 알림을 받을 수 있습니다. 자세한 내용은 [웹 끝점으로 Azure 앱 구성 이벤트 라우팅](./howto-app-configuration-event.md) 을 참조 하세요.

## <a name="importing-configuration-data-into-app-configuration"></a>구성 데이터를 앱 구성으로 가져오기

앱 구성에서는 Azure Portal 또는 CLI를 사용 하 여 현재 구성 파일에서 구성 설정을 대량으로 [가져올](https://aka.ms/azconfig-importexport1) 수 있는 옵션을 제공 합니다. 동일한 옵션을 사용 하 여 관련 저장소 간에 앱 구성에서 값을 내보낼 수도 있습니다. GitHub 리포지토리와의 지속적인 동기화를 설정 하려는 경우 [Github 작업](https://aka.ms/azconfig-gha2) 을 사용 하 여 앱 구성의 혜택을 받을 때 기존 원본 제어 방법을 계속 사용할 수 있습니다.

## <a name="multi-region-deployment-in-app-configuration"></a>앱 구성에서 다중 지역 배포

앱 구성은 지역 서비스입니다. 지역별 구성이 서로 다른 응용 프로그램의 경우 이러한 구성을 한 인스턴스에 저장 하면 단일 실패 지점이 발생할 수 있습니다. 여러 지역에 지역 당 하나의 앱 구성 인스턴스를 배포 하는 것이 더 나은 옵션 일 수 있습니다. 지역 재해 복구, 성능 및 보안 siloing에 도움이 될 수 있습니다. 또한 지역별로를 구성 하면 대기 시간이 향상 되 고, 제한이 인스턴스당 제한 되므로 분리 된 제한 할당량을 사용 합니다. 재해 복구 완화를 적용 하려면 [여러 구성 저장소](./concept-disaster-recovery.md)를 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [키 및 값](./concept-key-value.md)
