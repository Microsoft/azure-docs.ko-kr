---
title: Azure Data Factory에서 자체 호스팅 통합 런타임 문제 해결
description: Azure Data Factory에서 자체 호스팅 통합 런타임 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: b8492e8934c782451fb77d5a0ff56b96c34c9a00
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439883"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>자체 호스팅 integration runtime 문제 해결

이 문서에서는 Azure Data Factory에서 자체 호스팅 통합 런타임에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-resolutions"></a>일반적인 오류 및 해결 방법

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>오류 메시지: 자체 호스팅 integration runtime이 클라우드 서비스에 연결할 수 없음

![자체 호스팅 IR 연결 문제](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>원인 

자체 호스팅 통합 런타임을 Data Factory 서비스 (백 엔드)에 연결할 수 없습니다. 이 문제는 일반적으로 방화벽의 네트워크 설정에 의해 발생 합니다.

#### <a name="resolution"></a>해상도

1. Integration runtime 서비스가 실행 중인지 확인 합니다.
    
   ![자체 호스팅 IR 서비스 실행 상태](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 서비스가 실행 되 고 있으면 3 단계로 이동 합니다.

1. 자체 호스팅 통합 런타임 (기본 설정)에 프록시가 구성 되어 있지 않으면 자체 호스팅 통합 런타임이 설치 된 컴퓨터에서 다음 PowerShell 명령을 실행 합니다.

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 서비스 URL은 Data Factory 위치에 따라 다를 수 있습니다. **ADF UI** > **연결** 에서 서비스 url을 찾을 수 있습니다 > **INTEGRATION** Runtime > **자체 호스팅 IR** > **노드** 편집 > **서비스 url 보기**.
            
    예상 되는 응답은 다음과 같습니다.
            
    ![PowerShell 명령 응답](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 예상 되는 응답을 받지 못한 경우에는 상황에 따라 다음 방법 중 하나를 사용 합니다.
            
    * "원격 이름을 확인할 수 없습니다." 라는 메시지가 표시 되는 경우 DNS (Domain Name System) 문제가 있습니다. 이 문제를 해결 하려면 네트워크 팀에 문의 하세요.
    * "Ssl/tls 인증서를 신뢰할 수 없습니다." 라는 메시지가 표시 되 면 컴퓨터에서 https://wu2.frontend.clouddatahub.net/ 에 대한 인증서를 신뢰할 수 있는지 확인 하 고 인증서 관리자를 사용 하 여 공용 인증서를 설치 합니다. 이 작업을 수행 하면 문제가 완화 됩니다.
    * **Windows** > **이벤트 뷰어 (로그)**  > **응용 프로그램 및 서비스 로그** > **Integration Runtime** 로 이동 하 고 DNS, 방화벽 규칙 또는 회사 네트워크 설정에 따라 발생 한 오류를 확인 합니다. 이러한 오류가 발생 하면 연결을 강제로 닫습니다. 모든 회사에서 네트워크 설정이 사용자 지정 되었으므로 네트워크 팀에 문의 하 여 이러한 문제를 해결 하세요.

1. 자체 호스팅 통합 런타임에 "프록시"가 구성 된 경우 프록시 서버에서 서비스 엔드포인트에 액세스할 수 있는지 확인 합니다. 샘플 명령은 [PowerShell, 웹 요청 및 프록시](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)를 참조 하세요.    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

예상 되는 응답은 다음과 같습니다.
            
![Powershell 명령 응답 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> 프록시 고려 사항:
> * 프록시 서버를 안전한 받는 사람 목록에 배치 해야 하는지 여부를 확인 합니다. 그렇다면 [이러한 도메인이](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) 안전한 받는 사람 목록에 있는지 확인 합니다.
> * 프록시 서버에서 TLS/SSL 인증서 "wu2.frontend.clouddatahub.net/"를 신뢰할 수 있는지 확인 합니다.
> * 프록시에서 Active Directory 인증을 사용 하는 경우 "Integration Runtime Service"로 프록시에 액세스할 수 있는 사용자 계정으로 서비스 계정을 변경 합니다.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>오류 메시지: 자체 호스팅 통합 런타임 노드/논리적 SHIR이 비활성/"실행 중 (제한 됨)" 상태입니다.

#### <a name="cause"></a>원인 

다음 스크린샷에 표시 된 것 처럼 자체 호스팅 통합 런타임 노드에 **비활성** 상태가 있을 수 있습니다.

![비활성 자체 호스팅 IR 노드](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

이 동작은 노드가 서로 통신할 수 없는 경우에 발생 합니다.

#### <a name="resolution"></a>해상도

1. 노드 호스팅 VM에 로그인 합니다. **응용 프로그램 및 서비스 로그** > **Integration Runtime**에서 이벤트 뷰어을 열고 모든 오류 로그를 필터링 합니다.

1. 오류 로그에 다음 오류가 포함 되어 있는지 확인 합니다. 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.  Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
