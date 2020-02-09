---
title: Azure Data Lake Storage Gen1에 대 한 Azure Stream Analytics 인증
description: 이 문서에서는 관리 ID를 사용하여 Azure Data Lake Storage Gen1 출력에 대해 Azure Stream Analytics 작업을 인증하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934989"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>관리 id를 사용 하 여 Azure Data Lake Storage Gen1에 대 한 Stream Analytics 인증

Azure Stream Analytics는 ADLS(Azure Data Lake Storage) Gen1 출력을 사용하여 관리 ID 인증을 지원합니다. ID는 특정 Stream Analytics 작업을 표시하는 Azure Active Directory에 등록된 관리형 애플리케이션이며, 대상 리소스에 대해 인증하는 데 사용될 수 있습니다. 관리형 ID는 암호가 변경되거나 90일마다 사용자 토큰이 만료될 때 재인증해야 하는 필요와 같이 사용자 기반 인증 방법이 가진 제한을 없앱니다. 또한 관리 ID는 Azure Data Lake Storage Gen1을 출력되는 Stream Analytics 작업 배포의 자동화에 도움이 됩니다.

이 문서에서는 Azure Portal, Azure Resource Manager 템플릿 배포 및 Azure Stream Analytics Tools for Visual Studio를 통해 Azure Data Lake Storage Gen1로 출력되는 Azure Stream Analytics 작업에 대해 관리 ID를 사용하도록 설정하기 위한 세 가지 방법을 보여 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. 새 Stream Analytics 작업을 만들거나 Azure Portal에서 기존 작업을 열어 시작합니다. 화면 왼쪽에 있는 메뉴 모음에서 **구성**아래에 있는 **관리 되는 id** 를 선택 합니다.

   ![관리 id Stream Analytics 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. 오른쪽에 표시 되는 창에서 **시스템 할당 관리 Id 사용** 을 선택 합니다. Azure Active Directory에서 Stream Analytics 작업의 id를 확인 하려면 서비스 사용자에 게 **저장** 을 클릭 합니다. 새로 만든 ID의 수명 주기는 Azure에서 관리합니다. Stream Analytics 작업을 삭제하면 연결된 ID(즉, 서비스 주체)는 Azure에서 자동으로 삭제합니다.

   구성이 저장되는 경우 서비스 주체의 OID(개체 ID)는 아래와 같이 보안 주체 ID로 나열됩니다.

   ![Stream Analytics 서비스 주체 ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   서비스 주체에는 Stream Analytics 작업과 동일한 이름이 사용됩니다. 예를 들어 작업 이름이 **MyASAJob**인 경우 만든 서비스 주체 이름도 **MyASAJob**이 됩니다.

3. ADLS Gen1 출력 싱크의 출력 속성 창에서 인증 모드 드롭다운을 클릭 하 고 * * 관리 Id * *를 선택 합니다.

4. 나머지 속성을 입력합니다. ADLS 출력을 만드는 방법에 대한 자세한 내용은 [Stream Analytics를 사용하여 Data Lake Store 출력 만들기](../data-lake-store/data-lake-store-stream-analytics.md)를 참조하세요. 작업을 마쳤으면 **저장**을 클릭합니다.

   ![Azure Data Lake Storage 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. ADLS Gen1 개요 페이지로 이동하여 **데이터 탐색기**를 클릭합니다.

   ![Data Lake Storage 개요 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. 데이터 탐색기 창에서 **액세스**를 선택하고 액세스 창에서 **추가**를 클릭합니다.

   ![Data Lake Storage 액세스 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. **사용자 또는 그룹 선택** 창의 텍스트 상자에 서비스 주체의 이름을 입력합니다. 서비스 주체의 이름은 해당 Stream Analytics 작업의 이름이기도 합니다. 주체 이름을 입력하면 해당 이름이 텍스트 상자 아래에 표시됩니다. 원하는 서비스 주체 이름을 선택하고 **선택**을 클릭합니다.

   ![서비스 주체 이름 선택](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. **사용 권한** 창에서 **쓰기** 및 **실행** 권한을 확인하고 **이 폴더 및 모든 하위 폴더**에 할당합니다. 그런 다음, **확인**을 클릭합니다.

   ![쓰기 선택 및 권한 실행](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. 서비스 주체는 아래와 같이 **액세스** 창의 **할당된 권한** 아래에 나열됩니다. 이제 다시 돌아가서 Stream Analytics 작업을 시작할 수 있습니다.

   ![포털의 Stream Analytics 액세스 목록](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Data Lake Storage Gen1 파일 시스템 사용 권한에 대한 자세한 내용은 [Azure Data Lake Storage Gen1의 Access Control](../data-lake-store/data-lake-store-access-control.md)을 참조하세요.

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics Tools for Visual Studio

1. JobConfig.json에서 **시스템 할당 ID 사용**을 **True**로 설정합니다.

   ![Stream Analytics 작업 구성 관리 ID](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. ADLS Gen1 출력 싱크의 출력 속성 창에서 인증 모드 드롭다운을 클릭 하 고 * * 관리 Id * *를 선택 합니다.

   ![ADLS 출력 관리 ID](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. 나머지 속성을 입력하고 **저장**을 클릭합니다.

4. 쿼리 편집기에서 **Azure에 제출**을 클릭합니다.

   작업을 제출할 때 이 도구는 다음 두 가지 작업을 수행합니다.

   * Azure Active Directory에서 Stream Analytics 작업의 ID에 대한 서비스 주체를 자동으로 만듭니다. 새로 만든 ID의 수명 주기는 Azure에서 관리합니다. Stream Analytics 작업을 삭제하면 연결된 ID(즉, 서비스 주체)는 Azure에서 자동으로 삭제합니다.

   * 작업에 사용되는 ADLS Gen1에 대해 **쓰기** 및 **실행** 권한을 자동으로 설정하고 이 폴더 및 모든 하위 폴더에 할당합니다.

5. 빌드 컴퓨터에서 [Stream Analytics CI.CD Nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 버전 1.5.0 이상(Visual Studio 외부에 있음)을 사용하여 다음 속성을 갖는 Resource Manager 템플릿을 생성할 수 있습니다. 다음 섹션의 Resource Manager 템플릿 배포 단계에 따라 서비스 주체를 가져오고 PowerShell을 통해 서비스 주체에 액세스 권한을 부여합니다.

## <a name="resource-manager-template-deployment"></a>Resource Manager 템플릿 배포

1. Resource Manager 템플릿의 리소스 섹션에서 다음 속성을 포함하여 관리 ID를 통해 *Microsoft.StreamAnalytics/streamingjobs* 속성을 만들 수 있습니다.

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   이 속성을 통해 Azure Resource Manager에서 Azure Stream Analytics 작업에 대한 ID를 만들어서 관리합니다.

   **샘플 작업**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **샘플 작업 응답**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   필요한 ADLS 리소스에 대한 액세스 권한을 부여하려면 작업 응답에서 보안 주체 ID를 기록해 둡니다.

   **테넌트 ID**는 서비스 주체를 만든 Azure Active Directory 테넌트의 ID입니다. 서비스 주체는 구독이 신뢰하는 Azure 테넌트에서 생성됩니다.

   **형식**은 관리 ID 형식에서 설명한 것처럼 관리 ID 형식을 나타냅니다. 시스템 할당 형식만 지원됩니다.

2. PowerShell을 사용하여 서비스 주체에 대한 액세스 권한을 제공합니다. PowerShell을 통해 서비스 주체에 액세스하려면 다음 명령을 실행합니다.

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId**는 서비스 주체의 개체 ID로서 서비스 주체를 만들면 포털 화면에 나열됩니다. Resource Manager 템플릿 배포를 사용하여 작업을 만든 경우 개체 ID는 작업 응답의 ID 속성에 나열됩니다.

   **예제**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   위의 PowerShell 명령에 대 한 자세한 내용은 [AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) 설명서를 참조 하세요.

## <a name="limitations"></a>제한 사항
이 기능은 다음을 지원 하지 않습니다.

1. **다중 테넌트 액세스**: 지정 된 Stream Analytics 작업에 대해 만든 서비스 주체는 작업이 만들어진 Azure Active Directory 테넌트에 상주할 수 있으며 다른 Azure Active Directory에 있는 리소스에 대해 사용할 수 없습니다. 넌. 따라서 Azure Stream Analytics 작업과 동일한 Azure Active Directory 테넌트 내에 있는 ADLS Gen 1 리소스 에서만 MSI를 사용할 수 있습니다. 

2. **[사용자 할당 id](../active-directory/managed-identities-azure-resources/overview.md)** :은 지원 되지 않습니다. 즉, 사용자는 자신의 Stream Analytics 작업에서 사용할 자신의 서비스 사용자를 입력할 수 없습니다. 서비스 사용자는 Azure Stream Analytics에서 생성 됩니다.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics를 사용하여 Data Lake Store 만들기](../data-lake-store/data-lake-store-stream-analytics.md)
* [Visual Studio를 사용하여 로컬로 Stream Analytics 쿼리 테스트](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 
