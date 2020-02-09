---
title: Azure 앱 구성을 사용 하 여 데이터 가져오기 또는 내보내기 | Microsoft Docs
description: Azure 앱 구성에서 데이터를 가져오거나 내보내는 방법에 대해 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 64fcc8396fc1b771d0095ee595fd177d7fe99b58
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899522"
---
# <a name="import-or-export-configuration-data"></a>구성 데이터 가져오기 또는 내보내기

Azure 앱 구성은 데이터 가져오기 및 내보내기 작업을 지원 합니다. 이러한 작업을 사용 하 여 구성 데이터를 대량으로 작업 하 고 앱 구성 저장소와 코드 프로젝트 간에 데이터를 교환할 수 있습니다. 예를 들어 테스트용으로 하나의 앱 구성 저장소를 설정 하 고 프로덕션에 다른 앱 구성 저장소를 설정할 수 있습니다. 그런 다음 데이터를 두 번 입력할 필요가 없도록 파일을 통해 응용 프로그램 설정을 복사할 수 있습니다.

이 문서에서는 앱 구성을 사용 하 여 데이터를 가져오고 내보내는 방법에 대한 지침을 제공 합니다.

## <a name="import-data"></a>데이터 가져오기

가져오기는 구성 데이터를 수동으로 입력 하는 대신 기존 원본의 앱 구성 저장소로 가져옵니다. Import 함수를 사용 하 여 데이터를 앱 구성 저장소로 마이그레이션하거나 여러 원본에서 데이터를 집계 합니다. 앱 구성은 JSON, YAML 또는 속성 파일에서 가져오기를 지원 합니다.

[Azure Portal](https://portal.azure.com) 또는 [Azure CLI](./scripts/cli-import.md)를 사용 하 여 데이터를 가져옵니다. Azure Portal에서 다음 단계를 따릅니다.

1. 앱 구성 저장소로 이동 하 고 **가져오기/내보내기**를 선택 합니다.

2. **가져오기** 탭에서 **원본 서비스** > **구성 파일**을 선택 합니다.

3. 언어 > **파일 형식**으로 **를** 선택 합니다.

4. **폴더** 아이콘을 선택 하 고 가져올 파일을 찾습니다.

    ![파일 가져오기](./media/import-file.png)

5. **구분 기호**를 선택 하 고 필요에 따라 가져온 키 이름에 사용할 **접두사** 를 입력 합니다.

6. 필요에 따라 **레이블을**선택 합니다.

7. **적용** 을 선택 하 여 가져오기를 완료 합니다.

    ![파일 가져오기 완료](./media/import-file-complete.png)

## <a name="export-data"></a>데이터 내보내기

내보내기는 앱 구성에 저장 된 구성 데이터를 다른 대상에 씁니다. 예를 들어 내보내기 함수를 사용 하 여 배포 중에 응용 프로그램 코드에 포함 된 파일에 앱 구성 저장소의 데이터를 저장 합니다.

[Azure Portal](https://portal.azure.com) 또는 [Azure CLI](./scripts/cli-export.md)를 사용 하 여 데이터를 내보냅니다. Azure Portal에서 다음 단계를 따릅니다.

1. 앱 구성 저장소로 이동 하 고 **가져오기/내보내기**를 선택 합니다.

2. **내보내기** 탭에서 **대상 서비스** > **구성 파일**을 선택 합니다.

3. 필요에 따라 **접두사** 를 입력 하 고 내보낼 키에 대한 **레이블** 및 지정 시간을 선택 합니다.

4. **구분 기호** > **파일 형식을** 선택 합니다.

5. **적용** 을 선택 하 여 내보내기를 완료 합니다.

    ![내보내기 파일 완료](./media/export-file-complete.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)  
