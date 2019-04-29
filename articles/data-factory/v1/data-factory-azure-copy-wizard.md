---
title: Azure Data Factory 복사 마법사 | Microsoft 문서
description: Data Factory Azure 복사 마법사를 사용하여 지원되는 데이터 소스의 데이터를 싱크로 복사하는 방법에 대해 알아보세요.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567676"
---
# <a name="azure-data-factory-copy-wizard"></a>Azure Data Factory 복사 마법사
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 

Azure Data Factory 복사 마법사는 일반적으로 종단 간 데이터 통합 시나리오의 첫 번째 단계인 데이터 수집 프로세스를 용이하게 합니다. Azure Data Factory 복사 마법사를 진행할 때는 연결된 서비스, 데이터 집합 및 파이프라인에 대한 JSON 정의를 이해할 필요가 없습니다. 선택한 데이터 원본에서 선택한 대상으로 데이터를 복사하는 파이프라인이 자동으로 만들어집니다. 또한 복사 마법사는 만들 때 수집된 데이터의 유효성을 검사하는 데 도움을 줍니다. 이렇게 하면 특히 데이터 원본에서 처음으로 데이터를 처리할 때 시간을 절약할 수 있습니다. 복사 마법사를 시작하려면 Data Factory 홈 페이지에서 **데이터 복사** 타일을 클릭합니다.

