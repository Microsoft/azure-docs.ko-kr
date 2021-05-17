---
title: HDInsight의 Azure Data Lake Storage Gen1 개요
description: HDInsight의 Data Lake Storage Gen1 개요입니다.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938883"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>HDInsight의 Azure Data Lake Storage Gen1 개요

Azure Data Lake Storage Gen1은 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Azure Data Lake를 사용하면 크기, 유형 및 수집 속도에 관계없이 모든 데이터를 캡처할 수 있습니다. 이러한 모든 기능이 운영 및 예비 분석을 위해 한 곳에 제공됩니다.

Data Lake Storage Gen1은 WebHDFS 호환 REST API를 사용하여 Hadoop(HDInsight 클러스터에서 사용 가능)에서 액세스합니다. Data Lake Storage Gen1은 저장된 데이터를 분석하기 위해 설계되었으며, 데이터 분석 시나리오에 필요한 고성능을 제공하도록 튜닝되었습니다. Gen1은 실제 엔터프라이즈 사용 사례에 반드시 필요한 기능을 포함합니다. 이러한 기능에는 보안, 관리 효율성, 적응성, 안정성 및 가용성이 포함됩니다.

Azure Data Lake Storage Gen1에 대한 자세한 내용은 [Azure Data Lake Storage Gen1 개요](../data-lake-store/data-lake-store-overview.md)를 참조하세요.

Data Lake Storage Gen1의 주요 기능은 다음과 같습니다.

## <a name="compatibility-with-hadoop"></a>Hadoop과의 호환

Data Lake Storage Gen1은 HDFS 및 Hadoop 환경과 호환되는 Apache Hadoop 파일 시스템입니다.  WebHDFS API를 사용하는 HDInsight 애플리케이션 또는 서비스는 Data Lake Storage Gen1과 쉽게 통합할 수 있습니다. Data Lake Storage Gen1은 또한 애플리케이션에 대한 WebHDFS 호환 REST 인터페이스를 노출합니다.

Data Lake Storage Gen1에 저장된 데이터는 Hadoop 분석 프레임워크를 사용하여 쉽게 분석될 수 있습니다. MapReduce 또는 Hive와 같은 프레임워크. Azure HDInsight 클러스터는 Data Lake Storage Gen1에 저장된 데이터에 직접 액세스하도록 프로비전되고 구성될 수 있습니다.

## <a name="unlimited-storage-petabyte-files"></a>무제한 스토리지, 페타바이트 파일

Data Lake Storage Gen1은 무제한 스토리지를 제공하며 분석에 대한 다양한 종류의 데이터를 저장하는 데 적합합니다. 계정 크기 또는 파일 크기에는 제한이 없습니다. 또는 데이터 레이크에 저장될 수 있는 데이터 양도 제한되지 않습니다. 개별 파일의 범위는 킬로바이트에서 페타바이트까지 다양하므로 Data Lake Storage Gen1은 모든 형식의 데이터를 저장할 수 있는 유용한 옵션입니다. 데이터는 여러 복사본을 만들어 영구적으로 저장됩니다. 또한 데이터가 데이터 레이크에 저장될 수 있는 기간에는 제한이 없습니다.

## <a name="performance-tuning-for-big-data-analytics"></a>빅 데이터 분석에 대한 성능 조정

Data Lake Storage Gen1은 분석 시스템용으로 설계되었습니다. 많은 양의 데이터에 대한 쿼리 및 분석을 위해 대규모 처리 능력이 필요한 시스템입니다. 데이터 레이크는 몇 개의 개별 스토리지 서버에 파일의 일부분을 배포합니다. 데이터를 분석하는 경우 이러한 설정은 파일을 동시에 읽을 때의 읽기 처리량을 향상시킵니다.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>엔터프라이즈 준비 상태: 고가용성 및 보안

Data Lake Storage Gen1은 업계 표준 가용성과 안정성을 제공합니다. 데이터 자산은 모든 예기치 않은 오류로부터 보호하도록 중복 복사본을 만들어 영구적으로 저장됩니다. 기업에서는 기존 데이터 플랫폼의 중요한 부분으로 솔루션에서 Data Lake Storage Gen1을 사용할 수 있습니다.

