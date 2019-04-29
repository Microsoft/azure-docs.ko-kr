---
title: Azure Data Lake Storage Gen2용 Azure Blob 파일 시스템 드라이버
description: ABFS Hadoop 파일 시스템 드라이버
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 83e2f6f42de5c729667f366a6e068f1c8bd71f02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708620"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>ABFS(Azure Blob 파일 시스템) 드라이버: Hadoop 전용 Azure Storage 드라이버

Azure Data Lake Storage Gen2에서 데이터에 대한 기본 액세스 방법 중 하나는 [Hadoop 파일 시스템](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html)을 통해서입니다. Data Lake Storage Gen2를 사용하면 Azure Blob Storage 사용자가 새 드라이버인 `ABFS`(Azure Blob File System 드라이버)에 액세스할 수 있습니다. ABFS는 Apache Hadoop의 일부이며 많은 Hadoop의 상업용 배포 버전에 포함됩니다. 이 드라이버를 사용하면 많은 애플리케이션과 프레임워크에서 Data Lake Storage Gen2를 명시적으로 참조하는 코드 없이 Azure Blob Storage의 데이터에 액세스할 수 있습니다.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>이전 기능: Windows Azure Storage Blob 드라이버

[WASB 드라이버](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)(Windows Azure Storage Blob 드라이버)는 Azure Blob Storage에 대한 원래 지원을 제공했습니다. 이 드라이버는 파일 시스템 의미 체계를 Azure Blob Storage에서 공개한 개체 저장소 스타일 인터페이스의 드라이버에 매핑하는 복잡한 작업(Hadoop 파일 시스템 인터페이스에서 요구하는 대로)을 수행하였습니다. 이 드라이버는 이 모델을 계속 지원하면서 Blob에 저장된 데이터에 대한 고성능 액세스를 제공하지만 이 매핑을 수행하는 상당한 양의 코드를 담고 있어 유지하기가 어렵습니다. 또한 [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) 및 [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) 같은 몇몇 작업은 디렉터리에 적용될 때 개체 저장소의 디렉터리에 대한 지원이 부족하기 때문에 드라이버가 방대한 수의 작업을 수행하도록 요구해 종종 성능 저하로 이어집니다. ABFS 드라이버는 WASB의 고유한 결함을 해결하도록 설계되었습니다.

## <a name="the-azure-blob-file-system-driver"></a>Azure Blob 파일 시스템 드라이버

[Azure Data Lake Storage REST 인터페이스](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)는 Azure Blob Storage에 대해 파일 시스템 의미 체계를 지원하도록 설계됐습니다. Hadoop 파일 시스템도 동일한 의미 체계를 지원하도록 설계된 경우 드라이버에서 복잡한 매핑에 대한 요구 사항은 없습니다. 따라서 Azure Blob 파일 시스템(ABFS) 드라이버는 REST API에 대한 클라이언트 shim에 불과합니다.

그러나 이 드라이버가 아직 수행해야 할 몇 가지 기능이 있습니다.

### <a name="uri-scheme-to-reference-data"></a>참조 데이터에 대한 URI 스키마

Hadoop 내에서 파일 시스템 구현과 일치하는 ABFS 드라이버는 자체 URI 스키마를 정의하므로 리소스(디렉터리 및 파일)에 분명하게 대응할 수 있습니다. URI 스키마는 [Azure Data Lake Storage Gen2 URI 사용](./data-lake-storage-introduction-abfs-uri.md)에 설명되어 있습니다. URI 구조는 `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`과 같습니다.

위의 URI 형식을 사용하는 표준 Hadoop 도구 및 프레임워크는 이러한 리소스를 참조하는 데 사용될 수 있습니다.

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

내부적으로 ABFS 드라이버는 URI에 지정된 리소스를 파일 및 디렉터리로 변환하고 해당 리소스를 사용하여 Azure Data Lake Storage REST API를 호출합니다.

### <a name="authentication"></a>Authentication

ABFS 드라이버는 두 가지 형식의 인증을 지원하므로 Hadoop 애플리케이션은 Data Lake Storage Gen2 지원 계정 내에 포함된 리소스에 안전하게 액세스할 수 있습니다. 사용 가능한 인증 체계의 전체 세부 정보는 [Azure Storage 보안 가이드](../common/storage-security-guide.md)에 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- **공유 키:** 이렇게 하면 사용자가 계정의 모든 리소스에 액세스할 수 있습니다. 키는 암호화되어 Hadoop 구성에 저장됩니다.

- **Azure Active Directory OAuth 전달자 토큰:** Azure AD 전달자 토큰은 최종 사용자의 ID 또는 구성된 서비스 주체를 사용하여 드라이버에서 획득하고 새로 고칩니다. 이 인증 모델을 사용하면 모든 액세스 권한은 할당된 POSIX ACL(액세스 제어 목록)에 대해 평가되고 제공된 토큰과 연결된 ID를 사용하여 호출당 기준으로 부여됩니다.

### <a name="configuration"></a>구성

ABFS 드라이버에 대한 모든 구성은 <code>core-site.xml</code> 구성 파일에 저장됩니다. [Ambari](https://ambari.apache.org/)가 특징인 Hadoop 배포에서 웹 포털이나 Ambari REST API를 사용하여 구성을 관리할 수 있습니다.

지원되는 모든 구성 항목의 세부 정보는 [Hadoop 공식 설명서](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)에 나와 있습니다.

### <a name="hadoop-documentation"></a>Hadoop 설명서

ABFS 드라이버는 [Hadoop 공식 설명서](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)에 충분히 설명돼 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Databricks 클러스터 만들기](./data-lake-storage-quickstart-create-databricks-account.md)
- [Azure Data Lake Storage Gen2 URI 사용](./data-lake-storage-introduction-abfs-uri.md)
