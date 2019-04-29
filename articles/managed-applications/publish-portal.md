---
title: 포털을 통해 Azure 관리되는 애플리케이션 게시 | Microsoft Docs
description: 조직의 구성원을 위한 Azure 관리되는 애플리케이션을 Azure Portal을 사용하여 만드는 방법을 보여 줍니다.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: f27d30d4709fbf373c8458629d0c8c5af4333acf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61297017"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Azure Portal을 통해 서비스 카탈로그 애플리케이션 게시

조직의 구성원을 위한 Azure [관리되는 애플리케이션](overview.md)을 Azure Portal을 사용하여 게시할 수 있습니다. 예를 들어 조직 표준을 준수하도록 하는 IT 부서에서 관리형 애플리케이션을 게시할 수 있습니다. 이러한 관리되는 애플리케이션은 Azure Marketplace가 아닌 서비스 카탈로그를 통해 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

관리되는 애플리케이션을 게시하려면 리소스를 관리할 ID를 지정합니다. Azure Active Directory 사용자 그룹을 지정하는 것이 좋습니다. 새 Azure Active Directory 사용자 그룹을 만들려면 [그룹을 만들고 Azure Active Directory에 구성원 추가](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요. 

관리되는 애플리케이션 정의가 포함된 .zip 파일은 URI를 통해 제공되어야 합니다. 저장소 Blob에 .zip 파일을 업로드하는 것이 좋습니다. 

## <a name="create-managed-application-with-portal"></a>포털을 사용하여 관리되는 애플리케이션 만들기

1. 왼쪽 위 모서리에서 **+ 새로 만들기**를 선택합니다.

   ![새 서비스](./media/publish-portal/new.png)

1. **서비스 카탈로그**를 검색합니다.

1. 결과에서 **서비스 카탈로그 관리되는 애플리케이션 정의**를 찾을 때까지 스크롤 합니다. 이를 선택합니다.

   ![관리되는 애플리케이션 정의 검색](./media/publish-portal/select-managed-apps-definition.png)

1. **만들기**를 선택하여 관리 애플리케이션 정의 만들기 프로세스를 시작합니다.

   ![관리되는 애플리케이션 정의 만들기](./media/publish-portal/create-definition.png)

1. 이름, 표시 이름, 설명, 위치, 구독 및 리소스 그룹에 대한 값을 제공합니다. 패키지 파일 URI의 경우 만든 zip 파일의 경로를 제공합니다.

   ![값 제공](./media/publish-portal/fill-application-values.png)

1. [인증 및 잠금 수준] 섹션에 도달하면 **권한 부여 추가**를 선택합니다.

   ![권한 부여 추가](./media/publish-portal/add-authorization.png)

1. 리소스를 관리할 Azure Active Directory 그룹을 선택하고 **확인**을 선택합니다.

   ![권한 부여 그룹 추가](./media/publish-portal/add-auth-group.png)

1. 모든 값을 입력했으면 **만들기**를 선택합니다.

   ![관리되는 애플리케이션 만들기](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>다음 단계

* 관리형 애플리케이션에 대한 소개는 [관리형 애플리케이션 개요](overview.md)를 참조하세요.
* 관리되는 애플리케이션 예제는 [Azure 관리되는 애플리케이션의 샘플 프로젝트](sample-projects.md)를 참조하세요.
* 관리형 애플리케이션에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.