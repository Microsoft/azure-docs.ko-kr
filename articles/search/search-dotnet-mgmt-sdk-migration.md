---
title: Azure Search .NET Management SDK 버전 2로 업그레이드 - Azure Search
description: 이전 버전에서 Azure Search .NET Management SDK 버전 2로 업그레이드합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.custom: seodec2018
ms.openlocfilehash: 369e5283d7350729e0d8cc44f94f1f2e71b133c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817002"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Azure Search .NET Management SDK 버전 2로 업그레이드
버전 1.0.2 또는 이전 버전의 [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk)를 사용하는 경우 이 문서를 통해 버전 2로 애플리케이션을 업그레이드할 수 있습니다.

Azure Search .NET Management SDK 버전 2에는 이전 버전에서 변경된 사항이 일부 포함되어 있습니다. 대부분 소소한 변경이므로 코드를 변경하는 데 최소한의 작업만 필요합니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>버전 2의 새로운 기능
Azure Search .NET Management SDK 버전 2는 이전 SDK 버전(특히 2015-08-19)과 동일한 일반 공급 Azure Search Management REST API 버전을 대상으로 합니다. SDK 변경 내용은 SDK 자체의 유용성을 개선하기 위한 엄격한 클라이언트 쪽 변경 내용입니다. 이러한 변경 내용에는 다음이 포함됩니다.

* 이제 `Services.CreateOrUpdate` 및 해당 비동기 버전은 프로비저닝 `SearchService`를 자동으로 폴링하며, 서비스 프로비저닝이 완료될 때까지 반환되지 않습니다. 따라서 이러한 폴링 코드를 직접 작성하지 않아도 됩니다.
* 서비스 프로비저닝을 여전히 수동으로 폴링하려는 경우 새로운 `Services.BeginCreateOrUpdate` 메서드 또는 비동기 버전 중 하나를 사용할 수 있습니다.
* 새 메서드 `Services.Update` 및 해당 비동기 버전이 SDK에 추가되었습니다. 이러한 메서드는 HTTP PATCH를 사용하여 서비스의 증분 업데이트를 지원합니다. 예를 들어, 이제 원하는 `partitionCount` 및 `replicaCount` 속성만 포함하는 `SearchService` 인스턴스를 이러한 메서드에 전달하여 서비스를 크기를 조정할 수 있습니다. `Services.Get`을 호출하고, 반환된 `SearchService`를 수정한 후 `Services.CreateOrUpdate`에 전달하는 이전 방식은 계속 지원되지만 더 이상 필요하지 않습니다. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Management.Search` 에 대한 NuGet 참조를 업데이트합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구조화된 방식에 따라 다시 빌드할 수 있습니다. 다시 빌드할 수 있으면 배포할 준비가 된 것입니다.

빌드가 실패하는 경우 변경된 일부 SDK 인터페이스를 구현했기 때문일 수 있습니다(예: 단위 테스트를 위해). 이 문제를 해결하려면 `BeginCreateOrUpdateWithHttpMessagesAsync`와 같은 새로운 메서드를 구현해야 합니다.

모든 빌드 오류를 수정했다면 원하는 새 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능에 대한 자세한 내용은 [버전 2의 새로운 기능](#WhatsNew)에 나와 있습니다.

## <a name="conclusion"></a>결론
SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생하면 [Azure Search MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)을 통해 자유롭게 도움을 요청하세요. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Search]"라는 접두사를 지정해야 합니다.

Azure Search를 이용해 주셔서 감사합니다!
