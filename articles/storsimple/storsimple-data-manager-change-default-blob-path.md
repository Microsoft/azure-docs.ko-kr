---
title: 기본값에서 Blob 경로 변경 | Microsoft Docs
description: Azure 함수를 설정하여 Blob 파일 경로의 이름을 변경하는 방법 알아보기
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: cdaf991c25c23dee4f87b44142c1482bf892bcf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723801"
---
# <a name="change-a-blob-path-from-the-default-path"></a>기본 경로에서 Blob 경로 변경

StorSimple 데이터 관리자 서비스가 데이터를 변환하는 경우, 기본적으로 대상 리포지토리를 만드는 동안 지정된 대로, 변환된 Blob를 저장소 컨테이너에 배치합니다. Blob이 이 위치에 도달하면 이러한 Blob을 다른 위치로 이동하려고 할 수 있습니다. 이 문서에서는 기본 Blob 파일 경로의 이름을 바꾸고 Blob을 다른 위치로 이동하도록 Azure 함수를 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

StorSimple 데이터 관리자 서비스에서 작업 정의를 올바르게 구성했는지 확인합니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기

Azure 함수를 만들려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.

2. **+ 리소스 만들기**를 클릭합니다. **검색** 상자에 **Function App**을 입력한 다음 **Enter** 키를 누릅니다. 표시되는 앱 목록에서 **함수 앱**을 선택한 후 클릭합니다.

    ![검색 상자에 “Function App” 입력](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. **만들기**를 클릭합니다.

    ![Function App 창의 “만들기” 단추](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. **Function App** 구성 블레이드에서 다음 단계를 수행합니다.

    1. 고유한 **앱 이름**을 제공합니다.
    2. 드롭다운 목록에서 **구독**을 선택합니다. 이 구독은 데이터 StorSimple 데이터 관리자 서비스와 연결된 구독과 동일해야 합니다.
    3. **새 리소스 그룹 만들기**를 선택합니다.
    4. **호스팅 계획** 드롭다운 목록에서 **소비 계획**을 선택합니다.
    5. 함수가 실행되는 위치를 지정합니다. StorSimple 데이터 관리자 서비스 및 작업 정의와 연결된 저장소 계정이 있는 동일한 영역이 좋습니다.
    6. 기존 저장소 계정을 선택하거나 새 저장소 계정을 만듭니다. 저장소 계정은 함수에 대해 내부적으로 사용됩니다.

        ![새 Function App 구성 데이터 입력](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. **만들기**를 클릭합니다. 함수 앱이 생성됩니다.
     
        ![Function App이 생성됨](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. **함수**를 선택하고 **+ 새 함수**를 클릭합니다.

    ![+ 새 함수 클릭](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. 언어로 **C#** 을 선택합니다. 템플릿 타일 배열의 **QueueTrigger-CSharp** 타일에서 **C#** 을 선택합니다.

7. **큐 트리거**에서 다음을 수행합니다.

    1. 함수의 **이름**을 입력합니다.
    2. **큐 이름** 상자에 데이터 변환 작업 정의 이름을 입력합니다.
    3. **Storage 계정 연결**에서 **새로 만들기**를 클릭합니다. 저장소 계정 목록에서 작업 정의와 연결된 계정을 선택합니다. 연결 이름을 기록해 둡니다(강조 표시됨). 이 이름은 Azure 함수에서 나중에 필요합니다.

        ![새 C# 함수 만들기](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. **만들기**를 클릭합니다. **함수**가 만들어집니다.

     
10. 함수 창에서 _.csx_ 파일을 실행합니다.

    ![새 C# 함수 만들기](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    다음 단계를 수행합니다.

    1. 다음 코드를 붙여 넣습니다.

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. 11번 줄에서 **STORAGE_CONNECTIONNAME**을 사용자의 스토리지 계정 연결로 바꿉니다(7c단계 참조).

        ![저장소 연결 이름 복사](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. 함수를 **저장**합니다.

        ![함수 저장](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. 함수를 완료하려면 다음 단계를 수행하여 파일을 하나 더 추가합니다.

    1. **파일 보기**를 클릭합니다.

       ![“파일 보기” 링크](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. **+ 추가**를 클릭합니다.
        
        ![“파일 보기” 링크](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. **project.json**을 입력하고 **Enter** 키를 누릅니다. **project.json** 파일에 다음 코드를 붙여 넣습니다.

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. **저장**을 클릭합니다.

        ![“파일 보기” 링크](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Azure Function이 만들어졌습니다. 이 함수는 데이터 변환 작업에서 새 Blob이 생성될 때마다 트리거됩니다.

## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).
