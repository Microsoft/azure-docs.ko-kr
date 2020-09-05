---
title: Azure Stack Edge GPU 장치에 액세스 하는 Windows 클라이언트에서 TLS 1.2 구성
description: Azure Stack Edge GPU 장치에 액세스 하는 Windows 클라이언트에서 TLS 1.2를 구성 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: bf6b591ef3158a5944b1ebeb37dd0ef5935f7215
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268505"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-device"></a>Windows 클라이언트에서 Azure Stack Edge 장치에 액세스 하는 TLS 1.2 구성

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Windows 클라이언트를 사용 하 여 Azure Stack Edge 장치에 액세스 하는 경우 클라이언트에서 TLS 1.2을 구성 해야 합니다. 이 문서에서는 Windows 클라이언트에서 TLS 1.2를 구성 하기 위한 리소스 및 지침을 제공 합니다. 

여기에 제공 된 지침은 Windows Server 2016를 실행 하는 클라이언트에서 수행 된 테스트를 기반으로 합니다.

## <a name="configure-tls-12-for-current-powershell-session"></a>현재 PowerShell 세션에 대해 TLS 1.2 구성

클라이언트에서 TLS 1.2을 구성 하려면 다음 단계를 수행 합니다.

1. 관리자 권한으로 PowerShell을 실행합니다.
2. 현재 PowerShell 세션에 대해 TLS 1.2을 설정 하려면 다음을 입력 합니다.
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>클라이언트에서 TLS 1.2 구성

사용자 환경에 대 한 시스템 수준 TLS 1.2을 설정 하려면 다음 문서의 지침을 따르세요.

- [일반-TLS 1.2을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- [클라이언트에서의 TLS 1.2 사용 설정 방법](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [사이트 서버 및 원격 사이트 시스템에서의 TLS 1.2 사용 방법](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [TLS/SSL의 프로토콜 (Schannel SSP)](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [암호 그룹](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12): 특히 [TLS 암호 그룹 순서를 구성](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) 하면 현재 암호 그룹을 나열 하 고 다음 목록에 누락 된 항목이 추가 되었는지 확인 합니다.

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    레지스트리 설정을 직접 편집 하 여 이러한 암호 그룹을 추가할 수도 있습니다.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- 타원 곡선을 설정 하는 방법

    현재 타원 곡선을 나열 하 고 다음 목록에서 누락 된 것 앞에 추가 해야 합니다.

    - P-256 
    - P-384

    레지스트리 설정을 직접 편집 하 여 이러한 원형 곡선을 추가할 수도 있습니다.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [최소 RSA 키 교환 크기를 2048으로 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes)합니다.



## <a name="next-steps"></a>다음 단계

[Azure Resource Manager에 연결](azure-stack-edge-j-series-connect-resource-manager.md)