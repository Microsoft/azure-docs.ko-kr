---
title: 파워 쿼리로 Apache Hadoop에 Excel 연결 - Azure HDInsight
description: 비즈니스 인텔리전스 구성 요소를 사용하는 방법과 Excel용 파워 쿼리를 사용하여 HDInsight의 Hadoop에 저장된 데이터에 액세스하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 13862e642c6a91fe6f3c635df2efde91672ecbad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866814"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결

Microsoft의 빅데이터 솔루션의 주요 기능 중 하나는 Microsoft BI(비즈니스 인텔리전스) 구성 요소를 Azure HDInsight의 Apache Hadoop 클러스터와 통합하는 것입니다. 주요 예제로 Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 Hadoop 클러스터와 연결된 데이터가 포함된 Azure Storage 계정에 Excel을 연결하는 기능이 있습니다. 이 자습서에서는 파워 쿼리를 설정하고 사용하여 HDInsight로 관리하는 Hadoop 클러스터와 연결된 데이터를 쿼리하는 방법을 단계별로 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Hadoop 클러스터. [Linux에서 HDInsight 시작](./apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
* Windows 10, 7, Windows Server 2008 R2 이하의 운영 체제를 실행하는 워크스테이션.
* 엔터프라이즈용 Microsoft 365 앱, Office 2016, Office 2013 Professional Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Microsoft 파워 쿼리 설치

파워 쿼리는 출력되었거나 HDInsight 클러스터에 대해 실행되는 Hadoop 작업에서 생성된 데이터를 가져올 수 있습니다.

Excel 2016에서는 파워 쿼리가 데이터 리본의 가져오기 및 변환 섹션 아래에 통합되었습니다. 이전 Excel 버전의 경우 [Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?LinkID=286689)에서 Microsoft Excel용 파워 쿼리를 다운로드하여 설치합니다.

## <a name="import-hdinsight-data-into-excel"></a>Excel로 HDInsight 데이터 가져오기

Excel용 파워 쿼리 추가 기능을 사용하면 HDInsight 클러스터에서 Excel로 쉽게 데이터를 가져올 수 있으며, 여기서 PowerPivot 및 파워 맵 같은 BI 도구를 사용하여 데이터를 검사하고 분석하며 표시할 수 있습니다.

1. Excel을 실행합니다.

1. 비어 있는 새 통합 문서를 만듭니다.

1. Excel 버전에 따라 다음 단계를 수행합니다.

   * Excel 2016

     * **데이터** > **데이터 가져오기** > **Azure에서** > **Azure HDInsight(HDFS)에서** 를 선택합니다.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png" alt-text="HDI.PowerQuery.SelectHdiSource.2016" border="true":::

   * Excel 2013/2010

     * **파워 쿼리** > **Azure에서** > **Microsoft Azure HDInsight에서** 를 선택합니다.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png" alt-text="HDI.PowerQuery.SelectHdiSource" border="true":::

       **참고**: **파워 쿼리** 메뉴가 표시되지 않는 경우 **파일** > **옵션** > **추가 기능** 으로 이동하여 페이지 아래쪽에 있는 드롭다운 **관리** 상자에서 **COM 추가 기능** 을 선택합니다. **이동...** 단추를 선택하고 Excel용 파워 쿼리 추가 기능에 해당하는 상자가 선택되어 있는지 확인합니다.

       **참고**: 또한 파워 쿼리를 사용하면 **기타 원본에서** 를 선택하여 HDFS에서 데이터를 가져올 수 있습니다.

1. **Azure HDInsight(HDFS)** 대화 상자의 **계정 이름 또는 URL** 텍스트 상자에 클러스터와 연결된 Azure Blob Storage 계정의 이름을 입력합니다. 그런 다음, **확인** 을 선택합니다. 이 계정은 기본 스토리지 계정 또는 연결된 스토리지 계정이 될 수 있습니다.  형식은 `https://StorageAccountName.blob.core.windows.net/`입니다.

1. **계정 키** 에 Blob Storage 계정의 키를 입력한 다음 **연결** 을 선택합니다. (이 저장소에 처음 액세스할 때만 계정 정보를 입력하면 됩니다.)

1. 쿼리 편집기 왼쪽의 **탐색기** 창에서 클러스터와 연결된 Blob Storage 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다.

1. **Name** 열에서 **HiveSampleData.txt**(폴더 경로 **../hive/warehouse/hivesampletable/** )를 찾은 후 HiveSampleData.txt 왼쪽에 있는 **Binary** 를 선택합니다. HiveSampleData.txt는 모든 클러스터와 함께 제공됩니다. 필요에 따라 사용자의 파일을 사용할 수 있습니다.

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png" alt-text="HDI Excel 파워 쿼리 가져오기 데이터" border="true":::

1. 원하는 경우 열 이름을 바꿀 수 있습니다. 준비가 되면 **닫기 및 로드** 를 선택합니다.  통합 문서에 데이터가 로드됩니다.

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png" alt-text="HDI Excel 파워 쿼리로 가져온 테이블" border="true":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 파워 쿼리를 사용하여 HDInsight에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight에서 Azure SQL Database로 데이터를 가져올 수 있습니다. 데이터를 HDInsight에 업로드할 수도 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](../hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./../hdinsight-upload-data.md)
