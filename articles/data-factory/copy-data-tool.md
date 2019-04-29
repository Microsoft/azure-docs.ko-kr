---
title: Azure Data Factory 데이터 복사 도구 | Microsoft Docs
description: Azure Data Factory UI의 데이터 복사 도구에 대한 정보를 제공합니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787692"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Azure Data Factory의 데이터 복사 도구
Azure Data Factory 데이터 복사 도구는 일반적으로 종단 간 데이터 통합 시나리오의 첫 번째 단계인 Data Lake로의 데이터 수집 프로세스를 용이하게 하며 최적화합니다.  이 도구는 시간을 절감합니다. 특히, Azure Data Factory를 사용하여 처음으로 데이터 원본에서 데이터를 수집할 때 시간 절감 효과를 얻을 수 있습니다. 이 도구를 사용할 경우 몇 가지 이점은 다음과 같습니다.

- Azure Data Factory 데이터 복사 도구를 사용할 때는 연결된 서비스, 데이터 세트, 파이프라인, 작업 및 트리거에 대한 Data Factory 정의를 이해할 필요가 없습니다. 
- 데이터 복사 도구의 흐름을 보면 Data Lake에 데이터를 로드하는 과정을 쉽게 이해할 수 있습니다. 이 도구는 선택한 원본 데이터 저장소에서 선택한 대상/싱크 데이터 저장소로 데이터를 복사하는 데 필요한 모든 Data Factory 리소스를 자동으로 만듭니다. 
- 데이터 복사 도구는 작성 시에 수집되는 데이터의 유효성을 검사하여 처음부터 데이터 자체에 잠재적인 오류가 없도록 도와줍니다.
- Data Lake에 데이터를 로드하는 복잡한 비즈니스 논리를 구현해야 하는 경우에도, Data Factory UI의 작업별 작성 기능을 사용하여 데이터 복사 도구로 만든 Data Factory 리소스를 편집할 수 있습니다. 

다음 표에서는 데이터 복사 도구 및 Data Factory UI의 작업별 작성 기능을 사용해야 하는 경우에 대한 지침을 제공합니다. 

| 데이터 복사 도구 | 작업별(복사 작업) 작성 |
| -------------- | -------------------------------------- |
| Azure Data Factory 엔터티(연결된 서비스, 데이터 세트, 파이프라인 등)에 대해 학습하지 않고도 데이터 로드 태스크를 쉽게 빌드하려는 경우 | 데이터를 Lake에 로드하기 위한 복잡하고 유연한 논리를 구현하려는 경우 |
| Data Lake에 많은 수의 데이터 아티팩트를 신속하게 로드하려는 경우 | 데이터를 정리 또는 처리하기 위해 복사 작업을 후속 작업에 체인 연결하려는 경우 |

데이터 복사 도구를 시작하려면 데이터 팩터리 홈 페이지에서 **데이터 복사** 타일을 클릭합니다.

