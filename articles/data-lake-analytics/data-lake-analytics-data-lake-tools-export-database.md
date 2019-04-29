---
title: Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 데이터베이스 내보내기
description: Azure Data Lake Tools for Visual Studio를 사용하여 U-SQL 데이터베이스를 내보내고 자동으로 로컬 계정으로 가져오는 방법을 알아봅니다.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: fe28aa8b88f557d4bbcdabf1de1c4bc6491743ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628576"
---
# <a name="export-a-u-sql-database"></a>U-SQL 데이터베이스 내보내기

이 문서에서는 [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)를 사용하여 U-SQL 데이터베이스를 단일 U-SQL 스크립트 및 다운로드한 리소스로 내보내는 방법에 대해 알아봅니다. 동일한 프로세스에서 내보낸 데이터베이스를 로컬 계정으로 가져올 수 있습니다.

고객은 보통 개발, 테스트 및 프로덕션을 위해 여러 환경을 유지 관리합니다. 이러한 환경은 개발자 로컬 컴퓨터의 로컬 계정과 Azure의 Azure Data Lake Analytics 계정 모두에 호스팅됩니다. 

개발 및 테스트 환경에서 U-SQL 쿼리를 개발하고 튜닝할 때 개발자는 종종 프로덕션 데이터베이스에서 작업을 다시 만들어야 합니다. 데이터베이스 내보내기 마법사를 사용하면 이 프로세스가 가속화됩니다. 개발자는 이 마법사를 사용하여 기존 데이터베이스 환경과 샘플 데이터를 다른 Data Lake Analytics 계정에 복제할 수 있습니다.

## <a name="export-steps"></a>내보내기 단계

### <a name="step-1-export-the-database-in-server-explorer"></a>1단계: 서버 탐색기에서 데이터베이스 내보내기

서버 탐색기에는 권한이 있는 모든 Data Lake Analytics 계정이 나열됩니다. 데이터베이스를 내보내려면

1. 서버 탐색기에서 내보낼 데이터베이스가 포함된 계정을 펼칩니다.
2. 해당 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **내보내기**를 선택합니다. 
   
    ![서버 탐색기 - 데이터베이스 내보내기](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     **내보내기** 메뉴 옵션을 사용할 수 없는 경우 [도구를 최신 릴리스로 업데이트해야 합니다](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>2단계: 내보낼 개체 구성

큰 데이터베이스의 작은 부분만 필요한 경우 내보내기 마법사에서 내보낼 개체의 하위 집합을 구성할 수 있습니다. 

내보내기 작업은 U-SQL 작업을 실행하여 완료됩니다. 따라서 Azure 계정에서 내보내는 데는 약간의 비용이 발생합니다.

![데이터베이스 내보내기 마법사 - 내보내기 개체 선택](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>3단계: 개체 목록 및 기타 구성 확인

이 단계에서는 **내보내기 개체 목록** 상자에서 선택한 개체를 확인할 수 있습니다. 오류가 있으면 **이전**을 선택하여 돌아가서 내보내려는 개체를 올바르게 구성합니다.

내보내기 대상에 대해 다른 설정을 구성할 수도 있습니다. 구성에 대한 설명은 다음 표에 나와 있습니다.

|구성|설명|
|-------------|-----------|
|대상 이름|내보내는 데이터베이스 리소스를 저장할 위치를 나타냅니다. 예를 들어 어셈블리, 추가 파일 및 샘플 데이터가 있습니다. 이 이름을 갖는 폴더가 로컬 데이터 루트 폴더 아래에 만들어집니다.|
|프로젝트 디렉터리|내보내는 U-SQL 스크립트를 저장할 위치를 정의합니다. 모든 데이터베이스 개체 정의는 이 위치에 저장됩니다.|
|스키마만|이 옵션을 선택하면 데이터베이스 정의 및 리소스(예: 어셈블리 및 추가 파일)만 내보냅니다.|
|스키마 및 데이터|이 옵션을 선택하면 데이터베이스 정의, 리소스 및 데이터를 내보냅니다. 테이블의 상위 N개 행을 내보냅니다.|
|로컬 데이터베이스로 자동으로 가져오기|이 옵션을 선택하면 내보내기가 완료될 때 자동으로 해당 데이터베이스를 로컬 데이터베이스로 가져옵니다.|

![데이터베이스 내보내기 마법사 - 개체 목록 및 기타 구성 내보내기](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>4단계: 내보내기 결과 확인

내보내기가 완료되면 마법사의 로그 창에서 내보낸 결과를 확인할 수 있습니다. 다음 예제에서는 어셈블리, 추가 파일 및 샘플 데이터를 포함하여 내보낸 U-SQL 스크립트 및 데이터베이스 리소스를 찾는 방법을 보여 줍니다.

![데이터베이스 내보내기 마법사 - 내보내기 결과](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>내보낸 데이터베이스를 로컬 계정에 가져오기

내보낸 데이터베이스를 가져오는 가장 편리한 방법은 3단계의 내보내기 프로세스에서 **로컬 데이터베이스로 자동으로 가져오기** 확인란을 선택하는 것입니다. 이 확인란을 선택하지 않았으면 먼저 내보내기 로그에서 내보낸 U-SQL 스크립트를 찾습니다. 그런 다음 U-SQL 스크립트를 로컬로 실행하여 데이터베이스를 로컬 계정으로 가져옵니다.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>내보낸 데이터베이스를 Data Lake Analytics 계정에 가져오기

데이터베이스를 별도의 Data Lake Analytics 계정으로 가져오려면

1. 가져오려는 Data Lake Analytics 계정의 기본 Azure Data Lake Store 계정에 어셈블리, 추가 파일 및 샘플 데이터를 포함하여 내보낸 리소스를 업로드합니다. 내보낸 리소스 폴더는 로컬 데이터 루트 폴더 아래에 있습니다. 전체 폴더를 기본 Data Lake Store 계정의 루트에 업로드합니다.
2. 업로드가 완료되면 데이터베이스를 가져올 Data Lake Analytics 계정에 내보낸 U-SQL 스크립트를 제출합니다.

## <a name="known-limitations"></a>알려진 제한 사항

현재 3단계에서 **스키마 및 데이터** 옵션을 선택하는 경우 도구에서 U-SQL 작업을 실행하여 테이블에 저장된 데이터를 내보냅니다. 이로 인해 데이터 내보내기 프로세스가 느려지고 비용이 발생할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [U-SQL 데이터베이스에 대해 자세히 알아보기](/u-sql/data-definition-language-ddl-statements) 
* [로컬 실행 및 Azure Data Lake U-SQL SDK를 사용하여 U-SQL 작업 테스트 및 디버그](data-lake-analytics-data-lake-tools-local-run.md)


