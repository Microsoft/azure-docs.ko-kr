---
title: 'Excel, Python 또는 R에서 Azure Databricks에 연결 '
description: Simba 드라이버를 사용하여 Azure Databricks를 Excel, Python 또는 R에 연결하는 방법을 알아봅니다.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: 1805f04d7833dea180847defadd865cb23e9df62
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340849"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Excel, Python 또는 R에서 Azure Databricks에 연결

이 문서에서는 Databricks ODBC 드라이버를 사용하여 Azure Databricks를 Microsoft Excel, Python 또는 R 언어와 연결하는 방법에 대해 알아봅니다. 연결이 설정되면 Excel, Python 또는 R 클라이언트에서 Azure Databricks의 데이터에 액세스할 수 있습니다. 클라이언트를 사용하여 데이터를 자세히 분석할 수도 있습니다. 

## <a name="prerequisites"></a>필수 조건

* Azure Databricks 작업 영역, Spark 클러스터 및 클러스터와 연결된 샘플 데이터가 있어야 합니다. 이러한 필수 구성 요소가 아직 없으면 [Azure Portal을 사용하여 Azure Databricks에서 Spark 작업 실행](quickstart-create-databricks-workspace-portal.md) 빠른 시작을 완료합니다.

* [Databricks 드라이버 다운로드 페이지](https://databricks.com/spark/odbc-driver-download)에서 Databricks ODBC 드라이버를 다운로드합니다. Azure Databricks에 연결하려는 애플리케이션에 따라 32비트 또는 64비트 버전을 설치합니다. 예를 들어 Excel에서 연결하려면 32비트 버전의 드라이버를 설치합니다. R 및 Python에서 연결하려면 64비트 버전의 드라이버를 설치합니다.

* Databricks에서 개인용 액세스 토큰을 설정합니다. 지침은 [토큰 관리](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)를 참조하세요.

## <a name="set-up-a-dsn"></a>DSN 설정

DSN(데이터 원본 이름)은 특정 데이터 원본에 대한 정보를 포함합니다. ODBC 드라이버에서 데이터 원본에 연결하는 데 이 DSN이 필요합니다. 이 섹션에서는 Microsoft Excel, Python 또는 R과 같은 클라이언트에서 Azure Databricks에 연결하기 위해 Databricks ODBC 드라이버에서 사용할 수 있는 DSN을 설정합니다.

1. Azure Databricks 작업 영역에서 Databricks 클러스터로 이동합니다.

    ![Databricks 클러스터 열기](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Databricks 클러스터 열기")

2. **구성** 탭 아래에서 **JDBC/ODBC** 탭을 클릭하고, **서버 호스트 이름** 및 **HTTP 경로**에 대한 값을 복사합니다. 이 문서의 단계를 완료하는 데 이러한 값이 필요합니다.

    ![Databricks 구성 가져오기](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks 구성 가져오기")

3. 컴퓨터에서 애플리케이션에 따라 **ODBC 데이터 원본** 애플리케이션(32비트 또는 64비트)을 시작합니다. Excel에서 연결하려면 32비트 버전을 사용합니다. R 및 Python에서 연결하려면 64비트 버전을 사용합니다.

    ![ODBC 시작](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC 앱 시작")

4. **사용자 DSN** 탭 아래에서 **추가**를 클릭합니다. **새 데이터 원본 만들기**  대화 상자에서 **Simba Spark ODBC 드라이버**를 선택한 다음, **마침**을 클릭합니다.

    ![ODBC 시작](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC 앱 시작")

5. **Simba Spark ODBC 드라이버** 대화 상자에서 다음 값을 제공합니다.

    ![DSN 구성](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN 구성")

    다음 표에서는 대화 상자에서 제공하는 값에 대해 설명합니다.
    
    |필드  | 값  |
    |---------|---------|
    |**데이터 원본 이름**     | 데이터 원본의 이름을 제공합니다.        |
    |**호스트**     | *서버 호스트 이름*으로 Databricks 작업 영역에서 복사한 값을 제공합니다.        |
    |**포트**     | *443*을 입력합니다.        |
    |**인증** > **메커니즘**     | *사용자 이름 및 암호*를 선택합니다.        |
    |**사용자 이름**     | *token*을 입력합니다.        |
    |**암호**     | Databricks 작업 영역에서 복사한 토큰 값을 입력합니다. |
    
    DSN 설정 대화 상자에서 다음과 같은 추가 단계를 수행합니다.
    
    * **HTTP 옵션**을 클릭합니다. 열리는 대화 상자에서 Databricks 작업 영역에서 복사한 *HTTP 경로*에 대한 값을 붙여넣습니다. **확인**을 클릭합니다.
    * **SSL 옵션**을 클릭합니다. 열리는 대화 상자에서 **SSL 사용** 확인란을 선택합니다. **확인**을 클릭합니다.
    * **테스트**를 클릭하여 Azure Databricks에 대한 연결을 테스트합니다. **확인**을 클릭하여 구성을 저장합니다.
    * **ODBC 데이터 원본 관리자** 대화 상자에서 **확인**을 클릭합니다.

이제 DSN을 설정해야 합니다. 다음 섹션에서는 이 DSN을 사용하여 Excel, Python 또는 R에서 Azure Databricks에 연결합니다.

## <a name="connect-from-microsoft-excel"></a>Microsoft Excel에서 연결

이 섹션에서는 앞에서 만든 DSN을 사용하여 Azure Databricks의 데이터를 Microsoft Excel로 가져옵니다. 시작하기 전에 Microsoft Excel이 컴퓨터에 설치되어 있는지 확인합니다. [Microsoft Excel 평가판 링크](https://products.office.com/excel)에서 평가판 버전의 Excel을 사용할 수 있습니다.

1. Microsoft Excel에서 빈 통합 문서를 엽니다. **데이터** 리본에서 **데이터 가져오기**를 클릭합니다. **기타 원본**을 클릭한 다음, **ODBC**를 클릭합니다.

    ![Excel에서 ODBC 시작](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Excel에서 ODBC 시작")

2. **ODBC** 대화 상자에서 앞에서 만든 DSN을 선택한 다음, **확인**을 클릭합니다.

    ![DSN 선택](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN 선택")

3. 자격 증명을 묻는 메시지가 표시되면 사용자 이름에 대한 **토큰**을 입력합니다. 암호의 경우 Databricks 작업 영역에서 검색한 토큰 값을 제공합니다.

    ![Databricks에 대한 자격 증명 제공](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN 선택")

4. 탐색기 창에서 Excel에 로드하려는 테이블을 Databricks로 선택한 다음, **로드**를 클릭합니다. 

    ![Excel에 데이터 로드](./media/connect-databricks-excel-python-r/excel-load-data.png "Excel에 데이터 로드")

Excel 통합 문서에 데이터가 있으면 분석 작업을 수행할 수 있습니다.

## <a name="connect-from-r"></a>R에서 연결

> [!NOTE]
> 이 섹션에서는 Azure Databricks를 사용하여 데스크톱에서 실행되는 R Studio 클라이언트를 통합하는 방법에 대해 설명합니다. Azure Databricks 클러스터에서 R Studio를 사용하는 방법에 대한 지침은 [Azure Databricks의 R Studio](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html)를 참조하세요.

이 섹션에서는 R 언어 IDE를 사용하여 Azure Databricks에서 사용할 수 있는 데이터를 참조합니다. 시작하기 전에 컴퓨터에 다음을 설치해야 합니다.

* R 언어에 대한 IDE입니다. 이 문서에서는 데스크톱용 RStudio를 사용합니다. [R Studio 다운로드](https://www.rstudio.com/products/rstudio/download/)에서 설치할 수 있습니다.
* 데스크톱용 RStudio를 IDE로 사용하는 경우 [https://aka.ms/rclient/](https://aka.ms/rclient/)에서 Microsoft R Client도 설치합니다. 

RStudio를 열고 다음 단계를 수행합니다.

- `RODBC` 패키지를 참조합니다. 이렇게 하면 앞에서 만든 DSN을 사용하여 Azure Databricks에 연결하도록 설정할 수 있습니다.
- DSN을 사용하여 연결을 설정합니다.
- Azure Databricks에서 데이터에 대한 SQL 쿼리를 실행합니다. 다음 코드 조각에서 *radio_sample_data*는 Azure Databricks에 이미 있는 테이블입니다.
- 쿼리에서 몇 가지 작업을 수행하여 출력을 확인합니다. 

다음 코드 조각에서는 이러한 작업을 수행합니다.

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Python에서 연결

이 섹션에서는 Python IDE(예: IDLE)를 사용하여 Azure Databricks에서 사용할 수 있는 데이터를 참조합니다. 시작하기 전에 다음 필수 조건을 완료합니다.

* [여기](https://www.python.org/downloads/)에서 Python을 설치합니다. 이 링크에서 Python을 설치하면 IDLE도 설치됩니다.

* 컴퓨터의 명령 프롬프트에서 `pyodbc` 패키지를 설치합니다. 다음 명령 실행:

      pip install pyodbc

IDLE을 열고 다음 단계를 수행합니다.

- `pyodbc` 패키지를 가져옵니다. 이렇게 하면 앞에서 만든 DSN을 사용하여 Azure Databricks에 연결하도록 설정할 수 있습니다.
- 앞에서 만든 DSN을 사용하여 연결을 설정합니다.
-  만든 연결을 사용하여 SQL 쿼리를 실행합니다. 다음 코드 조각에서 *radio_sample_data*는 Azure Databricks에 이미 있는 테이블입니다.
- 쿼리에서 작업을 수행하여 출력을 확인합니다.

다음 코드 조각에서는 이러한 작업을 수행합니다.

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>다음 단계

* Azure Databricks에 데이터를 가져올 수 있는 원본에 대해 알아보려면 [Azure Databricks용 데이터 원본](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)을 참조하세요.


