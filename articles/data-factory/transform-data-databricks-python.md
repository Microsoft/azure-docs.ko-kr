---
title: Databricks Python을 사용하여 데이터 변환 - Azure | Microsoft Docs
description: Databricks Python을 실행하여 데이터를 처리하거나 변환하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 60aafd983d1c21777276683a8685376a247d11f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589219"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Azure Databricks에서 Python 작업을 실행하여 데이터 변환

[Data Factory 파이프라인](concepts-pipelines-activities.md)의 Azure Databricks Python 작업은 Azure Databricks 클러스터에서 Python 파일을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는  [데이터 변환 활동](transform-data.md)  문서에서 작성합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다.

11분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python 작업 정의

Databricks Python 작업에 대한 샘플 JSON 정의는 다음과 같습니다.

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python 작업 속성

다음 표에서는 JSON 정의에 사용하는 JSON 속성을 설명합니다.

|자산|설명|필수|
|---|---|---|
|이름|파이프라인의 작업 이름입니다.|예|
|description|작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.|아닙니다.|
|형식|Databricks Python 작업의 경우 작업 형식은 DatabricksSparkPython입니다.|예|
|linkedServiceName|Python 작업이 실행되는 Databricks 연결된 서비스의 이름입니다. 이 연결된 서비스에 대한 자세한 내용은  [연결된 서비스 Compute](compute-linked-services.md)  문서를 참조하세요.|예|
|pythonFile|실행할 Python 파일의 URI입니다. DBFS 경로만이 지원됩니다.|예|
|매개 변수|Python 파일에 전달되는 명령줄 매개 변수입니다. 문자열의 배열입니다.|아닙니다.|
|라이브러리|작업을 실행할 클러스터에 설치할 라이브러리의 목록입니다. <문자열, 개체>의 배열일 수 있습니다.|아닙니다.|

## <a name="supported-libraries-for-databricks-activities"></a>Databricks 활동에 지원되는 라이브러리

위의 Databricks 활동 정의에서 *jar*, *egg*, *maven*, *pypi*, *cran* 라이브러리 유형을 지정합니다.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

자세한 내용은 라이브러리 유형에 대한 [Databricks 설명서](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)를 참조하세요.

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks에서 라이브러리를 업로드하는 방법

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Azure Databricks 작업 영역 UI 사용](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

UI를 사용하여 추가된 라이브러리의 dbfs 경로를 얻으려면 [Databricks CLI(설치)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)를 사용할 수 있습니다. 

일반적으로 Jar 라이브러리는 UI를 사용하는 동안 dbfs:/FileStore/jars 아래에 저장됩니다. *databricks fs ls dbfs:/FileStore/jars* CLI를 통해 모두 나열할 수 있습니다. 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Databricks CLI를 사용하여 라이브러리 복사](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

예제: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*