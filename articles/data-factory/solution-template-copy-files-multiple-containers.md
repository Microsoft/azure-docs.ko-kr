---
title: Azure Data Factory를 사용 하 여 여러 컨테이너에서 파일을 복사 | Microsoft Docs
description: 솔루션 템플릿을 사용 하 여 Azure Data Factory를 사용 하 여 여러 컨테이너에서 파일을 복사 하는 방법에 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635183"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사

이 문서에서는 파일 저장소 간의 여러 컨테이너에서 파일을 복사 하는 데 사용할 수 있는 솔루션 템플릿을 설명 합니다. 예를 들어, AWS S3에서 Azure Data Lake Store에 데이터 레이크로를 사용할 수 있습니다. 또는 템플릿을 사용 하 여 다른 하나의 Azure Blob 저장소 계정에서 모든 복제 수 있습니다.

> [!NOTE]
> 것 보다 효율적으로 사용할 단일 컨테이너에서 파일을 복사 하려는 경우는 [데이터 복사 도구](copy-data-tool.md) 단일 복사 작업을 사용 하 여 파이프라인을 만듭니다. 이 문서에서 서식 파일은 간단한 시나리오에 필요한 것 보다 더 합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 원본 storage 저장소에서 컨테이너를 열거합니다. 대상 저장소에 해당 컨테이너를 복사합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **GetMetadata** 원본 storage 저장소를 검색 하 고 컨테이너 목록을 가져옵니다.
- **ForEach** 에서 컨테이너 목록을 가져옵니다 합니다 **GetMetadata** 활동 목록을 반복 후 및 복사 작업에 각 컨테이너를 전달 합니다.
- **복사** 대상 저장소를 원본 storage 저장소에서 각 컨테이너에 복사 합니다.

이 템플릿은 다음 두 개의 매개 변수를 정의합니다.
- *SourceFilePath* 컨테이너 목록을 가져올 수 있는 데이터 원본 저장소의 경로입니다. 대부분의 경우 이 경로는 여러 컨테이너 폴더를 포함하는 루트 디렉터리입니다. 이 매개 변수의 기본값은 `/`입니다.
- *대상 파일 경로* 대상 저장소에서에 파일 복사 될 경로입니다. 이 매개 변수의 기본값은 `/`입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 로 이동 합니다 **여러 파일 컨테이너 파일 저장소 간에 복사** 템플릿. 만들기는 **새로 만들기** 원본 storage 저장소에 연결 합니다. 원본 저장소 저장소에서 여러 컨테이너에서 파일을 복사 하려는 경우

    ![원본에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 만들기는 **새로 만들기** 대상 storage 저장소에 연결 합니다.

    ![대상에 대한 새 연결 만들기](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 선택 **이 템플릿을 사용 하 여**입니다.

    ![이 템플릿 사용](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 다음 예제와 같이 파이프라인을 볼 수 있습니다.

    ![파이프라인 표시](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 선택 **디버그**를 입력 합니다 **매개 변수**를 선택한 후 **마침**합니다.

    ![파이프라인 실행](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 결과를 검토합니다.

    ![결과 검토](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용 하 여 제어 테이블을 사용 하 여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)

- [Azure Data Factory를 사용 하 여 여러 컨테이너에서 파일을 복사 합니다.](solution-template-copy-files-multiple-containers.md)