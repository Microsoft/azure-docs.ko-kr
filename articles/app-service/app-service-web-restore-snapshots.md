---
title: 백업에서 복원 - Azure App Service
description: 스냅숏에서 앱을 복원하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.custom: seodec18
ms.openlocfilehash: 8d4290f1411749e2d8d3d27fbd792ceeeea47ef7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851368"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>스냅숏에서 Azure의 앱 복원
이 문서에서는 스냅숏에서 [Azure App Service](../app-service/overview.md)의 앱을 복원하는 방법을 설명합니다. 앱의 스냅숏 중 하나를 기반으로 이전 상태로 앱을 복원할 수 있습니다. 스냅숏 백업을 사용하도록 설정할 필요는 없으며 플랫폼이 데이터 복구를 위해 모든 앱의 스냅숏을 자동으로 저장합니다.

스냅숏은 증분 섀도 복사본으로, 일반 [백업](manage-backup.md)보다 몇 가지 장점이 있습니다.
- 파일 잠금으로 인한 파일 복사 오류가 없습니다.
- 저장소 크기 제한이 없습니다.
- 구성이 필요 없습니다.

스냅숏 복원은 **프리미엄** 계층 이상에서 실행되는 앱에서 사용할 수 있습니다. 앱 확장에 대한 자세한 내용은 [Azure에서 앱 확장](web-sites-scale.md)을 참조하세요.

## <a name="limitations"></a>제한 사항

- 이 기능은 현재 미리 보기로 제공됩니다.
- 동일한 앱이나 해당 앱에 속한 슬롯에만 복원할 수 있습니다.
- App Service는 복원을 수행하는 동안 대상 앱 또는 대상 슬롯을 중지합니다.
- App Service는 플랫폼 데이터 복구 목적으로 3개월 분량의 스냅숏을 보관합니다.
- 지난 30일간의 스냅숏만 복원할 수 있습니다.
- App Service Environment에서 실행되는 App Services는 스냅숏을 지원하지 않습니다.
 

## <a name="restore-an-app-from-a-snapshot"></a>스냅숏에서 앱 복원

1. [Azure Portal](https://portal.azure.com)에서 앱의 **설정** 페이지에서 **백업**을 클릭하여 **백업** 페이지를 표시합니다. 그런 다음 **스냅숏(미리 보기)** 섹션에서 **복원**을 클릭합니다.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. **복원** 페이지에서 복원할 스냅숏을 선택합니다.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. **복원 대상**에서 앱 복원 대상을 지정합니다.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > **덮어쓰기**를 선택하면 앱의 현재 파일 시스템에 있는 기존 데이터를 모두 지우고 덮어씁니다. **확인**을 클릭하기 전에 수행하려는 작업이 맞는지 확인하세요.
   > 
   > 
      
   > [!Note]
   > 현재의 기술적 제한으로 인해 동일한 배율 단위로만 앱을 복원할 수 있습니다. 이 제한 사항은 향후 릴리스에서 제거될 예정입니다.
   > 
   > 
   
    **기존 앱**을 선택하여 슬롯에 복원할 수 있습니다. 이 옵션을 사용하려면 먼저 앱에 슬롯을 만들어야 합니다.

4. 사이트 구성을 복원하도록 선택할 수 있습니다.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. **확인**을 클릭합니다.
