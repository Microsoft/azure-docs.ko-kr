---
title: 공유 액세스 서명을 사용하여 액세스 제한 - Azure HDInsight
description: 공유 액세스 서명을 사용하여 Azure 저장소 Blob에 저장된 데이터에 대한 HDInsight 액세스를 제한하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 7fa46e3a5f0ed6504e4bc927caa0378d75fcc4a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763400"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Azure Storage 공유 액세스 서명을 사용하여 HDInsight에서 데이터 액세스 제한

HDInsight는 클러스터와 연결된 Azure Storage 계정의 데이터에 대해 모든 액세스 권한을 갖습니다. blob 컨테이너에서 공유 액세스 서명을 사용하여 데이터에 대한 액세스를 제한할 수 있습니다. 공유 액세스 서명(SAS)은 데이터에 대한 액세스를 제한할 수 있는 Azure 저장소 계정의 기능입니다. 예를 들어 데이터에 대한 읽기 전용 액세스를 제공합니다.

> [!IMPORTANT]  
> Apache Ranger를 사용하는 솔루션의 경우 도메인에 가입된 HDInsight를 사용하는 것이 좋습니다. 자세한 내용은 [도메인에 가입된 HDInsight 구성](./domain-joined/apache-domain-joined-configure.md) 문서를 참조하세요.

> [!WARNING]  
> HDInsight는 클러스터의 기본 저장소에 대해 모든 액세스 권한이 있어야 합니다.

## <a name="requirements"></a>요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure 구독
* C# 또는 Python. C# 예제 코드가 Visual Studio 솔루션으로 제공됩니다.

  * Visual Studio는 버전 2013, 2015 또는 2017이어야 합니다.
  * Python은 버전 2.7 이상이어야 합니다.

* Linux 기반 HDInsight 클러스터 또는 [Azure PowerShell][powershell] - 기존 Linux 기반 클러스터가 있는 경우 Apache Ambari를 사용하여 클러스터에 공유 액세스 서명을 추가할 수 있습니다. 그렇지 않으면 Azure PowerShell을 사용하여 클러스터를 만들고 클러스터를 만들 때 공유 액세스 서명을 추가합니다.

    > [!IMPORTANT]  
    > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)의 예제 파일 이 리포지토리에는 다음 항목이 포함됩니다.

  * HDInsight에 사용할 저장소 컨테이너, 저장된 정책 및 SAS를 만들 수 있는 Visual Studio 프로젝트
  * HDInsight에 사용할 저장소 컨테이너, 저장된 정책 및 SAS를 만들 수 있는 Python 스크립트
  * HDInsight 클러스터를 만들고 SAS를 사용하도록 구성할 수 있는 PowerShell 스크립트

## <a name="shared-access-signatures"></a>공유 액세스 서명

두 가지 형태의 공유 액세스 서명이 있습니다.

* 임시: SAS에 대한 시작 시간, 만료 시간 및 사용 권한이 SAS URI에 모두 지정됩니다.

* 저장된 액세스 정책: 저장된 액세스 정책은 Blob 컨테이너 같은 리소스 컨테이너에서 정의됩니다. 정책은 하나 이상의 공유 액세스 서명에 대한 제약 조건을 관리하는 데 사용될 수 있습니다. SAS를 공유 액세스 정책과 연결할 경우 SAS는 저장된 액세스 정책에 대해 정의된 제약 조건(시작 시간, 만료 시간 및 사용 권한)을 상속합니다.

두 형식의 차이점은 주요 시나리오인 해지에 중요합니다. SAS는 URL이므로 SAS를 시작하도록 요청한 사용자에 상관없이 SAS를 획득한 모든 사용자가 SAS를 사용할 수 있습니다. SAS가 공개적으로 게시된 경우 전 세계의 모든 사용자가 SAS를 사용할 수 있습니다. 분산된 SAS는 다음 네 가지 중 하나에 해당할 때까지 유효합니다.

1. SAS에 지정된 만료 시간에 도달한 경우

2. SAS에서 참조된 저장된 액세스 정책에 대해 지정된 만료 시간에 도달했습니다. 다음과 같은 시나리오에서는 만료 시간에 도달합니다.

    * 시간 간격이 경과되었습니다.
    * 저장된 액세스 정책이 과거 만료 시간을 갖도록 수정되어 있습니다. SAS를 철회하는 한 가지 방법은 만료 시간을 변경하는 것입니다.

