---
title: 보안 및 액세스 제어 문제 해결
description: Azure Data Factory에서 보안 및 액세스 제어 문제를 해결 하는 방법에 대해 알아봅니다.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: 5e94ea989002d3d3c6d0e96123d5b8ddb5f078c3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568038"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>보안 및 액세스 제어 문제 Azure Data Factory 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 보안 및 액세스 제어에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>클라우드 데이터 저장소의 복사 작업에서 연결 문제가 발생 합니다.

#### <a name="symptoms"></a>증상

원본 또는 싱크 데이터 저장소에서 연결 문제가 발생 하는 경우 다양 한 오류 메시지가 반환 될 수 있습니다.

#### <a name="cause"></a>원인 

문제는 일반적으로 다음 요소 중 하나로 인해 발생 합니다.

* 자체 호스팅 IR을 사용 하는 경우 자체 호스팅 IR (통합 런타임) 노드의 프록시 설정입니다.

* 자체 호스팅 ir을 사용 하는 경우 자체 호스팅 IR 노드의 방화벽 설정

* 클라우드 데이터 저장소의 방화벽 설정입니다.

#### <a name="resolution"></a>해결 방법

* 연결 문제 인지 확인 하려면 다음 사항을 확인 하세요.

   - 이 오류는 원본 또는 싱크 커넥터에서 throw 됩니다.
   - 복사 작업을 시작할 때 오류가 발생 합니다.
   - 이 오류는 노드 중 일부에만 문제가 있는 경우 다중 노드 자체 호스팅 IR에서 임의 오류가 발생할 수 있기 때문에 Azure IR 또는 자체 호스팅 IR에 대해 일치 합니다.

* **자체 호스팅 IR** 을 사용 하는 경우 Azure IR를 사용 하는 경우 동일한 데이터 저장소에 연결 하는 데 문제가 발생할 수 있으므로 프록시, 방화벽 및 네트워크 설정을 확인 합니다. 이 시나리오를 해결 하려면 다음을 참조 하세요.

   * [자체 호스팅 IR 포트 및 방화벽](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Azure Data Lake Storage 커넥터](./connector-azure-data-lake-store.md)
  
* **Azure IR** 사용 하는 경우 데이터 저장소의 방화벽 설정을 사용 하지 않도록 설정 해 봅니다. 이 방법은 다음과 같은 두 가지 상황에서 문제를 해결할 수 있습니다.
  
   * [AZURE IR IP 주소](./azure-integration-runtime-ip-addresses.md) 는 허용 목록에 없습니다.
   * *신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용* 기능은 [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) 및 [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities)에 대해 해제 되어 있습니다.
   * Azure Data Lake Storage Gen1에 대해 *Azure 서비스에 대 한 액세스 허용* 설정이 사용 하도록 설정 되어 있지 않습니다.

이전 방법이 작동 하지 않는 경우 Microsoft에 도움을 요청 하세요.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>공용 네트워크 액세스를 사용 하지 않도록 설정 하 고 나면 인증 키 문제가 잘못 되었거나 비어 있음

#### <a name="symptoms"></a>증상

Data Factory에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정한 후에는 "인증 키가 잘못 되었거나 비어 있습니다." 라는 오류 메시지가 표시 됩니다.

#### <a name="cause"></a>원인

공용 연결을 사용 하지 않도록 설정 하 고 개인 끝점을 설정 하 여 다시 연결 하는 것을 방지 하기 때문에 DNS (Domain Name System) 확인 문제로 인해이 문제가 발생할 수 있습니다.

Data Factory FQDN (정규화 된 도메인 이름)이 공용 IP 주소로 확인 되는지 확인 하려면 다음을 수행 합니다.

1. Data Factory 개인 끝점과 동일한 가상 네트워크에서 Azure VM (가상 머신)을 만들었는지 확인 합니다.

