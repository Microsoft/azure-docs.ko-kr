---
title: 자체 호스팅 Integration Runtime 만들기
description: 데이터 팩터리가 개인 네트워크의 데이터 저장소에 액세스할 수 있도록 하는 Azure Data Factory에서 자체 호스팅 통합 런타임을 만드는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 06/18/2019
ms.openlocfilehash: 0d04ea7d7003f274b252e057b7afced7759bfaae
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928519"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기 및 구성

IR(통합 런타임)은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 컴퓨팅 인프라입니다. IR에 대한 세부 정보는 [통합 런타임 개요](concepts-integration-runtime.md)를 참조하세요.

자체 호스팅 integration runtime은 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 작업을 실행할 수 있습니다. 또한 온-프레미스 네트워크 또는 Azure 가상 네트워크의 계산 리소스에 대해 변환 작업을 디스패치할 수 있습니다. 자체 호스팅 통합 런타임을 설치 하는 경우에는 온-프레미스 컴퓨터 또는 개인 네트워크 내의 가상 컴퓨터가 필요 합니다.  

이 문서에서는 자체 호스팅 IR을 만들고 구성 하는 방법을 설명 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 설정

자체 호스팅 통합 런타임을 만들고 설정 하려면 다음 절차를 따르십시오.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Azure PowerShell를 통해 자체 호스팅 IR 만들기

1. 이 작업에 Azure PowerShell를 사용할 수 있습니다. 다음은 예제입니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. 로컬 컴퓨터에서 자체 호스팅 통합 런타임을 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)하여 설치합니다.

3. 인증 키를 검색한 다음 해당 키를 사용하여 자체 호스팅 통합 런타임을 등록합니다. 다음은 PowerShell 예제입니다.

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Azure Data Factory UI를 통해 자체 호스팅 IR 만들기

Azure Data Factory UI를 사용 하 여 자체 호스팅 IR을 만들려면 다음 단계를 사용 합니다.

1. Azure Data Factory UI의 **시작** 페이지에서 맨 왼쪽 창의 **작성자** 탭을 선택 합니다.

   ![홈 페이지 작성자 단추](media/doc-common-process/get-started-page-author-button.png)

