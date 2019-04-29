---
title: 데이터 이동 - 데이터 관리 게이트웨이 | Microsoft Docs
description: 데이터 게이트웨이를 설정하여 온-프레미스와 클라우드 간에 데이터를 이동합니다. Azure Data Factory의 데이터 관리 게이트웨이를 사용하여 데이터를 이동합니다.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4eb881992b7e40e0a9d67bd2cee94f1f09958e9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825985"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>온-프레미스 원본과 클라우드 간에 데이터 관리 게이트웨이로 데이터 이동
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory를 사용하여 온-프레미스 및 클라우드 간에 데이터 복사](../tutorial-hybrid-copy-powershell.md)를 참조하세요.

이 문서에서는 Data Factory를 사용하여 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터 통합의 개요를 제공합니다. [데이터 이동 활동](data-factory-data-movement-activities.md) 문서 및 [데이터 세트](data-factory-create-datasets.md)와 [파이프라인](data-factory-create-pipelines.md)과 같은 기타 Data Factory 핵심 개념 문서를 작성합니다.

## <a name="data-management-gateway"></a>데이터 관리 게이트웨이
온-프레미스 데이터 저장소 간에 데이터 이동을 사용할 수 있도록 온-프레미스 컴퓨터에 데이터 관리 게이트웨이를 설치해야 합니다. 게이트웨이가 데이터 저장소에 연결할 수 있는 한 데이터 저장소와 동일한 컴퓨터 또는 다른 컴퓨터에 게이트웨이를 설치할 수 있습니다.

> [!IMPORTANT]
> 데이터 관리 게이트웨이에 대한 세부 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요. 

다음 연습에서는 온-프레미스 **SQL Server** 데이터베이스에서 Azure Blob Storage로 데이터를 이동하는 파이프라인을 사용하여 데이터 팩터리를 만드는 방법을 보여 줍니다. 자습서의 일부로 컴퓨터에 데이터 관리 게이트웨이를 설치하고 구성합니다.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>연습: 클라우드에 온-프레미스 데이터 복사
이 연습에서는 다음 단계를 수행합니다. 

1. 데이터 팩터리를 만듭니다.
2. 데이터 관리 게이트웨이를 만듭니다. 
3. 원본 및 싱크 데이터 저장소에 대한 연결된 서비스를 만듭니다.
4. 입력 및 출력 데이터를 나타낼 데이터 세트를 만듭니다.
5. 데이터를 이동하는 복사 작업으로 파이프라인을 만듭니다.

## <a name="prerequisites-for-the-tutorial"></a>자습서의 필수 조건
이 연습을 시작하기 전에 다음 필수 조건이 있어야 합니다.

* **Azure 구독**.  구독이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 문서를 참조하세요.
* **Azure Storage 계정**. 이 자습서에서는 Blob Storage를 **대상/싱크** 데이터 저장소로 사용합니다. Azure Storage 계정이 없는 경우 새로 만드는 단계는 [스토리지 계정 만들기](../../storage/common/storage-quickstart-create-account.md) 문서를 참조하세요.
* **SQL Server**. 이 자습서에서는 온-프레미스 SQL Server 데이터베이스를 **원본** 데이터 저장소로 사용합니다. 

