---
title: HDInsight에서 HBase 예제 시작 - Azure
description: 이 Apache HBase 예제에 따라 HDInsight에서 hadoop 사용을 시작합니다. HBase 셸에서 테이블을 만들고 Hive를 사용하여 쿼리합니다.
keywords: hbasecommand, hbase 예제
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c1c582c60a7c91de40983daa07bdec1e8d748f8a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123130"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>HDInsight에서 Apache HBase 예제 시작

HDInsight에서 [Apache HBase](https://hbase.apache.org/) 클러스터를 만들고, HBase 테이블을 만들고 [Apache Hive](https://hive.apache.org/)를 사용하여 테이블을 쿼리하는 방법에 대해 알아봅니다.  일반 HBase 정보는 [HDInsight HBase 개요][hdinsight-hbase-overview]를 참조하세요.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>필수 조건
이 HBase 예제를 시작하기 전에 다음 항목이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* [SSH(Secure Shell)](../hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](https://curl.haxx.se/download.html).

## <a name="create-apache-hbase-cluster"></a>Apache HBase 클러스터 만들기
다음 절차에서는 Azure Resource Manager 템플릿을 사용하여 HBase 클러스터 및 종속된 기본 Azure Storage 계정을 만듭니다. 절차에 사용되는 매개 변수와 다른 클러스터 생성 메서드를 이해하려면 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. Data Lake Storage Gen2를 사용하는 방법에 대한 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.

1. Azure 포털에서 템플릿을 열려면 다음 이미지를 클릭합니다. 서식 파일에 위치한 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)합니다.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **사용자 지정 배포** 블레이드에서 다음 값을 입력합니다.
   
   * **구독**: 클러스터를 만드는 데 사용할 Azure 구독을 선택합니다.
   * **리소스 그룹**: Azure 리소스 관리 그룹을 만들거나 기존 그룹을 사용합니다.
   * **위치**: 리소스 그룹의 위치를 지정합니다. 
   * **ClusterName**: HBase 클러스터의 이름을 입력합니다.
   * **클러스터 로그인 이름 및 암호**: 기본 로그인 이름은 **admin**입니다.
   * **SSH 사용자 이름 및 암호**: 기본 사용자 이름은 **sshuser**입니다.  이름은 변경할 수 있습니다.
     
     다른 매개 변수는 선택 사항입니다.  
     
     각 클러스터에는 Azure Storage 계정 종속성이 있습니다. 클러스터를 삭제한 후에는 데이터가 저장소 계정에 유지됩니다. 클러스터 기본 저장소 계정 이름은 "저장소"가 추가된 클러스터 이름이입니다. 템플릿 변수 섹션에 하드 코딩됩니다.
3. **위에 명시된 사용 약관에 동의함**을 선택한 다음 **구매**를 클릭합니다. 클러스터를 만들려면 20분 정도가 걸립니다.

> [!NOTE]  
> HBase 클러스터를 삭제한 후에는 동일한 기본 Blob 컨테이너를 사용하여 다른 HBase 클러스터를 만들 수 있습니다. 새 클러스터에서는 원래 클러스터에서 만든 HBase 테이블을 선택합니다. 불일치를 방지하기 위해 클러스터를 삭제하기 전에 HBase 테이블을 사용하지 않도록 설정하는 것이 좋습니다.
> 
> 

## <a name="create-tables-and-insert-data"></a>테이블 만들기 및 데이터 삽입
SSH를 사용하여 HBase 클러스터를 연결하고 [Apache HBase 셸](https://hbase.apache.org/0.94/book/shell.html)을 사용하여 HBase 테이블을 만들고 데이터 및 쿼리 데이터를 삽입할 수 있습니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

대부분의 사람들의 경우, 데이터는 테이블 형식으로 나타납니다.

![HDInsight HBase 테이블 형식 데이터][img-hbase-sample-data-tabular]

[Cloud BigTable](https://cloud.google.com/bigtable/)의 구현인 HBase에서는 동일한 데이터가 다음과 같이 표시됩니다.

![HDInsight HBase BigTable 데이터][img-hbase-sample-data-bigtable]


**HBase 셸을 사용하려면**

1. SSH에서 다음 HBase 명령을 실행합니다.
   
    ```bash
    hbase shell
    ```

2. 두 열 패밀리가 있는 HBase를 만듭니다.

    ```hbaseshell   
    create 'Contacts', 'Personal', 'Office'
    list
    ```
3. 일부 데이터 삽입:
    
    ```hbaseshell   
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    scan 'Contacts'
    ```
   
    ![HDInsight Hadoop HBase 셸][img-hbase-shell]
4. 단일 행 가져오기
   
    ```hbaseshell
    get 'Contacts', '1000'
    ```
   
    행이 하나만 있기 때문에 스캔 명령을 사용하여 동일한 결과가 표시됩니다.
   
    HBase 테이블 스키마에 대한 자세한 내용은 [Apache HBase 스키마 디자인 소개][hbase-schema]를 참조하세요. HBase 명령에 대한 자세한 내용은 [Apache HBase 참조 가이드][hbase-quick-start]를 참조하세요.
5. 셸 종료
   
    ```hbaseshell
    exit
    ```

**연락처 HBase 테이블로 대량으로 데이터 로드**

HBase는 테이블로 데이터를 로드하는 여러 방법을 포함합니다.  자세한 내용은 [대량 로드](https://hbase.apache.org/book.html#arch.bulk.load)를 참조하세요.

공용 blob 컨테이너에서 샘플 데이터 파일을 찾을 수 있습니다 *wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt*합니다.  데이터 파일 내용은 다음과 같습니다.

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

필요에 따라 텍스트 파일을 만들고 고유한 저장소 계정에 파일을 업로드할 수 있습니다. 지침에 대해서는 [HDInsight에서 Apache Hadoop 작업용 데이터 업로드][hdinsight-upload-data]를 참조하세요.

> [!NOTE]  
> 이 절차는 마지막 절차에서 만든 연락처 HBase 테이블을 사용합니다.

1. SSH에서 다음 명령을 실행하여 데이터 파일을 StoreFiles로 변형하고 Dimporttsv.bulk.output에서 지정한 상대 경로에 저장합니다.  HBase 셸인 경우에는 exit 명령을 사용하여 종료합니다.

    ```bash   
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. /Example/data/storeDataFileOutput에서 HBase 테이블로 데이터를 업로드하려면 다음 명령을 실행합니다.
   
    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. HBase 셸을 열고 스캔 명령을 사용하여 테이블 내용을 나열할 수 있습니다.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Apache Hive를 사용하여 Apache HBase 쿼리

[Apache Hive](https://hive.apache.org/)를 사용하여 HBase 테이블의 데이터를 쿼리할 수 있습니다. 이 섹션에서는 HBase 테이블에 매핑되는 Hive 테이블을 만들고 이를 사용하여 HBase 테이블의 데이터를 쿼리합니다.

1. **PuTTY**를 열고 클러스터에 연결합니다.  이전 절차의 지침을 참조하세요.
2. SSH 세션에서 다음 명령을 사용하여 Beeline을 시작합니다.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Beeline에 대한 자세한 내용은 [Beeline을 사용하여 HDInsight에서 Hadoop과 Hive 사용](../hadoop/apache-hadoop-use-hive-beeline.md)을 참조하세요.
       
3. 다음 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) 스크립트를 실행하여 HBase 테이블에 매핑되는 Hive 테이블을 만듭니다. 이 문을 실행하기 전에 HBase 셸을 사용하여 이 자습서의 앞에서 참조한 샘플 테이블을 만들었는지 확인합니다.

    ```hiveql   
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

4. HBase 테이블에서 데이터를 쿼리하려면 다음 HiveQL 스크립트를 실행합니다.

    ```hiveql   
    SELECT count(rowkey) FROM hbasecontacts;
    ```

## <a name="use-hbase-rest-apis-using-curl"></a>Curl을 사용하여 HBase REST API 사용

REST API는 [기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 보안 HTTP(HTTPS)를 사용하여 요청해야 합니다.

1. 다음 명령을 사용하여 기존의 HBase 테이블을 나열합니다.

    ```bash
    curl -u <UserName>:<Password> \
    -G https://<ClusterName>.azurehdinsight.net/hbaserest/
    ```

1. 다음 명령을 사용하여 두 열 패밀리가 있는 새 HBase 테이블을 만듭니다.

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    스키마는 JSon 형식으로 제공됩니다.
1. 다음 명령을 사용하여 데이터 일부를 삽입합니다.

    ```bash   
    curl -u <UserName>:<Password> \
    -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```
   
    -d 스위치에 지정된 값을 base64로 인코딩해야 합니다. 예제:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single)를 사용하면 여러 (일괄 처리된) 값을 삽입할 수 있습니다.
1. 다음 명령을 사용하여 행을 가져옵니다.
   
    ```bash 
    curl -u <UserName>:<Password> \
    -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

HBase Rest에 대한 자세한 내용은 [Apache HBase 참조 가이드](https://hbase.apache.org/book.html#_rest)를 참조하세요.

> [!NOTE]  
> Thrift는 HDInsight의 HBase에서 지원되지 않습니다.
>
> WebHCat에서 Curl 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 클러스터 이름을 서버로 요청을 보내는 데 사용되는 URI(Uniform Resource Identifier)의 일부로 사용해야 합니다.
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    그러면 다음과 유사한 응답이 표시됩니다.
>   
>        {"status":"ok","version":"v1"}
   


## <a name="check-cluster-status"></a>클러스터 상태 확인
HDInsight에서 HBase는 클러스터 모니터링에 대한 웹 UI와 함께 제공됩니다. 웹 UI를 사용하여 지역에 대한 정보 또는 통계를 요청할 수 있습니다.

**HBase Master UI에 액세스하려면**

1. https://&lt;Clustername>.azurehdinsight.net에서 Ambari 웹 UI에 로그인합니다.
2. 왼쪽 메뉴에서 **HBase**를 클릭합니다.
3. 페이지 위쪽에서 **빠른 링크**를 클릭하고 활성 Zookeeper 노드 링크를 가리킨 다음 **HBase Master UI**를 클릭합니다.  UI는 다른 브라우저 탭에서 열립니다.

   ![HDInsight HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   HBase Master UI에는 다음 섹션이 포함되어 있습니다.

   - 지역 서버
   - 백업 마스터
   - 테이블
   - 태스크
   - 소프트웨어 특성

## <a name="delete-the-cluster"></a>클러스터 삭제
불일치를 방지하기 위해 클러스터를 삭제하기 전에 HBase 테이블을 사용하지 않도록 설정하는 것이 좋습니다.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](../hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Apache HBase 클러스터를 만드는 방법 및 테이블을 만들고 HBase 셸의 데이터를 이 테이블에서 보는 방법을 알아보았습니다. 또한 HBase 테이블에서 데이터에 대해 Hive 쿼리를 사용하는 방법 및 HBase C# REST API를 사용하여 HBase 테이블을 만들고 이 테이블에서 데이터를 검색하는 방법도 알아보았습니다.

자세한 내용은 다음을 참조하세요.

* [HDInsight HBase 개요][hdinsight-hbase-overview]: Apache HBase는 구조화되지 않은/반구조화된 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공하는 Apache Hadoop 기반의 Apache 오픈 소스 NoSQL 데이터베이스입니다.

[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hbase-reference]: https://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: https://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]:apache-hbase-overview.md
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hbase-shell]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