![시작 페이지 - 데이터 복사 도구 링크](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Data Lake에 데이터를 로드하기 위한 직관적인 흐름
이 도구를 사용하면 직관적인 흐름을 통해 다양한 원본에서 대상으로 데이터를 몇 분만에 쉽게 이동할 수 있습니다.  

1. **원본**에 대한 설정을 구성합니다.
2. **대상**에 대한 설정을 구성합니다. 
3. 열 매핑, 성능 설정 및 내결함성 설정과 같은 복사 작업에 대한 **고급 설정**을 구성합니다. 
4. 데이터 로드 작업에 대한 **일정**을 지정합니다. 
5. 만들려는 Data Factory 엔터티의 **요약**을 검토합니다. 
6. 필요에 따라 파이프라인을 **편집**하여 복사 작업에 대한 설정을 업데이트합니다. 

   이 도구는 처음부터 빅 데이터를 염두에 두고 설계되었으며 다양한 데이터 및 개체 유형을 지원합니다. 수백 개의 폴더, 파일 또는 테이블을 이동하는 데 이 도구를 사용할 수 있습니다. 이 도구는 자동 데이터 미리 보기, 스키마 캡처 및 자동 매핑, 데이터 필터링도 지원합니다.

![데이터 복사 도구](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>자동 데이터 미리 보기
선택된 원본 데이터 저장소의 데이터 일부를 미리 확인하여 복사할 데이터의 유효성을 검사할 수 있습니다. 또한 원본 데이터가 텍스트 파일에 있는 경우 데이터 복사 도구는 텍스트 파일을 구문 분석하여 행 및 열 구분 기호와 스키마를 자동으로 검색합니다.

![파일 설정](./media/copy-data-tool/file-format-settings.png)

검색 후:

![검색된 파일 설정 및 미리 보기](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>스키마 캡처 및 자동 매핑
데이터 원본의 스키마는 대부분의 경우에서 데이터 대상의 스키마와 동일하지 않을 수 있습니다. 이 시나리오에서는 원본 스키마의 열을 대상 스키마의 열에 매핑해야 합니다.

데이터 복사 도구는 원본 및 대상 저장소 간에 열을 매핑할 때 사용자의 동작을 모니터링하고 학습합니다. 원본 데이터 저장소에서 한 개 이상의 열을 선택하고 대상 스키마에 매핑하면, 데이터 복사 도구는 양쪽에서 선택한 열 쌍에 대한 패턴을 분석하기 시작합니다. 그런 다음, 나머지 열에 같은 패턴을 적용합니다. 따라서 몇 번의 클릭만으로 모든 열이 원하는 방식대로 대상에 매핑되는 것을 볼 수 있습니다.  데이터 복사 도구가 제공하는 열 매핑 선택 항목이 만족스럽지 않은 경우, 이를 무시하고 열을 수동으로 매핑할 수 있습니다. 한편, 데이터 복사 도구는 패턴을 지속적으로 학습 및 업데이트하며, 궁극적으로 사용자가 원하는 열 매핑의 올바른 패턴에 도달합니다. 

> [!NOTE]
> SQL Server 또는 Azure SQL Database에서 Azure SQL Data Warehouse로 데이터를 복사할 때, 테이블이 대상 저장소에 없을 경우 데이터 복사 도구는 원본 스키마를 사용하여 자동 테이블 만들기를 지원합니다. 

## <a name="filter-data"></a>데이터 필터링
싱크 데이터 저장소에 복사해야 하는 데이터만 선택하도록 원본 데이터를 필터링할 수 있습니다. 필터링하면 싱크 데이터 저장소에 복사할 데이터 양이 줄고 이에 따라 복사 작업의 처리량이 향상됩니다. 데이터 복사 도구는 SQL 쿼리 언어를 사용하여 관계형 데이터베이스의 데이터를 필터링하거나 Azure Blob 폴더의 파일을 필터링할 수 있는 유연한 방법을 제공합니다. 

### <a name="filter-data-in-a-database"></a>데이터베이스의 데이터 필터링
다음 스크린샷에서는 데이터를 필터링하는 SQL 쿼리를 보여 줍니다.

![데이터베이스의 데이터 필터링](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Azure Blob 폴더의 데이터 필터링
폴더 경로의 변수를 사용하여 폴더에서 데이터를 복사할 수 있습니다. 지원되는 변수는 **{year}**, **{month}**, **{day}**, **{hour}** 및 **{minute}** 입니다. 예를 들어 inputfolder/{year}/{month}/{day}와 같습니다. 

다음과 같은 형식의 입력 폴더가 있다고 가정하겠습니다. 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

**파일 또는 폴더**의 **찾아보기** 단추를 클릭하여 이러한 폴더(예: 2016->03->01->02)중 하나를 찾아서 **선택**을 클릭합니다. 텍스트 상자에 2016/03/01/02가 표시됩니다. 

그런 후 **2016**을 **{year}** 로, **03**을 **{month}** 로, **01**을 **{day}** 로, **02**를 **{hour}** 로 바꾼 다음, **탭** 키를 누릅니다. 이러한 네 가지 변수의 형식을 선택하는 드롭다운 목록이 표시됩니다.

![파일 또는 폴더 필터링](./media/copy-data-tool/filter-file-or-folder.png)

데이터 복사 도구는 파이프라인을 만들 때 {year}, {month}, {day}, {hour} 및 {minute}를 나타내는 데 사용할 수 있는 식, 함수 및 시스템 변수로 매개 변수를 생성합니다. 자세한 내용은 [분할된 데이터를 읽거나 쓰는 방법](how-to-read-write-partitioned-data.md) 문서를 참조하세요.

## <a name="scheduling-options"></a>일정 옵션
복사 작업을 한 번만 또는 일정에 따라(시간별, 일별, 등) 실행할 수 있습니다. 이러한 옵션은 온-프레미스, 클라우드 및 로컬 데스크톱을 포함한 다양한 환경의 커넥터에 사용할 수 있습니다. 

일 회 복사 작업을 통해 원본에서 대상으로 한 번만 데이터를 이동할 수 있습니다. 이는 모든 크기 및 지원되는 형식의 데이터에 적용됩니다. 예약 복사를 통해 지정한 반복 주기에 따라 데이터를 복사할 수 있습니다. 다양한 설정(예: 재시도, 시간 제한, 경고 등)을 사용하여 예약 복사를 구성할 수 있습니다.

![일정 옵션](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>다음 단계
데이터 복사 도구를 사용하는 다음 자습서를 시도해 보세요.

- [빠른 시작: 데이터 복사 도구를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-copy-data-tool.md)
- [자습서: 데이터 복사 도구를 사용하여 Azure에 데이터 복사](tutorial-copy-data-tool.md) 
- [자습서: 데이터 복사 도구를 사용하여 Azure에 온-프레미스 데이터 복사](tutorial-hybrid-copy-data-tool.md)
