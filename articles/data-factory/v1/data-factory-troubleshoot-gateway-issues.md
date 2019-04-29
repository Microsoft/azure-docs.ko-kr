---
title: 데이터 관리 게이트웨이 문제 해결 | Microsoft Docs
description: 데이터 관리 게이트웨이와 관련된 문제를 해결하기 위한 팁을 제공합니다.
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0559d89bd691323a95713d518df05e58283cef39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61252440"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>데이터 관리 게이트웨이 사용 관련 문제 해결
이 문서에서는 데이터 관리 게이트웨이 사용과 관련된 문제 해결에 대한 정보를 제공합니다.

> [!NOTE]
> 이 문서의 내용은 Azure Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory의 자체 호스팅 통합 런타임](../create-self-hosted-integration-runtime.md)을 참조하세요.

게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요. 게이트웨이를 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 이동하는 연습은 [온-프레미스와 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

## <a name="failed-to-install-or-register-gateway"></a>게이트웨이를 설치하거나 등록하지 못함
### <a name="1-problem"></a>1. 문제
게이트웨이 설치 파일을 다운로드하는 동안, 특히 게이트웨이를 설치하고 등록할 때 이 오류 메시지가 표시됩니다.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>원인
게이트웨이를 설치하려는 컴퓨터에서 네트워크 문제로 인해 다운로드 센터에서 최신 게이트웨이 설치 파일을 다운로드하지 못했습니다.

#### <a name="resolution"></a>해결 방법
방화벽 프록시 서버 설정을 검사하여 이 설정이 컴퓨터와 [다운로드 센터](https://download.microsoft.com/) 간의 네트워크 연결을 차단하는지 확인한 후 상황에 따라 설정을 업데이트합니다.

또는 다운로드 센터에 액세스할 수있는 다른 컴퓨터의 [다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 최신 게이트웨이의 설치 파일을 다운로드할 수 있습니다. 그런 다음 설치 프로그램 파일을 게이트웨이 호스트 컴퓨터에 복사한 다음 수동으로 실행하여 게이트웨이를 설치하고 업데이트할 수 있습니다.

### <a name="2-problem"></a>2. 문제
Azure Portal에서 **이 컴퓨터에 바로 설치**를 클릭하여 게이트웨이를 설치하려고 할 때 이 오류가 표시됩니다.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>원인
게이트웨이가 컴퓨터에 이미 설치되어 있습니다.

#### <a name="resolution"></a>해결 방법
컴퓨터에서 기존 게이트웨이를 제거하고 **이 컴퓨터에 바로 설치** 링크를 다시 클릭합니다.

### <a name="3-problem"></a>3. 문제
새 게이트웨이를 등록할 때 이 오류가 표시될 수 있습니다.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>원인
다음과 같은 이유 중 하나로 이 메시지가 표시될 수 있습니다.

* 게이트웨이 키의 형식이 잘못되었습니다.
* 게이트웨이 키가 무효화되었습니다.
* 게이트웨이 키가 포털에서 다시 생성되었습니다.  

#### <a name="resolution"></a>해결 방법
포털에서 올바른 게이트웨이 키를 사용하는지 확인합니다. 필요한 경우 키를 다시 생성하고 이 키를 사용하여 게이트웨이를 등록합니다.

### <a name="4-problem"></a>4. 문제
게이트웨이 등록할 때 다음 오류 메시지가 표시될 수 있습니다.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![키의 내용 또는 형식이 잘못됨](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>원인
입력된 게이트웨이 키의 내용 또는 형식이 잘못되었습니다. 그 이유 중 하나는 포털에서 키의 일부만 복사했거나 잘못된 키를 사용하고 있기 때문일 수 있습니다.

#### <a name="resolution"></a>해결 방법
포털에서 게이트웨이 키를 생성하고 복사 단추를 사용하여 전체 키를 복사합니다. 그런 다음 게이트웨이를 등록하려면 이 창에 붙여넣습니다.

### <a name="5-problem"></a>5. 문제
게이트웨이 등록할 때 다음 오류 메시지가 표시될 수 있습니다.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![게이트웨이 키가 잘못되었거나 비어 있음](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>원인
게이트웨이 키가 다시 생성되었거나 게이트웨이가 Azure Portal에서 삭제되었습니다. 데이터 관리 게이트웨이 설치가 최신이 아닌 경우에도 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법
데이터 관리 게이트웨이 설치가 최신 버전인지 확인하고 Microsoft [다운로드 센터](https://go.microsoft.com/fwlink/p/?LinkId=271260)에서 최신 버전을 찾을 수 있습니다.

설치가 최신이고 게이트웨이가 포털에 여전히 존재하는 경우 Azure Portal에서 게이트웨이 키를 생성하고 복사 단추를 사용하여 전체 키를 복사한 다음 이 창에 붙여넣어 게이트웨이를 등록합니다. 그렇지 않은 경우 게이트웨이를 다시 만들고 처음부터 다시 시작합니다.

### <a name="6-problem"></a>6. 문제
게이트웨이 등록할 때 다음 오류 메시지가 표시될 수 있습니다.

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![게이트웨이 키가 잘못되었거나 비어 있음](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>원인
게이트웨이가 삭제되었거나 연결된 게이트웨이 키가 다시 생성되었기 때문에 이 오류가 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법
게이트웨이가 삭제된 경우 포털에서 게이트웨이를 다시 만들고 **등록**을 클릭한 다음 포털에서 키를 복사하여 붙여넣고 게이트웨이를 등록합니다.

게이트웨이가 여전히 존재하지만 해당 키가 다시 생성된 경우 새 키를 사용하여 게이트웨이를 등록합니다. 키가 없는 경우 포털에서 키를 다시 생성합니다.

### <a name="7-problem"></a>7. 문제
게이트웨이를 등록할 때 인증서의 경로와 암호를 입력해야 할 수 있습니다.

![인증서 지정](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>원인
게이트웨이가 이전에 다른 컴퓨터에 등록되었습니다. 게이트웨이의 초기 등록 과정에서 암호화 인증서가 게이트웨이에 연결되었습니다. 이 인증서는 게이트웨이에서 자체 생성되었거나 사용자가 제공했을 수 있습니다.  이 인증서는 데이터 저장소(연결된 서비스)의 자격 증명을 암호화하는 데 사용됩니다.  

![인증서 내보내기](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

다른 호스트 컴퓨터에서 게이트웨이를 복원할 때 등록 마법사가 이전에 이 인증서로 암호화된 자격 증명의 암호를 해제하라는 메시지를 표시합니다.  이 인증서가 없으면 새 게이트웨이에서 자격 증명의 암호를 해독할 수 없으며 이 새 게이트웨이와 연결된 후속 복사 작업 실행이 실패합니다.  

#### <a name="resolution"></a>해결 방법
데이터 관리 게이트웨이 구성 관리자의 **설정** 탭에서 **내보내기** 단추를 사용하여 원래 게이트웨이 컴퓨터에서 자격 증명 인증서를 내보낸 경우 여기서 해당 인증서를 사용합니다.

게이트웨이를 복구할 때 이 단계를 건너뛸 수 없습니다. 인증서가 없는 경우 포털에서 게이트웨이를 삭제하고 새 게이트웨이 다시 만들어야 합니다.  또한 자격 증명을 다시 입력하여 게이트웨이와 관련된 모든 연결된 서비스를 업데이트합니다.

### <a name="8-problem"></a>8. 문제
다음 오류 메시지가 표시될 수 있습니다.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>원인
게이트웨이가 인터넷 리소스에 액세스하기 위해 HTTP 프록시가 필요한 환경에 있거나 프록시의 인증 암호가 변경되었지만 이에 따라 게이트웨이에서 해당 암호를 적절히 업데이트하지 않은 경우에 이 오류가 발생합니다.

#### <a name="resolution"></a>해결 방법
이 문서의 프록시 서버 고려 사항 섹션의 지침에 따라 데이터 관리 게이트웨이 구성 관리자를 사용하여 프록시 설정을 구성합니다.

## <a name="gateway-is-online-with-limited-functionality"></a>게이트웨이는 기능이 제한된 온라인입니다.
### <a name="1-problem"></a>1. 문제
게이트웨이 상태가 ‘온라인 상태이지만 기능이 제한됨’으로 표시됩니다.

#### <a name="cause"></a>원인
다음과 같은 이유 중 하나로 게이트웨이 상태가 ‘온라인 상태이지만 기능이 제한됨’으로 표시됩니다.

* 게이트웨이에서 Azure Service Bus를 통해 클라우드 서비스에 연결할 수 없습니다.
* 클라우드 서비스는 Service Bus를 통해 게이트웨이에 연결할 수 없습니다.

게이트웨이가 온라인 상태이지만 기능이 제한되면 Data Factory 복사 마법사를 사용하여 온-프레미스 데이터 저장소에(서) 데이터를 복사하기 위한 데이터 파이프라인을 만들지 못할 수도 없습니다. 이 문제를 해결하기 위해 포털, Visual Studio 또는 Azure PowerShell에서 Data Factory 편집기를 사용할 수 있습니다.

#### <a name="resolution"></a>해결 방법
게이트웨이가 클라우드 서비스에 연결할 수 없는지 아니면 다른 방법으로 연결할 수 있는지에 따라 이 문제(온라인 상태이지만 기능이 제한됨)를 해결할 수 있습니다. 다음 섹션에서는 이러한 해결 방법을 제공합니다.

### <a name="2-problem"></a>2. 문제
다음 오류가 표시됩니다.

`Error: Gateway cannot connect to cloud service through service bus`

![게이트웨이를 클라우드 서비스에 연결할 수 없음](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>원인
게이트웨이가 Service Bus를 통해 클라우드 서비스에 연결할 수 없습니다.

#### <a name="resolution"></a>해결 방법
게이트웨이를 다시 온라인으로 가져오려면 다음 단계를 따릅니다.

1. 게이트웨이 컴퓨터와 회사 방화벽에서 IP 주소 아웃바운드 규칙을 허용합니다. 다음과 같은 Windows 이벤트 로그에서 IP 주소를 찾을 수 있습니다(ID == 401). 액세스 권한 XX.XX.XX.XX:9350에 의해 숨겨진 소켓에 액세스를 시도했습니다.
1. 게이트웨이에 프록시 설정 구성 자세한 내용은 프록시 서버 고려 사항 섹션을 참조하세요.
1. 게이트웨이 컴퓨터의 Windows 방화벽 및 회사 방화벽 모두에서 5671 및 9350-9354 아웃바운드 포트를 사용하도록 설정합니다. 자세한 내용은 포트 및 방화벽 섹션을 참조하세요. 이 단계는 선택 사항이지만 성능을 고려하여 권장됩니다.

### <a name="3-problem"></a>3. 문제
다음 오류가 표시됩니다.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>원인
네트워크 연결에서 일시적인 오류가 발생했습니다.

#### <a name="resolution"></a>해결 방법
게이트웨이를 다시 온라인으로 가져오려면 다음 단계를 따릅니다.

1. 몇 분 정도 기다리면 오류가 사라지고 연결이 자동으로 복구됩니다.
1. 오류가 지속되면 게이트웨이 서비스를 다시 시작합니다.

## <a name="failed-to-author-linked-service"></a>연결된 서비스를 작성하지 못함
### <a name="problem"></a>문제
포털에서 자격 증명 관리자를 사용하여 연결된 새 서비스에 대한 자격 증명을 입력하거나 연결된 기존 서비스에 대한 자격 증명을 업데이트하려고 할 때 이 오류가 표시될 수 있습니다.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

이 오류가 표시되면 데이터 관리 게이트웨이 구성 관리자의 설정 페이지가 다음 스크린샷처럼 보일 수 있습니다.

![데이터베이스에 연결할 수 없음](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>원인
게이트웨이 컴퓨터에서 SSL 인증서가 손실되었을 수 있습니다. 게이트웨이에서 현재 SSL 암호화에 사용되는 인증서를 로드할 수 없습니다. 다음 메시지와 비슷한 오류 메시지가 이벤트 로그에 표시될 수도 있습니다.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>해결 방법
문제를 해결하려면 다음 단계를 수행합니다.

1. 데이터 관리 게이트웨이 구성 관리자를 시작합니다.
2. **설정** 탭으로 전환합니다.  
3. **변경** 단추를 클릭하여 SSL 인증서를 변경합니다.

   ![인증서 변경 단추](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. 새 인증서를 SSL 인증서로 선택합니다. 사용자 또는 다른 조직에서 만든 SSL 인증서를 사용할 수 있습니다.

   ![인증서 지정](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>복사 작업 실패
### <a name="problem"></a>문제
포털에서 파이프라인을 설정한 후 다음 "UserErrorFailedToConnectToSqlserver" 오류가 발생할 수 있습니다.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>원인
이 오류는 여러 가지 이유로 발생할 수 있으며 그에 따라 완화하는 방법이 달라집니다.

#### <a name="resolution"></a>해결 방법
SQL 데이터베이스에 연결하기 전에 데이터 관리 게이트웨이 클라이언트 쪽의 TCP/1433 포트를 통한 아웃바운드 TCP 연결을 허용합니다.

대상 데이터베이스가 Azure SQL 데이터베이스인 경우 Azure에 대한 SQL Server 방화벽 설정도 확인합니다.

온-프레미스 데이터 저장소에 대한 연결을 테스트하려면 다음 섹션을 참조하세요.

## <a name="data-store-connection-or-driver-related-errors"></a>데이터 저장소 연결 또는 드라이버 관련 오류
데이터 저장소 연결 또는 드라이버 관련 오류가 표시되면 다음 단계를 수행합니다.

1. 게이트웨이 컴퓨터에서 데이터 관리 게이트웨이 구성 관리자를 시작합니다.
2. **진단** 탭으로 전환합니다.
3. **연결 테스트**에서 게이트웨이 그룹 값을 추가합니다.
4. **연결 테스트**를 클릭하여 연결 정보와 자격 증명을 사용하여 게이트웨이 컴퓨터에서 온-프레미스 데이터 원본에 연결할 수 있는지 확인합니다. 드라이버를 설치한 후에 계속 연결 테스트가 실패하는 경우 최신 변경 내용을 반영하도록 게이트웨이를 다시 시작합니다.

![진단 탭의 연결 테스트](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>게이트웨이 로그
### <a name="send-gateway-logs-to-microsoft"></a>Microsoft로 게이트웨이 로그 보내기
게이트웨이 문제를 해결하기 위해 Microsoft 지원에 문의하는 경우 게이트웨이 로그를 공유하도록 요청받을 수 있습니다. 게이트웨이를 해제하면 데이터 관리 게이트웨이 구성 관리자에서 두 개의 단추를 클릭하여 필수 게이트웨이 로그를 쉽게 공유할 수 있습니다.    

1. 데이터 관리 게이트웨이 구성 관리자의 **진단** 탭으로 전환합니다.

    ![데이터 관리 게이트웨이 - 진단 탭](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. **로그 보내기**를 클릭하여 다음 대화 상자를 표시합니다.

    ![데이터 관리 게이트웨이 - 로그 보내기](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (선택 사항) **로그 보기**를 클릭하여 이벤트 뷰어에서 로그를 검토합니다.
4. (선택 사항) **개인 정보 취급 방침**을 클릭하여 Microsoft 웹 서비스 개인 정보 취급 방침을 검토합니다.
5. 업로드할 항목에 만족하면 **로그 보내기**를 클릭하여 문제를 해결하기 위해 지난 7일 동안의 로그를 Microsoft에 보냅니다. 다음 스크린샷과 같이 로그 보내기 작업의 상태를 확인해야 합니다.

    ![데이터 관리 게이트웨이 - 로그 보내기 상태](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. 작업이 완료되면 다음 스크린샷과 같은 대화 상자가 표시됩니다.

    ![데이터 관리 게이트웨이 - 로그 보내기 상태](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. **보고서 ID**를 저장하고 Microsoft 지원과 공유합니다. 보고서 ID는 문제를 해결하기 위해 업로드한 게이트웨이 로그를 찾는 데 사용됩니다.  또한 보고서 ID는 이벤트 뷰어에도 저장됩니다.  "25" 이벤트 ID를 확인하여 찾을 수 있으며 날짜와 시간을 확인할 수 있습니다.

    ![데이터 관리 게이트웨이 - 로그 보내기 보고서 ID](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>게이트웨이 호스트 컴퓨터에 게이트웨이 로그 보관
게이트웨이에 문제가 있고 게이트웨이 로그를 직접 공유할 수 없는 일부 시나리오가 있습니다.

* 수동으로 게이트웨이를 설치하고 등록합니다.
* 데이터 관리 게이트웨이 구성 관리자에서 다시 생성된 키로 게이트웨이를 등록하려고 합니다.
* 로그를 보내려고 하는데 게이트웨이 호스트 서비스를 연결할 수 없습니다.

이러한 시나리오에서는 게이트웨이 로그를 zip 파일로 저장하고 나중에 Microsoft 지원에 문의할 때 공유할 수 있습니다. 예를 들어 다음 스크린샷과 같이 게이트웨이를 등록하는 동안 오류가 발생하는 경우가 있습니다.   

![데이터 관리 게이트웨이 - 등록 오류](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

**게이트웨이 로그 보관** 링크를 클릭하여 로그를 보관하고 저장한 다음 Microsoft 지원과 zip 파일을 공유합니다.

![데이터 관리 게이트웨이 - 로그 보관](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>게이트웨이 로그 찾기
Windows 이벤트 로그에서 자세한 게이트웨이 로그 정보를 확인할 수 있습니다.

1. Windows **이벤트 뷰어**를 시작합니다.
2. **애플리케이션 및 서비스 로그** > **데이터 관리 게이트웨이** 폴더에서 로그를 찾습니다.

   게이트웨이 관련 문제를 해결할 때는 이벤트 뷰어에서 오류 수준 이벤트를 찾아봅니다.

![데이터 관리 게이트웨이 - 이벤트 뷰어의 로그](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
