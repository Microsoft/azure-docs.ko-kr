---
title: Azure 앱 구성을 사용 하 여 데이터 가져오기 또는 내보내기 | Microsoft Docs
description: 데이터 가져오기 또는 내보내기와 Azure 앱 구성 하는 방법을 알아봅니다
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 6557f3a4129076c1e8708208e5d4e0d4f53dcec3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741789"
---
# <a name="import-or-export-configuration-data"></a>구성 데이터 가져오기 또는 내보내기

Azure 앱 구성을 지 원하는 데이터 가져오기 및 내보내기 작업 합니다. 앱 구성 저장소와 코드 간에 데이터를 대량 및 exchange에서 구성 데이터와 함께 작동 하도록 이러한 작업을 사용 하 여 프로젝트입니다. 예를 들어, 하나의 앱 구성 저장소 테스트용 및 프로덕션용으로 다른 설정할 수 있습니다. 그런 다음 데이터를 두 번 입력 하지 않아도 되도록 응용 프로그램 설정 파일을 통해 서로 복사할 수 있습니다.

이 문서에서는 앱 구성을 사용 하 여 데이터 가져오기 및 내보내기에 대 한 지침을 제공 합니다.

## <a name="import-data"></a>데이터 가져오기

가져오기는 수동으로 입력 하는 대신 기존 원본에서 데이터를 앱 구성을 저장 하는 구성 합니다. 가져오기 함수를 사용 하 여 앱 구성 저장소나 여러 원본의 데이터를 집계로 데이터를 마이그레이션할 수 있습니다. 앱 구성 JSON, YAML, 또는 속성 파일에서 가져오는 것을 지원 합니다.

중 하나를 사용 하 여 데이터를 가져올는 [Azure portal](https://aka.ms/azconfig/portal) 또는 [Azure CLI](./scripts/cli-import.md)합니다. Azure portal에서 다음이 단계를 따르세요.

1. 앱 구성 저장소를 찾아 선택할 **Import/Export**합니다.

2. 에 **가져오기** 탭을 선택 **서비스를 원본** > **구성 파일**합니다.

3. 선택 **언어용** > **파일 형식**합니다.

4. 선택 된 **폴더** 아이콘과 가져올 파일을 찾습니다.

    ![파일 가져오기](./media/import-file.png)

5. 선택 된 **구분 기호**, 필요에 따라 입력을 **접두사** 가져온된 키 이름에 사용할.

6. 필요에 따라 선택 된 **레이블**합니다.

7. 선택 **적용** 가져오기를 완료 하려고 합니다.

    ![파일 가져오기 완료](./media/import-file-complete.png)

## <a name="export-data"></a>데이터 내보내기

내보내기는 다른 대상 앱 구성에 저장 된 구성 데이터를 씁니다. 예를 들어, 배포 하는 동안 응용 프로그램 코드를 사용 하 여 포함 된 파일에는 앱 구성 저장소에 데이터를 저장 하려면 내보내기 함수를 사용 합니다.

중 하나를 사용 하 여 데이터를 내보낼 합니다 [Azure portal](https://aka.ms/azconfig/portal) 또는 [Azure CLI](./scripts/cli-export.md)합니다. Azure portal에서 다음이 단계를 따르세요.

1. 앱 구성 저장소를 찾아 선택할 **Import/Export**합니다.

2. 에 **내보낼** 탭을 선택 **서비스를 대상** > **구성 파일**합니다.

3. 필요에 따라 입력을 **접두사** 선택한를 **레이블** 및에-시간 키를 내보낼 수 있습니다.

4. 선택 된 **파일 형식** > **구분 기호**합니다.

5. 선택 **적용** 내보내기를 완료 합니다.

    ![내보내기 파일 완료](./media/export-file-complete.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)  
