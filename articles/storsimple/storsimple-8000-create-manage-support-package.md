---
title: StorSimple 8000 시리즈 지원 패키지 만들기 | Microsoft Docs
description: StorSimple 8000 시리즈 디바이스용 지원 패키지를 만들고, 암호 해독 및 편집하는 방법을 알아봅니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: dfc2d8d763a1eb64a37af73e03992f2d948a6856
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481870"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>StorSimple 8000 시리즈용 지원 패키지 만들기 및 관리

## <a name="overview"></a>개요

StorSimple 지원 패키지는 StorSimple 디바이스 문제를 해결하는 데 있어서 Microsoft 지원을 지원하기 위해 모든 관련 로그를 수집하는 사용하기 쉬운 메커니즘입니다. 수집된 로그는 암호화되고 압축됩니다.

이 자습서는 StorSimple 8000 시리즈 디바이스에 대한 지원 패키지를 만들고 관리하도록 단계별 지침을 제공합니다. StorSimple 가상 배열을 사용하는 경우 [로그 패키지 생성](storsimple-ova-web-ui-admin.md#generate-a-log-package)으로 이동합니다.

## <a name="create-a-support-package"></a>지원 패키지 만들기

경우에 따라 StorSimple 용 Windows PowerShell을 통해 지원 패키지를 수동으로 만들어야 합니다. 예를 들면 다음과 같습니다.

* Microsoft 지원과 공유하기 전에 로그 파일에서 중요한 정보를 제거해야 하는 경우
* 연결 문제로 인해 패키지를 업로드하는 데 문제가 발생하는 경우

전자 메일을 통해 Microsoft 지원에 수동으로 생성된 지원 패키지를 공유할 수 있습니다. StorSimple용 Windows PowerShell에서 지원 패키지를 만들려면 다음 단계를 수행합니다.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell에서 지원 패키지를 만들려면

1. StorSimple 디바이스에 연결하는데 사용된 원격 컴퓨터에서 관리자 권한으로 Windows PowerShell 세션을 시작하려면 다음 명령을 입력합니다.
   
    `Start PowerShell`
2. Windows PowerShell 세션에서 디바이스의 SSAdmin 콘솔에 연결합니다.
   
   1. 명령 프롬프트에 다음을 입력합니다.
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. 열린 대화 상자에서 디바이스 관리자 암호를 입력합니다. 기본 암호는 _Password1_입니다.
     
      ![PowerShell 자격 증명 대화 상자](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. **확인**을 선택합니다.
   4. 명령 프롬프트에 다음을 입력합니다.
     
      `Enter-PSSession $MS`
3. 열린 세션에서 적절한 명령을 입력합니다.
   
   * 암호로 보호된 네트워크 공유에 다음을 입력합니다.
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       암호 및 암호화 암호에 대해 묻는 메시지가 나타납니다(지원 패키지가 암호화 되었기 때문에). 그러면 기본 폴더에 지원 패키지가 생성됩니다(디바이스 이름에 현재 날짜 및 시간이 추가됨).
   * 암호로 보호되지 않은 공유의 경우 `-Credential` 매개 변수가 필요하지 않습니다. 다음을 입력합니다.
     
       `Export-HcsSupportPackage`
     
       기본 폴더에 두 컨트롤러의 지원 패키지가 생성됩니다. 패키지는 문제 해결을 위해 Microsoft 지원으로 보낼 수 있는 암호화되고 압축된 파일입니다. 자세한 내용은 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)를 참조하세요.

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Export-HcsSupportPackage cmdlet 매개 변수

Export-HcsSupportPackage cmdlet으로 다음 매개 변수를 사용할 수 있습니다.

| 매개 변수 | 필수/선택 | 설명 |
| --- | --- | --- |
| `-Path` |필수 |지원 패키지가 배치된 네트워크 공유 폴더의 위치를 제공하는 데 사용합니다. |
| `-EncryptionPassphrase` |필수 |지원 패키지를 암호화하기 위해 암호를 제공하는 데 사용합니다. |
| `-Credential` |옵션 |네트워크 공유 폴더에 대한 액세스 자격 증명을 제공하는 데 사용합니다. |
| `-Force` |옵션 |암호화 암호 확인 단계를 건너뛰는 데 사용합니다. |
| `-PackageTag` |옵션 |지원 패키지가 배치된 *경로* 의 디렉터리를 지정하는 데 사용합니다. 기본값은 [디바이스 이름]-[현재 날짜 및 시간: yyyy-MM-dd-HH-mm-ss]입니다. |
| `-Scope` |옵션 |**클러스터** (기본값)로 지정하여 두 컨트롤러에 대한 지원 패키지를 만듭니다. 현재 컨트롤러에 대해서만 패키지를 만들려면 **컨트롤러**를 지정합니다. |

## <a name="edit-a-support-package"></a>지원 패키지 편집

지원 패키지를 생성한 후에 패키지를 편집하여 중요한 정보를 제거해야 합니다. 볼륨 이름, 디바이스 IP 주소 및 로그 파일에서 백업 이름을 포함할 수 있습니다.

> [!IMPORTANT]
> StorSimple용 Windows PowerShell을 통해 생성된 지원 패키지를 편집할 수 있습니다. StorSimple 디바이스 관리자 서비스로 Azure Portal에서 생성한 패키지를 편집할 수 없습니다.

Microsoft 지원 사이트에 업로드하기 전에 지원 패키지를 편집하려면 먼저 지원 패키지의 암호를 해독하고 파일을 편집한 다음 다시 암호화합니다. 다음 단계를 수행합니다.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell에서 지원 패키지를 편집하려면

1. 이전에 [StorSimple용 Windows PowerShell에서 지원 패키지 만들기](#to-create-a-support-package-in-windows-powershell-for-storsimple)에서 설명한 대로 지원 패키지를 생성합니다.
2. [스크립트](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) 를 로컬로 클라이언트에 다운로드합니다.
3. Windows PowerShell 모듈을 가져옵니다. 스크립트를 다운로드한 로컬 폴더로 경로를 지정합니다. 모듈을 가져오려면 다음을 입력합니다.
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. 모든 파일은 압축 및 암호화된 *.aes* 파일입니다. 압축을 해제하고 파일의 암호를 해독하려면 다음을 입력합니다.
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    실제 파일 확장명은 모든 파일에서 표시됩니다.
   
    ![지원 패키지 편집](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. 암호화 암호에 대한 메시지가 표시 되는 경우 지원 패키지를 만들 때 사용한 암호를 입력합니다.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. 로그 파일이 들어 있는 폴더로 이동합니다. 이제 로그 파일의 압축을 해제하고 암호를 해독했기 때문에 원본 파일 확장명을 가질 수 있습니다. 이러한 파일을 수정하여 볼륨 이름 및 디바이스 IP 주소와 같은 고객 관련 정보를 제거하고 파일을 저장합니다.
7. 파일을 닫아서 Gzip으로 압축한 다음 AES-256으로 암호화합니다. 네트워크를 통해 지원 패키지를 전송하는 경우 보안 및 속도를 위해서 이렇게 합니다. 파일을 압축하고 암호화하려면 다음을 입력합니다.
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![지원 패키지 편집](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. 메시지가 표시되면 수정된 지원 패키지에 암호화 암호를 제공합니다.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Microsoft 지원 요청 시 공유할 수 있도록 새 암호를 적어둡니다.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>예제: 지원 패키지를 암호로 보호 된 공유에 파일 편집

다음 예제에서는 지원 패키지의 암호를 해독하고 편집 및 다시 암호화하는 방법을 보여 줍니다.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>다음 단계

* [지원 패키지에서 수집된 정보](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)에 대해 자세히 알아보기
* [지원 패키지 및 디바이스 로그를 사용하여 디바이스 배포 문제를 해결](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)하는 방법을 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 알아봅니다.

