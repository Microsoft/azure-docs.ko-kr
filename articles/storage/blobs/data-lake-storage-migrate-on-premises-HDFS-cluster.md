---
title: Azure Data Box를 사용 하 여 온-프레미스 HDFS 저장소에서 Azure Storage로 마이그레이션
description: 온-프레미스 HDFS 저장소에서 Azure Storage로 데이터 마이그레이션
author: normesta
ms.service: storage
ms.date: 11/19/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: e82c325ad5ad91e6b4503949e6534b054023f1f2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990966"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Azure Data Box를 사용 하 여 온-프레미스 HDFS 저장소에서 Azure Storage로 마이그레이션

Data Box 장치를 사용 하 여 Hadoop 클러스터의 온-프레미스 HDFS 저장소에서 Azure Storage (blob Storage 또는 Data Lake Storage Gen2)로 데이터를 마이그레이션할 수 있습니다. 80-TB Data Box 또는 770-TB Data Box Heavy에서 선택할 수 있습니다.

이 문서는 다음 작업을 완료 하는 데 도움이 됩니다.

> [!div class="checklist"]
> * 데이터 마이그레이션을 준비 합니다.
> * 데이터를 Data Box 또는 Data Box Heavy 장치로 복사 합니다.
> * 장치를 Microsoft에 다시 배송 합니다.
> * 데이터를 Data Lake Storage Gen2로 이동 합니다.

## <a name="prerequisites"></a>필수 조건

마이그레이션을 완료 하려면 이러한 항목이 필요 합니다.

* 저장소 계정 2 개 하나는 계층적 네임 스페이스를 사용 하도록 설정 하 고 다른 하나는 그렇지 않습니다.

* 원본 데이터를 포함 하는 온-프레미스 Hadoop 클러스터입니다.

