---
title: StorSimple 디바이스 관리를 위한 PowerShell | Microsoft Docs
description: StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스를 관리하는 방법을 알아봅니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 564c121aa90746498a94022fd0fb8d8529142c91
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128586"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>StorSimple용 Windows PowerShell을 사용하여 디바이스 관리

## <a name="overview"></a>개요

StorSimple용 Windows PowerShell은 Microsoft Azure StorSimple 디바이스를 관리하는 데 사용할 수 있는 명령줄 인터페이스를 제공합니다. 이름에서 알 수 있듯이 제한된 Runspace에서 기본 제공되는 Windows PowerShell 기반의 명령줄 인터페이스입니다. 명령줄의 사용자 관점에서 제한된 Runspace는 Windows PowerShell의 제한된 버전으로 나타납니다. Windows PowerShell의 일부 기본 기능을 유지하는 동시에 이 인터페이스는 Microsoft Azure StorSimple 디바이스를 관리하기 위한 전용 cmdlet을 추가로 포함합니다.

이 문서에서는 이 인터페이스에 연결할 수 있는 방법을 포함하여 StorSimple용 Windows PowerShell 기능에 대해 설명하며 이 인터페이스를 사용하여 수행할 수 있는 단계별 절차 또는 워크플로에 대한 링크를 포함합니다. 워크플로에는 디바이스를 등록하고, 디바이스에서 네트워크 인터페이스를 구성하고, 디바이스가 유지 관리 모드에 있도록 요구하는 업데이트를 설치하고, 디바이스 상태를 변경하고, 발생할 수 있는 문제를 해결하는 방법이 포함됩니다.

이 문서를 읽은 후 다음을 수행할 수 있습니다.

* StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스에 연결
* StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스 관리
* StorSimple용 Windows PowerShell에서 도움말 보기