![복사 마법사](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>빅 데이터를 위한 설계
이 마법사를 사용하면 다양한 원본에서 대상으로 데이터를 몇 분만에 쉽게 이동할 수 있습니다. 마법사를 완료하면 종속 Data Factory 엔터티(연결된 서비스 및 데이터 집합)와 함께 복사 작업을 포함한 파이프라인이 자동으로 만들어집니다. 파이프라인을 만드는 데 필요한 추가 단계는 없습니다.   

![데이터 원본 선택](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Azure Blob에서 Azure SQL Database 테이블로 데이터를 복사하기 위한 샘플 파이프라인을 만드는 단계별 지침은 [복사 마법사 자습서](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.
>
>

마법사는 처음부터 빅 데이터를 염두에 두고 설계되었으며 다양한 데이터 및 개체 유형을 지원합니다. 수백 개의 폴더, 파일 또는 테이블을 이동하는 Data Factory 파이프라인을 만들 수 있습니다. 이 마법사는 자동 데이터 미리 보기, 스키마 캡처 및 매핑, 데이터 필터링을 지원합니다.

## <a name="automatic-data-preview"></a>자동 데이터 미리 보기
복사할 데이터인지 확인하기 위해 선택한 데이터 원본에서 데이터의 일부를 미리 볼 수 있습니다. 또한 원본 데이터가 텍스트 파일에 있는 경우 복사 마법사는 텍스트 파일을 구문 분석하여 행 및 열 구분 기호와 스키마를 자동으로 파악합니다.

![파일 형식 설정](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>스키마 캡처 및 매핑
입력 데이터의 스키마는 경우에 따라 출력 데이터의 스키마와 일치하지 않을 수 있습니다. 이 시나리오에서는 원본 스키마의 열을 대상 스키마의 열에 매핑해야 합니다.

> [!TIP]
> SQL Server 또는 Azure SQL Database에서 Azure SQL Data Warehouse로 데이터를 복사할 때 테이블이 대상 저장소에 없을 경우 Data Factory는 원본의 스키마를 사용하여 자동 테이블 만들기를 지원합니다. [Azure Data Factory를 사용하여 Azure SQL Data Warehouse 간 데이터 이동](./data-factory-azure-sql-data-warehouse-connector.md)에서 자세히 알아 보십시오.
>

드롭다운 목록을 사용하여 원본 스키마에서 대상 스키마의 열에 매핑할 열을 선택합니다. 복사 마법사는 열 매핑에 대한 패턴을 파악하려고 시도합니다. 나머지 열에도 동일한 패턴을 적용하므로 스키마 매핑을 완료하기 위해 각 열을 개별적으로 선택할 필요가 없습니다. 원하는 경우 드롭다운 목록을 사용하여 열을 하나씩 매핑하면 이러한 매핑을 무시할 수 있습니다. 열을 더 많이 매핑할수록 패턴이 더 정확하게 됩니다. 복사 마법사는 패턴을 지속적으로 업데이트하고, 궁극적으로는 도달하려는 열 매핑의 올바른 패턴에 도달합니다.     

![스키마 매핑](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>데이터 필터링
싱크 데이터 저장소에 복사해야 하는 데이터만 선택하도록 원본 데이터를 필터링할 수 있습니다. 필터링하면 싱크 데이터 저장소에 복사할 데이터 양이 줄고 이에 따라 복사 작업의 처리량이 향상됩니다. 마법사에서는 SQL 쿼리 언어를 사용하여 관계형 데이터베이스의 데이터를 필터링하거나 [Data Factory 함수 및 변수](data-factory-functions-variables.md)를 사용하여 Azure Blob 폴더의 파일을 필터링할 수 있는 유연한 방법을 제공합니다.   

### <a name="filtering-of-data-in-a-database"></a>데이터베이스 데이터 필터링
다음 스크린샷은 `Text.Format` 함수와 `WindowStart` 변수를 사용하는 SQL 쿼리를 보여 줍니다.

![식 유효성 검사](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure Blob 폴더의 데이터 필터링
폴더 경로의 변수를 사용하여 [시스템 변수](data-factory-functions-variables.md#data-factory-system-variables)를 기반으로 런타임 시 결정되는 폴더의 데이터를 복사할 수 있습니다. 지원되는 변수는 **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** 및 **{custom}** 입니다. 예를 들어 inputfolder/{year}/{month}/{day}와 같습니다.

다음과 같은 형식의 입력 폴더가 있다고 가정하겠습니다.

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

**파일 또는 폴더**의 **찾아보기** 단추를 클릭하여 이러한 폴더(예: 2016->03->01->02)중 하나를 찾아서 **선택**을 클릭합니다. 텍스트 상자에 `2016/03/01/02`가 표시됩니다. 이제 **2016**을 **{year}** 로, **03**을 **{month}** 로, **01**을 **{day}** 로, **02**를 **{hour}** 로 바꾼 다음 **탭** 키를 누릅니다. 이러한 네 가지 변수의 형식을 선택하는 드롭다운 목록이 표시됩니다.

![시스템 변수 사용](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

다음 스크린샷에 표시된 것처럼 **사용자 지정** 변수 및 모든 [지원되는 형식 문자열](https://msdn.microsoft.com/library/8kb3ddd4.aspx)도 사용할 수 있습니다. 해당 구조의 폴더를 선택하려면 먼저 **찾아보기** 단추를 사용합니다. 그런 다음 값을 **{custom}** 으로 바꾸고, **탭** 키를 누르면 형식 문자열을 입력할 수 있는 텍스트 상자가 표시됩니다.     

![사용자 지정 변수 사용](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>일정 옵션
복사 작업을 한 번만 또는 일정에 따라(시간별, 일별, 등) 실행할 수 있습니다. 이 두 가지 옵션은 온-프레미스, 클라우드 및 로컬 데스크톱 복사본을 포함한 환경의 다양한 커넥터에 모두 사용할 수 있습니다.

일 회 복사 작업을 통해 원본에서 대상으로 한 번만 데이터를 이동할 수 있습니다. 이는 모든 크기 및 지원되는 형식의 데이터에 적용됩니다. 예약 복사를 통해 미리 정해진 반복 주기에 따라 데이터를 복사할 수 있습니다. 다양한 설정(예: 재시도, 시간 제한, 경고 등)을 사용하여 예약 복사를 구성할 수 있습니다.

![일정 속성](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>다음 단계
Data Factory 복사 마법사를 사용하여 복사 작업이 있는 파이프라인을 만드는 방법은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.
