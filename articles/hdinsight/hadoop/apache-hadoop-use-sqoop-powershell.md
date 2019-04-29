---
title: PowerShell 및 Azure HDInsight를 사용하여 Apache Sqoop 작업 실행
description: 워크스테이션에서 Azure PowerShell을 사용하여 Apache Hadoop 클러스터와 Azure SQL 데이터베이스 간에 Apache Sqoop 가져오기 및 내보내기를 실행하는 방법에 대해 알아봅니다.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 49375b00607f1f23a99855f8b80f5f5c3c4dac22
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125025"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>HDInsight에서 Apache Hadoop용 Azure PowerShell을 사용하여 Apache Sqoop 작업 실행
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure PowerShell을 사용 하 여 가져오기 및 내보내기는 HDInsight 클러스터와 Azure SQL database 또는 SQL Server 데이터베이스 간에 데이터를 Azure HDInsight에서 Apache Sqoop 작업을 실행 하는 방법에 알아봅니다. 이 예제에서 데이터를 내보냅니다 `/tutorials/usesqoop/data/sample.log` 와 기본 저장소 계정, 다음 테이블에이 메서드는 호출 하는 가져오기 `log4jlogs` SQL Server 데이터베이스에 있습니다. 이 문서는 이어지는 [HDInsight에서 Hadoop과 Apache Sqoop을 사용 하 여](./hdinsight-use-sqoop.md)입니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 항목이 있어야 합니다.

* Azure PowerShell을 사용 하 여 워크스테이션 [AZ 모듈](https://docs.microsoft.com/powershell/azure/overview) 설치 합니다.

* 완료 [테스트 환경 설정](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) 에서 [HDInsight에서 Hadoop과 Apache Sqoop을 사용 하 여](./hdinsight-use-sqoop.md)입니다.


## <a name="run-apache-sqoop-by-using-powershell"></a>PowerShell을 사용하여 Apache Sqoop 실행
다음 PowerShell 스크립트를 미리 소스 파일을 처리 하 고 다음 테이블에는 Azure SQL database로 내보냅니다 `log4jlogs`합니다. 바꿉니다 `CLUSTERNAME`, `CLUSTERPASSWORD`, 및 `SQLPASSWORD` 필수 구성 요소에서 사용 되는 값을 사용 하 여 합니다.

```powershell 
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>제한 사항
Linux 기반 HDInsight에에 대한 제한 사항은 다음과 같습니다.

* 대량 내보내기: Microsoft SQL Server 또는 Azure SQL Database에 데이터를 내보내는 데 사용되는 Sqoop 커넥터는 현재 대량 삽입을 지원하지 않습니다.

* 일괄 처리: 삽입을 수행할 때 `-batch` 스위치를 사용하면 Sqoop에서 삽입 작업을 일괄 처리하는 대신 여러 번의 삽입 작업을 수행합니다. 

## <a name="next-steps"></a>다음 단계
이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight에서 Apache Oozie 사용](../hdinsight-use-oozie-linux-mac.md): Oozie 워크플로에서 Sqoop 작업을 사용합니다.
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md): HDInsight 또는 Azure Blob 스토리지에 데이터를 업로드하는 다른 방법을 찾습니다.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
