---
title: 여러 컨테이너의 파일 복사
description: Azure Data Factory를 사용 하 여 솔루션 템플릿을 사용 하 여 여러 컨테이너의 파일을 복사 하는 방법을 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 0c4c26ba163f83483b3eb48e51d91f9a919a887c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439816"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사

이 문서에서는 파일 저장소 간에 여러 컨테이너의 파일을 복사 하는 데 사용할 수 있는 솔루션 템플릿에 대해 설명 합니다. 예를 들어이 파일을 사용 하 여 data lake를 AWS S3에서 Azure Data Lake Store로 마이그레이션할 수 있습니다. 또는 템플릿을 사용 하 여 Azure Blob 저장소 계정 간에 모든 항목을 복제할 수 있습니다.

> [!NOTE]
> 단일 컨테이너에서 파일을 복사 하려면 [데이터 복사 도구](copy-data-tool.md) 를 사용 하 여 단일 복사 작업으로 파이프라인을 만드는 것이 더 효율적입니다. 이 문서에서는 이러한 간단한 시나리오에 대한 템플릿이 필요 합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 원본 저장소 저장소의 컨테이너를 열거 합니다. 그런 다음 해당 컨테이너를 대상 저장소에 복사 합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **GetMetadata** 는 원본 저장소 저장소를 검색 하 고 컨테이너 목록을 가져옵니다.
- **ForEach** **GetMetadata** 작업에서 컨테이너 목록을 가져온 다음 목록을 반복 하 고 각 컨테이너를 복사 작업에 전달 합니다.
- **복사** 는 원본 저장소 저장소의 각 컨테이너를 대상 저장소에 복사 합니다.

템플릿은 다음 매개 변수를 정의합니다.
- *Sourcefilefolder* 는 컨테이너 목록을 가져올 수 있는 데이터 원본 저장소의 폴더 경로입니다. 경로는 여러 컨테이너 폴더를 포함 하는 루트 디렉터리입니다. 이 매개 변수의 기본값은 `sourcefolder`입니다.
- *Sourcefiledirectory* 는 데이터 원본 저장소의 루트 디렉터리 아래에 있는 하위 폴더 경로입니다. 이 매개 변수의 기본값은 `subfolder`입니다.
- *Destinationfilefolder* 는 대상 저장소에서 파일이 복사 될 폴더 경로입니다. 이 매개 변수의 기본값은 `destinationfolder`입니다.
- *Destinationfiledirectory* 는 대상 저장소에서 파일이 복사 되는 하위 폴더 경로입니다. 이 매개 변수의 기본값은 `subfolder`입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **파일 저장 템플릿 간에 여러 파일 복사 컨테이너** 로 이동 합니다. 원본 저장소 저장소에 대한 **새** 연결을 만듭니다. 원본 저장소 저장소는에서 여러 컨테이너의 파일을 복사 하려는 위치입니다.

    ![원본에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 대상 저장소 저장소에 대한 **새** 연결을 만듭니다.

    ![대상에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. **이 템플릿 사용**을 선택합니다.

    ![이 템플릿 사용](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 다음 예제와 같이 파이프라인이 표시 됩니다.

    ![파이프라인 표시](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. **디버그**를 선택 하 고 **매개 변수**를 입력 한 다음 **마침**을 선택 합니다.

    ![파이프라인 실행](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 결과를 검토합니다.

    ![결과 검토](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 있는 컨트롤 테이블을 사용 하 여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)

- [Azure Data Factory를 사용 하 여 여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)
