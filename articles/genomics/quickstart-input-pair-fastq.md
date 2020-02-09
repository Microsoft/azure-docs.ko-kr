---
title: FASTQ 파일 입력을 사용하여 워크플로 제출
titleSuffix: Microsoft Genomics
description: 이 문서에서는 입력 파일이 단일의 FASTQ 파일 쌍 인 경우 Microsoft Genomics 서비스에 워크플로를 제출 하는 방법을 보여 줍니다.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 3806b165e5abb661e53c6a315650d025fd42e17f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248554"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Microsoft Genomics에서 FASTQ 파일 입력을 사용하여 워크플로 제출

이 문서에서는 입력 파일이 단일의 FASTQ 파일 쌍 인 경우 Microsoft Genomics 서비스에 워크플로를 제출 하는 방법을 보여 줍니다. 이 항목에서는 사용자가 이미 `msgen` 클라이언트를 설치하여 실행하고 있으며 Azure Storage 사용 방법에 익숙하다고 가정합니다. 제공 된 샘플 데이터를 사용 하 여 워크플로를 성공적으로 제출 하면이 문서를 진행할 준비가 된 것입니다. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>설정: FASTQ 파일을 Azure storage에 업로드 합니다.
두 개의 파일(*reads_1.fq.gz*와 *reads_2.fq.gz*)이 있고, Azure의 개인 스토리지 계정인 *myaccount*(**https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** 및 **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>** )에 파일을 업로드했다고 가정해보겠습니다. API URL과 액세스 키를 보유하고 있습니다. **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 에 출력하려 합니다.


## <a name="submit-your-job-to-the-msgen-client"></a>`msgen` 클라이언트에게 작업을 제출합니다. 

다음은 `msgen` 클라이언트에게 제공해야 하는 최소한의 인수 집합입니다. 쉽게 알 수 있도록 줄 바꿈이 추가되었습니다.

Windows의 경우:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Unix의 경우:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


구성 파일 사용을 선호하는 경우 다음 내용이 포함됩니다.

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

이 호출로 `config.txt` 파일 제출: `msgen submit -f config.txt`

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Storage에 한 쌍의 FASTQ 파일을 업로드했고 `msgen` python 클라이언트를 통해 워크플로를 Microsoft Genomics 서비스에 제출했습니다. Microsoft Genomics 서비스에서 사용할 수 있는 워크플로 제출 및 기타 명령에 대한 자세한 내용은 [FAQ](frequently-asked-questions-genomics.md)를 참조 하세요. 
