---
title: Microsoft Azure Stack 허브 준비 검사기 도구를 사용 하 여 인증서 만들기 | Microsoft Docs
description: Azure Stack Hub 준비 검사 도구를 사용 하 여 인증서 요청을 만든 다음 Azure Stack Edge GPU 장치에서 인증서를 가져오고 설치 하는 방법을 설명 합니다.
services: Azure Stack Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 7a6cf265f0be177aab436d544e694c5d59cfffd5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267380"
---
# <a name="create-certificates-for-your-azure-stack-edge-using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack 허브 준비 검사 도구를 사용 하 여 Azure Stack에 지에 대 한 인증서 만들기 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Stack Hub 준비 검사 도구를 사용 하 여 Azure Stack에 지에 대 한 인증서를 만드는 방법을 설명 합니다. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack 허브 준비 검사기 도구 사용

Azure Stack 허브 준비 검사기 도구를 사용 하 여 Azure Stack Edge 장치 배포에 대 한 Csr (인증서 서명 요청)을 만듭니다. Azure Stack Edge 장치에 대 한 주문을 배치 하 고 장치가 도착할 때까지 기다리는 후 이러한 요청을 만들 수 있습니다. 

> [!NOTE]
> 프로덕션 장치가 아닌 테스트 또는 개발 목적 으로만이 도구를 사용 합니다. 

Azure Stack 허브 준비 검사기 도구 (AzsReadinessChecker)를 사용 하 여 다음 인증서를 요청할 수 있습니다.

- 인증서 Azure Resource Manager
- 로컬 UI 인증서
- 노드 인증서
- Blob 인증서
- VPN 인증서


## <a name="prerequisites"></a>필수 구성 요소

Azure Stack Edge 장치 배포에 대 한 Csr을 만들려면 다음을 확인 합니다. 

- Windows 10 또는 Windows Server 2016 이상을 실행 하는 클라이언트가 있습니다. 
- 이 시스템의 [PowerShell 갤러리에서](https://aka.ms/AzsReadinessChecker) Microsoft Azure Stack 허브 준비 검사기 도구 1.2002.1133.85를 다운로드 했습니다. 이 패키지를 검색 해야 할 수 있습니다. 이 버전의 도구만 Azure Stack Edge 장치에 대 한 인증서를 만들 수 있습니다.
- 인증서에 대 한 다음 정보가 있습니다.
  - 디바이스 이름
  - 노드 일련 번호
  - 외부 FQDN (정규화 된 도메인 이름)

## <a name="generate-certificate-signing-requests"></a>인증서 서명 요청 생성

다음 단계를 사용 하 여 Azure Stack Edge 장치 인증서를 준비 합니다.

1. 관리자 권한으로 PowerShell을 실행 합니다 (5.1 이상).
2. Azure Stack 허브 준비 검사기 도구를 설치 합니다. PowerShell 프롬프트에서 다음을 입력 합니다. 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker -RequiredVersion 1.2002.1133.85 -Force
    ```

    설치 된 버전을 확인 하려면 다음을 입력 합니다.  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. 모든 인증서가 없는 경우 디렉터리를 만듭니다. 유형: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. 인증서 요청을 만들려면 다음 정보를 제공 합니다. VPN 인증서를 생성 하는 경우 이러한 입력 중 일부가 적용 되지 않습니다. 
    
    |입력 |Description  |
    |---------|---------|
    |`OutputRequestPath`|인증서 요청을 만들려는 로컬 클라이언트의 파일 경로입니다.        |
    |`DeviceName`|장치의 로컬 웹 UI에 있는 장치 **페이지의** 장치 이름입니다. <br> 이 필드는 VPN 인증서에는 필요 하지 않습니다.         |
    |`NodeSerialNumber`|장치의 로컬 웹 UI에 있는 **네트워크** 페이지에 있는 장치 노드의 일련 번호입니다. <br> 이 필드는 VPN 인증서에는 필요 하지 않습니다.       |
    |`ExternalFQDN`|장치의 로컬 웹 UI에 있는 **장치** 페이지의 DNSDomain 값입니다.         |
    |`RequestType`|요청 형식은 다양 한 끝점에 대해 서로 다른 인증서가 될 수도 있고 `MultipleCSR` , `SingleCSR` 모든 끝점에 대 한 단일 인증서 일 수도 있습니다. <br> 이 필드는 VPN 인증서에는 필요 하지 않습니다.     |

    VPN 인증서를 제외한 모든 인증서에 대해 다음을 입력 합니다. 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    VPN 인증서를 만들려면 다음을 입력 합니다. 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. 위의 OutputRequestPath 매개 변수에서 지정한 디렉터리 아래의 인증서 요청 파일을 찾을 수 있습니다. 매개 변수를 사용 하는 경우 `MultipleCSR` 확장명이 인 4 개의 파일이 표시 됩니다 `.req` . 파일은 다음과 같습니다.

    
    |파일 이름  |인증서 요청 유형  |
    |---------|---------|
    |부터 `DeviceName`     |로컬 웹 UI 인증서 요청      |
    |부터 `NodeSerialNumber`     |노드 인증서 요청         |
    |`login`로 시작     |Azure Resource Manager 끝점 인증서 요청       |
    |`wildcard`로 시작     |Blob storage 인증서 요청 여기에는 장치에서 만들 수 있는 모든 저장소 계정을 포함 하기 때문에 와일드 카드가 포함 되어 있습니다.          |
    |`AzureStackEdgeVPNCertificate`로 시작     |VPN 클라이언트 인증서 요청.         |

    또한 INF 폴더도 표시 됩니다. 여기에는 관리 기능이 포함 됩니다. 인증서 세부 정보를 설명 하는 일반 텍스트로 edge-장치> 정보 파일을 <합니다.  


6. 이러한 파일을 인증 기관 (내부 또는 공용)에 제출 합니다. CA는 생성 된 요청을 사용 하 여 [노드 인증서](azure-stack-edge-j-series-manage-certificates.md#node-certificates), [끝점 인증서](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)및 [로컬 UI 인증서](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates)에 대 한 Azure Stack Edge 인증서 요구 사항을 충족 하는 인증서를 생성 해야 합니다.

## <a name="prepare-certificates-for-deployment"></a>배포용 인증서 준비

CA (인증 기관)에서 가져오는 인증서 파일은에 지 장치의 인증서 요구 사항과 Azure Stack 일치 하는 속성을 사용 하 여 가져오고 내보내야 합니다. 인증서 서명 요청을 생성 한 동일한 시스템에서 다음 단계를 완료 합니다.

- 인증서를 가져오려면 [Azure Stack Edge 장치에 액세스 하는 클라이언트에서 인증서 가져오기](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)의 단계를 수행 합니다.

- 인증서를 내보내려면 [클라이언트에서 Azure Stack Edge 장치에 액세스 하는 인증서 내보내기](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)의 단계를 따릅니다.


## <a name="validate-certificates"></a>인증서 유효성 검사

먼저 적절 한 폴더 구조를 생성 하 고 해당 폴더에 인증서를 저장 합니다. 그런 다음 도구를 사용 하 여 인증서의 유효성을 검사 합니다.

1. 관리자 권한으로 PowerShell을 실행합니다.

2. 적절 한 폴더 구조를 생성 하려면 프롬프트에 다음을 입력 합니다.

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. PFX 암호를 보안 문자열로 변환 합니다. 유형:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. 그런 다음 인증서의 유효성을 검사 합니다. 유형:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 장치 배포](azure-stack-edge-gpu-deploy-prep.md)
