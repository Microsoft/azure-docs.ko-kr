---
title: Azure Cloud Services 역할에 .NET 설치 | Microsoft Docs
description: 이 문서에서는 클라우드 서비스 웹 및 작업자 역할에 .NET Framework를 수동으로 설치하는 방법을 설명합니다.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: jeconnoc
ms.openlocfilehash: bc861b6730e8bf9db6ba2ab005496914f7b9ed89
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759806"
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Azure Cloud Services 역할에 .NET 설치
이 문서에서는 Azure 게스트 OS와 함께 제공되지 않는 .NET Framework 버전을 설치하는 방법을 설명합니다. 클라우드 서비스 웹 및 작업자 역할을 구성하기 위해 게스트 OS에 .NET을 사용할 수 있습니다.

예를 들어 .NET 4.6 릴리스와 함께 제공되지 않는 게스트 OS 제품군 4에서 .NET 4.6.2를 설치할 수 있습니다. (게스트 OS 제품군 5는 .NET 4.6과 함께 제공됨) Azure 게스트 OS 릴리스에 대한 최신 정보는 [Azure 게스트 OS 릴리스 뉴스](cloud-services-guestos-update-matrix.md)를 참조하세요. 

>[!IMPORTANT]
>Azure SDK 2.9에는 게스트 OS 제품군 4 이하에서 .NET 4.6을 배포하는 데 제한이 있습니다. 제한에 대한 수정 사항은 [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) 사이트에서 사용할 수 있습니다.

웹 및 작업자 역할에 .NET을 설치하려면 클라우드 서비스 프로젝트의 일부로 .NET 웹 설치 관리자를 포함합니다. 역할의 시작 작업의 일부로 설치 관리자를 시작합니다. 

## <a name="add-the-net-installer-to-your-project"></a>프로젝트에 .NET 설치 관리자 추가
.NET Framework의 웹 설치 관리자를 다운로드하려면 설치하려는 버전을 선택합니다.

