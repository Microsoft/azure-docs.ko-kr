---
title: '자습서: Azure App Configuration을 사용하여 기능 플래그 관리'
titleSuffix: Azure App Configuration
description: 이 자습서에서는 Azure App Configuration을 사용하여 애플리케이션과 별도로 기능 플래그를 관리하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: dd816ebcf2a40e6a0b7febcc3fe5c1006dac20bb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209952"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>자습서: Azure App Configuration에서 기능 플래그 관리

Azure App Configuration에 모든 기능 플래그를 저장한 후 한 곳에서 관리할 수 있습니다. App Configuration에는 기능 플래그용으로 특별히 디자인된 **기능 관리자**라는 포털 UI가 있습니다. 또한 App Configuration은 기본적으로 .NET Core 기능 플래그 데이터 스키마를 지원합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration에서 기능 플래그를 정의하고 관리합니다.
> * 애플리케이션에서 기능 플래그에 액세스합니다.

## <a name="create-feature-flags"></a>기능 플래그 만들기

App Configuration의 Azure Portal에 있는 기능 관리자는 애플리케이션에서 사용하는 기능 플래그를 만들고 관리하기 위한 UI를 제공합니다.

새 기능 플래그를 추가하려면

1. **기능 관리자** >  **+추가**를 선택하여 기능 플래그를 추가합니다.

    ![기능 플래그 목록](./media/azure-app-configuration-feature-flags.png)

1. 기능 플래그의 고유 키 이름을 입력합니다. 이 이름은 코드에서 플래그를 참조하는 데 필요합니다.

1. 원한다면 기능 플래그에 설명을 제공합니다.

1. 기능 플래그의 초기 상태를 설정합니다. 이 상태는 일반적으로 *해제* 또는 *설정*입니다. 기능 플래그에 대한 필터를 추가할 경우 *설정* 상태가 *조건부*로 변경됩니다.

    ![기능 플래그 만들기](./media/azure-app-configuration-feature-flag-create.png)

1. 상태가 *설정*이면 **+필터 추가**를 선택하여 상태를 충족할 추가 조건을 지정합니다. 기본 제공 또는 사용자 지정 필터 키를 입력한 다음, **+ 매개 변수 추가**를 선택하여 하나 이상의 매개 변수를 필터와 연결합니다. 기본 제공 필터는 다음과 같습니다.

    | 키 | JSON 매개 변수 |
    |---|---|
    | Microsoft.Percentage | {"Value": 0-100 percent} |
    | Microsoft.TimeWindow | {"Start": UTC time, "End": UTC time} |

    ![기능 플래그 필터](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>기능 플래그 상태 업데이트

기능 플래그의 상태 값을 변경하려면

1. **기능 관리자**를 선택합니다.

1. 수정하려는 기능 플래그의 오른쪽에서 줄임표( **...** )를 선택한 다음, **편집**을 선택합니다.

1. 기능 플래그에 새로운 상태를 설정합니다.

## <a name="access-feature-flags"></a>기능 플래그 액세스

기능 관리자에서 만든 기능 플래그가 저장되고 일반 키 값으로 검색됩니다. 이러한 플래그는 특수 네임스페이스 접두사 `.appconfig.featureflag`로 보관됩니다. 기본 키 값을 보려면 구성 탐색기를 사용하세요. 애플리케이션에서는 App Configuration 구성 공급자, SDK, 명령줄 확장 및 REST API를 사용하여 이러한 값을 검색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 App Configuration을 사용하여 기능 플래그 및 해당 상태를 관리하는 방법을 알아보았습니다. App Configuration 및 ASP.NET Core의 기능 관리 지원에 대한 자세한 내용은 다음 문서를 참조하세요.

* [ASP.NET Core 앱에서 기능 플래그 사용](./use-feature-flags-dotnet-core.md)
