---
title: Azure에서 Windows 가상 머신 정품 인증 문제 해결 | Microsoft Docs
description: Azure에서 Windows 가상 머신 정품 인증 문제를 해결하기 위한 문제 해결 단계를 제공합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 18cd5a86cc2f52567c5f320719d1a9f21b377ed4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921279"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Windows Azure 가상 컴퓨터 정품 인증 문제 해결

사용자 지정 이미지에서 만든 Azure Windows VM(가상 머신)을 활성화하는 데 문제가 발생하는 경우 문제를 해결하려면 이 문서에 제공된 정보를 사용할 수 있습니다. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Azure Virtual Machines의 Windows 제품 정품 인증을 위한 Azure KMS 엔드포인트 이해

Azure는 VM이 상주하는 클라우드 영역에 따라 KMS 정품 인증에 다양한 엔드포인트를 사용합니다. 이 문제 해결 가이드를 사용하는 경우 사용자의 지역에 해당하는 적절한 KMS 엔드포인트를 사용합니다.

* Azure 공용 클라우드 지역: kms.core.windows.net:1688
* Azure 중국 21Viant 국가 클라우드 지역: kms.core.chinacloudapi.cn:1688
* Azure 독일 국가 클라우드 지역: kms.core.cloudapi.de:1688
* Azure 미국 정부 국가 클라우드 지역: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>증상

Windows Azure VM을 활성화하려고 할 때 다음 샘플과 유사한 오류 메시지가 표시됩니다.

**오류: 0xC004F074 소프트웨어 라이선싱 서비스에서 컴퓨터를 정품 인증할 수 없다고 보고되었습니다. KMS(Key Management Service)를 연결할 수 없습니다. 추가 정보는 애플리케이션 이벤트 로그를 참조하세요.**

## <a name="cause"></a>원인

일반적으로 Azure VM 정품 인증 문제는 Windows VM이 적절한 KMS 클라이언트 설정 키를 사용하여 구성되어 있지 않거나 Windows VM에 Azure KMS 서비스(kms.core.windows.net, 포트 1688)에 대한 연결 문제가 있는 경우에 발생합니다. 

## <a name="solution"></a>해결 방법

>[!NOTE]
>사이트 간 VPN 및 강제 터널링을 사용하는 경우 [강제 터널링을 사용하여 KMS 정품 인증을 활성화하도록 Azure 사용자 지정 경로 사용](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx)을 참조하세요. 
>
>ExpressRoute를 사용하고 기본 경로가 게시된 경우 [Azure VM이 ExpressRoute를 통해 활성화되지 못했습니다.](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx)를 참조하세요.

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>1단계 적절한 KMS 클라이언트 설정 키 구성(Windows Server 2016 및 Windows Server 2012 R2용)

Windows Server 2016 또는 Windows Server 2012 R2의 사용자 지정 이미지에서 만든 VM의 경우 VM에 적절한 KMS 클라이언트 설정 키를 구성해야 합니다.

이 단계는 Windows 2012 또는 Windows 2008 R2에는 적용되지 않습니다. Windows Server 2016 및 Windows Server 2012 R2에서만 지원되는 AVMA(Automation Virtual Machine Activation) 기능을 사용합니다.

1. 관리자 권한의 명령 프롬프트에서 **slmgr.vbs /dlv**를 실행합니다. 출력에서 설명 값을 확인하고 소매(소매 채널) 또는 볼륨(VOLUME_KMSCLIENT) 라이선스 미디어에서 만들어졌는지를 확인합니다.
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. **slmgr.vbs /dlv**가 소매 채널을 표시하는 경우 다음 명령을 실행하여 사용된 Windows Server의 버전에 [KMS 클라이언트 설정 키](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396)를 설정하고 활성화를 다시 시도합니다. 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    예를 들어 Windows Server 2016 데이터 센터에서 다음 명령을 실행합니다.

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>2단계 VM과 Azure KMS 서비스 간의 연결 확인

1. [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) 도구를 활성화하지 않는 VM의 로컬 폴더에 다운로드하고 압축합니다. 

2. 시작하고 Windows PowerShell에서 검색하고 Windows PowerShell을 마우스 오른쪽 단추로 클릭한 다음 관리자 권한으로 실행을 선택합니다.

3. VM이 올바른 Azure KMS 서버를 사용하도록 구성되어 있는지 확인합니다. 이렇게 하려면 다음 명령을 실행합니다.
  

    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    명령은 다음과 같은 내용을 반환합니다. Key Management Service 머신 이름이 kms.core.windows.net:1688로 설정되었습니다.

4. KMS 서버에 연결한 Psping을 사용하여 확인합니다. Pstools.zip 다운로드를 추출한 폴더로 전환하고 다음을 실행합니다.
  

    ```
    \psping.exe kms.core.windows.net:1688
    ```

  
   출력의 끝에서 두 번째 줄에 전송 = 4, 수신 = 4, 손실 = 0(0% 손실)이 표시되어야 합니다.

   손실이 0(영)보다 큰 경우 VM은 KMS 서버에 연결되어 있지 않습니다. 이 경우에 VM이 가상 네트워크에 있고 사용자 지정 DNS 서버를 지정하면 해당 DNS 서버가 kms.core.windows.net을 확인할 수 있어야 합니다. 또는 DNS 서버가 kms.core.windows.net을 확인할 수 있도록 변경합니다.

   모든 DNS 서버를 가상 네트워크에서 제거하면 VM은 Azure의 내부 DNS 서비스를 사용할 수 있습니다. 이 서비스는 kms.core.windows.net을 확인할 수 있습니다.
  
또한 게스트 방화벽이 정품 인증 시도를 차단하는 방식으로 구성되어 있는지 확인합니다.

1. kms.core.windows.net에 성공적으로 연결되었는지 확인한 후에 해당 관리자 권한 Windows PowerShell 프롬프트에서 다음 명령을 실행합니다. 이 명령은 여러 번 활성화되도록 시도합니다.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

성공적으로 활성화되면 다음과 같은 정보를 반환합니다.

**Windows(R), ServerDatacenter 버전 활성화(12345678-1234-1234-1234-12345678) … 제품을 성공적으로 활성화했습니다.**

## <a name="faq"></a>FAQ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Azure Marketplace에서 Windows Server 2016을 만들었습니다. Windows Server 2016을 활성화하기 위해 KMS 키를 구성해야 하나요? 

 
아니요. Azure Marketplace의 이미지는 적절한 KMS 클라이언트 설정 키를 이미 구성했습니다. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>VM이 Azure HUB(Hybrid Use Benefit)를 사용하는지 여부와 상관없이 동일하게 Windows 정품 인증이 작동하나요? 

 
예. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Windows 정품 인증 기간이 만료된 경우 어떻게 되나요? 

 
유예 기간이 만료되고 Windows가 여전히 활성화되지 않은 경우 Windows Server 2008 R2 및 Windows 이후 버전은 정품 인증에 대한 추가 알림을 표시합니다. 바탕 화면 배경 화면을 검은색으로 유지하고 Windows 업데이트를 통해 보안 및 중요 업데이트만을 설치하고 선택 사항 업데이트를 설치하지 않습니다. [라이선스 조건](https://technet.microsoft.com/library/ff793403.aspx) 페이지의 맨 아래에 있는 알림 섹션을 참조하세요.   

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.