---
title: Azure App Service Mobile Apps에서 Cordova 앱 만들기 | Microsoft Docs
description: 이 자습서에 따라 Azure 모바일 앱 백 엔드를 사용하여 Apache Cordova 개발을 시작할 수 있습니다.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
tags: ''
keywords: cordova,javascript,모바일,클라이언트
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: crdun
ms.openlocfilehash: 7014d09bbb62e78c37a9496628e3509b6eaaa4ac
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123334"
---
# <a name="create-an-apache-cordova-app"></a>Apache Cordova 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Apache Cordova 모바일 앱에 추가하는 방법을 보여 줍니다.  새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 *할 일 목록* Apache Cordova 앱을 만듭니다.

이 자습서를 완료해야 Azure App Service에서 Mobile Apps 기능을 사용하는 방법에 대한 다른 모든 Apache Cordova 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Visual Studio Community 2017] 이상이 설치된 PC
* [Visual Studio Tools for Apache Cordova]
* [활성 Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

Visual Studio를 바이패스하고 직접 Apache Cordova 명령줄을 사용할 수도 있습니다.  명령줄을 사용하면 Mac 컴퓨터에서 자습서를 완료하는 경우 유용합니다.  명령줄을 사용하여 Apache Cordova 클라이언트 애플리케이션을 컴파일하는 작업은 이 자습서에서 다루지 않습니다.

## <a name="create-an-azure-mobile-app-backend"></a>Azure 모바일 앱 백 엔드 만들기
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Apache Cordova 앱 다운로드 및 실행
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>다음 단계
이제 이 빠른 시작 자습서를 완료했으므로 다음 자습서 중 하나로 이동합니다.

* Apache Cordova 앱에 [오프라인 데이터 추가](app-service-mobile-cordova-get-started-offline-data.md)
* [인증을 추가](app-service-mobile-cordova-get-started-users.md) 합니다.
* [푸시 알림을 추가](app-service-mobile-cordova-get-started-push.md) 합니다.

Azure App Service의 주요 개념에 대해 자세히 알아봅니다.

* [오프라인 데이터]
* [인증]
* [푸시 알림]

SDK 사용 방법을 알아봅니다.

* [Apache Cordova SDK]
* [ASP.NET 서버 SDK]
* [Node.js 서버 SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[오프라인 데이터]: app-service-mobile-offline-data-sync.md
[인증]: app-service-mobile-auth.md
[푸시 알림]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET 서버 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 서버 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
