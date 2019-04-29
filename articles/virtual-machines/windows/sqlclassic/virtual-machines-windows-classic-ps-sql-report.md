---
title: PowerShell을 사용하여 기본 모드 보고서 서버로 VM 만들기 | Microsoft Docs
description: '이 항목에서는 Azure Virtual Machine에서 SQL Server Reporting Services 기본 모드 보고서 서버의 배포 및 구성에 대해 설명하고 안내합니다. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: 6339b49d0bc9c635457f305dad7b1a075327a1dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609946"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>PowerShell을 사용하여 기본 모드 보고서 서버로 Azure VM 만들기
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

이 항목에서는 Azure Virtual Machine에서 SQL Server Reporting Services 기본 모드 보고서 서버의 배포 및 구성에 대해 설명하고 안내합니다. 이 문서의 단계는 가상 머신 및 Windows PowerShell 스크립트를 만드는 수동 단계를 조합하여 VM에서 Reporting Services를 구성합니다. 구성 스크립트에는 HTTP 또는 HTTPS에 대해 방화벽 포트를 여는 작업이 포함되어 있습니다.

> [!NOTE]
> 보고서 서버에서 **HTTPS**가 필요하지 않은 경우 **2단계를 건너뜁니다**.
> 
> 1단계에서 VM을 만든 후 스크립트를 사용하여 보고서 서버 및 HTTP 구성 섹션으로 이동합니다. 스크립트를 실행하면 보고서 서버를 사용할 준비가 된 것입니다.