* [4.8.NET 웹 설치 관리자](https://dotnet.microsoft.com/download/thank-you/net48)
* [.NET 4.7.2 웹 설치 관리자](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET 4.6.2 웹 설치 관리자](https://www.microsoft.com/download/details.aspx?id=53345)

*웹* 역할에 대한 설치 관리자를 추가하려면
  1. **솔루션 탐색기** 내 클라우드 서비스 프로젝트의 **역할** 아래에서 *웹* 역할을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다. 이름이 **bin**인 폴더를 만듭니다.
  2. bin 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **기존 항목**을 선택합니다. .NET 설치 관리자를 선택하고 bin 폴더에 추가합니다.
  
*작업자* 역할에 대해 설치 관리자를 추가하려면
* *작업자* 역할을 마우스 오른쪽 단추로 클릭하고 **추가** > **기존 항목**을 선택합니다. .NET 설치 관리자를 선택하고 역할에 추가합니다. 

이러한 방식으로 역할 콘텐츠 폴더에 파일이 추가되면 클라우드 서비스 패키지에도 자동으로 추가됩니다. 그런 다음 파일은 가상 머신에서 일관된 위치에 배포됩니다. 모든 역할에 설치 관리자 복사본이 있도록 클라우드 서비스의 각 웹 및 작업자 역할에 대해 이 프로세스를 반복합니다.

> [!NOTE]
> 애플리케이션이 .NET 4.6을 대상으로 하는 경우 클라우드 서비스 역할에 .NET 4.6.2를 설치해야 합니다. 게스트 OS에는 기술 자료 [업데이트 3098779](https://support.microsoft.com/kb/3098779) 및 [업데이트 3097997](https://support.microsoft.com/kb/3097997)이 포함됩니다. .NET 4.6이 기술 자료 업데이트 맨 위에 설치된 경우 .NET 애플리케이션을 설치할 때 문제가 발생할 수 있습니다. 이러한 문제를 방지하려면 버전 4.6이 아닌 .NET 4.6.2를 설치합니다. 자세한 내용은 [기술 자료 문서 3118750](https://support.microsoft.com/kb/3118750) 및 [4340191](https://support.microsoft.com/kb/4340191)을 참조하세요.
> 
> 

![설치 관리자 파일이 포함된 역할 콘텐츠][1]

## <a name="define-startup-tasks-for-your-roles"></a>사용자 역할에 대한 시작 작업 정의
시작 작업을 사용하여 역할이 시작되기 전에 작업을 수행할 수 있습니다. .NET Framework를 시작 작업의 일부로 설치하면 애플리케이션 코드가 실행되기 전에 프레임워크가 설치됩니다. 시작 작업에 대한 자세한 내용은 [Azure에서 시작 작업 실행](cloud-services-startup-tasks.md)을 참조하세요. 

1. 모든 역할에 대해 **WebRole** 또는 **WorkerRole** 노드 아래의 ServiceDefinition.csdef 파일에 다음 콘텐츠를 추가합니다.
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    위 구성은 .NET Framework를 설치하기 위해 관리자 권한으로 `install.cmd` 콘솔 명령을 실행합니다. 이 구성에서는 이름이 **NETFXInstall**인 **LocalStorage** 요소도 만듭니다. 시작 스크립트는 이 로컬 저장소 리소스를 사용하도록 temp 폴더를 설정합니다. 
    
    > [!IMPORTANT]
    > 프레임워크가 올바르게 설치되도록 하려면 이 리소스의 크기를 1024MB 이상으로 설정합니다.
    
    시작 작업에 대한 자세한 내용은 [일반적인 Azure Cloud Services 시작 작업](cloud-services-startup-tasks-common.md)을 참조하세요.

2. **install.cmd**라는 파일을 만들어 다음 설치 스크립트를 파일에 추가합니다.

   스크립트는 레지스트리를 쿼리하여 지정된 .NET Framework 버전이 컴퓨터에 이미 설치되어 있는지 확인합니다. .NET 버전이 설치되어 있지 않으면 .NET 웹 설치 관리자가 열립니다. 문제 해결을 돕기 위해 이 스크립트는 **InstallLogs** 로컬 스토리지에 저장된 startuptasklog-(현재 날짜 및 시간).txt 파일에 모든 작업을 기록합니다.
   
   > [!IMPORTANT]
   > Windows 메모장 등의 기본 텍스트 편집기를 사용하여 install.cmd 파일을 만듭니다. Visual Studio를 사용하여 텍스트 파일을 만들고 확장명을 .cmd로 변경하는 경우 파일은 UTF-8 바이트 순서 표시를 계속 포함할 수 있습니다. 이 표시로 스크립트의 첫 줄을 실행할 때 오류가 발생할 수 있습니다. 이 오류를 방지하려면 스크립트의 첫 번째 줄을 바이트 순서 처리에서 건너뛸 수 있는 REM 문으로 만듭니다. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. 이 항목의 앞부분에 설명된 대로 **솔루션 탐색기**에서 **추가** > **기존 항목**을 사용하여 각 역할에 install.cmd 파일을 추가합니다. 

    이 단계가 완료된 후에는 모든 역할에 .NET 설치 관리자 파일과 install.cmd 파일이 있습니다.

   ![모든 파일이 포함된 역할 콘텐츠][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>진단을 구성하여 Blob Storage로 시작 작업 로그 전송
설치 문제의 해결을 간소화하려면 시작 스크립트 또는 .NET 설치 관리자에 의해 생성된 모든 로그 파일을 Azure Blob Storage로 전송하도록 Azure Diagnostics를 구성할 수 있습니다. 이 방법을 사용하면 원격 데스크톱을 통해 역할에 연결하는 대신 Blob Storage에서 로그 파일을 다운로드하여 로그를 볼 수 있습니다.


진단을 구성하려면 diagnostics.wadcfgx 파일을 열고 **디렉터리** 노드 아래에 다음 콘텐츠를 추가합니다. 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

이 XML은 log 디렉터리의 **NETFXInstall** 리소스에 있는 파일을 **netfx-install** Blob 컨테이너 내 진단 저장소 계정으로 전송하도록 진단을 구성합니다.

## <a name="deploy-your-cloud-service"></a>클라우드 서비스 배포
클라우드 서비스를 배포하면 .NET Framework가 아직 설치되어 있지 않은 경우 시작 작업이 .NET Framework를 설치합니다. 프레임워크가 설치되고 있는 동안에는 클라우드 서비스 역할이 *사용 중* 상태입니다. 프레임워크 설치를 다시 시작해야 하는 경우 서비스 역할도 다시 시작될 수 있습니다. 

## <a name="additional-resources"></a>추가 리소스
* [.NET Framework 설치][Installing the .NET Framework]
* [설치된 .NET Framework 버전 확인][How to: Determine Which .NET Framework Versions Are Installed]
* [.NET Framework 설치 문제 해결][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
