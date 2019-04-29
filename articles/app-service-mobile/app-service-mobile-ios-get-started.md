---
title: Azure App Service Mobile Apps에서 iOS 앱 만들기 | Microsoft Docs
description: 이 자습서에 따라 Objective-C 또는 Swift로 iOS용 Azure 모바일 앱 백 엔드 사용을 시작합니다.
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: d0d6a3d9da2768c2d7b04bd9c4a7c24fba9eb65e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129011"
---
# <a name="create-an-ios-app"></a>iOS 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요

이 자습서는 클라우드 백 엔드 서비스인 [Azure App Service Mobile Apps](app-service-mobile-value-prop.md)를 iOS 앱에 추가하는 방법을 보여줍니다. 첫 번째 단계는 Azure에서 새 모바일 백 엔드를 만드는 것입니다. 그런 다음, Azure에 데이터를 저장하는 간단한 *Todo 목록* iOS 샘플 앱을 다운로드합니다.

이 자습서를 완료하려면 Mac과 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>단계 i: 새 Azure 모바일 앱 백 엔드 만들기

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>단계 II: 백 엔드 프로젝트 구성

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>단계 III: iOS 앱 다운로드 및 실행

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