## <a name="create-data-factory"></a>데이터 팩터리 만들기
이 단계에서는 Azure Portal을 사용하여 **ADFTutorialOnPremDF**라는 Azure Data Factory 인스턴스를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 만들기**를 클릭하고 **인텔리전스 + 분석** 및 **데이터 팩터리**를 차례로 클릭합니다.

   ![새로 만들기->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. **새 데이터 팩터리** 페이지에서 이름으로 **ADFTutorialOnPremDF**를 입력합니다.

    ![시작 보드에 추가](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Azure Data Factory 이름은 전역적으로 고유해야 합니다. 만약 **데이터 팩터리 이름 “ADFTutorialOnPremDF”를 사용할 수 없습니다.** 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialOnPremDF) 다시 만드세요. 이 자습서의 나머지 단계를 수행하는 동안 ADFTutorialOnPremDF 대신에 이 이름을 사용합니다.
   >
   > 데이터 팩터리의 이름은 나중에 **DNS** 이름으로 등록되므로 공개적으로 표시될 수도 있습니다.
   >
   >
4. 데이터 팩터리를 만들려는 위치의 **Azure 구독** 을 선택합니다.
5. 기존 **리소스 그룹** 을 선택하거나 리소스 그룹을 만듭니다. 이 자습서의 경우 **ADFTutorialResourceGroup**이라는 이름의 리소스 그룹을 만듭니다.
6. **새 데이터 팩터리** 페이지에서 **만들기**를 클릭합니다.

   > [!IMPORTANT]
   > 데이터 팩터리 인스턴스를 만들려면 구독/리소스 그룹 수준에서 [데이터 팩터리 참여자](../../role-based-access-control/built-in-roles.md#data-factory-contributor) 역할의 구성원이어야 합니다.
   >
   >
7. 만들기가 완료되면 다음 이미지에서 보여준 대로 **데이터 팩터리** 페이지가 표시됩니다.

   ![데이터 팩터리 홈페이지](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>게이트웨이 만들기
1. **데이터 팩터리** 페이지에서 **작성자 및 배포** 타일을 클릭하여 데이터 팩터리에 대한 **편집기**를 시작합니다.

    ![작성 및 배포 타일](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Data Factory Editor의 도구 모음에서 **... 추가**를 클릭한 다음 **새 데이터 게이트웨이**를 클릭합니다. 또는 트리 보기에서 마우스 오른쪽 단추로 **데이터 게이트웨이**를 클릭하고 **새 데이터 게이트웨이**를 클릭할 수 있습니다.

   ![도구 모음의 새 데이터 게이트웨이](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. **만들기** 페이지에서 **이름**에 **adftutorialgateway**를 입력하고 **확인**을 클릭합니다.     

    ![게이트웨이 만들기 페이지](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > 이 연습에서는 하나의 노드(온-프레미스 Windows 컴퓨터)만 있는 논리 게이트웨이를 만듭니다. 여러 온-프레미스 컴퓨터를 게이트웨이와 연결하여 데이터 관리 게이트웨이의 규모를 확장할 수 있습니다. 노드에서 동시에 실행할 수 있는 데이터 이동 작업의 수를 늘려 강화할 수 있습니다. 이 기능은 단일 노드가 있는 논리 게이트웨이에서도 사용할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 데이터 관리 게이트웨이 확장](data-factory-data-management-gateway-high-availability-scalability.md) 문서를 참조하세요.  
4. **구성** 페이지에서 **이 컴퓨터에 직접 설치**를 클릭합니다. 이렇게 하면 컴퓨터에 게이트웨이의 설치 패키지가 다운로드되고 게이트웨이가 설치, 구성 및 등록됩니다.  

   > [!NOTE]
   > Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저를 사용합니다.
   >
   > Chrome을 사용하는 경우 [Chrome 웹 스토어](https://chrome.google.com/webstore/)로 이동하여 "ClickOnce" 키워드로 검색하고 ClickOnce 확장 중 하나를 선택해 설치합니다.
   >
   > Firefox의 경우에도 동일한 작업을 수행합니다(추가 기능 설치). 도구 모음(상단 오른쪽 모서리의 **가로 줄 세 개**)의 **열기 메뉴** 단추를 클릭하고 **추가 기능**을 클릭하고 "ClickOnce" 키워드로 검색하고 ClickOnce 확장 중 하나를 선택하고 설치합니다.    
   >
   >

    ![게이트웨이 - 구성 페이지](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    이 방법은 하나의 단계로 게이트웨이를 다운로드, 설치, 구성 및 등록하는 가장 쉬운 방법(한 번 클릭)입니다. **Microsoft 데이터 관리 게이트웨이 구성 관리자** 애플리케이션이 컴퓨터에 설치된 것을 확인할 수 있습니다. **ConfigManager.exe**라는 실행 파일이 다음 폴더에 있는 것도 확인할 수 있습니다. **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    또한 이 페이지에서 링크를 사용하여 게이트웨이를 수동으로 다운로드하여 설치하고 **새 키** 텍스트 상자에 표시된 키를 사용하여 등록할 수도 있습니다.

    게이트웨이에 대한 모든 세부 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요.

   > [!NOTE]
   > 데이터 관리 게이트웨이를 성공적으로 설치 및 구성하려면 로컬 컴퓨터의 관리자여야 합니다. **데이터 관리 게이트웨이 사용자** 로컬 Windows 그룹에 사용자를 더 추가할 수 있습니다. 이 그룹의 구성원은 데이터 관리 게이트웨이 구성 관리자 도구를 사용하여 게이트웨이를 구성할 수 있습니다.
   >
   >
5. 몇 분 정도 기다리거나 다음과 같은 알림 메시지가 나타날 때까지 기다립니다.

    ![성공적인 게이트웨이 설치](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. 컴퓨터에서 **데이터 관리 게이트웨이 구성 관리자** 애플리케이션을 시작합니다. **Search** 창에서 **데이터 관리 게이트웨이**를 입력하여 이 유틸리티에 액세스합니다. **ConfigManager.exe**라는 실행 파일이 다음 폴더에 있는 것도 확인할 수 있습니다. **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![게이트웨이 구성 관리자](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. `adftutorialgateway is connected to the cloud service` 메시지를 확인합니다. 맨 아래 상태 표시줄에 **색 확인 표시**와 함께 **클라우드 서비스에 연결됨**이 표시됩니다.

    **홈** 탭에서 다음 작업을 수행할 수 있습니다.

   * [등록] 단추를 사용하여 Azure Portal에서 키가 있는 게이트웨이를 **등록**합니다.
   * 게이트웨이 컴퓨터에서 실행 중인 데이터 관리 게이트웨이 호스트 서비스를 **중지**합니다.
   * 하루 중 특정 시간에 **업데이트를 설치하도록 예약**합니다.
   * 게이트웨이가 **마지막으로 업데이트된 시간**을 봅니다.
   * 게이트웨이 업데이트를 설치할 수 있는 시간을 지정합니다.
8. **설정** 탭으로 전환합니다. **인증서** 섹션에 지정된 인증서는 포털에서 지정한 온-프레미스 데이터 저장소에 대한 자격 증명을 암호화/해독하는 데 사용됩니다. (선택 사항) **변경**을 클릭하여 고유한 인증서를 대신 사용합니다. 기본적으로 게이트웨이는 데이터 팩터리 서비스에서 자동으로 생성되는 인증서를 사용합니다.

    ![게이트웨이 인증서 구성](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    **설정** 탭에서 다음 작업을 수행할 수도 있습니다.

   * 게이트웨이에서 사용 중인 인증서를 보거나 내보냅니다.
   * 게이트웨이에서 사용하는 HTTPS 엔드포인트를 변경합니다.    
   * 게이트웨이에서 사용할 HTTP 프록시를 설정합니다.     
9. (선택 사항) 게이트웨이와 관련된 모든 문제를 해결하는 데 사용할 수 있는 자세한 정보 로깅을 활성화하려면 **진단** 탭으로 전환하고 **자세한 정보 로깅 사용** 옵션을 체크합니다. 로깅 정보는 **애플리케이션 및 서비스 로그** -> **데이터 관리 게이트웨이** 노드 아래의 **이벤트 뷰어**에서 찾을 수 있습니다.

    ![진단 탭](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    **진단** 탭에서 다음 작업을 수행할 수도 있습니다.

   * 게이트웨이를 사용하여 온-프레미스 데이터 원본에 대한 **연결 테스트** 섹션을 사용합니다.
   * **로그 보기**를 클릭하여 이벤트 뷰어 창에서 데이터 관리 게이트웨이 로그를 확인합니다.
   * **로그 보내기**를 클릭하여 지난 7일 간의 로그가 포함된 zip 파일을 Microsoft에 업로드하여 문제를 원활하게 해결할 수 있습니다.
10. **진단** 탭의 **연결 테스트** 섹션에서 데이터 저장소 유형으로 **SqlServer**를 선택하고, 데이터베이스 서버 이름과 데이터베이스 이름을 입력하며, 인증 유형을 지정하고, 사용자 이름과 암호를 입력하며, **테스트**를 클릭하여 게이트웨이를 데이터베이스에 연결할 수 있는지 여부를 테스트합니다.
11. 웹 브라우저로 전환하고, **Azure Portal**의 **구성** 페이지에서 **확인**을 클릭한 다음 **새 데이터 게이트웨이** 페이지에서 [확인]을 클릭합니다.
12. 왼쪽 트리의 **데이터 게이트웨이** 아래에 **adftutorialgateway**가 표시되어야 합니다.  이 항목을 클릭하면 연결된 JSON이 나타납니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기
이 단계에서는 **AzureStorageLinkedService** 및 **SqlServerLinkedService**라는 두 개의 연결된 서비스를 만듭니다. **SqlServerLinkedService**는 온-프레미스 SQL Server 데이터베이스를 연결하며 **AzureStorageLinkedService** 연결된 서비스는 Azure Blob Storage를 Data Factory에 연결합니다. 이 연습의 뒷부분에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 파이프라인을 만듭니다.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>온-프레미스 SQL Server 데이터베이스에 연결된 서비스 추가
1. **데이터 팩터리 편집기**의 도구 모음에서 **새 데이터 저장소**를 클릭하고 **SQL Server**를 선택합니다.

   ![새로운 SQL Server 연결 서비스](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. 오른쪽에 있는 **JSON 편집기**에서 다음 단계를 수행합니다.

   1. **gatewayName**에 **adftutorialgateway**를 지정합니다.    
   2. **connectionString**에서 다음 단계를 수행합니다.    

      1. **servername**의 경우 SQL Server 데이터베이스를 호스팅하는 서버의 이름을 입력합니다.
      2. **databasename**의 경우 데이터베이스의 이름을 입력합니다.
      3. 도구 모음에서 **암호화** 단추를 클릭합니다. 자격 증명 관리자 애플리케이션이 표시됩니다.

         ![자격 증명 관리자 애플리케이션](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. **자격 증명 설정** 대화 상자에서 인증 유형, 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다. 연결이 성공하면 암호화된 자격 증명이 JSON에 저장되고 대화 상자가 닫힙니다.
      5. 대화 상자가 자동으로 닫히지 않고 Azure Portal을 사용하여 해당 탭으로 돌아간 경우 이 대화 상자를 시작한 빈 브라우저 탭을 닫습니다.

         게이트웨이 컴퓨터에서 데이터 팩터리 서비스가 소유하는 인증서를 사용하여 이러한 자격 증명을 **암호화**합니다. 대신 데이터 관리 게이트웨이와 연결된 인증서를 사용하려는 경우 안전하게 자격 증명 설정을 참조하세요.    
   3. 명령 모음에서 **배포**를 클릭하여 SQL Server 연결 서비스를 배포합니다. 트리 보기에서 연결된 서비스가 표시됩니다.

      ![트리 보기에서 SQL Server에 연결된 서비스](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Azure 저장소 계정에 대한 연결된 서비스 추가
1. **데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure 저장소**를 클릭합니다.
2. **계정 이름**에 Azure 저장소 계정의 이름을 입력합니다.
3. **계정 키**에 Azure 저장소 계정의 키를 입력합니다.
4. **배포**를 클릭하여 **AzureStorageLinkedService**를 배포합니다.

## <a name="create-datasets"></a>데이터 세트 만들기
이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = &gt; Azure Blob 저장소)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 세트를 만듭니다. 데이터 세트를 만들기 전에 다음 단계를 수행합니다(목록 뒤에 자세한 단계가 나옴).

* 데이터 팩터리에 연결된 서비스로 추가한 SQL Server 데이터베이스에서 **emp**라는 테이블을 만들고 테이블에 몇 가지 샘플 항목을 삽입합니다.
* 데이터 팩터리에 연결된 서비스로 추가한 Azure Blob Storage 계정에서 **adftutorial**로 명명된 BLOB 컨테이너를 만듭니다.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>자습서에 필요한 온-프레미스 SQL Server 준비
1. 온-프레미스 SQL Server 연결된 서비스(**SqlServerLinkedService**)에 대해 지정된 데이터베이스에서, 다음 SQL 스크립트를 사용하여 데이터베이스에 **emp** 테이블을 만듭니다.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. 테이블에 일부 샘플을 삽입합니다.

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>입력 데이터 세트 만들기

1. **데이터 팩터리 편집기**의 명령 모음에서 **... 추가**, **새 데이터 세트**를 차례로 클릭하고 **SQL Server 테이블**을 클릭합니다.
2. 오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   다음 사항에 유의하세요.

   * **type**을 **SqlServerTable**로 설정합니다.
   * **tableName**을 **emp**로 설정합니다.
   * **linkedServiceName**을 **SqlServerLinkedService**(연결된 서비스는 이 연습의 앞부분에서 만들었음)로 설정합니다.
   * Azure Data Factory의 다른 파이프라인에서 생성하지 않은 입력 데이터 세트의 경우 **external**을 **true**로 설정해야 합니다. 이 섹션은 입력 데이터가 Azure Data Factory 서비스 외부에서 생성되었음을 나타냅니다. **Policy** 섹션에서 **externalData** 요소를 사용하여 외부 데이터 정책을 선택적으로 지정할 수 있습니다.    

   JSON 속성에 대한 자세한 내용은 [SQL Server 간 데이터 이동 데이터 이동](data-factory-sqlserver-connector.md)을 참조하세요.
3. 명령 모음에서 **배포** 를 클릭하여 데이터 세트를 배포합니다.  

### <a name="create-output-dataset"></a>출력 데이터 세트 만들기

1. **데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure Blob Storage**를 클릭합니다.
2. 오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   다음 사항에 유의하세요.

   * **type**을 **AzureBlob**으로 설정합니다.
   * **linkedServiceName**을 **AzureStorageLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
   * **folderPath**를 **adftutorial/outfromonpremdf**로 설정합니다. 여기서 outfromonpremdf는 adftutorial 컨테이너의 폴더입니다. 아직 없는 경우 **adftutorial** 컨테이너를 만듭니다.
   * **가용성**은 **매시간**으로 설정됩니다(**빈도**는 **매시간**으로, **간격**은 **1**로 설정).  데이터 팩터리 서비스는 Azure SQL Database의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다.

   지정 하지 않는 경우는 **fileName** 에 대 한는 **출력 테이블**, 생성 된 파일에는 **folderPath** 다음 형식으로 이름이 지정 됩니다: `Data.<Guid>.txt` (예를 들어:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   **SliceStart** 시간을 기반으로 **folderPath** 및 **fileName**을 동적으로 설정하려면 partitionedBy 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 연도, 월 및 일을 사용하고 fileName은 SliceStart의 시간을 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    JSON 속성에 대한 자세한 내용은 [Azure Blob Storage 간의 데이터 이동](data-factory-azure-blob-connector.md)을 참조하세요.
3. 명령 모음에서 **배포** 를 클릭하여 데이터 세트를 배포합니다. 트리 보기에서 두 데이터 세트가 모두 표시되는지 확인합니다.  

## <a name="create-pipeline"></a>파이프라인 만들기
이 단계에서는 **EmpOnPremSQLTable**을 입력으로, **OutputBlobTable**을 출력으로 사용하는 **복사 작업**을 포함하는 **파이프라인**을 만듭니다.

1. [데이터 팩터리 편집기]에서 **... 추가**를 클릭하고 **새 파이프라인**을 클릭합니다.
2. 오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > **시작** 속성 값을 현재 날짜로 바꾸고 **종료** 값을 다음 날짜로 바꿉니다.
   >
   >

   다음 사항에 유의하세요.

   * activities 섹션에는 **type**이 **Copy**로 설정된 작업 하나밖에 없습니다.
   * 작업에 대한 **입력**을 **EmpOnPremSQLTable**로 설정하고 작업에 대한 **출력**을 **OutputBlobTable**로 설정합니다.
   * 에 **typeProperties** 섹션인 **SqlSource** 로 지정 됩니다는 **원본 유형** 및 **BlobSink** 합니다 로지정됩니다**싱크 유형**합니다.
   * **SqlSource**의 **sqlReaderQuery** 속성에 대해 SQL 쿼리 `select * from emp`을 지정합니다.

   start 및 end 날짜/시간은 둘 다 [ISO 형식](https://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예를 들면 다음과 같습니다. 2014-10-14T16:32:41Z. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다.

   **종료** 속성 값을 지정하지 않는 경우 "**시작 + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9/9/9999**를 지정합니다.

   각 Azure Data Factory 데이터 세트에 정의된 **가용성** 속성을 기준으로 데이터 조각이 처리되는 기간을 정의하고 있습니다.

   예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.        
3. 명령 모음에서 **배포**를 클릭하여 데이터 세트를 배포합니다(테이블은 사각형 데이터 세트임). **파이프라인** 노드의 트리 보기에서 파이프라인이 표시되는지 확인합니다.  
4. 이제 **X**를 두 번 클릭하여 페이지를 닫고 **ADFTutorialOnPremDF**의 **데이터 팩터리** 페이지로 돌아갑니다.

**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 데이터 세트 및 파이프라인을 성공적으로 만들고 해당 파이프라인을 예약했습니다.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>다이어그램 뷰에서 데이터 팩터리 보기
1. **Azure Portal**에서 **ADFTutorialOnPremDF** 데이터 팩터리의 홈페이지에 있는 **다이어그램** 타일을 클릭합니다. :

    ![다이어그램 링크](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. 다음 이미지와 유사한 다이어그램이 표시됩니다.

    ![다이어그램 뷰](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    확대, 축소, 100% 확대, 크기에 맞게, 자동으로 파이프라인 및 데이터 세트 위치 지정, 계보 정보 표시(선택한 항목의 업스트림/다운스트림 항목 강조 표시)를 수행할 수 있습니다.  개체(입출력 데이터 세트 또는 파이프라인)를 두 번 클릭하면 해당 속성을 볼 수 있습니다.

## <a name="monitor-pipeline"></a>파이프라인 모니터링
이 단계에서는 Azure Portal을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 세트와 파이프라인을 모니터링할 수도 있습니다. 모니터링에 대한 자세한 내용은 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 서를 참조하세요.

1. 다이어그램에서 **EmpOnPremSQLTable**을 두 번 클릭합니다.  

    ![EmpOnPremSQLTable 조각](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. 파이프라인 기간(시작 시간에서 종료 시간까지)이 과거이기 때문에 모든 데이터 조각이 **준비** 상태에 있습니다. 또한 SQL Server 데이터베이스에 데이터를 삽입했기 때문이며 항상 데이터베이스에 데이터가 있습니다. 맨 아래의 **Problem slices(문제 조각)** 섹션에 표시되는 조각이 없는지 확인합니다. 모든 조각을 보려면 조각 목록의 맨 아래에 있는 **자세히 보기**를 클릭합니다.
3. 이제 **데이터 세트** 페이지에서 **OutputBlobTable**을 클릭합니다.

    ![OputputBlobTable 조각](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 페이지가 표시됩니다. 해당 조각에 대한 작업 실행이 표시됩니다. 일반적으로 하나의 작업 실행만 표시됩니다.  

    ![데이터 조각 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    조각이 **준비** 상태가 아닌 경우 **준비되지 않은 업스트림 슬라이스** 목록에서 [준비] 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.
5. 맨 아래 목록에서 **작업 실행**을 클릭하여 **작업 실행 세부 정보**를 표시합니다.

   ![작업 실행 세부 정보 페이지](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   처리량, 기간 및 데이터를 전송하는 데 사용하는 게이트웨이와 같은 정보가 표시됩니다.
6. **X**를 클릭하여 모든 페이지를 닫아
7. **ADFTutorialOnPremDF**의 홈페이지로 돌아갑니다.
8. (선택 사항) **파이프라인**, **ADFTutorialOnPremDF**를 차례로 클릭한 다음, 입력 데이터 세트(**Consumed**) 또는 출력 데이터 세트(**Produced**)를 드릴스루합니다.
9. [Microsoft 저장소 탐색기](https://storageexplorer.com/)와 같은 도구를 사용하여 매 시간마다 Blob/파일이 만들어졌는지 확인합니다.

   ![Azure Storage 탐색기](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>다음 단계
* 데이터 관리 게이트웨이에 대한 모든 세부 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요.
* 복사 작업을 사용하여 원본 데이터 저장소에서 싱크 데이터 저장소에 데이터로 이동하는 방법에 대해 자세히 알아보려면 [Azure Blob에서 Azure SQL로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
