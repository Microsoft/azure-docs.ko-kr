---
title: Azure Data Factory의 삭제 작업
description: Azure Data Factory에서 삭제 작업을 사용하여 다양한 파일 저장소에서 파일을 삭제하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: bcc7ebd8d9a6e61425ba7cd980a400c3fe756492
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762337"
---
# <a name="delete-activity-in-azure-data-factory"></a>Azure Data Factory의 삭제 작업

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Azure Data Factory에서 삭제 작업을 사용 하 여 온-프레미스 저장소 저장소나 클라우드 저장소 저장소에서 파일이 나 폴더를 삭제할 수 있습니다. 파일이 더 이상 필요하지 않을 때 파일을 정리하거나 보관하려면 이 작업을 사용합니다.

> [!WARNING]
> 저장소에서 일시 삭제를 사용 하도록 설정 하지 않은 경우 삭제 된 파일 또는 폴더를 복원할 수 없습니다. 따라서 삭제 작업을 사용하여 파일이나 폴더를 삭제할 때는 주의해야 합니다.

## <a name="best-practices"></a>모범 사례

삭제 작업을 사용하기 위한 몇 가지 권장 사항은 다음과 같습니다.

-   나중에 복원해야 하는 파일은 삭제 작업을 사용하여 삭제하기 전에 백업합니다.

-   Data Factory에 스토리지 저장소에서 폴더 또는 파일을 삭제하기 위한 쓰기 권한이 있는지 확인합니다.

-   쓰고 있는 파일을 동시에 삭제하지 않아야 합니다. 

-   온-프레미스 시스템에서 파일 또는 폴더를 삭제 하려면 3.14 보다 큰 버전의 자체 호스팅 통합 런타임을 사용 하 고 있는지 확인 합니다.

## <a name="supported-data-stores"></a>지원되는 데이터 저장소

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [파일 시스템](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)