또한 Data Lake Storage Gen1은 저장된 데이터에 대한 엔터프라이즈급 보안을 제공합니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 데이터 보안](#data-security-in-data-lake-storage-gen1)을 참조하세요.

## <a name="flexible-data-structures"></a>유연한 데이터 구조

Data Lake Storage Gen1은 사전 변환 없이 모든 데이터를 원시 형식으로 그대로 저장할 수 있습니다. Data Lake Storage Gen1에서는 데이터를 로드하기 전에 먼저 스키마를 정의할 필요가 없습니다. 개별 분석 프레임워크는 데이터를 해석하고 분석 시에 스키마를 정의합니다. Data Lake Storage Gen1은 구조화된 데이터를 처리할 수 있습니다. 반구조화 및 비구조화 데이터도 처리할 수 있습니다.

데이터에 대한 Data Lake Storage Gen1 컨테이너는 기본적으로 폴더 및 파일입니다. SDK, Azure Portal 및 Azure Powershell을 사용하여 저장된 데이터에서 작동합니다. 이러한 인터페이스 및 컨테이너를 사용하여 스토리지에 데이터를 저장하면 모든 데이터 형식을 저장할 수 있습니다. Data Lake Storage Gen1은 데이터의 형식에 따라 데이터의 특수한 처리를 수행하지 않습니다.

## <a name="data-security-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1의 데이터 보안

Data Lake Storage Gen1은 인증을 위해 Azure Active Directory를 사용하고 데이터에 대한 액세스를 관리하기 위해 ACL(액세스 제어 목록)을 사용합니다.

| **기능** | **설명** |
| --- | --- |
| 인증 |Data Lake Storage Gen1은 Data Lake Storage Gen1에 저장된 모든 데이터에 대한 ID 및 액세스 관리를 위해 Azure Active Directory(Azure AD)와 통합합니다. 이러한 통합으로 인해 Data Lake Storage Gen1은 모든 Azure AD 기능의 이점을 얻습니다. 이러한 기능에는 다단계 인증, 조건부 액세스 및 Azure 역할 기반 액세스 제어가 포함됩니다. 또한 애플리케이션 사용량 모니터링, 보안 모니터링 및 경고 등도 포함됩니다. Data Lake Storage Gen1은 REST 인터페이스에서 인증을 위한 OAuth 2.0 프로토콜을 지원합니다. [Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1에 인증](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)을 참조하세요.|
| Access Control |Data Lake Storage Gen1은 WebHDFS 프로토콜에 의해 노출되는 POSIX 스타일 권한을 지원하여 액세스 제어를 제공합니다. ACL은 루트 폴더, 하위 폴더 및 개별 파일에서도 사용할 수 있습니다. Data Lake Storage Gen1의 컨텍스트에서 ACL 작동 방식에 대한 자세한 내용은 [Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md)를 참조하세요. |
| 암호화 |또한 Data Lake Storage Gen1은 계정에 저장된 데이터에 대한 암호화를 제공합니다. Data Lake Storage Gen1 계정을 만드는 동안 암호화 설정을 지정합니다. 암호화된 데이터 또는 암호화 없음을 선택할 수 있습니다. 자세한 내용은 [Data Lake Storage Gen1의 암호화](../data-lake-store/data-lake-store-encryption.md)를 참조하세요. 암호화 관련 구성을 제공하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](../data-lake-store/data-lake-store-get-started-portal.md)을 참조하세요. |

Data Lake Storage Gen1의 데이터 보안 방법에 대해 자세히 알아보려면 [Azure Data Lake Storage Gen1에 저장된 데이터 보안](../data-lake-store/data-lake-store-secure-data.md)을 참조하세요.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1과 호환되는 애플리케이션

Data Lake Storage Gen1은 Hadoop 환경의 오픈 소스 구성 요소 대부분과 호환됩니다. 또한 다른 Azure 서비스와 원활하게 통합됩니다.  다른 Azure 서비스뿐만 아니라 오픈 소스 구성 요소와 함께 Data Lake Storage Gen1을 사용할 수 있는 방법을 자세히 알아보려면 아래 링크로 이동하세요.

* [Azure Data Lake Storage Gen1에서 작동하는 오픈 소스 빅 데이터 애플리케이션](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)을 참조하세요.
* Data Lake Storage Gen1을 다른 Azure 서비스와 사용하여 광범위한 시나리오를 활성화하는 방법을 이해하려면 [Azure Data Lake Storage Gen1을 다른 Azure 서비스와 통합](../data-lake-store/data-lake-store-integrate-with-other-services.md)을 참조하세요.
* [빅 데이터 요구 사항에 Azure Data Lake Storage Gen1 사용](../data-lake-store/data-lake-store-data-scenarios.md)을 참조하세요.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 파일 시스템(adl://)

Hadoop 환경에서 새로운 파일 시스템인 AzureDataLakeFilesystem(adl://)을 통해 Data Lake Storage Gen1에 액세스할 수 있습니다. `adl://`을 사용하는 애플리케이션 및 서비스의 성능은 현재 WebHDFS에서 사용할 수 없는 방식으로 최적화할 수 있습니다. 따라서 권장 adl://를 사용하여 최상의 성능을 제공하는 유연성을 얻을 수 있습니다. 또는 WebHDFS API를 직접 계속 사용하여 기존 코드를 유지 관리합니다. Azure HDInsight는 Data Lake Storage Gen1에서 최상의 성능을 제공하도록 AzureDataLakeFilesystem을 완벽하게 활용합니다.

다음 URI를 사용하여 Data Lake Storage Gen1의 데이터에 액세스할 수 있습니다.

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Data Lake Storage Gen1의 데이터에 액세스하는 방법에 대한 자세한 내용은 [저장된 데이터에 대해 사용할 수 있는 작업](../data-lake-store/data-lake-store-get-started-portal.md#properties)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Storage 소개](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen2 개요](./overview-data-lake-storage-gen2.md)