---
title: 여러 입력-Microsoft Genomics를 사용 하 여 워크플로 제출
titleSuffix: Azure
description: 이 문서에는 경우 입력된 파일에 여러 개의 FASTQ 또는 BAM 파일을 동일한 샘플에서 가져온를 Microsoft Genomics 서비스에 워크플로 제출 하는 방법을 보여 줍니다. 이미 msgen 클라이언트가 설치 되어 있고 서비스를 통해 샘플 데이터를 성공적으로 실행 했습니다.
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 399b1ed735ce1b7a3fca1d27155863f6bfa18776
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780881"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>동일한 샘플의 여러 입력을 사용하여 워크플로 제출

이 문서에서는 입력된 파일이 여러 FASTQ 또는 BAM 파일 경우 Microsoft Genomics 서비스에 워크플로를 제출 하는 방법을 보여 줍니다 **동일한 샘플에서 가져온**합니다. 예를 들어 시퀀서의 여러 레인에서 **동일한 샘플**을 실행한 경우 시퀀서는 각 레인별로 한 쌍의 FASTQ 파일을 출력할 수 있습니다. 정렬 및 변이 추출 전에 이러한 FASTQ 파일을 연결하는 대신 이러한 입력을 모두 `msgen` 클라이언트에 직접 제출할 수 있습니다. `msgen` 클라이언트의 출력은 .bam, .bai, .vcf 파일을 포함한 파일의 **단일 집합**입니다. 

그러나 같은 제출에서 FASTQ와 BAM 파일을 혼합**할 수** 없습니다. 또한 여러 개인의 여러 FASTQ 또는 BAM 파일은 **제출할 수 없습니다**. 

이 문서에서는 `msgen` 클라이언트를 이미 설치하여 실행하고 있으며 Azure Storage를 사용하는 방법을 잘 알고 있다고 가정합니다. 제공된 된 샘플 데이터를 사용 하 여 워크플로 성공적으로 제출한 경우이 문서를 진행할 준비가 되었습니다. 


## <a name="multiple-bam-files"></a>여러 개의 BAM 파일

### <a name="upload-your-input-files-to-azure-storage"></a>입력 파일을 Azure 저장소에 업로드
입력 파일로서 *reads.bam*, *additional_reads.bam*, *yet_more_reads.bam* 등 BAM 파일이 여러 개 있고 Azure의 개인 저장소 계정인 *myaccount*에 업로드했다고 가정해보겠습니다. API URL과 액세스 키를 보유하고 있습니다. **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 에 출력하려 합니다.


### <a name="submit-your-job-to-the-msgen-client"></a>`msgen` 클라이언트에게 작업을 제출합니다. 

--input-blob-name-1 인수에 모든 이름을 전달하여 여러 BAM 파일을 제출할 수 있습니다. 모든 파일은 동일한 샘플에서 가져와야 하지만 순서는 중요하지 않습니다. 다음 섹션에서는 Windows, Unix 및 구성 파일을 사용하는 명령줄의 예제 제출에 대해 자세히 설명합니다. 쉽게 알 수 있도록 줄 바꿈이 추가되었습니다.


Windows의 경우:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Unix의 경우

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


구성 파일 사용을 선호하는 경우 다음 내용이 포함됩니다.

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

이 호출로 `config.txt` 파일 제출: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>쌍으로 연결된 여러 개의 FASTQ 파일

### <a name="upload-your-input-files-to-azure-storage"></a>입력 파일을 Azure 저장소에 업로드
입력 파일로서 *reads_1.fq.gz* 및 *reads_2.fq.gz*, *additional_reads_1.fq.gz* 및 *additional_reads_2.fq.gz*, *yet_more_reads_1.fq.gz* 및 *yet_more_reads_2.fq.gz* 등 쌍으로 연결된 FASTQ 파일이 여러 개 있다고 가정해보겠습니다. Azure의 개인 저장소 계정인 *myaccount*에 업로드했으며 API URL과 액세스 키를 가지고 있습니다. **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 에 출력하려 합니다.


### <a name="submit-your-job-to-the-msgen-client"></a>`msgen` 클라이언트에게 작업을 제출합니다. 

쌍으로 연결된 FASTQ 파일은 동일한 샘플에서 나온 것이어야 할 뿐만 아니라 함께 처리해야 합니다.  파일 이름의 순서는 --input-blob-name-1 및 --input-blob-name-2에 인수로서 전달될 때와 관련이 있습니다. 

다음 섹션에서는 Windows, Unix 및 구성 파일을 사용하는 명령줄의 예제 제출에 대해 자세히 설명합니다. 쉽게 알 수 있도록 줄 바꿈이 추가되었습니다.


Windows의 경우:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
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
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

이 호출로 `config.txt` 파일 제출: `msgen submit -f config.txt`

## <a name="next-steps"></a>다음 단계
이 문서에서는 여러 개의 BAM 파일 또는 쌍으로 연결된 FASTQ 파일을 Azure Storage에 업로드했고 `msgen` python 클라이언트를 통해 워크플로를 Microsoft Genomics 서비스에 제출했습니다. Microsoft Genomics 서비스에서 사용할 수 있는 워크플로 제출 및 기타 명령에 대한 자세한 내용은 [FAQ](frequently-asked-questions-genomics.md)를 참조하세요. 