## <a name="syntax"></a>구문

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "storeSettings": {
            "type": "<source type>",
            "recursive": true/false,
            "maxConcurrentConnections": <number>
        },
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>형식 속성

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 데이터 세트 | 삭제할 파일이나 폴더를 결정하기 위한 데이터 세트 참조를 제공합니다. | 예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 파일을 재귀적으로 삭제할지 여부를 나타냅니다.  | 아니요. 기본값은 `false`입니다. |
| maxConcurrentConnections | 폴더 또는 파일 삭제를 위해 동시에 스토리지 저장소에 연결할 수 있는 연결 수입니다.   |  아니요. 기본값은 `1`입니다. |
| enablelogging | 삭제된 폴더 또는 파일 이름을 기록해야 하는지 여부를 나타냅니다. true이면 로그 파일을 읽어 삭제 작업 동작을 추적할 수 있도록 로그 파일을 저장할 스토리지 계정을 추가로 제공해야 합니다. | 예 |
| logStorageSettings | enablelogging = true일 때만 해당됩니다.<br/><br/>삭제 작업으로 삭제한 폴더 또는 파일 이름이 들어 있는 로그 파일을 저장할 위치를 지정할 수 있는 스토리지 속성 그룹입니다. | 예 |
| linkedServiceName | enablelogging = true일 때만 해당됩니다.<br/><br/>삭제 작업에 의해 삭제 된 폴더 또는 파일 이름이 포함 된 로그 파일을 저장 하는 [Azure Storage](connector-azure-blob-storage.md#linked-service-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties)또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 의 연결 된 서비스입니다. 이는 삭제 작업에 사용 된 것과 동일한 형식의 Integration Runtime 구성 하 여 파일을 삭제 해야 합니다. | 예 |
| 경로 | enablelogging = true일 때만 해당됩니다.<br/><br/>스토리지 계정에 로그 파일을 저장할 경로입니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 예 |

## <a name="monitoring"></a>모니터링

다음 두 위치에서 삭제 작업의 결과를 보고 모니터링할 수 있습니다. 
-   삭제 작업의 출력
-   로그 파일

### <a name="sample-output-of-the-delete-activity"></a>삭제 작업의 샘플 출력

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>샘플 로그 샘플 로그 파일

| 속성 | 범주 | 상태 | 오류 |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | 파일 | 삭제됨 |  |
| test2/hello789.txt | 파일 | 삭제됨 |  |
| test2/test3/hello000.txt | 파일 | 삭제됨 |  |
| test2/test3/zzz.json | 파일 | 삭제됨 |  |

## <a name="examples-of-using-the-delete-activity"></a>삭제 작업 사용 예제

### <a name="delete-specific-folders-or-files"></a>특정 폴더 또는 파일 삭제

저장소는 다음 폴더 구조를 포함합니다.

Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

이제 데이터 세트 및 삭제 작업의 다양한 속성 값을 조합하여 삭제 작업을 통해 폴더나 파일을 삭제해봅니다.

| folderPath | fileName | recursive | 출력 |
|:--- |:--- |:--- |:--- |
| Root/ Folder_A_2 | NULL | False | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/ Folder_A_2 | NULL | True | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Root/ Folder_A_2 | *.txt | False | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/ Folder_A_2 | *.txt | True | Root/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>시간 분할 폴더 또는 파일을 주기적으로 정리

시간 분할 폴더 또는 파일을 주기적으로 정리하기 위한 파이프라인을 만들 수 있습니다.  예를 들어 폴더 구조는 `/mycontainer/2018/12/14/*.csv`와 비슷합니다.  각 파이프라인 실행에서 삭제해야 하는 폴더 또는 파일을 식별하기 위해 일정 트리거에서 사용할 수 있는 ADF 시스템 변수를 활용할 수 있습니다. 

#### <a name="sample-pipeline"></a>샘플 파이프라인

```json
{
    "name":"cleanup_time_partitioned_folder",
    "properties":{
        "activities":[
            {
                "name":"DeleteOneFolder",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"PartitionedFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "TriggerTime":{
                                "value":"@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type":"Expression"
                            }
                        }
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    }
                }
            }
        ],
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>샘플 데이터 세트

```json
{
    "name":"PartitionedFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@dataset().TriggerTime",
                    "type":"Expression"
                },
                "container":{
                    "value":"mycontainer",
                    "type":"Expression"
                }
            }
        }
    }
}
```

#### <a name="sample-trigger"></a>샘플 트리거

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>2018년 1월 1일 이전에 마지막으로 수정한 만료된 파일 정리

데이터 집합의 "LastModified" 파일 특성 필터를 활용 하 여 이전 또는 만료 된 파일을 정리 하는 파이프라인을 만들 수 있습니다.  

#### <a name="sample-pipeline"></a>샘플 파이프라인

```json
{
    "name":"CleanupExpiredFiles",
    "properties":{
        "activities":[
            {
                "name":"DeleteFilebyLastModified",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"BlobFilesLastModifiedBefore201811",
                        "type":"DatasetReference"
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true,
                        "modifiedDatetimeEnd":"2018-01-01T00:00:00.000Z"
                    }
                }
            }
        ],
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>샘플 데이터 세트

