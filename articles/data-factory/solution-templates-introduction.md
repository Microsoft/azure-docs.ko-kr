---
title: Azure Data Factory용 템플릿 개요 | Microsoft Docs
description: 미리 정의된 템플릿을 사용하여 Azure Data Factory를 빠르게 시작하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60786979"
---
# <a name="templates"></a>템플릿

템플릿은 Data Factory를 빠르게 시작할 수 있게 해주는 미리 정의된 Azure Data Factory 파이프라인입니다. 템플릿은 Data Factory를 처음 사용하며 빠르게 시작하려는 경우에 유용합니다. 이러한 템플릿은 데이터 통합 프로젝트를 빌드하기 위한 개발 시간을 줄여 개발자의 생산성을 향상합니다.

## <a name="create-data-factory-pipelines-from-templates"></a>템플릿에서 Data Factory 파이프라인 만들기

다음 두 가지 방법으로 템플릿에서 Data Factory 파이프라인 생성을 시작할 수 있습니다.

1.  개요 페이지에서, **템플릿에서 파이프라인 만들기**를 선택하여 템플릿 갤러리를 엽니다.

    ![개요 페이지에서 템플릿 갤러리 열기](media/solution-templates-introduction/templates-intro-image1.png)

1.  리소스 탐색기의 작성자 탭에서 **+**, **템플릿에서 파이프라인 만들기**를 차례로 선택하여 템플릿 갤러리를 엽니다.

    ![작성자 탭에서 템플릿 갤러리 열기](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>템플릿 갤러리

![템플릿 갤러리](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>기본 제공 Data Factory 템플릿

Data Factory는 데이터 팩터리 파이프라인 템플릿을 저장하기 위해 Azure Resource Manager 템플릿을 사용합니다. 초과 하 여 사용에 대 한 상자 데이터 팩터리 템플릿에서 매니페스트 파일과 함께 모든 Resource Manager 템플릿을 확인할 수 있습니다 합니다 [공식 Azure 데이터 팩터리 GitHub 리포지토리의](https://github.com/Azure/Azure-DataFactory/tree/master/templates)합니다. Microsoft에서 제공하는 미리 정의된 템플릿에는 다음 항목이 포함되지만 이에만 제한되지는 않습니다.

-   템플릿 복사:

    -   [데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)
    
    -   [LastModifiedDate 하 여 새 파일 복사](solution-template-copy-new-files-lastmodifieddate.md)

    -   [파일 기반 저장소 간에 여러 파일 컨테이너 복사](solution-template-copy-files-multiple-containers.md)

    -   [데이터베이스에서 델타 복사](solution-template-delta-copy-with-control-table.md)

    -   \<원본\>에서 \<대상\>으로 복사

        -   Amazon S3에서 Azure Data Lake Store Gen 2로 복사

        -   Google Big Query에서 Azure Data Lake Store Gen 2로 복사

        -   HDF에서 Azure Data Lake Store Gen 2로 복사

        -   Netezza에서 Azure Data Lake Store Gen 1로 복사

        -   온-프레미스 SQL Server에서 Azure SQL Database로 복사

        -   온-프레미스 SQL Server에서 Azure SQL Data Warehouse로 복사

        -   온-프레미스 Oracle에서 Azure SQL Data Warehouse로 복사

-   SSIS 템플릿

    -   SSIS 패키지를 실행하도록 Azure-SSIS Integration Runtime 예약

-   템플릿 변환

    -   [Azure Databricks를 사용한 ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>내 템플릿

파이프라인 탭에서 **템플릿으로 저장**을 선택하여 파이프라인을 템플릿으로 저장할 수도 있습니다.

![파이프라인을 템플릿으로 저장](media/solution-templates-introduction/templates-intro-image4.png)

템플릿 갤러리의 **내 템플릿** 섹션에서 템플릿으로 저장된 파이프라인을 볼 수 있습니다. 리소스 탐색기의 **템플릿** 섹션에서도 확인할 수 있습니다.

![내 템플릿](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> 내 템플릿 기능을 사용하려면 GIT 통합을 사용하도록 설정해야 합니다. Azure DevOps GIT 및 GitHub가 둘 다 지원됩니다.
