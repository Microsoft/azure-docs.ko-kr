---
title: Azure Blob 스토리지의 샘플 데이터 - Team Data Science Process
description: 프로그래밍 방식으로 다운로드한 다음, Python으로 작성된 절차에 따라 샘플링하여 Azure Blob 스토리지에 저장된 데이터를 샘플링합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1c455106e5faa4aa20ec56f37788e0b8c324fee1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042919"
---
# <a name="heading"></a>Azure Blob Storage에서 데이터 샘플링

이 문서에서는 프로그래밍 방식으로 다운로드한 다음, Python으로 작성된 절차에 따라 샘플링하여 Azure Blob Storage에 저장된 데이터를 샘플링하는 방법에 대해 알아봅니다.

**데이터를 샘플링하는 이유**
분석할 데이터 세트가 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 그러면 데이터 이해, 탐색 및 기능 엔지니어링이 용이해집니다. Cortana 분석 프로세스에서는 데이터 처리 기능 및 기계 학습 모델의 빠른 프로토타입 제작을 지원하는 역할을 합니다.

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)의 단계입니다.

## <a name="download-and-down-sample-data"></a>데이터 다운로드 및 저해상도로 처리
1. 다음 샘플 Python 코드에서 Blob 서비스를 사용하여 Azure Blob Storage에서 데이터를 다운로드합니다. 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. 다운로드한 파일에서 Pandas 데이터 프레임으로 데이터를 읽습니다.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. 다음과 같이 `numpy`의 `random.choice`를 사용하여 데이터를 저해상도로 처리합니다.
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

이제 추가 탐색 및 기능 생성을 위해 1% 샘플을 사용하여 위의 데이터 프레임으로 작업할 수 있습니다.

## <a name="heading"></a>데이터 업로드 및 Azure 기계 학습으로 읽어오기
다음 샘플 코드를 사용하여 데이터를 다운 샘플링하고 Azure Machine Learning에서 직접 사용할 수 있습니다.

1. 데이터 프레임을 로컬 파일에 기록합니다.
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 다음 샘플 코드를 사용하여 Azure Blob에 로컬 파일을 업로드합니다.
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. 아래 이미지에 표시된 대로 Azure Machine Learning [데이터 가져오기](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)를 사용하여 Azure Blob에서 데이터를 읽습니다.

![판독기 blob](./media/sample-data-blob/reader_blob.png)