```json
{
    "name":"BlobFilesLastModifiedBefore201811",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"*",
                "folderPath":"mydirectory",
                "container":"mycontainer"
            }
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>복사 작업 및 삭제 작업을 연결하여 파일 이동

복사 작업을 사용 하 여 파일을 복사 하 고 삭제 작업을 사용 하 여 파이프라인의 파일을 삭제 하는 방식으로 파일을 이동할 수 있습니다.  여러 파일을 이동하려는 경우 다음 샘플과 같이 GetMetadata 작업 + 필터 작업 + Foreach 작업 + 복사 작업 + 삭제 작업을 사용할 수 있습니다.

> [!NOTE]
> 폴더 경로만 포함된 데이터 세트를 정의한 후 복사 작업과 삭제 작업을 사용하여 폴더를 나타내는 동일한 데이터 집합을 참조하는 방식으로 전체 폴더를 이동할 때에는 매우 조심해야 합니다. 복사 작업과 삭제 작업 중간에 새 파일이 폴더에 들어오지 않는지를 확인해야 하기 때문입니다.  복사 작업이 방금 복사 작업을 완료했지만 삭제 작업이 시작되지 않은 순간에 새 파일에 폴더에 도착하면 삭제 작업은 전체 폴더를 삭제하여 대상으로 아직 복사되지 않은 이러한 새로 도착한 파일을 삭제할 수 있습니다. 

#### <a name="sample-pipeline"></a>샘플 파이프라인

```json
{
    "name":"MoveFiles",
    "properties":{
        "activities":[
            {
                "name":"GetFileList",
                "type":"GetMetadata",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"OneSourceFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "Container":{
                                "value":"@pipeline().parameters.SourceStore_Location",
                                "type":"Expression"
                            },
                            "Directory":{
                                "value":"@pipeline().parameters.SourceStore_Directory",
                                "type":"Expression"
                            }
                        }
                    },
                    "fieldList":[
                        "childItems"
                    ],
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    },
                    "formatSettings":{
                        "type":"BinaryReadSettings"
                    }
                }
            },
            {
                "name":"FilterFiles",
                "type":"Filter",
                "dependsOn":[
                    {
                        "activity":"GetFileList",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('GetFileList').output.childItems",
                        "type":"Expression"
                    },
                    "condition":{
                        "value":"@equals(item().type, 'File')",
                        "type":"Expression"
                    }
                }
            },
            {
                "name":"ForEachFile",
                "type":"ForEach",
                "dependsOn":[
                    {
                        "activity":"FilterFiles",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('FilterFiles').output.value",
                        "type":"Expression"
                    },
                    "batchCount":20,
                    "activities":[
                        {
                            "name":"CopyAFile",
                            "type":"Copy",
                            "dependsOn":[

                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "source":{
                                    "type":"BinarySource",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageReadSettings",
                                        "recursive":false,
                                        "deleteFilesAfterCompletion":false
                                    },
                                    "formatSettings":{
                                        "type":"BinaryReadSettings"
                                    },
                                    "recursive":false
                                },
                                "sink":{
                                    "type":"BinarySink",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageWriteSettings"
                                    }
                                },
                                "enableStaging":false,
                                "dataIntegrationUnits":0
                            },
                            "inputs":[
                                {
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs":[
                                {
                                    "referenceName":"OneDestinationFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.DestinationStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.DestinationStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name":"DeleteAFile",
                            "type":"Delete",
                            "dependsOn":[
                                {
                                    "activity":"CopyAFile",
                                    "dependencyConditions":[
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "dataset":{
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                },
                                "logStorageSettings":{
                                    "linkedServiceName":{
                                        "referenceName":"BloblinkedService",
                                        "type":"LinkedServiceReference"
                                    },
                                    "path":"container/log"
                                },
                                "enableLogging":true,
                                "storeSettings":{
                                    "type":"AzureBlobStorageReadSettings",
                                    "recursive":true
                                }
                            }
                        }
                    ]
                }
            }
        ],
        "parameters":{
            "SourceStore_Location":{
                "type":"String"
            },
            "SourceStore_Directory":{
                "type":"String"
            },
            "DestinationStore_Location":{
                "type":"String"
            },
            "DestinationStore_Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-datasets"></a>샘플 데이터 세트

GetMetadata 작업에서 파일 목록을 열거하는 데 사용하는 데이터 세트

```json
{
    "name":"OneSourceFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

복사 작업 및 삭제 작업에서 사용하는 데이터 원본용 데이터 세트

```json
{
    "name":"OneSourceFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

복사 활동에서 사용하는 데이터 대상용 데이터 세트

```json
{
    "name":"OneDestinationFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

[여기](solution-template-move-files.md)에서 파일을 이동 하는 템플릿을 가져올 수도 있습니다.

## <a name="known-limitation"></a>알려진 제한 사항

-   삭제 작업은 와일드 카드에서 설명 하는 폴더 목록 삭제를 지원 하지 않습니다.

-   Delete 활동에서 파일 특성 필터를 사용 하는 경우: modifiedDatetimeStart 및 modifiedDatetimeEnd에서 삭제할 파일을 선택 하려면 delete 활동에 "wildcardFileName": "*"를 설정 해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Data Factory에서 파일을 이동 하는 방법에 대해 자세히 알아보세요.

-   [Azure Data Factory의 데이터 복사 도구](copy-data-tool.md)