3. SAS에서 참조되는 저장된 액세스 정책을 삭제한 경우(SAS를 해지하는 다른 방법). 똑같은 이름을 사용하여 저장된 액세스 정책을 다시 만들면 이전 정책에 대한 모든 SAS 토큰이 다시 유효해집니다(SAS의 만료 시간이 경과하지 않은 경우). SAS를 해지하기 위해 만료 시간을 미래의 시간으로 지정하여 액세스 정책을 다시 만들 경우 다른 이름을 사용해야 합니다.

4. SAS를 만드는 데 사용된 계정 키를 다시 생성한 경우. 키를 다시 생성하면 이전 키를 사용하는 모든 애플리케이션이 인증에 실패합니다. 모든 구성 요소를 새 키로 업데이트합니다.

> [!IMPORTANT]  
> 공유 액세스 서명 URI는 서명을 만드는 데 사용된 계정 키 및 저장된 관련 액세스 정책(있는 경우)에 연결됩니다. 저장된 액세스 정책을 지정하지 않는 경우 공유 액세스 서명을 해지하는 방법은 계정 키를 변경하는 것뿐입니다.

항상 저장된 액세스 정책을 사용하는 것이 좋습니다. 저장된 정책을 사용하는 경우 필요에 따라 서명을 철회하거나 만료 날짜를 연장할 수 있습니다. 이 문서의 단계에서는 SAS를 생성하는 데 저장된 액세스 정책을 사용합니다.

