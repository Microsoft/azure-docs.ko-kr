---
title: DMZ에 사용할 Azure 샘플 애플리케이션
titlesuffix: Azure Virtual Network
description: 트래픽 흐름 시나리오를 테스트하기 위해 DMZ을 만든 다음 이 간단한 웹 애플리케이션을 배포합니다.
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 6753b3a76ff5d3e0266f238d8e354943dec694a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60788918"
---
# <a name="sample-application-for-use-with-dmzs"></a>DMZ에 사용할 샘플 애플리케이션
[보안 경계 모범 사례 페이지로 돌아가기][HOME]

이러한 PowerShell 스크립트는 IIS01 및 AppVM01 서버에서 로컬로 실행하여 프런트 엔드 IIS01 서버에서 백 엔드 AppVM01 서버의 콘텐츠가 포함된 html 페이지를 표시하는 간단한 웹 애플리케이션을 설치 및 설정할 수 있습니다.

그러면 다양한 DMZ 예제를 위한 간단한 테스트 환경을 제공하고 엔드포인트, NSG, UDR, 방화벽 규칙을 변경할 경우 트래픽 흐름에 어떤 영향을 미치는지 확인할 수 있습니다.

## <a name="firewall-rule-to-allow-icmp"></a>ICMP를 허용하기 위한 방화벽 규칙
이 간단한 PowerShell 문은 모든 Windows VM에서 실행하여 ICMP(Ping) 트래픽을 허용할 수 있습니다. 이 경우 방화벽 업데이트가 Windows 방화벽을 통과하도록 하여 테스트와 문제 해결을 더욱 간단히 수행할 수 있습니다(대부분의 Linux 배포판에서 ICMP는 기본적으로 사용하도록 설정되어 있음).

```powershell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

다음 스크립트를 사용하는 경우 이 방화벽 규칙 추가가 첫 번째 문입니다.

## <a name="iis01---web-application-installation-script"></a>IIS01 - 웹 애플리케이션 설치 스크립트
이 스크립트는 다음과 같은 기능을 수행합니다.

1. 손쉬운 테스트를 위해 로컬 서버 Windows 방화벽에서 IMCPv4(Ping)를 엽니다.
2. IIS를 설치 하는.NET Framework v4.5
3. ASP.NET 웹 페이지와 Web.config 파일을 만듭니다.
4. 파일에 쉽게 액세스할 수 있도록 기본 애플리케이션 풀을 변경합니다.
5. 관리자 계정과 암호에 익명 사용자를 설정합니다.

이 PowerShell 스크립트는 IIS01에 RDP 처리된 동안 로컬로 실행해야 합니다.

```powershell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .NET 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesn''t really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="https://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - 파일 서버 설치 스크립트
이 스크립트는 이 간단한 애플리케이션의 백 엔드를 설정합니다. 이 스크립트는 다음과 같은 기능을 수행합니다.

1. 손쉬운 테스트를 위해 방화벽에서 IMCPv4(Ping)를 엽니다.
2. 웹 사이트에 대한 디렉터리를 만듭니다.
3. 웹 페이지에서 원격으로 액세스할 텍스트 파일을 만듭니다.
4. 디렉터리와 파일에 익명으로 액세스할 수 있는 권한을 설정합니다.
5. 이 서버에서 쉽게 탐색할 수 있도록 IE의 강화된 보안을 해제합니다.

> [!IMPORTANT]
> **모범 사례**: 프로덕션 서버에서 IE의 보안 강화를 해제하지 마십시오. 프로덕션 서버에서 웹을 탐색하는 것도 좋지 않습니다. 또한 익명 액세스를 위해 파일 공유를 여는 것도 바람직하지 않지만 여기서는 간단한 설명을 위해 사용합니다.
>
>

이 PowerShell 스크립트는 AppVM01에 RDP 처리된 동안 로컬로 실행해야 합니다. 관리자가 성공적 실행을 보장하기 위해 PowerShell을 실행해야 합니다.

```powershell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - DNS 서버 설치 스크립트
이 샘플 애플리케이션에는 DNS 서버를 설치할 스크립트가 포함되어 있지 않습니다. 방화벽 규칙을 테스트하는 경우 NSG 또는 UDR에서 DNS 트래픽을 포함해야 하며 DNS01 서버를 수동으로 설정해야 합니다. 두 예제의 네트워크 구성 xml 파일 및 Resource Manager 템플릿에는 기본 DNS 서버로 DNS01이 포함되어 있으며 수준 3에서 호스팅하는 공용 DNS 서버는 백업 DNS 서버로 포함되어 있습니다. 수준 3 DNS 서버가 비로컬 트래픽에 사용되는 실제 DNS 서버가 되며, DNS01이 설정되지 않은 경우 로컬 네트워크 DNS가 발생하지 않습니다.

## <a name="next-steps"></a>다음 단계
* IIS 서버에서 IIS01 스크립트 실행
* AppVM01에서 파일 서버 스크립트 실행
* 빌드 유효성을 검증하기 위해 IIS01의 공용 IP로 이동

<!--Link References-->
[HOME]: ../best-practices-network-security.md
