---
title: Azure Portal를 사용 하 여 GPU로 Azure Stack에 지 문제 해결 | Microsoft Docs
description: Azure Stack Edge GPU 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: alkohli
ms.openlocfilehash: 22f0c63c2b60b6c72ad297492045df17e10dd06c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268325"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에서 문제 해결 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Stack Edge GPU 장치에서 문제를 해결 하는 방법을 설명 합니다. 


## <a name="run-diagnostics"></a>진단 실행

디바이스 오류를 진단하고 해결하기 위해 진단 테스트를 실행합니다. 진단 테스트를 실행하려면 디바이스의 로컬 웹 UI에서 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **문제 해결 > 진단 테스트**로 이동합니다. 실행할 테스트를 선택 하 고 **테스트 실행**을 선택 합니다. 그러면 네트워크, 디바이스, 웹 프록시, 시간 또는 클라우드 설정에서 문제가 될만한 내용을 진단하는 테스트가 시작됩니다. 디바이스에서 테스트가 실행 중이라는 알림이 표시됩니다.

    ![테스트 선택 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. 테스트가 완료된 후 결과가 표시됩니다. 

    ![테스트 결과 보기](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    테스트가 실패하면 권장 조치에 대한 URL이 표시됩니다. URL을 선택 하 여 권장 작업을 표시 합니다.
 
    ![실패한 테스트에 대한 경고 살펴보기](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>지원 패키지 수집

로크 패키지는 Microsoft 지원에서 디바이스 문제를 해결하는 데 도움을 줄 수 있는 모든 관련 로그로 구성됩니다. 로그 패키지는 로컬 웹 UI를 통해 생성할 수 있습니다.

지원 패키지를 수집하려면 다음 단계를 수행합니다. 

1. 로컬 웹 UI에서 **문제 해결 > 지원**으로 이동합니다. **지원 패키지 만들기**를 선택 합니다. 시스템이 지원 패키지를 수집하기 시작합니다. 패키지 수집에는 몇 분 정도 걸릴 수 있습니다.

    ![사용자 추가 선택](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. 지원 패키지를 만든 후 **지원 패키지 다운로드**를 선택 합니다. 압축된 패키지가 선택한 경로에 다운로드됩니다. 패키지의 압축을 풀면 시스템 로그 파일을 볼 수 있습니다.

    ![사용자 추가 선택](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>고급 보안 로그 수집

고급 보안 로그는 Azure Stack Edge 장치에 대 한 소프트웨어 또는 하드웨어 침입 로그가 될 수 있습니다.

### <a name="software-intrusion-logs"></a>소프트웨어 침입 로그

인바운드 및 아웃 바운드 트래픽에 대 한 소프트웨어 침입 또는 기본 방화벽 로그가 수집 됩니다. 

- 장치가 공장에서 이미지를 사용 하는 경우 기본 방화벽 로깅이 사용 됩니다. 이러한 로그는 로컬 UI를 통해 또는 장치의 Windows PowerShell 인터페이스를 통해 지원 패키지를 만들 때 기본적으로 지원 패키지에 번들로 제공 됩니다.

- 지원 패키지에서 장치에 있는 모든 소프트웨어 (NW) 침입을 검토 하는 데 방화벽 로그만 필요한 경우 `-Include FirewallLog` 지원 패키지를 만들 때 옵션을 사용 합니다. 

- 특정 포함 옵션을 제공 하지 않으면 방화벽 로그가 지원 패키지에 기본값으로 포함 됩니다.

- 지원 패키지에서 방화벽 로그는이 `pfirewall.log` 고 루트 폴더에 있습니다. 다음은 Azure Stack Edge 장치에 대 한 소프트웨어 침입 로그의 예입니다. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>하드웨어 침입 로그

장치에서 하드웨어 침입을 감지 하기 위해 현재 섀시 열기 또는 닫기와 같은 모든 섀시 이벤트를 기록 합니다. 

- Cmdlet을 사용 하 여 장치의 시스템 이벤트 로그를 읽습니다 `racadm` . 그런 다음 이러한 이벤트가 파일의 섀시 관련 이벤트에 대해 필터링 됩니다 `HWIntrusion.txt` .

- 지원 패키지에서 하드웨어 침입 로그만 가져오려면 `-Include HWSelLog` 지원 패키지를 만들 때 옵션을 사용 합니다. 

- 특정 포함 옵션을 제공 하지 않으면 하드웨어 침입 로그가 지원 패키지에 기본값으로 포함 됩니다.

- 지원 패키지에서 하드웨어 침입 로그는 `HWIntrusion.txt` 이며 루트 폴더에 있습니다. 다음은 Azure Stack Edge 장치에 대 한 하드웨어 침입 로그의 예입니다. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>로그를 사용하여 문제 해결

업로드 및 새로 고침 프로세스 중에 발생한 모든 오류는 해당 오류 파일에 포함됩니다.

1. 오류 파일을 보려면 공유로 이동 하 여 콘텐츠를 볼 공유를 선택 합니다. 


2. _Microsoft Data Box Edge 폴더_를 선택 합니다. 이 폴더에는 두 개의 하위 폴더가 있습니다.

    - 업로드 오류에 대한 로그 파일이 있는 Upload 폴더
    - 새로 고침 중 오류를 위한 Refresh 폴더

    다음은 새로 고침에 대한 샘플 로그 파일입니다.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 이 파일에 오류가 표시되면(샘플에 강조 표시됨), 오류 코드를 적어두십시오. 이 경우에는 16001입니다. 다음 오류 참조에서 이 오류 코드에 대한 설명을 찾아봅니다.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>오류 목록을 사용 하 여 문제 해결

오류 목록은 식별 된 시나리오에서 컴파일되며 자체 진단 및 문제 해결에 사용할 수 있습니다. 

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

장치에 액세스 하 Azure Resource Manager 구성 중에 표시 될 수 있는 오류는 다음과 같습니다. 

| **문제/오류** |  **해결 방법** | 
|------------|-----------------|
|일반적인 문제|<li>[에 지 장치가 올바르게 구성 되어 있는지 확인](#verify-the-device-is-configured-properly)합니다.<li> [클라이언트가 올바르게 구성 되었는지 확인](#verify-the-client-is-configured-properly)|
|Get-azurermenvironment: 요청을 보내는 동안 오류가 발생 했습니다.<br>줄: 1 문자: 1<br>+ Get-azurermenvironment-Name Az3-ARMEndpoint " https://management.dbe ...|이 오류는 Azure Stack Edge 장치에 연결할 수 없거나 제대로 구성 되지 않았음을 의미 합니다. Edge 장치 및 클라이언트가 올바르게 구성 되어 있는지 확인 합니다. 지침은이 표의 **일반 문제** 행을 참조 하십시오.|
|서비스에서 오류를 반환 했습니다. 자세한 내용은 InnerException 확인: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다. |   이 오류는 하나 이상의 사용자 고유의 인증서 단계가 잘못 수행 된 것으로 인해 발생할 수 있습니다. [여기](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-connect-resource-manager#step-2-create-and-install-certificates)에서 지침을 찾을 수 있습니다. |
|작업에서 잘못 된 상태 코드 ' ServiceUnavailable 수 없음 '이 반환 되었습니다. <br> 응답 상태 코드는 성공: 503 (서비스를 사용할 수 없음)을 나타내지 않습니다. | 이러한 조건으로 인해이 오류가 발생할 수 있습니다.<li>ArmStsPool가 중지 된 상태입니다.</li><li>Azure Resource Manager/보안 토큰 서비스 웹 사이트 중 하나가 다운 되었습니다.</li><li>Azure Resource Manager 클러스터 리소스가 다운 되었습니다.</li><br><strong>참고:</strong> 어플라이언스를 다시 시작 하면 문제가 해결 될 수 있지만 추가로 디버그할 수 있도록 지원 패키지를 수집 해야 합니다.|
|AADSTS50126: 사용자 이름 또는 암호가 잘못 되었습니다.<br>추적 ID: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>상관 관계 ID: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>타임 스탬프: 2019-11-15 09:21:57Z: 원격 서버에서 오류를 반환 했습니다. (400) 잘못 된 요청입니다.<br>줄: 1 문자: 1 |이러한 조건으로 인해이 오류가 발생할 수 있습니다.<li>잘못 된 사용자 이름 및 암호의 경우 [여기](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-set-azure-resource-manager-password) 에 설명 된 단계를 수행 하 고 올바른 암호를 사용 하 여 고객이 Azure Portal 암호를 변경 했는지 확인 합니다.<li>테 넌 트 ID가 잘못 된 경우 테 넌 트 ID는 고정 GUID 이며로 설정 되어야 합니다. `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-azurermaccount: AADSTS90056: 리소스가 비활성화 되었거나 존재 하지 않습니다. 앱 코드를 확인하여 액세스하려는 리소스의 정확한 리소스 URL을 지정했는지 확인하세요.<br>추적 ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>상관 관계 ID: 75c8ef5a-830e-48b5-b039-595a96488ff9 Timestamp: 2019-11-18 07:00:51Z: 원격 서버에서 오류를 반환 했습니다. (400) 잘못 됨 |명령에 사용 된 리소스 끝점이 `Add-AzureRmEnvironment` 잘못 되었습니다.|
|클라우드에서 끝점을 가져올 수 없습니다.<br>네트워크에 연결 되어 있는지 확인 하세요. 오류 세부 정보: HTTPSConnectionPool (host = ' of4k6suvm.microsoftdatabox.com ', port = 30005): url을 사용 하 여 최대 재시도 횟수를 초과 했습니다. api-version = 2015-01-01 (SSLError (SSLError ("잘못 된 핸드셰이크: Error ([(' SSL 루틴 ', ' tls_process_server_certificate ', ' certificate verify failed ')],)",)) |이 오류는 주로 Mac/Linux 환경에 표시 되며 다음과 같은 문제로 인해 발생 합니다.<li>PEM 형식 인증서가 python 인증서 저장소에 추가 되지 않았습니다.</li> |

### <a name="verify-the-device-is-configured-properly"></a>장치가 올바르게 구성 되었는지 확인 합니다.

1. 로컬 UI에서 장치 네트워크가 올바르게 구성 되어 있는지 확인 합니다.

2. [여기](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)에 설명 된 대로 모든 끝점에 대 한 인증서가 업데이트 되었는지 확인 합니다.

3. 로컬 UI의 **장치** 페이지에서 Azure Resource Manager 관리 및 로그인 끝점을 가져옵니다.

4. 장치가 활성화 되 고 Azure에 등록 되었는지 확인 합니다.


### <a name="verify-the-client-is-configured-properly"></a>클라이언트가 올바르게 구성 되어 있는지 확인 합니다.

1. [여기](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client)에 설명 된 대로 올바른 PowerShell 버전이 설치 되어 있는지 확인 합니다.

2. [여기](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)에 설명 된 대로 올바른 PowerShell 모듈이 설치 되어 있는지 확인 합니다.

3. Azure Resource Manager 및 로그인 끝점에 연결할 수 있는지 확인 합니다. 끝점에 대 한 ping을 시도할 수 있습니다. 예를 들면 다음과 같습니다.

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   연결할 수 없는 경우 [여기](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)에 설명 된 대로 DNS/호스트 파일 항목을 추가 합니다.
   
4. [여기](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)에 설명 된 대로 클라이언트 인증서가 설치 되어 있는지 확인 합니다.

5. 고객이 PowerShell을 사용 하는 경우이 PowerShell 명령을 실행 하 여 자세한 메시지를 보려면 디버그 기본 설정을 사용 하도록 설정 해야 합니다. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>장치에서 Blob Storage 

Azure Stack Edge/Data Box Gateway 장치의 blob 저장소와 관련 된 오류는 다음과 같습니다.

| **문제/오류** |  **해결 방법** | 
|--------------------|-----------------|
|자식 리소스를 검색할 수 없습니다. HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.| **편집** 메뉴에서 **대상 Azure Stack api**를 선택 합니다. 그런 다음 Azure Storage 탐색기를 다시 시작 합니다.|
|getaddrinfo ENOTFOUND <accountname> . <serialnumber> microsoftdatabox.com|끝점 이름이 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 이 경로 ( `C:\Windows\System32\drivers\etc\hosts` Windows 또는 Linux)의 호스트 파일에 추가 되었는지 확인 합니다. `/etc/hosts`|
|자식 리소스를 검색할 수 없습니다.<br> 세부 정보: 자체 서명 된 인증서 |장치에 대 한 SSL 인증서를 Azure Storage 탐색기으로 가져옵니다. <ol><li>Azure Portal에서 인증서를 다운로드 합니다. 자세한 내용은 [인증서 다운로드](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)를 참조 하세요.</li><li>**편집** 메뉴에서 SSL 인증서를 선택한 다음 **인증서 가져오기**를 선택 합니다.</li></ol>|
|이 오류를 표시 하기 전에 AzCopy 명령이 1 분 동안 중단 된 것으로 나타납니다.<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|끝점 이름이의 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 호스트 파일에 추가 되었는지 확인 `C:\Windows\System32\drivers\etc\hosts` 합니다.|
|이 오류를 표시 하기 전에 AzCopy 명령이 1 분 동안 중단 된 것으로 나타납니다.<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |장치의 SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)를 참조 하세요.|
|이 오류를 표시 하기 전에 AzCopy 명령이 20 분 동안 중단 되는 것으로 나타납니다.<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |끝점 이름이의 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 호스트 파일에 추가 되었는지 확인 `/etc/hosts` 합니다.|
|이 오류를 표시 하기 전에 AzCopy 명령이 20 분 동안 중단 되는 것으로 나타납니다.<br>`Error parsing source location… The SSL connection could not be established`. |장치의 SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)를 참조 하세요.|
|이 오류를 표시 하기 전에 AzCopy 명령이 20 분 동안 중단 되는 것으로 나타납니다.<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|끝점 이름이의 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 호스트 파일에 추가 되었는지 확인 `/etc/hosts` 합니다.|
|이 오류를 표시 하기 전에 AzCopy 명령이 20 분 동안 중단 되는 것으로 `Error parsing source location… The SSL connection could not be established` 나타납니다.|장치의 SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)를 참조 하세요.|
|HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.|Data Box에서 Python 용 Microsoft Azure Storage 라이브러리의 설치 된 버전이 지원 되지 않습니다. 지원 되는 버전에 대 한 Azure Data Box Blob 저장소 요구 사항을 참조 하세요.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...| Python을 실행 하기 전에 REQUESTS_CA_BUNDLE 환경 변수를 b a s e 64로 인코딩된 SSL 인증서 파일의 경로로 설정 합니다. [인증서를 다운로드](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#download-certificate)하는 방법을 참조 하세요. 예를 들면 다음과 같습니다.<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>또는 시스템의 인증서 저장소에 인증서를 추가한 다음이 환경 변수를 해당 저장소의 경로로 설정 합니다. 예를 들어 Ubuntu에서 <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|연결 시간이 초과 되었습니다.|Azure Stack Edge에 로그인 한 후 잠금이 해제 되었는지 확인 합니다. 장치가 다시 시작 될 때마다 사용자가 로그인 할 때까지 잠긴 상태로 유지 됩니다.|


## <a name="next-steps"></a>다음 단계

- [이 릴리스의 알려진 문제](azure-stack-edge-gpu-2008-release-notes.md)에 대해 알아봅니다.