공유 액세스 서명에 대한 자세한 내용은 [SAS 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

### <a name="create-a-stored-policy-and-sas-using-c"></a>C\#을 사용하여 저장된 정책 및 SAS 만들기

1. Visual Studio에서 솔루션을 엽니다.

2. 솔루션 탐색기에서 **SASToken** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

3. **설정** 을 선택하고 다음 항목에 대한 값을 추가합니다.

   * StorageConnectionString: 저장된 정책 및 SAS를 만들 스토리지 계정에 대한 연결 문자열입니다. 형식은 `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`여야 하며 여기서 `myaccount`는 사용자의 저장소 계정 이름이고 `mykey`는 저장소 계정에 대한 키입니다.

   * ContainerName: 액세스를 제한할 스토리지 계정의 컨테이너입니다.

   * SASPolicyName: 만들려는 저장된 정책에 사용할 이름입니다.

   * FileToUpload: 컨테이너에 업로드되는 파일의 경로입니다.

4. 프로젝트를 실행합니다. SAS가 생성되면 다음 텍스트와 유사한 정보가 표시됩니다.

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    SAS 정책 토큰, 저장소 계정 이름 및 컨테이너 이름을 저장합니다. 저장소 계정을 HDInsight 클러스터에 연결할 때 이러한 값이 사용됩니다.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Python을 사용하여 저장된 정책 및 SAS 만들기

1. SASToken.py 파일을 열고 다음 값을 변경합니다.

   * policy\_name: 만들려는 저장된 정책에 사용할 이름입니다.

   * storage\_account\_name: 사용자 스토리지 계정의 이름입니다.

   * storage\_account\_key: 저장소 계정의 키입니다.

   * storage\_container\_name: 액세스를 제한할 스토리지 계정의 컨테이너입니다.

   * example\_file\_path: 컨테이너에 업로드되는 파일의 경로입니다.

2. 스크립트를 실행합니다. 스크립트가 완료되면 다음 텍스트와 유사한 SAS 토큰이 표시됩니다.

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    SAS 정책 토큰, 저장소 계정 이름 및 컨테이너 이름을 저장합니다. 저장소 계정을 HDInsight 클러스터에 연결할 때 이러한 값이 사용됩니다.

## <a name="use-the-sas-with-hdinsight"></a>HDInsight에 SAS 사용

HDInsight 클러스터를 만들 때 기본 저장소 계정을 지정해야 하며 필요에 따라 추가 저장소 계정을 지정할 수 있습니다. 저장소를 추가하는 이 두 가지 방법 모두에 사용된 저장소 계정 및 컨테이너에 대한 모든 권한이 필요합니다.

공유 액세스 서명을 사용하여 컨테이너에 대한 액세스를 제한하려면 클러스터에 대한 **core-site** 구성에 사용자 지정 항목을 추가합니다.

* **Windows 기반** 또는 **Linux 기반** HDInsight 클러스터의 경우 PowerShell을 사용하여 클러스터를 만드는 동안 항목을 추가할 수 있습니다.
* **Linux 기반** HDInsight 클러스터의 경우 Ambari를 사용하여 클러스터를 만든 후 구성을 변경합니다.

### <a name="create-a-cluster-that-uses-the-sas"></a>SAS를 사용하는 클러스터 만들기

SAS를 사용하는 HDInsight 클러스터를 만드는 예제는 리포지토리의 `CreateCluster` 디렉터리에 있습니다. 이를 사용하려면 다음 단계를 수행합니다.

1. 텍스트 편집기에서 `CreateCluster\HDInsightSAS.ps1` 파일을 열고 문서 맨 앞에 있는 다음 값을 수정합니다.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    예를 들어 `'mycluster'` 를 만들려는 클러스터의 이름으로 변경합니다. SAS 값은 저장소 계정 및 SAS 토큰을 만들 때 이전 단계에서 사용한 값과 일치해야 합니다.

    값을 변경한 후 파일을 저장합니다.

2. 새 Azure PowerShell 프롬프트를 엽니다. Azure PowerShell에 익숙하지 않거나 설치되지 않은 경우 [Azure PowerShell 설치 및 구성][powershell]을 참조하세요.

1. 프롬프트에서 다음 명령을 사용하여 Azure 구독에 대해 인증합니다.

    ```powershell
    Connect-AzAccount
    ```

    Azure 구독에 대 한 메시지가 표시 되 면는 계정으로 로그인 합니다.

    계정이 여러 Azure 구독과 연결되는 경우 `Select-AzSubscription` 을 사용하여 사용할 구독을 선택해야 합니다.

4. 프롬프트에서 디렉터리를 HDInsightSAS.ps1 파일이 있는 `CreateCluster` 디렉터리로 변경합니다. 그런 후 다음 명령을 사용하여 스크립트를 실행합니다.

    ```powershell
    .\HDInsightSAS.ps1
    ```

    스크립트를 실행하면 리소스 그룹 및 스토리지 계정이 생성되면서 출력이 PowerShell 프롬프트에 기록됩니다. HDInsight 클러스터에 대한 HTTP 사용자를 입력하라는 메시지가 표시됩니다. 이 계정은 클러스터에 대한 보안 HTTP/s 액세스를 보호하는 데 사용됩니다.

    Linux 기반 클러스터를 만드는 경우 SSH 사용자 계정 이름 및 암호를 묻는 메시지가 표시됩니다. 이 계정은 클러스터에 원격으로 로그인 됩니다.

   > [!IMPORTANT]  
   > HTTP/s 또는 SSH 사용자 이름 및 암호를 묻는 메시지가 나타나면 다음 조건을 충족하는 암호를 제공해야 합니다.
   >
   > * 길이가 10자 이상이어야 합니다.
   > * 숫자를 1개 이상 포함해야 합니다.
   > * 영숫자가 아닌 문자를 1개 이상 포함해야 합니다.
   > * 대문자 또는 소문자를 1개 이상 포함해야 합니다.

이 스크립트를 완료하는 데는 일반적으로 약 15분이 소요됩니다. 스크립트가 오류 없이 완료되면 클러스터가 만들어진 것입니다.

### <a name="use-the-sas-with-an-existing-cluster"></a>기존 클러스터에서 SAS 사용

기존 Linux 기반 클러스터가 있는 경우 다음 단계에 따라 **core-site** 구성에 SAS를 추가할 수 있습니다.

1. 클러스터에 대한 Ambari 웹 UI를 엽니다. 이 페이지에 대한 주소는 https://YOURCLUSTERNAME.azurehdinsight.net입니다. 메시지가 표시되면 클러스터를 만들 때 사용한 관리자 이름(admin)과 암호를 사용하여 클러스터를 인증합니다.

2. Ambari 웹 UI의 왼쪽에서 **HDFS** 를 선택한 다음 페이지 중간에서 **Configs** 탭을 선택합니다.

3. **Advanced** 탭을 선택한 다음 **Custom core-site** 섹션이 나올 때까지 스크롤합니다.

4. **Custom core-site** 섹션을 확장한 후 끝까지 스크롤하여 **Add property...** 링크를 선택합니다. **Key** 및 **Value** 필드에 다음 값을 사용합니다.

   * **키**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **값**: 이전에 실행한 C# 또는 Python 애플리케이션에서 반환된 SAS

     **CONTAINERNAME** 을 C# 또는 SAS 애플리케이션에서 사용한 컨테이너 이름으로 바꿉니다. **STORAGEACCOUNTNAME**을 사용한 스토리지 계정 이름으로 바꿉니다.

5. **Add** 단추를 클릭하여 이 키 및 값을 저장한 후 **Save** 단추를 클릭하여 구성 변경을 저장합니다. 메시지가 나타나면 변경에 대한 설명(예: "SAS 저장소 액세스 추가")을 추가하고 **저장**을 클릭합니다.

    변경이 완료되었으면 **확인** 을 클릭합니다.

   > [!IMPORTANT]  
   > 변경 내용을 적용하기 전에 여러 서비스를 다시 시작해야 합니다.

6. Ambari 웹 UI의 왼쪽 목록에서 **HDFS**를 선택한 다음, 오른쪽의 **Service Actions** 드롭다운 목록에서 **Restart All Affected**을 선택합니다. 메시지가 나타나면 __모두 다시 시작 확인__을 선택합니다.

    MapReduce2 및 YARN에 대해 이 프로세스를 반복합니다.

7. 서비스가 다시 시작된 후 각 서비스를 선택하고 **서비스 작업** 드롭다운에서 유지 관리 모드를 비활성화합니다.

## <a name="test-restricted-access"></a>제한된 액세스 테스트

액세스 제한 했는지를 확인 하려면 SSH를 사용 하 여 클러스터에 연결 합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

클러스터에 연결되면 다음 단계에 따라 SAS 저장소 계정의 항목에 대한 읽기 및 목록 전용 권한이 있는지 확인합니다.

1. 컨테이너의 콘텐츠를 나열하려면 프롬프트에서 다음 명령을 사용합니다. 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    **SASCONTAINER** 를 SAS 저장소 계정에 대해 만든 컨테이너의 이름으로 바꿉니다. **SASACCOUNTNAME**을 SAS에 사용된 저장소 계정의 이름으로 바꿉니다.

    목록에는 컨테이너와 SAS를 만들 때 업로드된 파일이 포함됩니다.

2. 다음 명령을 사용하여 파일의 내용을 읽을 수 있는지 확인합니다. 이전 단계처럼 **SASCONTAINER** 및 **SASACCOUNTNAME**을 바꿉니다. **FILENAME** 을 이전 명령에 표시된 파일 이름으로 바꿉니다.

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    이 명령은 파일 내용을 나열합니다.

3. 다음 명령을 사용하여 파일을 로컬 파일 시스템에 다운로드합니다.

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    이 명령은 파일을 **testfile.txt**라는 로컬 파일에 다운로드합니다.

4. 다음 명령을 사용하여 로컬 파일을 SAS 저장소의 새 **testupload.txt** 파일에 업로드합니다.

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    다음 텍스트와 유사한 메시지가 표시됩니다.

        put: java.io.IOException

    저장소 위치가 읽기 + 목록 전용이므로 이 오류가 발생합니다. 다음 명령을 사용하여 쓰기 가능한 클러스터의 기본 저장소에 데이터를 저장합니다.

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    이때 작업이 성공적으로 완료되어야 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="a-task-was-canceled"></a>작업이 취소됨

**증상**: PowerShell 스크립트를 사용하여 클러스터를 만들 때 다음과 같은 오류 메시지가 나타날 수 있습니다.

    New-AzHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**원인**: 클러스터의 admin/HTTP 사용자 또는 Linux 기반 클러스터인 경우 SSH 사용자에 대해 암호를 사용하는 경우 이 오류가 발생할 수 있습니다.

**해결 방법**: 다음 조건을 충족하는 암호를 사용합니다.

* 길이가 10자 이상이어야 합니다.
* 숫자를 1개 이상 포함해야 합니다.
* 영숫자가 아닌 문자를 1개 이상 포함해야 합니다.
* 대문자 또는 소문자를 1개 이상 포함해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 HDInsight 클러스터에 액세스가 제한된 저장소를 추가하는 방법을 배웠으므로 클러스터에서 데이터에 대해 작업하는 다른 방법에 알아보겠습니다.

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