1. 왼쪽 창의 맨 아래에서 **연결** 을 선택 하 고 **연결** 창에서 **통합 런타임** 을 선택 합니다. **+ 새로 만들기**를 선택 합니다.

   ![Integration Runtime 만들기](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. **Integration runtime 설정** 창에서 **데이터 이동 수행 및 외부 계산에 대한 활동 디스패치**를 선택 하 고 **계속**을 선택 합니다.

1. IR의 이름을 입력 하 고 **만들기**를 선택 합니다.

1. **옵션 1** 아래의 링크를 선택 하 여 컴퓨터에서 빠른 설치를 엽니다. 또는 **옵션 2** 의 단계에 따라 수동으로 설정 합니다. 다음 지침은 수동 설치를 기반으로 합니다.

   ![통합 런타임 설정](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. 인증 키를 복사 하 여 붙여넣습니다. **Integration Runtime 다운로드 및 설치를**선택 합니다.

    1. 로컬 Windows 컴퓨터에서 자체 호스팅 통합 런타임을 다운로드 합니다. 설치 관리자를 실행합니다.

    1. **Integration Runtime(자체 호스팅) 등록** 페이지에서 이전에 저장 한 키를 붙여넣고 **등록**을 선택 합니다.
    
       ![통합 런타임 등록](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. **새 통합 런타임(자체 호스팅) 노드** 페이지에서 **마침**을 선택합니다.

1. 자체 호스팅 통합 런타임이 성공적으로 등록 되 면 다음 창이 표시 됩니다.

    ![성공적인 등록](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 통해 Azure VM에서 자체 호스팅 IR 설정

Azure 가상 컴퓨터에서 자체 호스트 ir 설치를 자동화 하려면 [자체 호스트 ir 템플릿 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)를 사용 합니다. 이 템플릿은 Azure virtual network 내에서 완전히 작동 하는 자체 호스팅 IR을 제공 하는 쉬운 방법을 제공 합니다. IR은 노드 수를 2 이상으로 설정 하는 경우 고가용성 및 확장성 기능을 제공 합니다.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>로컬 PowerShell을 통해 기존 자체 호스팅 IR 설정

명령줄을 사용 하 여 기존 자체 호스팅 IR을 설정 하거나 관리할 수 있습니다. 이 사용은 특히 자체 호스팅 IR 노드의 설치 및 등록을 자동화 하는 데 도움이 될 수 있습니다.

Dmgcmd는 자체 호스팅 설치 관리자에 포함 되어 있습니다. 일반적으로 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ 폴더에 있습니다. 이 응용 프로그램은 다양 한 매개 변수를 지원 하며, 자동화를 위해 일괄 처리 스크립트를 사용 하 여 명령줄을 통해 호출할 수 있습니다.

다음과 같이 응용 프로그램을 사용 합니다.

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

응용 프로그램의 매개 변수 및 속성에 대한 자세한 내용은 다음과 같습니다. 

| 자산                                                    | 설명                                                  | 필수 |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode** "`<AuthenticationKey>`"                     | 지정 된 인증 키를 사용 하 여 자체 호스팅 integration runtime 노드를 등록 합니다. | 아닙니다.       |
| **RegisterNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | 지정 된 인증 키와 노드 이름을 사용 하 여 자체 호스팅 integration runtime 노드를 등록 합니다. | 아닙니다.       |
| **Enableremoteaccess** "`<port>`" ["`<thumbprint>`"]            | 현재 노드에서 원격 액세스를 사용 하도록 설정 하 여 고가용성 클러스터를 설정 합니다. 또는 Azure Data Factory를 거치지 않고 자체 호스팅 IR에 대해 직접 자격 증명을 설정할 수 있습니다. 후자는 동일한 네트워크에 있는 원격 컴퓨터에서 **AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet을 사용 하 여 수행 합니다. | 아닙니다.       |
| **Enableremoteaccessincontainer** "`<port>`" ["`<thumbprint>`"] | 노드가 컨테이너에서 실행 될 때 현재 노드에 대한 원격 액세스를 사용 하도록 설정 합니다. | 아닙니다.       |
| **DisableRemoteAccess**                                         | 현재 노드에 대한 원격 액세스를 사용 하지 않도록 설정 합니다. 다중 노드 설정에는 원격 액세스가 필요 합니다. **AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet은 원격 액세스가 사용 하지 않도록 설정 된 경우에도 계속 작동 합니다. 이 동작은 cmdlet이 자체 호스팅 IR 노드와 동일한 컴퓨터에서 실행 되는 경우에만 적용 됩니다. | 아닙니다.       |
| **키** "`<AuthenticationKey>`"                                 | 이전 인증 키를 덮어쓰거나 업데이트 합니다. 이 작업에 주의 하세요. 이전 자체 호스팅 IR 노드는 키가 새 통합 런타임 인 경우 오프 라인으로 전환할 수 있습니다. | 아닙니다.       |
| **Generatebackupfile** "`<filePath>`" "`<password>`"            | 현재 노드에 대한 백업 파일을 생성 합니다. 백업 파일에는 노드 키와 데이터 저장소 자격 증명이 포함 됩니다. | 아닙니다.       |
| **Importbackupfile** "`<filePath>`" "`<password>`"              | 백업 파일에서 노드를 복원 합니다.                          | 아닙니다.       |
| **다시 시작**                                                     | 자체 호스팅 integration runtime 호스트 서비스를 다시 시작 합니다.   | 아닙니다.       |
| **시작**                                                       | 자체 호스팅 integration runtime 호스트 서비스를 시작 합니다.     | 아닙니다.       |
| **중지**                                                        | 자체 호스팅 integration runtime 호스트 서비스를 중지 합니다.        | 아닙니다.       |
| **StartUpgradeService**                                         | 자체 호스팅 integration runtime 업그레이드 서비스를 시작 합니다.       | 아닙니다.       |
| **StopUpgradeService**                                          | 자체 호스팅 integration runtime 업그레이드 서비스를 중지 합니다.        | 아닙니다.       |
| **원래 비 자동 업데이트**                                            | 자체 호스팅 integration runtime 자동 업데이트를 설정 합니다.        | 아닙니다.       |
| **TurnOffAutoUpdate**                                           | 자체 호스팅 integration runtime 자동 업데이트를 해제 합니다.       | 아닙니다.       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | 새 계정으로 실행 되도록 DIAHostService를 설정 합니다. 시스템 계정 및 가상 계정에는 빈 암호 ""를 사용 합니다. | 아닙니다.       |


## <a name="command-flow-and-data-flow"></a>명령 흐름 및 데이터 흐름

온-프레미스와 클라우드 간에 데이터를 이동 하는 경우 활동은 자체 호스팅 통합 런타임을 사용 하 여 온-프레미스 데이터 원본과 클라우드 간에 데이터를 전송 합니다.

다음은 자체 호스팅 IR을 사용 하 여 복사 하는 데이터 흐름 단계를 간략하게 요약 한 것입니다.

![데이터 흐름에 대한 개략적인 개요](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. 데이터 개발자는 PowerShell cmdlet을 사용 하 여 Azure data factory 내에서 자체 호스팅 통합 런타임을 만듭니다. 현재 Azure Portal이 기능을 지원 하지 않습니다.
1. 데이터 개발자가 온-프레미스 데이터 저장소에 대한 연결 된 서비스를 만듭니다. 개발자는 서비스에서 데이터 저장소에 연결 하는 데 사용 해야 하는 자체 호스팅 통합 런타임 인스턴스를 지정 하 여이를 수행 합니다.
1. 자체 호스팅 통합 런타임 노드가 Windows DPAPI(데이터 보호 응용 프로그래밍 인터페이스)를 사용하여 자격 증명을 암호화하고 로컬에 저장합니다. 고가용성을 위해 여러 노드가 설정된 경우 자격 증명이 다른 노드 간에 동기화됩니다. 각 노드는 DPAPI를 사용하여 자격 증명을 암호화하고 로컬에 저장합니다. 자격 증명 동기화는 데이터 개발자에게는 표시되지 않으며, 자체 호스팅 IR에서 처리됩니다.
1. Azure Data Factory은 자체 호스팅 통합 런타임과 통신 하 여 작업을 예약 하 고 관리 합니다. 통신은 공유 [Azure Service Bus 릴레이](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) 연결을 사용 하는 컨트롤 채널을 통해 전달 됩니다. 활동 작업을 실행 해야 하는 경우 Data Factory는 자격 증명 정보와 함께 요청을 큐에 대기 시킵니다. 이는 자격 증명이 자체 호스팅 통합 런타임에 아직 저장 되지 않은 경우에 발생 합니다. 자체 호스팅 통합 런타임은 큐를 폴링한 후 작업을 시작 합니다.
1. 자체 호스팅 통합 런타임은 온-프레미스 저장소와 클라우드 저장소 간에 데이터를 복사 합니다. 복사 방향은 데이터 파이프라인에서 복사 작업을 구성 하는 방법에 따라 달라 집니다. 이 단계에서 자체 호스팅 통합 런타임은 보안 HTTPS 채널을 통해 Azure Blob 저장소와 같은 클라우드 기반 저장소 서비스와 직접 통신 합니다.

## <a name="considerations-for-using-a-self-hosted-ir"></a>자체 호스팅 IR 사용을 위한 고려 사항

- 단일 자체 호스팅 통합 런타임을 사용 하 여 여러 온-프레미스 데이터 원본에 사용할 수 있습니다. 동일한 Azure Active Directory (Azure AD) 테넌트 내에서 다른 데이터 팩터리에 공유할 수도 있습니다. 자세한 내용은 [자체 호스팅 통합 런타임 공유](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)를 참조하세요.
- 단일 컴퓨터에 자체 호스팅 통합 런타임의 인스턴스를 하나만 설치할 수 있습니다. 온-프레미스 데이터 원본에 액세스 해야 하는 두 개의 데이터 팩터리가 있는 경우 자체 호스팅 ir [공유 기능](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) 을 사용 하 여 자체 호스팅 ir을 공유 하거나 각 데이터 팩터리에 대해 하나씩, 두 개의 온-프레미스 컴퓨터에 자체 호스팅 ir을 설치 합니다.  
- 자체 호스팅 통합 런타임은 데이터 원본과 동일한 컴퓨터에 있을 필요가 없습니다. 그러나 자체 호스팅 통합 런타임이 데이터 원본에 가까이 있으면 자체 호스팅 통합 런타임이 데이터 원본에 연결 하는 데 걸리는 시간을 줄일 수 있습니다. 온-프레미스 데이터 원본을 호스팅하는 컴퓨터와 다른 컴퓨터에 자체 호스팅 통합 런타임을 설치 하는 것이 좋습니다. 자체 호스팅 통합 런타임 및 데이터 원본이 서로 다른 컴퓨터에 있는 경우 자체 호스팅 통합 런타임은 리소스의 데이터 원본과 경쟁 하지 않습니다.
- 서로 다른 컴퓨터의 여러 자체 호스팅 통합 런타임이 동일한 온-프레미스 데이터 원본에 연결할 수 있습니다. 예를 들어 두 개의 데이터 팩터리를 제공 하는 자체 호스팅 통합 런타임이 두 개인 경우 두 데이터 팩터리에 동일한 온-프레미스 데이터 원본을 등록할 수 있습니다.
- Power BI 시나리오를 처리 하기 위해 컴퓨터에 게이트웨이가 이미 설치 되어 있는 경우 다른 컴퓨터에 Data Factory에 대해 별도의 자체 호스팅 통합 런타임을 설치 합니다.
- 자체 호스팅 통합 런타임을 사용 하 여 Azure 가상 네트워크 내에서 데이터 통합을 지원 합니다.
- Azure ExpressRoute를 사용하더라도 데이터 원본은 방화벽으로 보호되는 온-프레미스 데이터 원본으로 취급해야 합니다. 자체 호스팅 integration runtime을 사용 하 여 서비스를 데이터 원본에 연결 합니다.
- 데이터 저장소가 Azure IaaS (Infrastructure as a Service) 가상 머신의 클라우드에 있는 경우에도 자체 호스팅 통합 런타임을 사용 합니다.
- FIPS 규격 암호화를 사용 하는 Windows 서버에 설치한 자체 호스팅 통합 런타임에서 태스크가 실패할 수 있습니다. 이 문제를 해결하려면 서버에서 FIPS 규격 암호화를 사용하지 않도록 설정합니다. FIPS 호환 암호화를 사용 하지 않도록 설정 하려면 다음 레지스트리 하위 키의 값을 1 (사용)에서 0 (사용 안 함): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`로 변경 합니다.

## <a name="prerequisites"></a>전제 조건

- 지원 되는 Windows 버전은 다음과 같습니다.
  + Windows 7 서비스 팩 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   도메인 컨트롤러에 자체 호스팅 통합 런타임 설치가 지원 되지 않습니다.
- .NET Framework 4.6.1 이상이 필요합니다. Windows 7 컴퓨터에 자체 호스팅 통합 런타임을 설치하는 경우 .NET Framework 4.6.1 이상을 설치합니다. 자세한 내용은 [.NET Framework 시스템 요구 사항](/dotnet/framework/get-started/system-requirements)을 참조하세요.
- 자체 호스팅 통합 런타임 컴퓨터에 권장 되는 최소 구성은 4 개 코어, 8gb RAM 및 80 GB의 사용 가능한 하드 드라이브 공간을 포함 하는 2 GHz 프로세서입니다.
- 호스트 컴퓨터가 최대 절전 모드인 경우 자체 호스팅 통합 런타임이 데이터 요청에 응답 하지 않습니다. 따라서 자체 호스팅 통합 런타임을 설치하기 전에 컴퓨터에서 전원 관리 옵션을 적절하게 구성하세요. 컴퓨터가 최대 절전 모드로 구성 된 경우 자체 호스팅 통합 런타임 설치 관리자에서 메시지를 표시 합니다.
- 자체 호스팅 통합 런타임을 성공적으로 설치 및 구성 하려면 컴퓨터의 관리자 여야 합니다.
- 복사 작업 실행은 특정 빈도로 발생 합니다. 컴퓨터의 프로세서 및 RAM 사용량은 최대 및 유휴 시간과 동일한 패턴을 따릅니다. 리소스 사용량은 이동 하는 데이터의 양에 따라 크게 달라 집니다. 여러 복사 작업이 진행 중인 경우 사용량이 많은 시간 동안 리소스 사용량이 증가하는 것을 볼 수 있습니다.
- Parquet, ORC 또는 Avro 형식의 데이터를 추출 하는 동안 태스크가 실패할 수 있습니다. Parquet에 대한 자세한 내용은 [Azure Data Factory Parquet 형식](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)을 참조 하세요. 파일 생성은 자체 호스팅 통합 컴퓨터에서 실행 됩니다. 정상적으로 작동 하려면 파일을 만들려면 다음 필수 구성 요소가 필요 합니다.
    - [Visual C++ 2010 재배포 가능 패키지](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) 패키지 (x64)
    - Jre (Java Runtime) 버전 8 (예: [OpenJDK 도입](https://adoptopenjdk.net/)) `JAVA_HOME` 환경 변수가 설정 되어 있는지 확인 합니다.

## <a name="installation-best-practices"></a>설치 모범 사례

[Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 MSI 설치 패키지를 다운로드 하 여 자체 호스팅 통합 런타임을 설치할 수 있습니다. 단계별 지침은 [온-프레미스와 클라우드 간에 데이터 이동](tutorial-hybrid-copy-powershell.md) 문서를 참조 하세요.

- 컴퓨터가 최대 절전 모드로 전환 되지 않도록 자체 호스팅 통합 런타임에 대한 호스트 컴퓨터에서 전원 계획을 구성 합니다. 호스트 컴퓨터가 최대 절전 모드로 설정되면 자체 호스팅 통합 런타임도 오프라인 상태가 됩니다.
- 자체 호스팅 통합 런타임과 연결 된 자격 증명을 정기적으로 백업 합니다.
- 자체 호스팅 IR 설정 작업을 자동화 하려면 [PowerShell을 통해 기존 자체 호스트 Ir 설정](#setting-up-a-self-hosted-integration-runtime)을 참조 하세요.  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Microsoft 다운로드 센터에서 자체 호스팅 IR 설치 및 등록

1. [Microsoft 통합 런타임 다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=39717)로 이동합니다.
1. **다운로드**를 선택 하 고, 64 비트 버전을 선택 하 고, **다음**을 선택 합니다. 32 비트 버전이 지원 되지 않습니다.
1. MSI 파일을 직접 실행 하거나 하드 드라이브에 저장 하 고 실행 합니다.
1. **시작** 창에서 언어를 선택 하 고 **다음**을 선택 합니다.
1. Microsoft 소프트웨어 사용 조건에 동의하고 **다음**을 선택합니다.
1. 자체 호스팅 통합 런타임을 설치할 **폴더**를 선택하고 **다음**을 선택합니다.
1. **설치 준비 완료** 페이지에서 **설치**를 선택합니다.
1. **마침** 을 선택 하 여 설치를 완료 합니다.
1. PowerShell을 사용 하 여 인증 키를 가져옵니다. 인증 키 검색을 위한 PowerShell 예제는 다음과 같습니다.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. 컴퓨터에서 실행 되는 Microsoft Integration Runtime Configuration Manager의 **Integration Runtime(자체 호스팅) 등록** 창에서 다음 단계를 수행 합니다.

    1. 텍스트 영역에 인증 키를 붙여넣습니다.

    1. 필요에 따라 **인증 키 표시**를 선택하여 키 텍스트를 확인합니다.

    1. **등록**을 선택합니다.

## <a name="high-availability-and-scalability"></a>고가용성 및 확장성

자체 호스팅 통합 런타임을 여러 온-프레미스 컴퓨터 또는 Azure의 가상 머신과 연결할 수 있습니다. 이러한 컴퓨터를 노드라고 합니다. 최대 4개의 노드를 자체 호스팅 통합 런타임에 연결할 수 있습니다. 논리 게이트웨이에 대해 게이트웨이가 설치 된 온-프레미스 컴퓨터에서 여러 노드를 사용할 경우의 이점은 다음과 같습니다.

* 빅 데이터 솔루션의 단일 실패 지점 또는 Data Factory와 클라우드 데이터 통합이 더 이상 필요 하지 않도록 자체 호스팅 통합 런타임의 가용성이 높습니다. 이 가용성은 최대 4 개의 노드를 사용 하는 경우 연속성을 보장 하는 데 도움이 됩니다.
* 온-프레미스 및 클라우드 데이터 저장소 간의 데이터 이동 성능 및 처리량을 향상시킵니다. 자세한 내용은 [성능 비교](copy-activity-performance.md)를 참조하세요.

[다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스팅 integration runtime 소프트웨어를 설치 하 여 여러 노드를 연결할 수 있습니다. 그런 다음 [자습서](tutorial-hybrid-copy-powershell.md)에 설명 된 대로 **AzDataFactoryV2IntegrationRuntimeKey** cmdlet에서 가져온 인증 키 중 하나를 사용 하 여 등록 합니다.

> [!NOTE]
> 각 노드를 연결 하기 위해 자체 호스팅 통합 런타임을 새로 만들 필요는 없습니다. 자체 호스팅 통합 런타임을 다른 컴퓨터에서 설치하고 동일한 인증 키를 사용하여 등록할 수 있습니다.

> [!NOTE]
> 고가용성 및 확장성을 위해 다른 노드를 추가 하기 전에 첫 번째 노드에서 **인트라넷에 원격으로 액세스** 옵션을 사용 하도록 설정 해야 합니다. 이렇게 하려면 **인트라넷에 대한 원격 액세스** > Configuration Manager > **설정** **Microsoft Integration Runtime** 선택 합니다.

### <a name="scale-considerations"></a>크기 조정 고려 사항

#### <a name="scale-out"></a>확장

프로세서 사용량이 많고 자체 호스팅 IR에서 사용 가능한 메모리가 부족 한 경우 컴퓨터 간에 부하를 확장 하는 데 도움이 되는 새 노드를 추가 합니다. 시간이 초과 되거나 자체 호스트 된 IR 노드가 오프 라인 상태 여 서 작업이 실패 하는 경우 게이트웨이에 노드를 추가 하는 경우 도움이 됩니다.

#### <a name="scale-up"></a>강화

프로세서 및 사용 가능한 RAM이 잘 활용 되지 않지만 동시 작업의 실행이 노드의 제한에 도달 하면 노드가 실행할 수 있는 동시 작업 수를 늘려서 확장 합니다. 자체 호스팅 IR이 오버 로드 되므로 활동 시간이 초과 되 면 확장할 수도 있습니다. 다음 이미지와 같이 노드의 최대 용량을 늘릴 수 있습니다.  

![노드에서 실행할 수 있는 동시 작업 수 늘리기](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 인증서 요구 사항

Integration runtime 노드 간의 통신을 보호 하는 데 사용 하는 TLS/SSL 인증서에 대한 요구 사항은 다음과 같습니다.

- 인증서는 공개적으로 신뢰할 수 있는 X509 v3 인증서여야 합니다. 공용 파트너 CA (인증 기관)에서 발급 한 인증서를 사용 하는 것이 좋습니다.
- 각 통합 런타임 노드는 이 인증서를 신뢰해야 합니다.
- 마지막 SAN 항목만 사용 되므로 SAN (주체 대체 이름) 인증서를 사용 하지 않는 것이 좋습니다. 다른 모든 SAN 항목은 무시 됩니다. 예를 들어 SAN 인증서가 **node1.domain.contoso.com** 및 **node2.domain.contoso.com**인 san 인증서가 있는 경우 FQDN (정규화 된 도메인 이름)이 **node2.domain.contoso.com**인 컴퓨터 에서만이 인증서를 사용할 수 있습니다.
- 인증서는 SSL 인증서에 대해 Windows Server 2012 r 2에서 지 원하는 모든 키 크기를 사용할 수 있습니다.
- CNG 키를 사용 하는 인증서는 지원 되지 않습니다.  

> [!NOTE]
> 이 인증서는 다음과 같이 사용 됩니다.
>
> - 자체 호스팅 IR 노드의 포트를 암호화 합니다.
> - 상태 동기화에 대한 노드 간 통신의 경우 노드 간 연결 된 서비스의 자격 증명 동기화를 포함 합니다.
> - PowerShell cmdlet이 로컬 네트워크 내에서 연결 된 서비스 자격 증명 설정에 사용 되는 경우
>
> 개인 네트워크 환경이 안전 하지 않거나 개인 네트워크 내에서 노드 간의 통신을 보호 하려는 경우이 인증서를 사용 하는 것이 좋습니다.
>
> 자체 호스팅 IR에서 다른 데이터 저장소로 전송 되는 데이터 이동은이 인증서의 설정 여부에 관계 없이 항상 암호화 된 채널 내에서 발생 합니다.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 공유 자체 호스팅 통합 런타임 만들기

데이터 팩터리에 이미 설치한 기존의 자체 호스팅 통합 런타임 인프라를 재사용할 수 있습니다. 이렇게 다시 사용 하면 기존 공유 자체 호스팅 IR을 참조 하 여 다른 데이터 팩터리에 연결 된 자체 호스팅 통합 런타임을 만들 수 있습니다.

이 기능에 대한 소개와 데모를 보려면 다음 12 분 분량의 비디오를 시청 하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>용어

- **공유 ir**: 실제 인프라에서 실행 되는 원래의 자체 호스팅 IR입니다.  
- **연결 된 ir**: 다른 공유 ir을 참조 하는 ir입니다. 연결 된 IR은 논리 IR 이며 다른 공유 자체 호스팅 IR 인프라를 사용 합니다.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>자체 호스팅 integration runtime을 공유 하는 방법

자체 호스팅 통합 런타임을 여러 데이터 팩터리와 공유 하려면 [공유 자체 호스팅 통합 런타임 만들기](create-shared-self-hosted-integration-runtime-powershell.md) 에 대한 자세한 내용을 참조 하세요.

### <a name="monitoring"></a>모니터링

#### <a name="shared-ir"></a>공유 IR

![공유 통합 런타임을 찾기 위한 선택 항목](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![공유 통합 런타임 모니터링](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>연결 된 IR

![연결 된 통합 런타임을 찾기 위한 선택 항목](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![연결 된 통합 런타임 모니터링](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>자체 호스팅 IR 공유에 대해 알려진 제한 사항

* 연결 된 IR이 생성 되는 데이터 팩터리에는 [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)가 있어야 합니다. 기본적으로 Azure Portal 또는 PowerShell cmdlet에서 만든 데이터 팩터리에는 암시적으로 만들어진 MSI가 있습니다. 그러나 Azure Resource Manager 템플릿이나 SDK를 통해 데이터 팩터리를 만드는 경우 **Identity** 속성을 명시적으로 설정 해야 합니다. 이 설정을 사용 하면 리소스 관리자 MSI를 포함 하는 데이터 팩터리를 만듭니다.

* 이 기능을 지 원하는 Data Factory .NET SDK는 1.1.0 이상 버전 이어야 합니다.

* 권한을 부여 하려면 공유 IR이 존재 하는 데이터 팩터리에 소유자 역할 또는 상속 된 소유자 역할이 필요 합니다.

* 공유 기능은 동일한 Azure AD 테넌트 내의 데이터 팩터리에 대해서만 작동 합니다.

* Azure AD [게스트 사용자](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)의 경우 검색 키워드를 사용 하 여 모든 데이터 팩터리를 나열 하는 UI의 검색 기능이 [작동 하지 않습니다](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). 그러나 게스트 사용자가 데이터 팩터리의 소유자 인 경우 검색 기능 없이 IR을 공유할 수 있습니다. IR을 공유 해야 하는 data factory의 MSI의 경우 **할당 권한** 상자에 msi를 입력 하 고 Data Factory UI에서 **추가** 를 선택 합니다.

  > [!NOTE]
  > 이 기능은 Data Factory v 2 에서만 사용할 수 있습니다.

## <a name="notification-area-icons-and-notifications"></a>알림 영역 아이콘 및 알림

알림 영역에서 아이콘이 나 메시지 위로 커서를 이동 하는 경우 자체 호스팅 통합 런타임의 상태에 대한 세부 정보를 볼 수 있습니다.

![알림 영역의 알림](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>포트 및 방화벽

다음 두 가지 방화벽을 고려해 야 합니다.

- 조직의 중앙 라우터에서 실행 되는 *회사 방화벽*
- 자체 호스팅 통합 런타임이 설치 된 로컬 컴퓨터에서 디먼으로 구성 된 *Windows 방화벽*

![방화벽](media/create-self-hosted-integration-runtime/firewall.png)

회사 방화벽 수준에서 다음 도메인 및 아웃 바운드 포트를 구성 해야 합니다.

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

Windows 방화벽 수준 또는 컴퓨터 수준에서는 이러한 아웃 바운드 포트를 일반적으로 사용할 수 있습니다. 그렇지 않으면 자체 호스팅 통합 런타임 컴퓨터에서 도메인 및 포트를 구성할 수 있습니다.

> [!NOTE]
> 원본 및 싱크에 따라 회사 방화벽 또는 Windows 방화벽에서 추가 도메인 및 아웃 바운드 포트를 허용 해야 할 수도 있습니다.
>
> Azure SQL Database 및 Azure Data Lake와 같은 일부 클라우드 데이터베이스의 경우 방화벽 구성에서 자체 호스팅 통합 런타임 컴퓨터의 IP 주소를 허용 해야 할 수 있습니다.

### <a name="copy-data-from-a-source-to-a-sink"></a>원본에서 싱크로 데이터 복사

회사 방화벽, 자체 호스팅 통합 런타임 컴퓨터의 Windows 방화벽 및 데이터 저장소 자체에 방화벽 규칙을 적절 하 게 사용 하도록 설정 해야 합니다. 이러한 규칙을 사용 하면 자체 호스팅 통합 런타임이 원본 및 싱크에 성공적으로 연결 될 수 있습니다. 복사 작업과 관련된 각 데이터 저장소에 대해 규칙을 사용하도록 설정합니다.

예를 들어 온-프레미스 데이터 저장소에서 SQL Database 싱크 또는 Azure SQL Data Warehouse 싱크로 복사 하려면 다음 단계를 수행 합니다.

1. Windows 방화벽 및 회사 방화벽 둘 다에 대해 포트 1433에서 아웃 바운드 TCP 통신을 허용 합니다.
1. SQL database의 방화벽 설정을 구성 하 여 허용 된 IP 주소 목록에 자체 호스팅 integration runtime 컴퓨터의 IP 주소를 추가 합니다.

> [!NOTE]
> 방화벽에서 아웃 바운드 포트 1433을 허용 하지 않는 경우 자체 호스팅 통합 런타임에서 SQL 데이터베이스에 직접 액세스할 수 없습니다. 이 경우 [준비 된 복사본](copy-activity-performance.md) 을 사용 하 여 SQL Database 하 고 SQL Data Warehouse 수 있습니다. 이 시나리오에서는 데이터 이동에 HTTPS (포트 443)만 필요 합니다.

## <a name="proxy-server-considerations"></a>프록시 서버 고려 사항

회사 네트워크 환경에서 프록시 서버를 사용하여 인터넷에 액세스하는 경우 자체 호스팅 통합 런타임이 적절한 프록시 설정을 사용하도록 구성합니다. 초기 등록 단계에서 프록시를 설정할 수 있습니다.

![프록시 지정](media/create-self-hosted-integration-runtime/specify-proxy.png)

구성 된 경우 자체 호스팅 통합 런타임은 프록시 서버를 사용 하 여 클라우드 서비스의 원본 및 대상에 연결 합니다 (HTTP 또는 HTTPS 프로토콜 사용). 초기 설치 중에 **변경 링크** 를 선택 해야 합니다.

![프록시 설정](media/create-self-hosted-integration-runtime/set-http-proxy.png)

이 대화 상자에는 세 가지 구성 옵션이 있습니다.

- **프록시 사용 안 함**: 자체 호스팅 통합 런타임이 클라우드 서비스에 연결 하는 데 프록시를 명시적으로 사용 하지 않습니다.
- **시스템 프록시 사용**: 자체 호스팅 통합 런타임에서는 diahost 및 diahost에서 구성 된 프록시 설정을 사용 합니다. 이러한 파일에 프록시 구성이 지정 되어 있지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결 합니다.
- **사용자 지정 프록시 사용**: diahost 및 diahost의 구성을 사용 하는 대신 자체 호스팅 통합 런타임에 사용할 HTTP 프록시 설정을 구성 합니다. **주소** 및 **포트** 값은 필수입니다. **사용자 이름** 및 **암호** 값은 프록시의 인증 설정에 따라 선택 사항입니다. 모든 설정은 자체 호스팅 통합 런타임d에서 Windows DPAPI를 사용하여 암호화되며 컴퓨터에 로컬로 저장됩니다.

업데이트 된 프록시 설정을 저장 하면 integration runtime 호스트 서비스가 자동으로 다시 시작 됩니다.

자체 호스팅 integration runtime을 등록 한 후 프록시 설정을 확인 하거나 업데이트 하려면 Microsoft Integration Runtime Configuration Manager를 사용 합니다.

1. **Microsoft 통합 런타임 구성 관리자**를 엽니다.
1. **설정** 탭을 선택합니다.
1. **Http 프록시**에서 **변경** 링크를 선택 하 여 **http 프록시 설정** 대화 상자를 엽니다.
1. **다음**을 선택합니다. 그러면 프록시 설정을 저장 하 고 integration runtime 호스트 서비스를 다시 시작할 수 있는 권한을 요청 하는 경고가 표시 됩니다.

구성 관리자 도구를 사용 하 여 HTTP 프록시를 확인 하 고 업데이트할 수 있습니다.

![프록시 보기 및 업데이트](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> NTLM 인증을 사용 하 여 프록시 서버를 설정 하는 경우 통합 런타임 호스트 서비스는 도메인 계정에서 실행 됩니다. 나중에 도메인 계정에 대한 암호를 변경 하는 경우 서비스에 대한 구성 설정을 업데이트 하 고 서비스를 다시 시작 해야 합니다. 이 요구 사항으로 인해 암호를 자주 업데이트 하지 않아도 되는 전용 도메인 계정을 사용 하 여 프록시 서버에 액세스 하는 것이 좋습니다.

### <a name="configure-proxy-server-settings"></a>프록시 서버 설정 구성

HTTP 프록시에 대해 **시스템 프록시 사용** 옵션을 선택 하는 경우 자체 호스팅 통합 런타임은 diahost 및 diahost .exe .config에서 프록시 설정을 사용 합니다. 이러한 파일에서 프록시를 지정 하지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결 합니다. 다음 절차에서는 diahost.exe.config 파일을 업데이트하는 지침을 제공합니다.

1. 파일 탐색기에서 원래 파일의 백업으로 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config의 안전한 복사본을 만듭니다.
1. 관리자 권한으로 실행 하는 메모장을 엽니다.
1. 메모장에서 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. 텍스트 파일을 엽니다.
1. 다음 코드에 표시 된 것 처럼 기본 **system.net** 태그를 찾습니다.

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    그러면 다음 예제와 같이 프록시 서버 세부 정보를 추가할 수 있습니다.

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    프록시 태그를 사용 하면 추가 속성을 사용 하 여 `scriptLocation`와 같은 필수 설정을 지정할 수 있습니다. 구문은 [\<proxy\> 요소 (네트워크 설정)](https://msdn.microsoft.com/library/sa91de1e.aspx) 를 참조 하세요.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. 구성 파일을 원래 위치에 저장 합니다. 그런 다음 자체 호스팅 통합 런타임 호스트 서비스를 다시 시작 하 여 변경 내용을 선택 합니다.

   서비스를 다시 시작 하려면 제어판의 서비스 애플릿을 사용 합니다. 또는 통합 런타임 구성 관리자에서 **서비스 중지** 단추를 선택한 후 **서비스 시작**을 선택합니다.

   서비스가 시작 되지 않으면 편집한 응용 프로그램 구성 파일에 잘못 된 XML 태그 구문이 추가 되었을 가능성이 높습니다.

> [!IMPORTANT]
> diahost.exe.config 및 diawp.exe.config를 둘 다 업데이트해야 합니다.

또한 Microsoft Azure 회사의 허용 목록에 있는지 확인 해야 합니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=41653)에서 유효한 Azure IP 주소 목록을 다운로드할 수 있습니다.

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>방화벽 및 프록시 서버와 관련 된 문제에 대한 가능한 증상

다음과 같은 오류 메시지가 표시 되는 경우 방화벽 또는 프록시 서버가 잘못 구성 된 것일 수 있습니다. 이러한 구성을 통해 자체 호스팅 통합 런타임이 Data Factory에 연결 하 여 자신을 인증할 수 없습니다. 이전 섹션을 참조하여 방화벽 및 프록시 서버가 올바르게 구성되었는지 확인합니다.

* 자체 호스팅 통합 런타임을 등록 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다. "이 Integration Runtime 노드를 등록 하지 못했습니다. 인증 키가 올바르며 integration service 호스트 서비스가이 컴퓨터에서 실행 되 고 있는지 확인 하십시오. "
* 통합 런타임 구성 관리자를 열 때 상태가 **연결 끊김** 또는 **연결 중**으로 표시됩니다. Windows 이벤트 로그를 볼 때 **이벤트 뷰어** > **응용 프로그램 및 서비스 로그** > **Microsoft Integration Runtime**에서 다음과 같은 오류 메시지가 표시 됩니다.

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>인트라넷에서 원격 액세스를 사용 하도록 설정

PowerShell을 사용 하 여 자체 호스팅 통합 런타임을 설치한 위치가 아닌 네트워크로 연결 된 컴퓨터에서 자격 증명을 암호화 하는 경우 **인트라넷에서 원격 액세스** 옵션을 사용 하도록 설정할 수 있습니다. 자체 호스팅 통합 런타임을 설치한 컴퓨터에서 PowerShell을 실행 하 여 자격 증명을 암호화 하는 경우 **인트라넷에서 원격 액세스**를 사용 하도록 설정할 수 없습니다.

고가용성 및 확장성을 위해 다른 노드를 추가 하기 전에 **인트라넷에서 원격 액세스** 를 사용 하도록 설정 합니다.  

자체 호스팅 integration runtime 설치 버전 3.3 이상을 실행 하는 경우 기본적으로 자체 호스팅 integration runtime 설치 관리자는 자체 호스팅 통합 런타임 컴퓨터의 **인트라넷에서 원격 액세스** 를 사용 하지 않도록 설정 합니다.

파트너 또는 다른 사용자의 방화벽을 사용 하는 경우 수동으로 포트 8060 또는 사용자 구성 포트를 열 수 있습니다. 자체 호스팅 integration runtime을 설정 하는 동안 방화벽 문제가 발생 하는 경우 다음 명령을 사용 하 여 방화벽을 구성 하지 않고 자체 호스팅 통합 런타임을 설치 합니다.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

자체 호스팅 통합 런타임 컴퓨터에서 포트 8060을 열지 않도록 선택 하는 경우 자격 증명 설정 응용 프로그램 이외의 메커니즘을 사용 하 여 데이터 저장소 자격 증명을 구성 합니다. 예를 들어 **AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell cmdlet을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

단계별 지침은 [자습서: 온-프레미스 데이터를 클라우드로 복사](tutorial-hybrid-copy-powershell.md)를 참조 하세요.