## <a name="prerequisites-and-assumptions"></a>필수 조건 및 가정
* **Azure 구독**: Azure 구독에서 사용 가능한 코어 수를 확인합니다. 권장되는 VM 크기인 **A3**을 만드는 경우 **4**개의 사용 가능한 코어가 필요합니다. **A2**의 VM 크기를 사용하는 경우 **2**개의 사용 가능한 코어가 필요합니다.
  
  * 구독의 코어 제한을 확인하려면, Azure Portal의 왼쪽 창에서 설정을 클릭하고 위쪽 메뉴에서 사용을 클릭합니다.
  * 코어 할당량을 늘리려면 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의하세요. VM 크기 정보는 [Azure에 대한 Virtual Machine 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* **Windows PowerShell 스크립팅**: 이 항목에서는 Windows PowerShell의 기본 실무 지식이 있다고 가정합니다. Windows PowerShell을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.
  
  * [Windows Server에서 Windows PowerShell 시작](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Windows PowerShell 시작](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>1단계: Azure Virtual Machine 프로비전
1. Azure Portal을 찾아봅니다.
2. 왼쪽 창에서 **Virtual Machines**를 클릭합니다.
   
    ![Microsoft Azure 가상 머신](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. **새로 만들기**를 클릭합니다.
   
    ![새 단추](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. **갤러리에서**를 클릭합니다.
   
    ![갤러리의 새 VM](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. **SQL Server 2014 RTM 표준 – Windows Server 2012 R2** 를 클릭한 다음 화살표를 클릭하여 계속합니다.
   
    ![다음](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Reporting Services 데이터 기반 구독 기능이 필요한 경우 **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**를 선택합니다. SQL Server 버전 및 기능 지원에 대한 자세한 내용은 [SQL Server 2012 버전에서 지원하는 기능](https://msdn.microsoft.com/library/cc645993.aspx#Reporting)을 참조하세요.
6. **가상 머신 구성** 페이지에서 다음 필드를 편집합니다.
   
   * **버전 릴리스 날짜**가 둘 이상 있는 경우 가장 최신 버전을 선택합니다.
   * **가상 머신 이름**: 머신 이름은 다음 구성 페이지에서 기본 클라우드 서비스 DNS 이름으로도 사용됩니다. Azure 서비스에서 DNS 이름은 고유해야 합니다. VM의 용도에 대해 설명하는 컴퓨터 이름을 사용하여 VM을 구성하는 것이 좋습니다. 예를 들어 ssrsnativecloud입니다.
   * **계층**: Standard
   * **크기: A3** 은 SQL Server 작업에 권장되는 VM 크기입니다. VM이 보고서 서버로만 사용되는 경우 보고서 서버의 작업이 크지 않는 한 VM 크기는 A2이면 충분합니다. VM 가격 책정 정보는 [Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)을 참조하세요.
   * **새 사용자 이름**: 제공하는 이름은 VM에서 관리자로 만들어집니다.
   * **새 암호** 및 **확인**. 이 암호는 새 관리자 계정에 대해 사용되므로 강력한 암호를 사용하는 것이 좋습니다.
   * **다음**을 클릭합니다. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. 다음 페이지에서 다음 필드를 편집합니다.
   
   * **클라우드 서비스**: **새 클라우드 서비스 만들기**를 선택합니다.
   * **클라우드 서비스 DNS 이름**: VM과 연결된 클라우드 서비스의 공용 DNS 이름입니다. 기본 이름은 VM 이름에 입력한 이름입니다. 이 항목의 이후 단계에서 신뢰할 수 있는 SSL 인증서를 만들면 DNS 이름이 인증서의 “**발급 대상**” 값에 사용됩니다.
   * **지역/선호도 그룹/Virtual Network**: 최종 사용자에게 가장 가까운 지역을 선택합니다.
   * **스토리지 계정**: 자동으로 생성된 스토리지 계정을 사용합니다.
   * **가용성 집합**: 없음.
   * **엔드포인트** **원격 데스크톱** 및 **PowerShell** 엔드포인트를 그대로 유지한 다음 사용자 환경에 따라 HTTP 또는 HTTPS 엔드포인트를 추가합니다.
     
     * **HTTP**: 기본 공용 포트 및 개인 포트는 **80**입니다. 80 이외의 개인 포트를 사용하는 경우 HTTP 스크립트에서 **$HTTPport = 80** 을 수정합니다.
     * **HTTPS**: 기본 공용 포트 및 개인 포트는 **443**입니다. 보안 모범 사례는 개인 포트를 변경하고 개인 포트를 사용하도록 방화벽 및 보고서 서버를 구성하는 것입니다. 엔드포인트에 대한 자세한 내용은 [Virtual Machine으로 엔드포인트를 설정하는 방법](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요. 443 이외의 포트를 사용하는 경우 HTTPS 스크립트에서 매개 변수 **$HTTPsport = 443** 을 변경합니다.
   * 다음을 클릭합니다. ![다음](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. 마법사의 마지막 페이지에서 기본 **VM 에이전트 설치** 를 선택한 상태로 유지합니다. 이 항목의 단계에서 VM 에이전트를 이용하지 않지만 이 VM을 유지하려는 경우 VM 에이전트 및 확장을 사용하면 CM이 향상됩니다.  VM 에이전트에 대한 자세한 내용은 [VM 에이전트 및 확장 – 1부](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/)를 참조하세요. AD 실행을 설치한 기본 확장 중 하나가 VM 데스크톱에서 내부 IP 및 여유 드라이브 공간 같은 시스템 정보를 표시하는 “BGINFO” 확장입니다.
9. 완료를 클릭합니다. ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. VM의 **상태**는 프로비전 프로세스 중에는 **시작 중(프로비전 중)** 으로 표시되다가 VM이 프로비전되고 사용할 준비가 되면 **실행 중**으로 표시됩니다.

## <a name="step-2-create-a-server-certificate"></a>2단계: 서버 인증서 만들기
> [!NOTE]
> 보고서 서버에서 HTTPS가 필요하지 않은 경우 **2단계를 건너뛰고** **스크립트를 사용하여 보고서 서버 및 HTTP 구성** 섹션으로 이동합니다. HTTP 스크립트를 사용하여 신속하게 보고서 서버를 구성하면 보고서 서버를 사용할 준비가 된 것입니다.

VM에서 HTTPS를 사용하려면 신뢰할 수 있는 SSL 인증서가 필요합니다. 시나리오에 따라 다음 두 방법 중 하나를 사용할 수 있습니다.

* CA(인증 기관)에서 발급하고 Microsoft에서 신뢰하는 유효한 SSL 인증서. CA 루트 인증서는 Microsoft 루트 인증서 프로그램을 통해 배포되어야 합니다. 이 프로그램에 대한 자세한 내용은 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(구성원 CA)](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) 및 [Microsoft 루트 인증서 프로그램 소개](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx)를 참조하세요.
* 자체 서명된 인증서. 자체 서명된 인증서는 프로덕션 환경에 권장되지 않습니다.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>신뢰할 수 있는 CA(인증 기관)에서 만든 인증서를 사용하려면
1. **인증 기관에서 웹 사이트에 대한 서버 인증서를 요청합니다**. 
   
    웹 서버 인증서 마법사를 사용하여 인증 기관에 보내는 인증서 요청 파일(Certreq.txt)을 생성하거나 온라인 인증 기관에 대한 요청을 생성할 수 있습니다. 예를 들어 Windows Server 2012의 Microsoft 인증서 서비스입니다. 서버 인증서에서 제공하는 식별 보증 수준에 따라 인증 기관이 요청을 승인하고 인증서 파일을 보내는 데 며칠에서 몇 개월까지 걸립니다. 
   
    서버 인증서를 요청하는 방법에 대한 자세한 내용은 다음을 참조하세요. 
   
   * [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx) 사용.
   * Windows Server 2012를 관리하는 보안 도구
     
     [Windows Server 2012를 관리하는 보안 도구](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > 신뢰할 수 있는 SSL 인증서의 **발급 대상** 필드는 새 VM에 사용한 **클라우드 서비스 DNS 이름**과 동일해야 합니다.

2. **웹 서버에 서버 인증서를 설치합니다**. 이 경우의 웹 서버는 보고서 서버를 호스트하는 VM이고 Reporting Services를 구성할 때 이후 단계에서 웹 사이트가 만들어집니다. 인증서 MMC 스냅인을 사용하여 웹 서버에 서버 인증서를 설치하는 방법에 대한 자세한 내용은 [서버 인증서 설치](https://technet.microsoft.com/library/cc740068)를 참조하세요.
   
    이 항목에 포함된 스크립트를 사용하여 보고서 서버를 구성하려면 스크립트의 매개 변수로 인증서 **지문** 의 값이 필요합니다. 인증서 지문을 가져오는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.
3. 보고서 서버에 서버 인증서를 할당합니다. 할당은 다음 섹션에서 보고서 서버를 구성할 때 완료됩니다.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Virtual Machines의 자체 서명된 인증서를 사용하려면
VM이 프로비전되었을 때 자체 서명된 인증서가 VM에 만들어졌습니다. 인증서 이름은 VM DNS 이름과 동일합니다. 인증서 오류를 방지하려면 VM 자체에서 뿐만 아니라 사이트의 모든 사용자도 인증서를 신뢰해야 합니다.

1. 로컬 VM에서 인증서의 루트 CA를 신뢰하려면 인증서를 **신뢰할 수 있는 루트 인증 기관**에 추가합니다. 다음은 필요한 단계에 대한 요약입니다. CA를 신뢰하는 방법에 대한 자세한 단계는 [서버 인증서 설치](https://technet.microsoft.com/library/cc740068)를 참조하세요.
   
   1. Azure Portal에서 VM을 선택하고 연결을 클릭합니다. 브라우저 구성에 따라 VM에 연결하기 위해 .rdp 파일을 저장하라는 메시지가 표시될 수 있습니다.
      
       ![Azure 가상 머신에 연결](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) VM을 만들 때 구성한 사용자 VM 이름, 사용자 이름 및 암호를 사용합니다. 
      
       예를 들어 다음 이미지에서 VM 이름은 **ssrsnativecloud**이고 사용자 이름은 **testuser**입니다.
      
       ![로그인에 vm 이름이 포함 되어 있습니다.](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Mmc.exe를 실행합니다. 자세한 내용은 [방법: MMC 스냅인을 사용하여 인증서 보기](https://msdn.microsoft.com/library/ms788967.aspx)를 참조하세요.
   3. 콘솔 애플리케이션 **파일** 메뉴에서 **인증서** 스냅인을 추가하고 메시지가 표시되면 **컴퓨터 계정**을 선택한 후 **다음**을 클릭합니다.
   4. 관리할 **로컬 컴퓨터**를 선택한 후 **마침**을 클릭합니다.
   5. **확인**을 클릭한 다음 **인증서 - 개인** 노드를 확장한 다음 **인증서**를 클릭합니다. 인증서 이름은 VM의 DNS 이름을 따서 지정되고 **cloudapp.net**으로 끝납니다. 인증서 이름을 마우스 오른쪽 단추로 클릭하고 **복사**를 클릭합니다.
   6. **T신뢰할 수 있는 루트 인증 기관** 노드를 확장한 다음 **인증서**를 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 클릭합니다.
   7. 유효성을 검사하려면 **신뢰할 수 있는 루트 인증 기관** 에서 인증서 이름을 두 번 클릭하여 오류가 없는지, 인증서가 표시되는지 확인합니다. 이 항목에 포함된 HTTPS 스크립트를 사용하여 보고서 서버를 구성하려면 스크립트의 매개 변수로 인증서 **지문** 의 값이 필요합니다. **지문 값을 가져오려면**다음을 완료합니다. 또한 [스크립트를 사용하여 보고서 서버 및 HTTPS 구성](#use-script-to-configure-the-report-server-and-https)섹션에 지문을 검색하는 PowerShell 샘플도 있습니다.
      
      1. 인증서의 이름(예: Ssrsnativecloud.cloudapp.net)을 두 번 클릭합니다.
      2. **세부 정보** 탭을 클릭합니다.
      3. 왼쪽 창에서 **지문**. 지문 값이 세부 정보 필드에 표시됩니다. 예를 들어 ‎a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f입니다.
      4. 지문을 복사하고 나중을 위해 값을 저장하거나 지금 스크립트를 편집합니다.
      5. (*) 스크립트를 실행하기 전에 값 쌍 사이의 공백을 제거합니다. 예를 들어 앞에서 설명한 지문은 이제 ‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f가 됩니다.
      6. 보고서 서버에 서버 인증서를 할당합니다. 할당은 다음 섹션에서 보고서 서버를 구성할 때 완료됩니다.

자체 서명된 SSL 인증서를 사용하는 경우 인증서 이름이 이미 VM의 호스트 이름과 일치합니다. 따라서 컴퓨터의 DNS는 이미 전역으로 등록되어 있으므로 모든 클라이언트에서 액세스할 수 있습니다.

## <a name="step-3-configure-the-report-server"></a>3단계: 보고서 서버 구성
이 섹션에서는 Reporting Services 기본 모드 보고서 서버로 VM을 구성하는 과정을 안내합니다. 다음 방법 중 하나를 사용하여 보고서 서버를 구성할 수 있습니다.

* 스크립트를 사용하여 보고서 서버 구성
* 구성 관리자를 사용하여 보고서 서버를 구성합니다.

자세한 단계는 [Virtual Machine에 연결 및 Reporting Services 구성 관리자 시작](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager)섹션을 참조하세요.

**인증 참고:** Windows 인증은 권장되는 인증 방법이며 기본 Reporting Services 인증입니다. VM에 구성된 사용자만 Reporting Services에 액세스할 수 있으며 Reporting Services 역할에 할당됩니다.

### <a name="use-script-to-configure-the-report-server-and-http"></a>스크립트를 사용하여 보고서 서버 및 HTTP 구성
Windows PowerShell 스크립트를 사용하여 보고서 서버를 구성하려면 다음 단계를 완료합니다. 구성에 HTTPS가 아닌 HTTP가 포함됩니다.

1. Azure Portal에서 VM을 선택하고 연결을 클릭합니다. 브라우저 구성에 따라 VM에 연결하기 위해 .rdp 파일을 저장하라는 메시지가 표시될 수 있습니다.
   
    ![Azure 가상 머신에 연결](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) VM을 만들 때 구성한 사용자 VM 이름, 사용자 이름 및 암호를 사용합니다. 
   
    예를 들어 다음 이미지에서 VM 이름은 **ssrsnativecloud**이고 사용자 이름은 **testuser**입니다.
   
    ![로그인에 vm 이름이 포함 되어 있습니다.](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. VM에서 관리자 권한으로 **Windows PowerShell ISE** 를 엽니다. PowerShell ISE는 Windows Server 2012에 기본적으로 설치되어 있습니다. 스크립트를 ISE에 붙여넣고 스크립트를 수정한 다음 스크립트를 실행할 수 있도록 표준 Windows PowerShell 창 대신 ISE를 사용하는 것이 좋습니다.
3. Windows PowerShell ISE에서 **보기** 메뉴를 클릭한 다음 **스크립트 창 표시**를 클릭합니다.
4. 다음 스크립트를 복사하고 Windows PowerShell ISE 스크립트 창으로 스크립트를 붙여넣습니다.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. HTTP 포트 80 이외의 포트를 사용하여 VM을 만든 경우 매개 변수 $HTTPport = 80을 수정합니다.
6. 스크립트가 현재 Reporting Services에 대해 구성되어 있습니다. Reporting Services에 대한 스크립트를 실행하려는 경우 Get-WmiObject 문에서 네임스페이스 경로의 버전 부분을 "v11"로 수정합니다.
7. 스크립트를 실행합니다.

**유효성 검사**: 기본 보고서 서버 기능이 작동하는지 확인하려면 이 항목의 뒷부분에 나오는 [구성 확인](#verify-the-configuration) 섹션을 참조하세요.

### <a name="use-script-to-configure-the-report-server-and-https"></a>스크립트를 사용하여 보고서 서버 및 HTTPS 구성
Windows PowerShell을 사용하여 보고서 서버를 구성하려면 다음 단계를 완료합니다. 구성에 HTTP가 아닌 HTTPS가 포함됩니다.

1. Azure Portal에서 VM을 선택하고 연결을 클릭합니다. 브라우저 구성에 따라 VM에 연결하기 위해 .rdp 파일을 저장하라는 메시지가 표시될 수 있습니다.
   
    ![Azure 가상 머신에 연결](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) VM을 만들 때 구성한 사용자 VM 이름, 사용자 이름 및 암호를 사용합니다. 
   
    예를 들어 다음 이미지에서 VM 이름은 **ssrsnativecloud**이고 사용자 이름은 **testuser**입니다.
   
    ![로그인에 vm 이름이 포함 되어 있습니다.](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. VM에서 관리자 권한으로 **Windows PowerShell ISE** 를 엽니다. PowerShell ISE는 Windows Server 2012에 기본적으로 설치되어 있습니다. 스크립트를 ISE에 붙여넣고 스크립트를 수정한 다음 스크립트를 실행할 수 있도록 표준 Windows PowerShell 창 대신 ISE를 사용하는 것이 좋습니다.
3. 스크립트를 실행하도록 설정하려면 다음 Windows PowerShell 명령을 실행합니다.
   
        Set-ExecutionPolicy RemoteSigned
   
    그런 후 다음을 실행하면 정책을 확인할 수 있습니다.
   
        Get-ExecutionPolicy
4. **Windows PowerShell ISE**에서 **보기** 메뉴를 클릭한 다음 **스크립트 창 표시**를 클릭합니다.
5. 다음 스크립트를 복사하고 Windows PowerShell ISE 스크립트 창에 붙여넣습니다.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. 스크립트에서 **$certificatehash** 매개 변수를 수정합니다.
   
   * 이는 **필수** 매개 변수입니다. 이전 단계에서 인증서 값을 저장하지 않은 경우 다음 방법 중 하나를 사용하여 인증서 지문에서 인증서 해시 값을 복사합니다.
     
       VM에서 Windows PowerShell ISE를 열고 다음 명령을 실행합니다.
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       출력은 다음과 유사합니다. 스크립트가 빈 줄을 반환하면 VM에서 인증서가 구성되지 않은 것입니다. 예를 보려면 [Virtual Machines의 자체 서명된 인증서를 사용하려면](#to-use-the-virtual-machines-self-signed-certificate) 섹션을 참조하세요.
     
     또는
   * VM에서 mmc.exe를 실행한 다음 **인증서** 스냅인을 추가합니다.
   * **신뢰할 수 있는 루트 인증 기관** 노드 아래에서 인증서 이름을 두 번 클릭합니다. VM의 자체 서명된 인증서를 사용하는 경우 인증서 이름은 VM의 DNS 이름을 따서 지정되고 **cloudapp.net**으로 끝납니다.
   * **세부 정보** 탭을 클릭합니다.
   * 왼쪽 창에서 **지문**. 지문 값이 세부 정보 필드에 표시됩니다. 예를 들어 af 11 60 b6 4b 28 8d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48입니다.
   * **스크립트를 실행하기 전에**값 쌍 사이의 공백을 제거합니다. 예를 들어 af1160b64b288d890a8212ff6ba9c3664f319048입니다.
7. **$httpsport** 매개 변수를 수정합니다. 
   
   * HTTPS 엔드포인트에 포트 443을 사용한 경우 스크립트에서 이 매개 변수를 업데이트할 필요가 없습니다. 그렇지 않은 경우 VM에서 HTTPS 개인 엔드포인트를 구성할 때 선택한 포트 값을 사용합니다.
8. **$DNSName** 매개 변수를 수정합니다. 
   
   * 스크립트가 와일드카드 인증서 $DNSName= "+"에 대해 구성됩니다. 와일드카드 인증서 바인딩에 대해 구성하지 않으려면 $DNSName="+"를 주석으로 처리하고 전체 $DNSNAme 참조인 ##$DNSName="$server.cloudapp.net” 줄을 사용하도록 설정합니다.
     
       Reporting Services에 대해 가상 머신의 DNS 이름을 사용하지 않으려는 경우 $DNSName 값을 변경합니다. 매개 변수를 사용하는 경우 인증서가 이 이름을 사용해야 하며 DNS 서버에서 전역으로 이름을 등록해야 합니다.
9. 스크립트가 현재 Reporting Services에 대해 구성되어 있습니다. Reporting Services에 대한 스크립트를 실행하려는 경우 Get-WmiObject 문에서 네임스페이스 경로의 버전 부분을 "v11"로 수정합니다.
10. 스크립트를 실행합니다.

**유효성 검사**: 기본 보고서 서버 기능이 작동하는지 확인하려면 이 항목의 뒷부분에 나오는 구성 확인 섹션을 참조하세요. 인증서 바인딩을 확인하려면 관리자 권한으로 명령 프롬프트를 연 후 다음 명령을 실행합니다.

    netsh http show sslcert

결과에는 다음이 들어 있습니다.

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>구성 관리자를 사용하여 보고서 서버 구성
PowerShell 스크립트를 실행하여 보고서 서버를 구성하지 않으려는 경우 이 섹션의 단계를 따라 Reporting Services 기본 모드 구성 관리자를 사용하여 보고서 서버를 구성합니다.

1. Azure Portal에서 VM을 선택하고 연결을 클릭합니다. VM을 만들 때 구성한 사용자 이름 및 암호를 사용합니다.
   
    ![Azure 가상 머신에 연결](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Windows 업데이트를 실행하고 VM에 대한 업데이트를 설치합니다. VM을 다시 시작해야 하는 경우 VM을 다시 시작하고 Azure Portal에서 VM에 다시 연결합니다.
3. VM의 시작 메뉴에서 **Reporting Services**를 입력하고 **Reporting Services 구성 관리자**를 엽니다.
4. **서버 이름** 및 **보고서 서버 인스턴스**의 기본값을 그대로 둡니다. **Connect**를 클릭합니다.
5. 왼쪽 창에서 **웹 서비스 URL**을 클릭합니다.
6. 기본적으로 RS는 IP가 “모두 할당됨"으로 HTTP 포트 80에 대해 구성됩니다. HTTPS를 추가하려면:
   
   1. **SSL 인증서**에서: 사용하려는 인증서를 선택합니다. 예를 들어 [VM 이름].cloudapp.net입니다. 인증서가 나열되지 않은 경우 **2단계: 서버 인증서 만들기** 섹션에서 VM에 인증서를 설치하고 신뢰하는 방법을 참조하세요.
   2. **SSL 포트**에서: 443을 선택합니다. VM에 다른 개인 포트를 사용하여 HTTPS 개인 엔드포인트를 구성한 경우 해당 값을 여기에 사용합니다.
   3. **적용** 을 클릭하고 작업이 완료되기를 기다립니다.
7. 왼쪽 창에서 **데이터베이스**를 클릭합니다.
   
   1. **데이터베이스 변경**을 클릭합니다.
   2. **새 보고서 서버 데이터베이스 만들기**를 클릭한 후 **다음**을 클릭합니다.
   3. 기본 **서버 이름**을 VM 이름으로 그대로 적용하고 기본 **인증 유형**을 **현재 사용자** – **통합된 보안**으로 그대로 적용합니다. **다음**을 클릭합니다.
   4. 기본 **데이터베이스 이름**을 **ReportServer**로 그대로 적용하고 **다음**을 클릭합니다.
   5. 기본 **인증 유형**을 **서비스 자격 증명**으로 그대로 적용하고 **다음**을 클릭합니다.
   6. 왼쪽 창에서 **다음** on the **다음** 을 클릭합니다.
   7. 구성 단계가 완료되면 **마침**을 클릭합니다.
8. 왼쪽 창에서 **보고서 관리자 URL**을 클릭합니다. 기본 **가상 디렉터리**를 **보고서**로 그대로 적용하고 **적용**을 클릭합니다.
9. **종료** 를 클릭하여 Reporting Services 구성 관리자를 닫습니다.

## <a name="step-4-open-windows-firewall-port"></a>4단계: Windows 방화벽 포트 열기
> [!NOTE]
> 스크립트 중 하나를 사용하여 보고서 서버를 구성한 경우 이 섹션을 건너뛸 수 있습니다. 스크립트에는 방화벽 포트를 여는 단계가 포함되어 있습니다. 기본값은 HTTP의 경우 포트 80이고 HTTPS의 경우 포트 443입니다.
> 
> 

가상 머신의 보고서 관리자 또는 보고서 서버에 원격으로 연결하려면 VM에서 TCP 엔드포인트가 필요합니다. 이는 VM의 방화벽에서 동일한 포트를 여는 데 필요합니다. VM이 프로비전되었을 때 엔드포인트가 만들어졌습니다.

이 섹션에서는 방화벽 포트를 여는 방법에 대한 기본 정보를 제공합니다. 자세한 내용은 [보고서 서버 액세스를 위한 방화벽 구성](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> 스크립트를 사용하여 보고서 서버를 구성한 경우 이 섹션을 건너뛰면 됩니다. 스크립트에는 방화벽 포트를 여는 단계가 포함되어 있습니다.
> 
> 

HTTPS에 대해 443 이외의 개인 포트를 구성한 경우 다음 스크립트를 적절하게 수정합니다. Windows 방화벽에서 포트 **443** 을 열려면 다음을 완료합니다.

1. 관리자 권한으로 Windows PowerShell 창을 엽니다.
2. VM에서 HTTPS 엔드포인트를 구성할 때 443 이외의 포트를 사용한 경우 다음 명령에서 포트를 업데이트하고 명령을 실행합니다.
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. 명령이 완료되면 **Ok** 가 명령 프롬프트에 표시됩니다.

포트가 열렸는지 확인하려면 Windows PowerShell 창을 열고 다음 명령을 실행합니다.

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>구성 확인
이제 기본 보고서 서버 기능이 작동하는지 확인하려면 관리자 권한으로 브라우저를 열고 다음 보고서 서버 AD 보고서 관리자 URL로 이동합니다.

* VM에서 보고서 서버 URL로 이동합니다.
  
        http://localhost/reportserver
* VM에서 보고서 관리자 URL로 이동합니다.
  
        http://localhost/Reports
* 로컬 컴퓨터의 VM에서 **원격** 보고서 관리자로 이동합니다. 다음 예제에서 DNS 이름을 적절하게 업데이트합니다. 암호를 입력하라는 메시지가 표시되면 VM이 프로비전되었을 때 만든 관리자 자격 증명을 사용합니다. 사용자 이름은 [도메인]\[사용자 이름] 형식이며, 여기서 도메인은 VM 컴퓨터 이름입니다. 예를 들어 ssrsnativecloud\testuser입니다. HTTP**S**를 사용하지 않는 경우 URL에서 **s**를 제거합니다. VM에서 추가 사용자를 만드는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* 로컬 컴퓨터에서 원격 보고서 서버 URL로 이동합니다. 다음 예제에서 DNS 이름을 적절하게 업데이트합니다. HTTPS를 사용하지 않는 경우 URL에서 s를 제거합니다.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>사용자 만들기 및 역할 할당
보고서 서버를 구성하고 확인한 후 일반적인 관리 작업은 하나 이상의 사용자를 만들고 Reporting Services 역할에 사용자를 할당하는 것입니다. 자세한 내용은 

* [로컬 사용자 계정 만들기](https://technet.microsoft.com/library/cc770642.aspx)
* [보고서 서버에 사용자 액세스 권한 부여(보고서 관리자)](https://msdn.microsoft.com/library/ms156034.aspx)
* [역할 할당 만들기 및 관리](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>보고서를 만들고 Azure Virtual Machine에 게시하려면
다음 표에는 온-프레미스 컴퓨터의 기존 보고서를 Microsoft Azure Virtual Machine에 호스트된 보고서 서버에 게시하는 데 사용 가능한 일부 옵션이 요약되어 있습니다.

* **RS.exe script**: RS.exe 스크립트를 사용하여 기존 보고서 서버의 보고서 항목을 Microsoft Azure Virtual Machine에 복사합니다. 자세한 내용은 [보고서 서버 간 콘텐츠 마이그레이션을 위한 예제 Reporting Services rs.exe 스크립트](https://msdn.microsoft.com/library/dn531017.aspx)의 "기본 모드에서 기본 모드로 – Microsoft Azure Virtual Machine" 섹션을 참조하세요.
* **보고서 작성기**: 가상 머신은 Microsoft SQL Server 보고서 작성기의 ClickOnce 버전을 포함합니다. 가상 머신에서 처음으로 보고서 작성기를 시작하려면:
  
  1. 관리자 권한으로 브라우저를 시작합니다.
  2. 가상 머신에서 보고서 관리자로 이동하고 리본에서 **보고서 작성기**를 클릭합니다.
     
     자세한 내용은 [보고서 작성기 설치, 제거 및 지원](https://technet.microsoft.com/library/dd207038.aspx)을 참조하세요.
* **SQL Server Data Tools: VM**:  SQL Server 2012를 사용하여 VM을 만든 경우 SQL Server Data Tools가 가상 머신에 설치되어 있으므로 가상 머신에서 **보고서 서버 프로젝트** 및 보고서를 만드는 데 사용할 수 있습니다. SQL Server Data Tools는 보고서를 가상 머신의 보고서 서버에 게시할 수 있습니다.
  
    SQL Server 2014에서 VM을 만든 경우 SQL Server Data Tools - Visual Studio용 BI를 설치할 수 있습니다. 자세한 내용은 
  
  * [Microsoft SQL Server Data Tools - Visual Studio 2013용 비즈니스 인텔리전스](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools - Visual Studio 2012용 비즈니스 인텔리전스](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SSDT-BI(SQL Server Data Tools 및 SQL Server Business Intelligence)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools: 원격**:  로컬 컴퓨터에서 SQL Server Data Tools로 Reporting Services 보고서가 포함된 Reporting Services 프로젝트를 만듭니다. 웹 서비스 URL에 연결하도록 프로젝트를 구성합니다.
  
    ![SSRS 프로젝트의 SSDT 프로젝트 속성](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **스크립트 사용**: 스크립트를 사용하여 보고서 서버 콘텐츠를 복사합니다. 자세한 내용은 [보고서 서버 간 콘텐츠 마이그레이션을 위한 예제 Reporting Services rs.exe 스크립트](https://msdn.microsoft.com/library/dn531017.aspx)를 참조하세요.

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>VM을 사용하지 않는 경우 비용 최소화
> [!NOTE]
> Azure Virtual Machines를 사용하지 않을 때 요금을 최소화하려면 Azure Portal에서 VM을 종료합니다. VM 내의 Windows 전원 옵션을 사용하여 VM을 종료하면 VM에 대해 동일한 요금이 계속 청구됩니다. 요금을 줄이려면 Azure Portal에서 VM을 종료해야 합니다. VM이 더 이상 필요하지 않은 경우 저장소 요금이 부과되지 않도록 VM 및 연결된 .vhd 파일을 삭제해야 합니다. 자세한 내용은 [Virtual Machines 가격 책정 정보](https://azure.microsoft.com/pricing/details/virtual-machines/)의 FAQ 섹션을 참조하세요.

## <a name="more-information"></a>추가 정보
### <a name="resources"></a>리소스
* SQL Server Business Intelligence 및 SharePoint 2013의 단일 서버 배포와 관련된 유사한 내용은 [Windows PowerShell을 사용하여 SQL Server BI 및 SharePoint 2013에서 Azure VM 만들기](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/)를 참조하세요.
* Azure Virtual Machines에서 SQL Server Business Intelligence 배포와 관련된 일반 정보는 [Azure Virtual Machines에서 SQL Server Business Intelligence](virtual-machines-windows-classic-ps-sql-bi.md)를 참조하세요.
* Azure 계산 요금의 비용에 대한 자세한 내용은 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines)의 Virtual Machines 탭을 참조하세요.

### <a name="community-content"></a>커뮤니티 콘텐츠
* 스크립트를 사용하지 않고 Reporting Services 기본 모드 보고서 서버를 만드는 방법에 대한 단계별 지침은 [Azure Virtual Machine에 SQL Reporting Services 호스트](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html)를 참조하세요.

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Azure VM의 SQL Server에 대한 기타 리소스 링크
[Azure Virtual Machines의 SQL Server 개요](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

