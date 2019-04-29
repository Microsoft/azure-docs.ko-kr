---
title: Azure Portal을 사용하여 서비스 카탈로그 앱 배포 | Microsoft Docs
description: 관리되는 애플리케이션의 소비자에게 Azure Portal을 통해 서비스 카탈로그 앱을 배포하는 방법을 보여 줍니다.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589043"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Azure Portal을 통해 서비스 카탈로그 앱 배포

[이전에 나온 빠른 시작](publish-managed-app-definition-quickstart.md)에서는 관리되는 애플리케이션 정의를 게시했습니다. 이 빠른 시작에서는 해당 정의에서 서비스 카탈로그 앱을 만듭니다.

## <a name="create-service-catalog-app"></a>서비스 카탈로그 앱 만들기

Azure Portal에서 다음 단계를 사용하세요.

1. **리소스 만들기**를 선택합니다.

   ![리소스 만들기](./media/deploy-service-catalog-quickstart/create-new.png)

1. **서비스 카탈로그 관리되는 응용 프로그램**을 검색하고 사용 가능한 옵션 중에서 선택합니다.

   ![서비스 카탈로그 애플리케이션 검색](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. 관리되는 애플리케이션 서비스에 대한 설명을 확인합니다. **만들기**를 선택합니다.

   ![만들기 선택](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. 포털에 사용자가 액세스할 수 있는 관리되는 애플리케이션 정의가 표시됩니다. 사용 가능한 정의에서 배포하려는 정의를 선택합니다. 이 빠른 시작에서는 이전 빠른 시작에서 만든 **관리되는 저장소 계정**을 사용합니다. **만들기**를 선택합니다.

   ![배포할 정의 선택](./media/deploy-service-catalog-quickstart/select-definition.png)

1. **기본 사항** 탭에 값을 지정합니다. 서비스 카탈로그 앱을 배포할 Azure 구독을 선택합니다. **applicationGroup**이라는 새 리소스 그룹을 만듭니다. 앱의 위치를 선택합니다. 작업을 마쳤으면 **확인**을 선택합니다.

   ![기본 사항에 대한 값 제공](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. 저장소 계정 이름의 접두사를 제공합니다. 만들려는 저장소 계정의 유형을 선택합니다. 작업을 마쳤으면 **확인**을 선택합니다.

   ![저장소 값 제공](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. 요약을 검토합니다. 유효성 검사가 성공하면 **확인**을 선택하여 배포를 시작합니다.

   ![요약 보기](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>결과 보기

서비스 카탈로그 앱이 배포되면 두 개의 새 리소스 그룹이 생성됩니다. 한 리소스 그룹은 서비스 카탈로그 앱을 포함합니다. 다른 리소스 그룹은 서비스 카탈로그 앱의 리소스를 포함합니다.

1. **applicationGroup** 리소스 그룹에서 서비스 카탈로그 앱을 확인합니다.

   ![애플리케이션 보기](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. **applicationGroup{hash-characters}** 리소스 그룹에서 서비스 카탈로그 앱의 리소스를 확인합니다.

   ![리소스 보기](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션의 정의 파일을 만드는 방법을 알아보려면 [관리되는 애플리케이션 정의 만들기 및 게시](publish-service-catalog-app.md)를 참조하세요.
* Azure CLI의 경우 [Azure CLI를 사용하여 서비스 카탈로그 앱 배포](./scripts/managed-application-cli-sample-create-application.md)를 참조하세요.
* PowerShell의 경우 [PowerShell을 사용하여 서비스 카탈로그 앱 배포](./scripts/managed-application-poweshell-sample-create-application.md)를 참조하세요.