> [!NOTE]
> * StorSimple용 Windows PowerShell cmdlet을 사용하면 직렬 콘솔에서 또는 Windows PowerShell 원격을 통해 원격으로 StorSimple 디바이스를 관리할 수 있습니다. 이 인터페이스에서 사용할 수 있는 개별 cmdlet에 대한 자세한 내용은 [StorSimple용 Windows PowerShell에 대한 cmdlet 참조](https://technet.microsoft.com/library/dn688168.aspx)를 참조하세요.
> * Azure PowerShell StorSimple cmdlet은 명령줄에서 StorSimple 서비스 수준 및 마이그레이션 작업을 자동화할 수 있게 해주는 다른 cmdlet 컬렉션입니다. StorSimple용 Azure PowerShell cmdlet에 대한 자세한 내용은 [Azure StorSimplecmdlet 참조](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure)를 참조하세요.


다음 방법 중 하나를 사용하여 StorSimple용 Windows PowerShell에 액세스할 수 있습니다.

* [StorSimple 디바이스 직렬 콘솔에 연결](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Windows PowerShell을 사용하여 StorSimple에 원격으로 연결](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>디바이스 직렬 콘솔을 통해 StorSimple용 Windows PowerShell에 연결

[PuTTY](https://www.putty.org/) 또는 유사한 터미널 에뮬레이션 소프트웨어를 다운로드하여 StorSimple용 Windows PowerShell에 연결할 수 있습니다. Microsoft Azure StorSimple 디바이스에 액세스하도록 PuTTY를 구성해야 합니다. 다음 항목에는 PuTTy를 구성하고 디바이스에 연결하는 방법에 대한 자세한 단계가 포함되어 있습니다. 직렬 콘솔의 다양한 메뉴 옵션에 대해서도 설명합니다.

### <a name="putty-settings"></a>PuTTY 설정

다음 PuTTY 설정을 사용하여 직렬 콘솔에서 Windows PowerShell 인터페이스에 연결해야 합니다.

#### <a name="to-configure-putty"></a>PuTTY를 구성하려면

1. PuTTY **재구성** 대화 상자의 **범주** 창에서 **키보드**를 선택합니다.
2. 다음 옵션이 선택되었는지 확인합니다(새 세션을 시작할 때의 기본 설정임).
   
   | 키보드 항목 | 여기서 |
   | --- | --- |
   | 백스페이스 키 |Ctrl-? (127) |
   | Home 및 End 키 |Standard |
   | 기능 키 및 키패드 |Esc[n~ |
   | 커서 키의 초기 상태 |정상 |
   | 숫자 키패드의 초기 상태 |정상 |
   | 추가 키보드 기능 사용 |Ctrl-Alt는 AltGr과 다름 |
   
    ![지원되는 Putty 설정](./media/storsimple-windows-powershell-administration/IC740877.png)
3. **적용**을 클릭합니다.
4. **범주** 창에서 **변환**을 선택합니다.
5. **원격 문자 집합** 목록 상자에서 **UTF-8**을 선택합니다.
6. **선 그리기 문자 처리** 아래에서 **유니코드 선 그리기 코드 포인트 사용**을 선택합니다. 다음 스크린샷은 올바른 PuTTY 선택 항목을 보여 줍니다.
   
    ![UTF Putty 설정](./media/storsimple-windows-powershell-administration/IC740878.png)
7. **적용**을 클릭합니다.

이제 PuTTY에서 다음 단계를 수행하여 디바이스 직렬 콘솔에 연결할 수 있습니다.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>직렬 콘솔 정보

직렬 콘솔을 통해 StorSimple 디바이스의 Windows PowerShell 인터페이스에 액세스하면 배너 메시지와 메뉴 옵션이 차례로 표시됩니다.

배너 메시지에는 모델, 이름, 설치된 소프트웨어 버전 및 액세스 중인 컨트롤러의 상태와 같은 기본 StorSimple 디바이스 정보가 포함되어 있습니다. 다음 그림은 배너 메시지의 예를 보여 줍니다.

![직렬 배너 메시지](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> 배너 메시지를 사용하여 연결된 컨트롤러가 _능동_ 또는 _수동_인지 식별할 수 있습니다.

다음 그림은 직렬 콘솔 메뉴에서 사용할 수 있는 다양한 Runspace 옵션을 보여 줍니다.

![디바이스 2 등록](./media/storsimple-windows-powershell-administration/IC740906.png)

다음 설정에서 선택할 수 있습니다.

1. **모든 권한으로 로그인** 이 옵션을 사용하면 적절한 자격 증명으로 로컬 컨트롤러의 **SSAdminConsole** Runspace에 연결할 수 있습니다. 로컬 컨트롤러는 StorSimple 디바이스의 직렬 콘솔을 통해 현재 액세스하는 컨트롤러입니다. 이 옵션을 사용하여 Microsoft 지원에서 가능한 디바이스 문제를 해결하기 위해 무제한 Runspace(지원 세션)에 액세스하도록 허용할 수도 있습니다. 옵션 1을 사용하여 로그온한 후 특정 cmdlet을 실행하여 Microsoft 지원 엔지니어가 무제한 Runspace에 액세스하도록 허용할 수 있습니다. 자세한 내용은 [지원 세션 시작](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)을 참조하세요.
   
2. **모든 권한으로 피어 컨트롤러에 로그인** 이 옵션은 적절한 자격 증명으로 피어 컨트롤러의 **SSAdminConsole** Runspace에 연결할 수 있다는 점을 제외하고 옵션 1과 동일합니다. StorSimple 디바이스는 능동-수동 구성으로 두 개의 컨트롤러를 포함하는 고가용성 디바이스이기 때문에 피어는 직렬 콘솔을 통해 액세스하는 디바이스의 다른 컨트롤러를 가리킵니다.
   옵션 1과 마찬가지로, 이 옵션을 사용하여 Microsoft 지원이 피어 컨트롤러의 무제한 Runspace에 액세스하도록 허용할 수도 있습니다.

3. **제한된 액세스 권한으로 연결** 이 옵션은 제한된 모드로 Windows PowerShell 인터페이스에 액세스하는 데 사용됩니다. 액세스 자격 증명을 묻는 메시지가 표시되지 않습니다. 이 옵션은 옵션 1과 2에 비해 더 제한된 Runspace에 연결합니다.  이 Runspace에서 수행할 수 **없는데* 옵션 1을 통해 사용할 수 있는 작업 중 일부는 다음과 같습니다.
   
   * 출하 시 설정으로 복원
   * 암호 변경
   * 지원 액세스 사용 또는 사용 안 함
   * 업데이트 적용
   * 핫픽스 설치

     > [!NOTE]
     > 디바이스 관리자 암호를 잊어버렸으며 옵션 1 또는 2를 통해 연결할 수 없는 경우의 기본 옵션입니다.

4. **언어 변경** 이 옵션을 사용하면 Windows PowerShell 인터페이스의 표시 언어를 변경할 수 있습니다. 지원되는 언어는 영어, 일본어, 러시아어, 프랑스어, 한국어, 스페인어, 이탈리아어, 독일어, 중국어 및 포르투갈어(브라질)입니다.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 사용하여 StorSimple에 원격으로 연결

Windows PowerShell 원격을 사용하여 StorSimple 디바이스에 연결할 수 있습니다. 이러한 방식으로 연결하면 메뉴가 표시되지 않습니다. (디바이스의 직렬 콘솔을 사용하여 연결하는 경우에만 메뉴가 표시됩니다. 원격으로 연결하면 직렬 콘솔의 "옵션 1 - 모든 권한"에 해당하는 권한을 직접 갖습니다.) Windows PowerShell 원격을 사용하여 특정 Runspace에 연결합니다. 표시 언어를 지정할 수도 있습니다.

표시 언어는 직렬 콘솔 메뉴에서 **언어 변경** 옵션을 사용하여 설정하는 언어와 독립적입니다. 지정되지 않은 경우 원격 PowerShell은 연결에 사용되는 디바이스의 로캘을 자동으로 선택합니다.

> [!NOTE]
> Microsoft Azure 가상 호스트 및 StorSimple Cloud Appliance로 작업하는 경우 Windows PowerShell 원격 및 가상 호스트를 사용하여 클라우드 어플라이언스에 연결할 수 있습니다. Windows PowerShell 세션에서 정보를 저장할 호스트의 공유 위치를 설정한 경우 _Everyone_ 사용자 보안 주체에 인증된 사용자만 포함된다는 것을 알아야 합니다. 따라서 _Everyone_의 액세스를 허용하도록 공유를 설정한 후 자격 증명을 지정하지 않고 연결하면 인증되지 않은 익명 보안 주체가 사용되며 오류가 표시됩니다. 이 문제를 해결하려면 공유 호스트에서 게스트 계정을 사용하도록 설정한 다음 공유에 대한 모든 권한을 게스트 계정에 부여하거나 Windows PowerShell cmdlet과 함께 유효한 자격 증명을 지정해야 합니다.


HTTP 또는 HTTPS를 사용하여 Windows PowerShell 원격을 통해 연결할 수 있습니다. 다음 자습서의 지침을 사용합니다.

* [HTTP를 사용하여 원격으로 연결](storsimple-8000-remote-connect.md#connect-through-http)
* [HTTPS를 사용하여 원격으로 연결](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>연결 보안 고려 사항

StorSimple용 Windows PowerShell에 연결하는 방법을 결정하는 경우 다음 사항을 고려하세요.

* 디바이스 직렬 콘솔에 직접 연결하는 것은 안전하지만 네트워크 스위치를 통해 직렬 콘솔에 연결하는 것은 안전하지 않습니다. 네트워크 스위치를 통해 디바이스 직렬에 연결할 때는 보안 위험에 주의하세요.
* HTTP 세션을 통해 연결하는 경우 네트워크에서 직렬 콘솔을 통해 연결하는 것보다 보안이 강화될 수 있습니다. 가장 안전한 방법은 아니지만 신뢰할 수 있는 네트워크에서는 적합합니다.
* HTTPS 세션을 통해 연결하는 것이 가장 안전하고 권장되는 옵션입니다.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스 관리

다음 표에서 모든 일반 관리 작업 및 StorSimple 디바이스의 Windows PowerShell 인터페이스 내에서 수행할 수 있는 복잡한 워크플로의 요약을 보여 줍니다. 각 워크플로에 대한 자세한 내용은 표에서 적절한 항목을 클릭합니다.

#### <a name="windows-powershell-for-storsimple-workflows"></a>StorSimple용 Windows PowerShell 워크플로

| 수행하려는 작업 ... | 이 절차를 사용합니다. |
| --- | --- |
| 디바이스 등록 |[StorSimple용 Windows PowerShell을 사용하여 디바이스 구성 및 등록](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| 웹 프록시 구성</br>웹 프록시 설정 보기 |[StorSimple 디바이스에 대한 웹 프록시 구성](storsimple-8000-configure-web-proxy.md) |
| 디바이스에서 DATA 0 네트워크 인터페이스 설정 수정 |[StorSimple 디바이스에 대한 DATA 0 네트워크 인터페이스 수정](storsimple-8000-modify-data-0.md) |
| 컨트롤러 중지  </br> 컨트롤러 다시 시작 또는 종료 </br> 디바이스 종료</br>디바이스를 공장 기본 설정으로 재설정 |[디바이스 컨트롤러 관리](storsimple-8000-manage-device-controller.md) |
| 유지 관리 모드 업데이트 및 핫픽스 설치 |[디바이스 업데이트](storsimple-update-device.md) |
| 유지 관리 모드 시작  </br>유지 관리 모드 종료 |[StorSimple 디바이스 모드](storsimple-8000-device-modes.md) |
| 지원 패키지 만들기</br>지원 패키지 암호 해독 및 편집 |[지원 패키지 만들기 및 관리](storsimple-8000-create-manage-support-package.md) |
| 지원 세션 시작</br> |[StorSimple용 Windows PowerShell에서 지원 세션 시작](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell에서 도움말 보기

StorSimple용 Windows PowerShell에서 cmdlet 도움말을 사용할 수 있습니다. 시스템의 도움말을 업데이트하는 데 사용할 수 있는 이 도움말의 온라인 최신 버전도 제공됩니다.

이 인터페이스에서 도움을 받는 방법은 Windows PowerShell과 유사하며 대부분의 도움말 관련 cmdlet이 작동합니다. 온라인 Windows PowerShell에 대 한 도움말을 찾을 수 있습니다. [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

다음은 도움말을 업데이트하는 방법을 포함하여 이 Windows PowerShell 인터페이스의 도움말 형식에 대한 간략한 설명입니다.

### <a name="to-get-help-for-a-cmdlet"></a>cmdlet에 대한 도움말을 보려면

* cmdlet 또는 함수에 대한 도움말을 보려면 다음 명령을 사용합니다. `Get-Help <cmdlet-name>`
* cmdlet에 대한 온라인 도움말을 보려면 `-Online` 매개 변수와 함께 이전 cmdlet을 사용합니다. `Get-Help <cmdlet-name> -Online`
* 전체 도움말을 보려면 `–Full` 매개 변수를 사용할 수 있습니다. 예제를 보려면 `–Examples` 매개 변수를 사용합니다.

### <a name="to-update-help"></a>도움말을 업데이트하려면

Windows PowerShell 인터페이스에서 도움말을 쉽게 업데이트할 수 있습니다. 시스템의 도움말을 업데이트하려면 다음 단계를 따르세요.

#### <a name="to-update-cmdlet-help"></a>cmdlet 도움말을 업데이트하려면
1. **관리자 권한으로 실행** 옵션을 사용하여 Windows PowerShell을 시작합니다.
2. 명령 프롬프트에 `Update-Help`를 입력합니다.
3. 업데이트된 도움말 파일이 설치됩니다.
4. 도움말 파일이 설치된 후 다음을 입력합니다. `Get-Help Get-Command`. 도움말을 사용할 수 있는 cmdlet 목록이 표시됩니다.

> [!NOTE]
> Runspace에서 사용 가능한 모든 cmdlet의 목록을 가져오려면 해당 메뉴 옵션에 로그인한 다음 `Get-Command` cmdlet을 실행합니다.


## <a name="next-steps"></a>다음 단계

위의 워크플로 중 하나를 수행할 때 StorSimple 디바이스에서 문제가 발생하는 경우 [StorSimple 배포 문제를 해결하는 도구](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)를 참조하세요.

