---
title: Dynamics 365 for Customer Engagement 기술 자산 만들기 - Azure Marketplace | Microsoft Docs
description: Dynamics 365 for Customer Engagement 애플리케이션 제안에 대한 기술 자산을 만듭니다.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 50c4fd512206cdf17ebb555acb88de2a3f74c2bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472671"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Azure 애플리케이션 제안에 대한 기술 자산 만들기

일반적으로 [Dynamics 365 for Customer Engagement 앱용 SDK](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk)를 사용하여 솔루션을 개발합니다.  솔루션은 [Dynamics 365 for Customer Engagement 앱에 대한 프로그래밍 모델](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models)에서 설명한 대로 다양한 형태를 취하고 있습니다.  솔루션 요구 사항에 가장 적합한 형태를 선택합니다.  솔루션을 개발하는 경우 확장성 선택 항목, 솔루션 구성 요소 및 버전 호환성과 같이 해결해야 하는 여러 가지 문제가 있습니다.  자세한 내용은 [솔루션 소개](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions)를 참조하세요.

AppSource에 게시되는 대부분의 Dynamics 365 솔루션은 패키지 파일로 배포되는 관리형 애플리케이션입니다.


## <a name="creating-and-storing-the-package"></a>패키지 만들기 및 저장

Dynamics 365 for Customer Engagement 제안을 만드는 방법에 대한 내용은 설명서의 [AppSource에 앱 게시](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) 섹션에서 찾을 수 있습니다.  다음 항목에서는 솔루션 패키지 파일을 만들고 이를 Azure 스토리지에 업로드하는 방법에 대해 자세히 설명합니다.

- [4단계: 앱에 사용할 AppSource 패키지 만들기](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) - 관리형 애플리케이션을 나타내고 솔루션 자산 폴더, 사용자 지정 코드 DLL, MIME 형식 정보 파일, AppSource 패키지 아이콘, 사용 조건 파일(HTML) 및 콘텐츠 파일(XML)을 포함하는 압축 파일(zip)을 만드는 방법에 대해 설명합니다.
- [5단계: Azure Storage에 AppSource 패키지 저장 및 SAS 키를 사용하여 URL 생성](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) - AppSource 패키지 파일을 Microsoft Azure Blob 스토리지 계정에 저장하고 SAS(공유 액세스 서명) 키를 사용하여 패키지 파일을 공유하는 방법에 대해 설명합니다. Azure Storage 위치에서 인증을 위한 패키지 파일이 검색된 다음, AppSource 평가 및 게시를 위한 패키지 파일이 검색됩니다.


## <a name="next-steps"></a>다음 단계

아직 만들지 않은 경우 [Dynamics 365 for Customer Engagement 제안을 만듭니다](./cpp-create-offer.md).  그러면 [제안을 게시](./cpp-publish-offer.md)할 준비가 됩니다.