* [Azure Data Box 장치](https://azure.microsoft.com/services/storage/databox/)입니다.

  * Data Box 또는 [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered) [를 주문](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) 합니다. 장치를 정렬 하는 동안 계층 구조 네임 스페이스를 사용 하도록 설정 **되지** 않은 저장소 계정을 선택 해야 합니다. Data Box 장치가 아직 Azure Data Lake Storage Gen2에 대 한 직접 수집을 지원 하지 않기 때문입니다. 저장소 계정에 복사한 다음 두 번째 복사 작업을 ADLS Gen2 계정으로 수행 해야 합니다. 이에 대 한 지침은 아래 단계에서 제공 됩니다.

  * [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 또는 [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) 를 온-프레미스 네트워크에 연결 합니다.

준비가 되 면 시작 해 보겠습니다.

## <a name="copy-your-data-to-a-data-box-device"></a>Data Box 장치에 데이터 복사

데이터가 단일 Data Box 장치에 맞는 경우 데이터를 Data Box 장치로 복사 합니다. 

데이터 크기가 Data Box 장치의 용량을 초과 하는 경우에는 [선택적 절차를 사용 하 여 데이터를 여러 Data Box 장치로 분할](#appendix-split-data-across-multiple-data-box-devices) 한 다음이 단계를 수행 합니다. 

온-프레미스 HDFS 저장소에서 Data Box 장치로 데이터를 복사 하려면 몇 가지를 설정 하 고 [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 도구를 사용 합니다.

Blob/Object storage의 REST Api를 통해 데이터를 Data Box 장치에 복사 하려면 다음 단계를 수행 합니다. REST API 인터페이스는 장치가 클러스터에 HDFS 저장소로 표시 되도록 합니다.

1. REST를 통해 데이터를 복사 하기 전에 Data Box 또는 Data Box Heavy에서 REST 인터페이스에 연결 하기 위한 보안 및 연결 기본 형식을 확인 합니다. Data Box의 로컬 웹 UI에 로그인 하 고 **연결 및 페이지 복사** 로 이동 합니다. 장치에 대 한 Azure storage 계정에 대해 **액세스 설정**에서를 찾아 **REST**를 선택 합니다.

    !["연결 및 복사" 페이지](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 액세스 저장소 계정 및 데이터 업로드 대화 상자에서 **Blob service 엔드포인트** 및 **저장소 계정 키**를 복사 합니다. Blob service 엔드포인트에서 `https://`와 후행 슬래시를 생략 합니다.

    이 경우 엔드포인트은 `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`입니다. 사용할 URI의 호스트 부분은 `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`입니다. 예제는 [http를 통해 REST에 연결](/azure/databox/data-box-deploy-copy-data-via-rest)하는 방법을 참조 하세요. 

     !["저장소 계정 액세스 및 데이터 업로드" 대화 상자](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 각 노드에서 엔드포인트 및 Data Box 또는 Data Box Heavy 노드 IP 주소를 `/etc/hosts`에 추가 합니다.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    DNS에 다른 메커니즘을 사용 하는 경우 Data Box 엔드포인트을 확인할 수 있는지 확인 해야 합니다.

4. 셸 변수 `azjars` `hadoop-azure`의 위치로 설정 하 고 jar 파일을 `azure-storage` 합니다. 이러한 파일은 Hadoop 설치 디렉터리에서 찾을 수 있습니다.

    이러한 파일이 있는지 확인 하려면 다음 명령을 사용 합니다. `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. `<hadoop_install_dir>` 자리 표시자를 Hadoop을 설치한 디렉터리의 경로로 바꿉니다. 정규화 된 경로를 사용 해야 합니다.

    예시:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 데이터 복사에 사용 하려는 저장소 컨테이너를 만듭니다. 또한이 명령의 일부로 대상 디렉터리를 지정 해야 합니다. 이 시점에서 더미 대상 디렉터리가 될 수 있습니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` 자리 표시자를 blob service 엔드포인트의 이름으로 바꿉니다.

    * `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉니다.

    * `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

    * `<destination_directory>` 자리 표시자를 데이터를 복사할 대상 디렉터리의 이름으로 바꿉니다.

6. 목록 명령을 실행 하 여 컨테이너 및 디렉터리를 만들었는지 확인 합니다.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * `<blob_service_endpoint>` 자리 표시자를 blob service 엔드포인트의 이름으로 바꿉니다.

   * `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉니다.

   * `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

7. Hadoop HDFS에서 이전에 만든 컨테이너에 Data Box Blob 저장소로 데이터를 복사 합니다. 복사할 디렉터리를 찾을 수 없는 경우이 명령은 자동으로 만듭니다.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` 자리 표시자를 blob service 엔드포인트의 이름으로 바꿉니다.

    * `<account_key>` 자리 표시자를 계정의 액세스 키로 바꿉니다.

    * `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

    * `<exlusion_filelist_file>` 자리 표시자를 파일 제외 목록을 포함 하는 파일의 이름으로 바꿉니다.

    * `<source_directory>` 자리 표시자를 복사 하려는 데이터가 포함 된 디렉터리의 이름으로 바꿉니다.

    * `<destination_directory>` 자리 표시자를 데이터를 복사할 대상 디렉터리의 이름으로 바꿉니다.

    `-libjars` 옵션은 `distcp`에 `hadoop-azure*.jar` 및 종속 `azure-storage*.jar` 파일을 사용할 수 있도록 하는 데 사용 됩니다. 일부 클러스터에 대해이 문제가 이미 발생 했을 수 있습니다.

    다음 예에서는 `distcp` 명령을 사용 하 여 데이터를 복사 하는 방법을 보여 줍니다.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    복사 속도를 향상 시키려면 다음을 수행 합니다.

    * 매퍼 수를 변경해 보세요. 위의 예제에서는 `m` = 4 매퍼를 사용 합니다.

    * 여러 `distcp`를 병렬로 실행 해 보세요.

    * 규모가 작은 파일은 작은 파일 보다 성능이 뛰어납니다.

## <a name="ship-the-data-box-to-microsoft"></a>Microsoft에 Data Box 제공

Data Box 장치를 준비 하 고 Microsoft에 제공 하려면 다음 단계를 수행 합니다.

1. 먼저 [Data Box 또는 Data Box Heavy에서 배송 준비](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)합니다.

2. 장치 준비가 완료 되 면 BOM 파일을 다운로드 합니다. 이러한 BOM 또는 매니페스트 파일을 나중에 사용 하 여 Azure에 업로드 된 데이터를 확인 합니다.

3. 장치를 종료 하 고 케이블을 제거 합니다.

4. UPS를 사용하여 픽업을 예약합니다.

    * Data Box 장치의 경우 [Data Box 제공](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)을 참조 하세요.

    * Data Box Heavy 장치의 경우 [Data Box Heavy 제공](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)을 참조 하세요.

5. Microsoft에서 장치를 받은 후에는 데이터 센터 네트워크에 연결 되 고 장치 순서를 배치할 때 사용자가 지정한 저장소 계정 (계층적 네임 스페이스 사용 안 함)으로 데이터가 업로드 됩니다. 모든 데이터가 Azure에 업로드 되는 BOM 파일에 대해 확인 합니다. 이제이 데이터를 Data Lake Storage Gen2 Storage 계정으로 이동할 수 있습니다.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>데이터를 Azure Data Lake Storage Gen2 이동

이미 Azure Storage 계정에 데이터가 있습니다. 이제 Azure Data Lake 저장소 계정에 데이터를 복사 하 고 파일 및 디렉터리에 대 한 액세스 권한을 적용 합니다.

> [!NOTE]
> Azure Data Lake Storage Gen2를 데이터 저장소로 사용 하는 경우이 단계가 필요 합니다. 계층적 네임 스페이스가 없는 blob storage 계정만 데이터 저장소로 사용 하는 경우이 섹션을 건너뛸 수 있습니다.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Azure Data Lake Storage Gen 2 계정에 데이터 복사

Azure Data Factory를 사용 하거나 Azure 기반 Hadoop 클러스터를 사용 하 여 데이터를 복사할 수 있습니다.

* Azure Data Factory을 사용 하려면 [Azure Data Factory를 참조 하 여 데이터를 ADLS Gen2으로 이동](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)합니다. **Azure Blob Storage** 를 원본으로 지정 해야 합니다.

* Azure 기반 Hadoop 클러스터를 사용 하려면 다음 DistCp 명령을 실행 합니다.

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * `<source_account>` 및 `<dest_account>` 자리 표시자를 원본 및 대상 저장소 계정의 이름으로 바꿉니다.

    * `<source_container>` 및 `<dest_container>` 자리 표시자를 원본 및 대상 컨테이너의 이름으로 바꿉니다.

    * `<source_path>` 및 `<dest_path>` 자리 표시자를 원본 및 대상 디렉터리 경로로 바꿉니다.

    * `<source_account_key>` 자리 표시자를 데이터를 포함 하는 저장소 계정의 액세스 키로 바꿉니다.

    이 명령은 저장소 계정에 있는 데이터와 메타 데이터를 모두 Data Lake Storage Gen2 저장소 계정에 복사 합니다.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 계정에 대 한 서비스 주체 만들기

서비스 주체를 만들려면 [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)를 참조 하세요.

* 문서의 [애플리케이션을 역할에 할당](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) 섹션에 있는 단계를 수행할 때 **Storage Blob 데이터 참가자** 역할을 서비스 주체에 할당해야 합니다.

* 문서의 [로그인에 사용할 값 가져오기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 섹션의 단계를 수행 하는 경우 응용 프로그램 ID 및 클라이언트 암호 값을 텍스트 파일에 저장 합니다. 곧 이 값들이 필요합니다.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>사용 권한이 있는 복사 된 파일의 목록을 생성 합니다.

온-프레미스 Hadoop 클러스터에서 다음 명령을 실행 합니다.

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

이 명령은 해당 권한이 있는 복사 된 파일의 목록을 생성 합니다.

> [!NOTE]
> HDFS의 파일 수에 따라이 명령을 실행 하는 데 시간이 오래 걸릴 수 있습니다.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Id 목록을 생성 하 고 id를 Azure Active Directory (추가) id에 매핑합니다.

1. `copy-acls.py` 스크립트를 다운로드 합니다. 이 문서의 [도우미 스크립트 다운로드 및 실행에 대 한에 지 노드 설정](#download-helper-scripts) 섹션을 참조 하세요.

2. 이 명령을 실행 하 여 고유한 id 목록을 생성 합니다.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   이 스크립트는 추가 기반 id에 매핑해야 하는 id를 포함 하는 `id_map.json` 라는 파일을 생성 합니다.

3. 텍스트 편집기에서 `id_map.json` 파일을 엽니다.

4. 파일에 표시 되는 각 JSON 개체에 대해 적절 한 매핑된 id를 사용 하 여 AAD UPN (사용자 계정 이름) 또는 ObjectId (OID)의 `target` 특성을 업데이트 합니다. 작업을 완료 한 후 파일을 저장 합니다. 이 파일은 다음 단계에서 필요 합니다.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>복사한 파일에 사용 권한 적용 및 id 매핑 적용

다음 명령을 실행 하 여 Data Lake Storage Gen2 계정에 복사한 데이터에 사용 권한을 적용 합니다.

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

* `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

* `<application-id>` 및 `<client-secret>` 자리 표시자를 서비스 주체를 만들 때 수집한 응용 프로그램 ID 및 클라이언트 암호로 바꿉니다.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>부록: 여러 Data Box 장치에서 데이터 분할

데이터를 Data Box 장치로 이동 하기 전에 일부 도우미 스크립트를 다운로드 하 고, 데이터가 Data Box 장치에 맞게 구성 되어 있는지 확인 하 고, 불필요 한 파일을 제외 해야 합니다.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>도우미 스크립트를 다운로드 하 고에 지 노드를 설정 하 여 실행 합니다.

1. 온-프레미스 Hadoop 클러스터의에 지 또는 헤드 노드에서 다음 명령을 실행 합니다.

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   이 명령은 도우미 스크립트가 포함 된 GitHub 리포지토리를 복제 합니다.

2. 로컬 컴퓨터에 [jq](https://stedolan.github.io/jq/) 패키지가 설치 되어 있는지 확인 합니다.

   ```bash
   
   sudo apt-get install jq
   ```

3. [요청](https://2.python-requests.org/en/master/) python 패키지를 설치 합니다.

   ```bash
   
   pip install requests
   ```

4. 필요한 스크립트에 대 한 실행 권한을 설정 합니다.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>데이터가 Data Box 장치에 맞게 구성 되어 있는지 확인

데이터 크기가 단일 Data Box 장치의 크기를 초과 하는 경우 여러 Data Box 장치에 저장할 수 있는 그룹으로 파일을 분할할 수 있습니다.

데이터가 단일 Data Box 장치의 크기를 초과 하지 않는 경우 다음 섹션으로 진행할 수 있습니다.

1. 상승 된 권한으로 이전 섹션의 지침에 따라 다운로드 한 `generate-file-list` 스크립트를 실행 합니다.

   명령 매개 변수에 대 한 설명은 다음과 같습니다.

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 작업에서 액세스할 수 있도록 생성 된 파일 목록을 HDFS에 복사 합니다.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>불필요 한 파일 제외

DisCp 작업에서 일부 디렉터리를 제외 해야 합니다. 예를 들어 클러스터를 계속 실행 하는 상태 정보를 포함 하는 디렉터리를 제외 합니다.

DistCp 작업을 시작 하려는 온-프레미스 Hadoop 클러스터에서 제외 하려는 디렉터리 목록을 지정 하는 파일을 만듭니다.

예를 들면 다음과 같습니다.

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>다음 단계

Data Lake Storage Gen2 HDInsight 클러스터에서 작동 하는 방법을 알아봅니다. [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.
