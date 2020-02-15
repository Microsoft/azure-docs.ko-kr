---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: e7717cdeec33a32f926cb5ef4c1f6d28c25f9d93
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989090"
---
## <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기

이 섹션에서는 자체 호스팅 Integration Runtime을 만들고 이를 Microsoft SQL Server 데이터베이스와 함께 온-프레미스 컴퓨터에 연결합니다. 자체 호스팅된 통합 런타임은 머신의 SQL Server에서 Azure SQL 데이터베이스로 데이터를 복사하는 구성 요소입니다. 

1. 통합 런타임의 이름에 대한 변수를 만듭니다. 고유한 이름을 사용하고 기록해 둡니다. 이 자습서의 뒷부분에서 사용합니다. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. 자체 호스팅 통합 런타임을 만듭니다. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   샘플 출력은 다음과 같습니다.

   ```json
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. 만든 통합 런타임의 상태를 검색하려면 다음 명령을 실행합니다. **State** 속성의 값이 **NeedRegistration**으로 설정되어 있는지 확인합니다. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   샘플 출력은 다음과 같습니다.

   ```json  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. 자체 호스팅 통합 런타임을 클라우드의 Azure Data Factory 서비스에 등록하는 데 사용되는 인증 키를 검색하려면 다음 명령을 실행합니다. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   샘플 출력은 다음과 같습니다.

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. 다음 단계에서 컴퓨터에 설치할 자체 호스팅된 통합 런타임을 등록하는 데 사용되는 키 중 하나를 복사합니다(큰 따옴표 제외).  

## <a name="install-the-integration-runtime-tool"></a>통합 런타임 도구 설치

1. 컴퓨터에 통합 런타임이 이미 있는 경우 **프로그램 추가/제거**를 사용하여 제거합니다. 

2. 로컬 Windows 컴퓨터에 자체 호스팅된 통합 런타임을 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)합니다. 설치를 실행합니다.

3. **Microsoft Integration Runtime 설치 시작** 페이지에서 **다음**을 선택합니다.

4. **최종 사용자 사용권 계약** 페이지에서 사용권 계약에 동의하고 **다음**을 클릭합니다.

5. **대상 폴더** 페이지에서 **다음**을 선택합니다.

6. **Microsoft Integration Runtime을 설치할 준비가 됨** 페이지에서 **설치**를 선택합니다.

7. **Microsoft Integration Runtime 설치 완료** 페이지에서 **마침**을 선택합니다.

8. **통합 런타임(자체 호스팅) 등록** 페이지에 이전 섹션에서 저장한 키를 붙여넣고 **등록**을 선택합니다. 

    ![통합 런타임 등록](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. **새 통합 런타임(자체 호스팅) 노드** 페이지에서 **마침**을 선택합니다. 

10. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 메시지가 표시됩니다.

    ![성공적으로 등록되었습니다.](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. **통합 런타임(자체 호스팅) 등록** 페이지에서 **구성 관리자 시작**을 선택합니다.

15. 노드가 클라우드 서비스에 연결되면 다음 페이지가 표시됩니다.

    ![노드가 페이지에 연결됨](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. 이제, Microsoft SQL Server 데이터베이스에 대한 연결을 테스트합니다.

    ![진단 탭](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. **구성 관리자** 페이지에서 **진단** 탭으로 이동합니다.

    b. 데이터 원본 유형에 대한 **SqlServer**를 선택합니다.

    다. 서버 이름을 입력합니다.

    d. 데이터베이스 이름을 입력합니다.

    e. 인증 모드를 선택합니다.

    f. 사용자 이름을 입력합니다.

    g. 사용자 이름과 연결된 암호를 입력합니다.

    h. 통합 런타임을 SQL Server에 연결할 수 있는지 확인하려면 **테스트**를 선택합니다. 연결이 성공하면 녹색 확인 표시가 나타납니다. 연결이 실패하면 오류 메시지가 나타납니다. 모든 문제를 해결하고 통합 런타임을 SQL Server에 연결할 수 있는지 확인합니다.    

    > [!NOTE]
    > 인증 유형, 서버, 데이터베이스, 사용자 및 암호의 값을 기록해둡니다. 이 자습서의 뒷부분에서 사용됩니다.