2. Azure VM에서 Data Factory FQDN으로 PsPing 및 Ping을 실행 합니다.

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > PsPing 명령에 대 한 포트를 지정 해야 합니다. 여기에는 포트 443이 표시 되지만 반드시 필요한 것은 아닙니다.

3. 두 명령이 지정 된 영역을 기반으로 하는 Azure Data Factory 공용 IP로 확인 되는지 확인 합니다. IP는 다음과 같은 형식 이어야 합니다. `xxx.xxx.xxx.0`

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음을 수행하십시오.

- 옵션으로 "개인 링크 DNS 영역" Data Factory "Virtual Network 링크"를 수동으로 추가 하는 것이 좋습니다. 자세한 내용은 [Azure Data Factory에 대 한 Azure 개인 링크](./data-factory-private-link.md#dns-changes-for-private-endpoints) 문서를 참조 하세요. 지침은 개인 IP 주소에 대 한 Data Factory FQDN을 확인 하도록 개인 DNS 영역 또는 사용자 지정 DNS 서버를 구성 하기 위한 것입니다. 

- 그러나 개인 DNS 영역 또는 사용자 지정 DNS 서버를 구성 하지 않으려면 다음 임시 솔루션을 시도해 보세요.

  1. Windows에서 호스트 파일을 변경 하 고 개인 IP (Azure Data Factory 개인 끝점)를 Azure Data Factory FQDN에 매핑합니다.
  
     Azure VM에서로 이동 하 여 `C:\Windows\System32\drivers\etc` 메모장에서 *호스트* 파일을 엽니다. 파일의 끝에 있는 FQDN에 개인 IP를 매핑하는 줄을 추가 하 고 변경 내용을 저장 합니다.
     
     ![호스트에 개인 IP를 매핑하는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. 위의 확인 단계와 동일한 명령을 다시 실행 하 여 개인 IP를 포함 하는 응답을 확인 합니다.

  1. 자체 호스팅 통합 런타임을 다시 등록 하면 문제를 해결 해야 합니다.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>개인 링크로 인해 자체 호스트 된 Vm에 IR 인증 키를 등록할 수 없습니다.

#### <a name="symptoms"></a>증상

개인 링크를 사용 하도록 설정 되어 있으므로 자체 호스팅 VM에서 IR 인증 키를 등록할 수 없습니다. 다음과 같은 오류 메시지가 표시됩니다.

"ADF 서비스에서 키를 사용 하 여 서비스 토큰을 가져오지 못했습니다. * * * * * * * * * * * * * * * * 0.1250079 초, 오류 코드: InvalidGatewayKey, 작업 id: XXXXXXX 및 자세한 오류 메시지는 클라이언트 IP 주소가 올바른 개인 IP가 아닙니다. Data factory가 공용 네트워크에 액세스할 수 없어서 클라우드로 연결 하 여 성공적인 연결을 만들 수 없습니다."

#### <a name="cause"></a>원인

이 문제는 자체 호스팅 IR을 설치 하려는 VM 때문에 발생할 수 있습니다. 클라우드에 연결 하려면 공용 네트워크 액세스를 사용 하도록 설정 해야 합니다.

#### <a name="resolution"></a>해결 방법

**해결 방법 1**
 
이 문제를 해결하려면 다음을 수행하십시오.

1. [팩터리-업데이트](/rest/api/datafactory/Factories/Update) 페이지로 이동 합니다.

1. 오른쪽 위에서 **사용해 보기** 단추를 선택 합니다.
1. **매개 변수** 에서 필요한 정보를 입력 합니다. 
1. **본문** 아래에 다음 속성을 붙여넣습니다.

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. **실행** 을 선택 하 여 함수를 실행 합니다. 

1. **매개 변수** 에서 필요한 정보를 입력 합니다. 

1. **본문** 아래에 다음 속성을 붙여넣습니다.
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. **실행** 을 선택 하 여 함수를 실행 합니다. 
1. **응답 코드: 200** 이 표시 되는지 확인 합니다. 붙여넣은 속성은 JSON 정의에도 표시 되어야 합니다.

1. 통합 런타임에 IR 인증 키를 다시 추가 합니다.

**해결 방법 2**

이 문제를 해결 하려면 [Azure Data Factory에 대 한 Azure 개인 링크](./data-factory-private-link.md)로 이동 합니다.

다음 스크린샷에 표시 된 것 처럼 사용자 인터페이스에서 공용 네트워크 액세스를 사용 하도록 설정 해 봅니다.

![네트워킹 창에서 "공용 네트워크 액세스 허용"에 대 한 "사용" 제어의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>ADF 개인 DNS 영역 재정의 Azure Resource Manager DNS 확인에서 ' 찾을 수 없음 ' 오류 발생

#### <a name="cause"></a>원인
Azure Resource Manager와 ADF는 모두 동일한 개인 영역을 사용 하 여 Azure Resource Manager 레코드를 찾을 수 없는 시나리오와 함께 고객의 개인 DNS에서 잠재적 충돌을 생성 합니다.

#### <a name="solution"></a>해결 방법
1. Azure Portal에서 **privatelink.azure.com** 사설 DNS 영역을 찾습니다.
![사설 DNS 영역을 찾기 위한 스크린샷](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. A 레코드 **adf** 가 있는지 확인 합니다.
![레코드의 스크린샷](media/security-access-control-troubleshoot-guide/a-record.png)
3.  **가상 네트워크 링크** 로 이동 하 여 모든 레코드를 삭제 합니다.
![가상 네트워크 링크의 스크린샷](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Azure Portal에서 데이터 팩터리로 이동 하 여 Azure Data Factory 포털에 대 한 개인 끝점을 다시 만듭니다.
![개인 끝점을 다시 만드는 스크린샷](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  사설 DNS 영역으로 돌아가서 새 개인 DNS 영역 **privatelink.adf.azure.com** 이 있는지 확인 합니다.
![새 DNS 레코드의 스크린샷](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>공용 끝점에 연결 오류가 있습니다.

#### <a name="symptoms"></a>증상

Azure Blob Storage 계정 공용 액세스를 사용 하 여 데이터를 복사 하는 경우 다음과 같은 오류로 인해 파이프라인이 임의로 실패 합니다.

예: Azure Blob Storage 싱크에서 Azure IR (관리 되지 않는 공용 VNet)를 사용 하 고 Azure SQL Database 원본에서 관리 되는 VNet IR을 사용 하 고 있었습니다. 또는 원본/싱크에서 저장소 공용 액세스와 함께 관리 되는 VNet IR을 사용 합니다.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>원인

ADF는 관리 되는 VNet IR을 계속 사용할 수 있지만, 관리 되는 VNet에서 Azure Blob Storage 하는 공용 끝점은 테스트 결과를 기반으로 안정적이 지 않으므로 이러한 오류가 발생할 수 있습니다. Azure Blob Storage 및 Azure Data Lake Gen2는 관리 되는 [가상 네트워크 & 관리 되는 전용 끝점](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network)에 따라 ADF 관리 Virtual Network의 공용 끝점을 통해 연결 될 수 없습니다.

#### <a name="solution"></a>해결 방법

- 관리 되는 VNet IR을 사용 하는 경우 원본 및 싱크 쪽에서 개인 끝점을 사용 하도록 설정 합니다.
- 여전히 공용 끝점을 사용 하려는 경우 원본 및 싱크에 대해 관리 되는 VNet IR을 사용 하는 대신 공용 IR로 전환할 수 있습니다. 공용 IR로 다시 전환 하더라도 관리 되는 VNet IR이 여전히 있는 경우 ADF에서 관리 되는 VNet IR을 계속 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory에 대 한 개인 링크](data-factory-private-link.md)
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대 한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)