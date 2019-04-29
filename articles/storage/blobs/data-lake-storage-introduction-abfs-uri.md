---
title: Azure Data Lake Storage Gen2 URI 사용
description: Azure Data Lake Storage Gen2 URI 사용
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e4f148ac6f66cfbd7d89eff3623418197c2e0a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708484"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Azure Data Lake Storage Gen2 URI 사용

Azure Data Lake Storage Gen2와 호환되는 [Hadoop 파일 시스템](https://www.aosabook.org/en/hdfs.html) 드라이버는 해당 체계 식별자 `abfs`(Azure Blob 파일 시스템)로 인식됩니다. 다른 Hadoop 파일 시스템 드라이버와 일치하는 ABFS 드라이버는 Data Lake Storage Gen2 지원 계정 내에서 파일 및 디렉터리의 주소를 지정하기 위해 URI 형식을 사용합니다.

## <a name="uri-syntax"></a>URI 구문

Data Lake Storage Gen2에 대한 URI 구문은 스토리지 계정이 Data Lake Storage Gen2를 기본 파일 시스템으로 포함하도록 설정되었는지 여부에 따라 다릅니다.

주소를 지정할 Data Lake Storage Gen2 지원 계정이 계정을 만드는 동안 기본 파일 시스템으로 설정되지 **않은** 경우, 줄임 URI 구문은 다음과 같습니다.

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **체계 식별자**: `abfs` 프로토콜이 체계 식별자로 사용됩니다. SSL(Secure Sockets Layer) 연결 사용 여부에 관계없이 연결하는 옵션이 있습니다. `abfss`을 사용하여 Secure Socket Layer 연결에 연결합니다.

2. **파일 시스템**: 파일 및 폴더를 포함하는 부모 위치입니다. Azure Storage Blob 서비스의 컨테이너와 동일합니다.

3. **계정 이름**: 만드는 동안 스토리지 계정에 지정된 이름입니다.

4. **경로**: 슬래시(`/`)로 구분된 디렉터리 구조의 표현입니다.

5. **파일 이름**: 개별 파일의 이름입니다. 이 매개 변수는 디렉터리의 주소를 지정하는 경우, 선택 사항입니다.

그렇지만 주소를 지정할 계정이 계정을 만드는 동안 기본 파일 시스템으로 설정되지 않은 경우, 줄임 URI 구문은 다음과 같습니다.

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **경로**: 슬래시(`/`)로 구분된 디렉터리 구조의 표현입니다.

2. **파일 이름**: 개별 파일의 이름입니다.


## <a name="next-steps"></a>다음 단계

- [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
