---
title: StorSimple 8000 시리즈 배포 문제 해결 | Microsoft Docs
description: StorSimple을 처음 배포할 때 발생하는 오류를 진단하고 해결하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 6bb587de2f0f3ef9c4e8c4a856ee4b7430e9b9cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631555"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>StorSimple 디바이스 배포 문제 해결
## <a name="overview"></a>개요
이 문서는 Microsoft Azure StorSimple 배포에 대한 유용한 문제 해결 지침을 제공합니다. 일반 문제, 가능한 원인 및 StorSimple을 구성할 때 발생할 수 있는 문제를 해결할 수 있는 권장 단계를 설명합니다. 

이 정보는 StorSimple 8000 시리즈 물리적 디바이스 및 StorSimple Cloud Appliance에 모두 적용됩니다.

> [!NOTE]
> 발생할 수 있는 디바이스 구성 관련 문제는 처음으로 디바이스를 배포할 때 나타나거나 디바이스가 작동 가능한 경우 나중에 나타날 수 있습니다. 이 문서는 처음 배포 시 문제 해결에 중점을 둡니다. 운영 디바이스 문제를 해결하려면 [진단 도구를 사용하여 운영 디바이스 문제 해결](storsimple-8000-diagnostics.md)로 이동합니다.

또한 이 문서는 StorSimple 배포 문제 해결을 위한 도구에 대해 설명하며 단계별 문제 해결 예제를 제공합니다.

## <a name="first-time-deployment-issues"></a>처음 배포 시 문제
처음으로 디바이스를 배포할 때 문제가 발생하는 경우 다음 사항을 고려합니다.

* 물리적 디바이스의 문제를 해결하는 경우 하드웨어가 [StorSimple 8100 디바이스 설치](storsimple-8100-hardware-installation.md) 또는 [StorSimple 8600 디바이스 설치](storsimple-8600-hardware-installation.md)에 설명된 대로 설치 및 구성되었는지 확인합니다.
* 배포를 위한 필수 구성 요소를 확인합니다. [배포 구성 검사 목록](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)에 모든 정보가 설명되어 있는지 확인합니다.
* StorSimple 릴리스 정보를 검토하여 해당 문제가 설명되어 있는지 확인합니다. 릴리스 정보는 알려진 설치 문제에 대한 해결 방법을 포함합니다. 

디바이스 배포 중 사용자가 접하는 가장 일반적인 문제는 해당 설치 마법사를 실행하는 경우 및 StorSimple용 Windows PowerShell을 통해 디바이스를 등록하는 경우에 발생합니다. (StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스를 등록하고 구성합니다. 장치 등록에 대 한 자세한 내용은 참조 하세요. [3 단계: 구성 및 StorSimple 용 Windows PowerShell을 통해 장치를 등록](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

다음 섹션에서는 StorSimple 디바이스를 처음 구성할 때 발생하는 문제를 해결할 수 있습니다.

## <a name="first-time-setup-wizard-process"></a>처음 설치 마법사 프로세스
다음 단계에서는 설치 마법사 프로세스를 간략하게 설명합니다. 자세한 설치 정보는 [온-프레미스 StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)를 참조하세요.

1. [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) cmdlet을 실행하여 나머지 과정을 안내하는 설치 마법사를 시작합니다. 
2. 네트워크 구성: 설치 마법사를 사용하면 StorSimple 디바이스에서 데이터 0 네트워크 인터페이스에 대한 네트워크 설정을 구성합니다. 이 설정은 다음을 포함합니다.
   * 가상 IP(VIP), 서브넷 마스크 및 게이트웨이 – [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) cmdlet이 백그라운드에서 실행됩니다. StorSimple 디바이스의 데이터 0 네트워크 인터페이스에 대한 IP 주소, 서브넷 마스크 및 게이트웨이를 구성합니다.
   * 기본 DNS 서버 - [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) cmdlet이 백그라운드에서 실행됩니다. StorSimple 솔루션에 대한 DNS 설정을 구성합니다.
   * NTP 서버 – [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet이 백그라운드에서 실행됩니다. StorSimple 솔루션에 대한 NTP 서버 설정을 구성합니다.
   * 선택적 웹 프록시 - [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) cmdlet이 백그라운드에서 실행됩니다. StorSimple 솔루션에 대한 웹 프록시 구성을 설정하고 사용합니다.
3. 암호 설정: 다음 단계는 디바이스 관리자 암호 설정입니다.
   디바이스 관리자 암호는 디바이스에 로그온하는 데 사용됩니다. 기본 디바이스 암호는 **Password1**입니다.
        
     > [!IMPORTANT]
     > 등록 하기 전에 암호가 수집되지만 디바이스를 성공적으로 등록한 후에만 적용됩니다. 암호를 적용하지 못한 경우 필요한 암호(복잡성 요구 사항에 맞는)가 수집될 때까지 다시 암호를 입력하라는 메시지가 표시됩니다.
     
4. 디바이스 등록: 최종 단계는 Microsoft Azure에서 실행되는 StorSimple 디바이스 관리자 서비스를 사용하는 디바이스 등록입니다. 등록할 때 Azure Portal에서 [서비스 등록 키를 가져와](storsimple-8000-manage-service.md#get-the-service-registration-key) 설치 마법사에서 제공해야 합니다. **디바이스가 성공적으로 등록되면 서비스 데이터 암호화 키가 제공됩니다. 해당 서비스로 모든 후속 디바이스 서비스를 등록할 때 필요하기 때문에 이 암호화 키를 안전한 위치에 보관해야 합니다.**

## <a name="common-errors-during-device-deployment"></a>디바이스 배포 중 일반 오류
다음의 경우 발생할 수 있는 일반 오류를 아래 표에서 표시합니다.

* 필요한 네트워크 설정을 구성합니다.
* 선택적 웹 프록시 설정을 구성합니다.
* 디바이스 관리자 암호를 설정합니다.
* 디바이스를 등록합니다.

## <a name="errors-during-the-required-network-settings"></a>필요한 네트워크 설정 중 오류
| 아니요. | 오류 메시지 | 가능한 원인 | 권장 작업 |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: 이 명령은 활성 컨트롤러 에서만 실행할 수 있습니다. |수동 컨트롤러에서 구성을 수행합니다. |활성 컨트롤러에서 이 명령을 실행합니다. 자세한 내용은 [디바이스에서 활성 컨트롤러 식별](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)을 참조하세요. |
| 2 |Invoke-HcsSetupWizard: 장치가 준비 되지 않았습니다. |데이터 0에 대한 네트워크 연결 문제가 있습니다. |데이터 0에 대한 실제 네트워크 연결을 확인합니다. |
| 3 |Invoke-HcsSetupWizard: 네트워크 상의 다른 시스템과 충돌 하는 IP 주소 (HRESULT의 예외: 0x80070263). |데이터 0에 대해 제공된 IP가 이미 다른 시스템에서 사용 중입니다. |사용되지 않는 새 IP를 제공합니다. |
| 4 |Invoke-HcsSetupWizard: 클러스터 리소스 실패 했습니다. (HRESULT의 예외: 0x800713AE). |VIP가 중복되었습니다. 제공된 IP가 이미 사용 중입니다. |사용되지 않는 새 IP를 제공합니다. |
| 5 |Invoke-HcsSetupWizard: 잘못 된 IPv4 주소입니다. |IP 주소는 잘못된 형식으로 제공됩니다. |형식을 확인하고 다시 사용자의 IP 주소를 제공합니다. 자세한 내용은 [Ipv4 주소 지정][1]을 참조하세요. |
| 6 |Invoke-HcsSetupWizard: 잘못 된 IPv6 주소입니다. |IP 주소는 잘못된 형식으로 제공됩니다. |형식을 확인하고 다시 사용자의 IP 주소를 제공합니다. 자세한 내용은 [Ipv6 주소 지정][2]을 참조하세요. |
| 7 |Invoke-HcsSetupWizard: 끝점 매퍼에서 사용할 수 있는 끝점이 더 이상 있습니다. (HRESULT의 예외: 0x800706D9) |클러스터 기능이 작동하지 않습니다. |[Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>선택적 웹 프록시 설정 중 오류
| 번호 | 오류 메시지 | 가능한 원인 | 권장 작업 |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: 잘못 된 매개 변수 (HRESULT의 예외: 0x80070057) |프록시 설정에 대해 제공된 매개 변수 중 하나가 잘못되었습니다. |URI는 올바른 형식으로 제공되지 않습니다. 다음 형식을 사용합니다. http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: RPC 서버를 사용할 수 없습니다 (HRESULT의 예외: 0x800706ba) |근본 원인은 다음 중 하나입니다.<ol><li>클러스터가 켜지지 않았습니다.</li><li>수동 컨트롤러는 활성 컨트롤러와 통신할 수 없으며 명령은 수동 컨트롤러에서 실행되었습니다.</li></ol> |근본 원인에 따라:<ol><li>[Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하여 클러스터가 작동하는지 확인합니다.</li><li>활성 컨트롤러에서 명령을 실행합니다. 수동 컨트롤러에서 명령을 실행하려는 경우 수동 컨트롤러가 활성 컨트롤러와 통신할 수 있는지 확인해야 합니다. 이 연결이 끊어진 경우, [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)해야 합니다.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC 호출이 실패 했습니다 (HRESULT의 예외: 0x800706be) |클러스터의 작동이 중단되었습니다. |[Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하여 클러스터가 작동하는지 확인합니다. |
| 4 |Invoke-HcsSetupWizard: 클러스터 리소스를 찾을 수 없습니다 (HRESULT의 예외: 0x8007138f) |클러스터 리소스를 찾을 수 없습니다. 설치가 올바르지 않은 경우 발생할 수 있습니다. |디바이스를 공장 기본 설정으로 다시 설정해야 합니다. [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하여 클러스터 리소스를 만듭니다. |
| 5 |Invoke-HcsSetupWizard: 클러스터 리소스가 온라인 상태가 아님 (HRESULT의 예외: 0x8007138c) |클러스터 리소스가 온라인 상태가 아닙니다. |[Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. |

## <a name="errors-related-to-device-administrator-password"></a>디바이스 관리자 암호 관련 오류
기본 디바이스 관리자 암호는 **Password1**입니다. 처음 로그온한 후 이 암호가 만료되므로 변경하려면 설치 마법사를 사용해야 합니다. 처음으로 디바이스를 등록하는 경우 새 디바이스 관리자 암호를 입력해야 합니다. 

암호는 다음 요구 사항을 충족하는지 확인합니다.

* 디바이스 관리자 암호는 8자에서 15자 사이여야 합니다.
* 암호는 대문자, 소문자, 숫자 및 특수 문자의 4가지 문자 유형 중 3가지 문자 유형을 포함해야 합니다. 
* 암호는 마지막 24개의 암호와 동일할 수 없습니다.

또한 암호가 만료되는 해를 염두하고 디바이스를 성공적으로 등록한 후에만 변경할 수 있습니다. 어떤 이유로든 등록이 실패하면, 암호가 변경되지 않습니다.

디바이스 관리자 암호에 대한 자세한 내용을 보려면 [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 암호 변경](storsimple-8000-change-passwords.md)으로 이동합니다.

디바이스 관리자 및 StorSimple Snapshot Manager 암호를 설정할 때 다음 오류 중 하나 이상이 발생할 수 있습니다.

| 아니요. | 오류 메시지 | 권장 작업 |
| --- | --- | --- |
| 1 |암호는 최대 길이를 초과합니다. |디바이스 관리자 암호는 8자에서 15자 사이여야 합니다. |
| 2 |암호가 필요한 길이에 맞지 않습니다. |디바이스 관리자 암호는 8자에서 15자 사이여야 합니다.|
| 3 |암호는 소문자 문자를 포함해야 합니다. |암호는 소문자, 대문자, 숫자 및 특수 문자의 4가지 문자 유형 중 3가지 문자 유형을 포함해야 합니다. 암호가 이 요구 사항을 충족하는지 확인합니다. |
| 4 |암호는 숫자를 포함해야 합니다. |암호는 소문자, 대문자, 숫자 및 특수 문자의 4가지 문자 유형 중 3가지 문자 유형을 포함해야 합니다. 암호가 이 요구 사항을 충족하는지 확인합니다. |
| 5 |암호는 특수 문자를 포함해야 합니다. |암호는 소문자, 대문자, 숫자 및 특수 문자의 4가지 문자 유형 중 3가지 문자 유형을 포함해야 합니다. 암호가 이 요구 사항을 충족하는지 확인합니다. |
| 6 |암호는 대문자, 소문자, 숫자 및 특수 문자의 4가지 문자 유형 중 3가지 유형을 포함해야 합니다. |암호는 필요한 종류의 문자를 포함하지 않습니다. 암호가 이 요구 사항을 충족하는지 확인합니다. |
| 7 |매개 변수는 확인과 일치하지 않습니다. |암호 요구 사항을 모두 만족하고 이를 올바르게 입력했는지 확인합니다. |
| 8 |암호는 기본값과 일치할 수 없습니다. |기본 암호는 *Password1*입니다. 처음으로 로그온 한 후에 이 암호를 변경해야 합니다. |
| 9 |입력한 암호는 디바이스 암호와 일치하지 않습니다. 암호를 다시 입력하십시오. |암호를 확인하고 다시 입력합니다. |

디바이스를 등록하기 전에 암호가 수집되지만 디바이스를 성공적으로 등록한 후에만 적용됩니다. 암호 복구 워크플로를 사용하려면 디바이스를 등록해야 합니다.

> [!IMPORTANT]
> 일반적으로 암호를 적용하려는 시도가 실패하면 성공할 때까지 다음 소프트웨어가 반복적으로 암호를 수집합니다. 드물지만 암호를 적용할 수 없는 경우가 있습니다. 이 상황에서는 디바이스를 등록하여 계속할 수 있지만 암호가 변경되지 않습니다. Azure Portal에서 등록 후 디바이스 관리자 암호를 변경할 수 있습니다.


StorSimple 디바이스 관리자 서비스를 통해 Azure Portal에서 암호를 재설정할 수 있습니다. 자세한 내용을 보려면 [디바이스 관리자 암호 변경](storsimple-8000-change-passwords.md#change-the-device-administrator-password)으로 이동합니다.

## <a name="errors-during-device-registration"></a>디바이스 등록 중 오류
Microsoft Azure에서 실행되는 StorSimple 디바이스 관리자 서비스를 사용하여 디바이스를 등록합니다. 디바이스를 등록하는 동안 다음 문제 중 하나 이상이 발생할 수 있습니다.

| 아니요. | 오류 메시지 | 가능한 원인 | 권장 작업 |
| --- | --- | --- | --- |
| 1 |오류 350027: StorSimple 장치 관리자를 사용 하 여 장치를 등록 하지 못했습니다. | |몇 분간 기다린 다음 작업을 다시 시도하세요. 문제가 지속되면, [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. |
| 2 |Error 350013: 장치를 등록 하는 중에 오류가 발생 했습니다. 잘못된 서비스 등록 키 때문일 수 있습니다. | |올바른 서비스 등록 키로 디바이스를 다시 등록하세요. 자세한 내용은 [서비스 등록 키 받기](storsimple-8000-manage-service.md#get-the-service-registration-key)를 참조하세요. |
| 3 |오류 350063: StorSimple 장치 관리자 서비스를 통과 했지만 등록에 인증이 실패 했습니다. 잠시 후 작업을 다시 시도하세요. |이 오류는 ACS로 인증이 통과했지만 서비스에 대한 레지스터 호출이 실패했음을 나타냅니다. 간헐적인 네트워크 결함의 결과일 수 있습니다. |문제가 지속되면 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. |
| 4 |350049 오류: 등록하는 동안 서비스에 도달할 수 없습니다. |서비스에 호출되면 웹 예외가 수신됩니다. 경우에 따라 작업을 나중에 다시 시도하여 수정될 수 있습니다. |IP 주소 및 DNS 이름을 확인한 다음 작업을 다시 시도합니다. 문제가 지속되면, [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. |
| 5 |오류 350031: 장치가 이미 등록 되었습니다. | |필요한 작업이 없습니다. |
| 6 |오류 350016: 장치 등록에 실패 했습니다. | |등록 키가 올바른지 확인하세요. |
| 7 |Invoke-HcsSetupWizard: 장치를 등록 하는 동안 오류가 발생 했습니다. 잘못 된 IP 주소 또는 DNS 이름 때문일 수 있습니다. 네트워크 설정을 확인하고 다시 시도하세요. 문제가 계속되면 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. (오류 350050) |디바이스에 외부 네트워크를 ping할 수 있는지 확인합니다. 외부 네트워크에 연결되지 않은 경우 이 오류와 함께 등록이 실패할 수 있습니다. 이 오류는 다음 중 하나 이상의 조합일 수 있습니다:<ul><li>잘못된 IP</li><li>잘못된 서브넷</li><li>잘못된 게이트웨이</li><li>잘못된 DNS 설정</li></ul> |[단계별 문제 해결 예제](#step-by-step-storsimple-troubleshooting-example)의 단계를 참조하세요. |
| 8 |Invoke-HcsSetupWizard: 내부 서비스 오류 [0x1fbe2]로 인해 현재 작업이 실패 했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |모든 사용자가 서비스 또는 에이전트에서 볼 수 없는 오류에 대해 발생한 일반 오류입니다. 가장 일반적인 이유가 ACS 인증 실패일 수 있습니다. 실패에 대한 가능한 원인은 NTP 서버 구성에 문제가 있고 디바이스의 시간이 올바르게 설정되지 않았기 때문일 수 있습니다. |시간을 수정하고(문제가 있는 경우) 등록 작업을 다시 시도하세요. Set-HcsSystem -Timezone 명령을 사용하여 표준 시간대를 조정하는 경우 표준 시간대의 각 단어 첫 글자를 대문자로 표시합니다(예: "Pacific Standard Time").  이 문제가 지속되면 다음 단계는 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. |
| 9 |경고: 장치를 활성화할 수 없습니다. 디바이스 관리자 및 StorSimple Snapshot Manager 암호가 변경되지 않습니다. |등록이 실패하면, 디바이스 관리자 및 StorSimple Snapshot Manager 암호가 변경되지 않습니다. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>StorSimple 배포 문제 해결을 위한 팁
StorSimple은 StorSimple 솔루션 문제를 해결하는데 사용할 수 있는 여러 도구를 포함합니다. 내용은 다음과 같습니다.

* 지원 패키지 및 디바이스 로그
* 문제 해결을 위해 특별히 설계된 Cmdlet

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>문제 해결에 사용 가능한 지원 패키지 및 디바이스 로그
지원 패키지는 디바이스 문제를 해결하는 Microsoft 기술 지원 서비스 팀을 지원할 수 있는 모든 관련 로그를 포함합니다. StorSimple용 Windows PowerShell을 사용하여 지원 담당자와 공유할 수 있는 암호화된 지원 패키지를 생성할 수 있습니다.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>지원 패키지의 로그 또는 내용을 보려면
1. [지원 패키지 만들기 및 관리](storsimple-8000-create-manage-support-package.md)에서 설명한 대로 지원 패키지를 생성하려면 StorSimple용 Windows PowerShell을 사용합니다.
2. [암호 해독 스크립트](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65)를 로컬로 클라이언트 컴퓨터에 다운로드합니다.
3. 이 [단계별 절차](storsimple-8000-create-manage-support-package.md#edit-a-support-package)를 사용하여 지원 패키지를 열고 암호를 해독합니다.
4. 암호 해독된 지원 패키지 로그는 etw/etvx 형식입니다. 다음 단계를 수행하여 Windows 이벤트 뷰어에서 이 파일을 볼 수 있습니다.
   
   1. Windows 클라이언트에서 **eventvwr** 명령을 실행합니다. 이벤트 뷰어를 시작합니다.
   2. **작업** 창에서 **저장된 로그 열기**를 클릭하고 etvx/etw 형식(지원 패키지)의 로그 파일을 가리킵니다. 이제 파일을 볼 수 있습니다. 파일을 연 후 마우스 오른쪽 단추로 클릭하고 텍스트로 파일을 저장할 수 있습니다.
      
      > [!IMPORTANT]
      > **Get-WinEvent** cmdlet을 사용하여 Windows PowerShell에서 이 파일을 열 수도 있습니다. 자세한 내용은 Windows PowerShell cmdlet 참조 설명서의 [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx)를 참조하세요.
     
5. 로그가 이벤트 뷰어에서 열리면, 디바이스 구성과 관련된 문제를 포함하는 다음 로그를 찾습니다.
   
   * hcs_pfconfig/Operational 로그
   * hcs_pfconfig/Config
6. 로그 파일에서, 설치 마법사에서 호출한 cmdlet와 관련된 문자열을 검색합니다. 이 cmdlet의 목록은 [처음 설치 마법사 프로세스](#first-time-setup-wizard-process)를 참조하세요.
7. 문제의 원인을 알아낼 수 없는 경우, 다음 단계는 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)할 수 있습니다. Microsoft 지원에 문의하는 경우 [지원 요청 만들기](storsimple-8000-contact-microsoft-support.md#create-a-support-request)의 단계를 사용합니다.

## <a name="cmdlets-available-for-troubleshooting"></a>문제 해결에 사용할 수 있는 Cmdlet
다음 Windows PowerShell cmdlet을 사용하여 연결 오류를 검색합니다.

* `Get-NetAdapter`: 이 cmdlet을 사용 하 여 네트워크 인터페이스의 상태를 검색 합니다.
* `Test-Connection`: 이 cmdlet을 사용 하 여 내부 및 외부 네트워크에서 네트워크 연결을 확인 합니다.
* `Test-HcsmConnection`: 이 cmdlet을 사용 하 여 성공적으로 등록 된 장치의 연결을 확인 합니다.
* `Sync-HcsTime`: 이 cmdlet을 사용 하 여 장치 시간을 표시 하 고 강제로 시간을 NTP 서버와 동기화 합니다.
* `Enable-HcsPing` 및 `Disable-HcsPing`: 이러한 cmdlet을 사용 하 여 StorSimple 장치에서 네트워크 인터페이스에 ping 호스트 수 있습니다. 기본적으로 StorSimple 네트워크 인터페이스는 ping 요청에 응답하지 않습니다.
* `Trace-HcsRoute`: 경로 추적 도구로 이 cmdlet을 사용합니다. 일정 기간 동안 최종 대상으로 각 라우터에 패킷이 전송을 전송하고 각 홉에서 반환되는 패킷에 기준으로 결과를 계산합니다. `Trace-HcsRoute`가 특정 라우터의 패킷 손실의 정도를 표시하기 때문에 어떤 라우터 또는 링크가 네트워크 문제를 초래할 수 있는지 파악할 수 있습니다.
* `Get-HcsRoutingTable`: 로컬 IP 라우팅 테이블을 표시 하려면이 cmdlet을 사용 합니다.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Get NetAdapter cmdlet 문제 해결
처음 디바이스 배포에 대한 네트워크 인터페이스를 구성할 때 해당 디바이스가 서비스에 아직 등록되지 않아 하드웨어 상태가 StorSimple 디바이스 관리자 서비스 UI에 표시되지 않습니다. 또한 서비스 동기화에 영향을 주는 문제가 있는 경우 특히, **하드웨어 상태** 블레이드가 디바이스의 상태를 올바르게 반영하지 않을 수 있습니다. 이 상황에서는 `Get-NetAdapter` cmdlet을 사용하여 네트워크 인터페이스의 상태를 확인할 수 있습니다.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>디바이스에서 모든 네트워크 어댑터의 목록을 보려면
1. StorSimple용 Windows PowerShell을 시작한 다음 `Get-NetAdapter`를 입력합니다. 
2. `Get-NetAdapter` cmdlet의 출력 및 다음 지침을 사용하여 네트워크 인터페이스의 상태를 이해합니다.
   
   * 인터페이스가 정상 상태이고 사용 가능한 경우 **ifIndex** 상태가 **작동**으로 표시됩니다.
   * 인터페이스가 정상 상태이지만 물리적으로 연결되어 있지 않은 경우(네트워크 케이블로), **ifIndex**는 **사용 안함**으로 표시됩니다.
   * 인터페이스가 정상 상태이지만 사용 불가능한 경우 **ifIndex** 상태가 **표시되지 않음**으로 나타납니다.
   * 인터페이스가 없는 경우 이 목록에 나타나지 않습니다. StorSimple 디바이스 관리자 서비스 UI에서는 이 인터페이스가 계속 실패한 상태로 표시됩니다.

이 cmdlet을 사용하는 방법에 대한 자세한 내용은 Windows PowerShell cmdlet 참조의 [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps)로 이동합니다.

다음 섹션에서는 샘플 `Get-NetAdapter` cmdlet의 출력 샘플을 표시합니다.

 이 예제에서는 컨트롤러 0은 수동 컨트롤러이었으며 다음과 같이 구성되었습니다.

* 데이터 0, 데이터 1, 데이터 2 및 데이터 3 네트워크 인터페이스는 디바이스에 존재합니다.
* 데이터 4 및 데이터 5 네트워크 인터페이스 카드는 없으며 출력에 나열되지 않습니다.
* 데이터 0이 활성화되었습니다.

컨트롤러 1은 활성 컨트롤러이었으며 다음과 같이 구성되었습니다.

* 데이터 0, 데이터 1, 데이터 2, 데이터 3 데이터 4 및 데이터 5 네트워크 인터페이스는 디바이스에 존재합니다.
* 데이터 0이 활성화되었습니다.

**샘플 출력 - 컨트롤러 0**

컨트롤러 0(수동 컨트롤러)의 출력은 다음과 같습니다. 데이터 1, 데이터 2 및 데이터 3는 연결되지 않습니다. 데이터 4 및 데이터 5가 디바이스에 존재하지 않기 때문에 표시되지 않습니다.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**샘플 출력 - 컨트롤러 1**

컨트롤러 1(활성 컨트롤러)의 출력은 다음과 같습니다. 디바이스에서 데이터 0 네트워크 인터페이스만이 구성되며 작동합니다.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Test-Connection cmdlet 문제 해결
`Test-Connection` cmdlet을 사용하여 StorSimple 디바이스를 외부 네트워크에 연결할 수 있는지 여부를 확인할 수 있습니다. DNS를 포함한 모든 네트워킹 매개 변수가 설치 마법사에서 올바르게 구성된 경우, `Test-Connection` cmdlet을 사용하여 outlook.com과 같은 네트워크 외부에 알려진 주소를 ping할 수 있습니다.

Ping이 비활성화된 경우 ping을 활성화하여 이 cmdlet과 연결 문제를 해결해야 합니다.

`Test-Connection` cmdlet에서 다음과 같은 출력 샘플을 확인하세요.

> [!NOTE]
> 첫 번째 샘플에서 디바이스는 잘못된 DNS로 구성됩니다. 두 번째 샘플에서 DNS가 올바릅니다.

**샘플 출력 – 잘못된 DNS**

다음 샘플에서 IPV4 및 IPV6 주소에 대한 출력이 없으며, DNS가 해석되지 않음을 표시합니다. 외부 네트워크에 연결되지 않았으며 올바른 DNS를 지정해야 함을 의미합니다.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**샘플 출력 – 올바른 DNS**

다음 샘플에서 DNS는 DNS가 올바르게 구성되었음을 나타내는 IPV4 주소를 반환합니다. 외부 네트워크에 대한 연결인지 확인합니다.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Test-HcsmConnection cmdlet 문제 해결
이미 연결되고 StorSimple 디바이스 관리자 서비스에 등록된 디바이스에는 `Test-HcsmConnection` cmdlet을 사용합니다. 이 cmdlet을 사용하면 등록된 디바이스 및 해당 StorSimple 디바이스 관리자 서비스 간의 연결을 확인합니다. StorSimple용 Windows PowerShell에서 이 명령을 실행할 수 있습니다.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Test-HcsmConnection cmdlet을 실행하려면
1. 디바이스가 등록되어 있는지 확인합니다.
2. 디바이스 상태를 확인합니다. 유지 관리 모드 또는 오프라인으로 디바이스를 비활성화한 경우 다음 오류 중 하나가 나타날 수 있습니다.
   
   * ErrorCode.CiSDeviceDecommissioned – 디바이스가 비활성화되었음을 표시합니다.
   * ErrorCode.DeviceNotReady – 디바이스가 유지 관리 모드에 있음을 나타냅니다.
   * ErrorCode.DeviceNotReady – 디바이스가 온라인 상태가 아님을 나타냅니다.
3. StorSimple 디바이스 관리자 서비스가 실행 중인지 확인합니다([Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) cmdlet 사용). 서비스를 실행하지 않는 경우 다음 오류가 나타날 수 있습니다.
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – Get-ClusterResource를 실행할 때 예외가 있음을 나타냅니다.
4. ACS(Access Control Service) 토큰을 확인합니다. 웹 예외가 발생한 경우, 게이트웨이 문제, 프록시 인증 누락, 잘못된 DNS 또는 인증 오류의 결과일 수 있습니다. 다음 오류가 표시될 수 있습니다.
   
   * ErrorCode.CiSApplianceGateway – HttpStatusCode.BadGateway 예외를 표시합니다. 이름 확인자 서비스는 호스트 이름을 확인할 수 없습니다.
   * ErrorCode.CiSApplianceProxy – HttpStatusCode.ProxyAuthenticationRequired 예외(HTTP 상태 코드 407)를 표시합니다. 클라이언트는 프록시 서버와 인증할 수 없습니다.
   * ErrorCode.CiSApplianceDNSError – WebExceptionStatus.NameResolutionFailure 예외를 표시합니다. 이름 확인자 서비스는 호스트 이름을 확인할 수 없습니다.
   * ErrorCode.CiSApplianceACSError – 서비스가 인증 오류를 반환했지만 연결되었음을 나타냅니다.
     
     웹 예외가 발생하지 않은 경우 ErrorCode.CiSApplianceFailure을 확인하세요. 이 오류는 어플라이언스가 실패했음을 나타냅니다.
5. 클라우드 서비스 연결을 확인하세요. 서비스에서 웹 예외가 발생한 경우 다음 오류가 나타낼 수 있습니다.
   
   * ErrorCode.CiSApplianceGateway – HttpStatusCode.BadGateway 예외를 나타냅니다. 중간 프록시 서버는 다른 프록시 또는 원래 서버에서 잘못된 요청을 수신했습니다.
   * ErrorCode.CiSApplianceProxy – HttpStatusCode.ProxyAuthenticationRequired 예외(HTTP 상태 코드 407)를 표시합니다. 클라이언트는 프록시 서버와 인증할 수 없습니다.
   * ErrorCode.CiSApplianceDNSError – WebExceptionStatus.NameResolutionFailure 예외를 표시합니다. 이름 확인자 서비스는 호스트 이름을 확인할 수 없습니다.
   * ErrorCode.CiSApplianceACSError – 서비스가 인증 오류를 반환했지만 연결되었음을 나타냅니다.
     
     웹 예외가 발생하지 않은 경우 ErrorCode.CiSApplianceSaasServiceError를 확인하세요. 이 오류는 StorSimple 디바이스 관리자 서비스에 문제가 있음을 나타냅니다.
6. Azure Service Bus 연결을 확인합니다. ErrorCode.CiSApplianceServiceBusError는 디바이스가 Service Bus에 연결할 수 없음을 나타냅니다.

로그 파일 CiSCommandletLog0Curr.errlog 및 CiSAgentsvc0Curr.errlog에는 예외 세부 정보와 같은 추가 정보가 있습니다.

Cmdlet을 사용하는 방법에 대한 자세한 내용을 보려면 Windows PowerShell 참조 설명서의 [HcsmConnection 테스트](https://technet.microsoft.com/library/dn715782.aspx)로 이동합니다.

> [!IMPORTANT]
> 활성 및 수동 컨트롤러 모두에 대해 이 cmdlet을 실행할 수 있습니다.

`Test-HcsmConnection` cmdlet에서 다음과 같은 출력 샘플을 확인하세요.

**예제 출력 – StorSimple 업데이트 3을 실행하는 성공적으로 등록된 디바이스**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**예제 출력 – 오프라인 디바이스** 

Azure Portal의 **오프라인** 상태인 디바이스에서 이 예제를 가져왔습니다.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

디바이스는 현재 웹 프록시 구성을 사용하여 연결할 수 없습니다. 웹 프록시 구성 또는 네트워크 연결에 문제가 발생할 수 있습니다. 이 경우, 웹 프록시 설정이 올바르며 웹 프록시 서버가 온라인 상태이고 연결할 수 있는지 확인해야 합니다.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Sync-HcsTime cmdlet를 사용하여 문제 해결
이 cmdlet을 사용하여 디바이스 시간을 표시합니다. 디바이스 시간에 NTP 서버와 오프셋에 있는 경우 이 cmdlet을 사용하여 NTP 서버와 시간을 강제로 동기화 할 수 있습니다.
- NTP 서버와 디바이스 간의 오프셋이 5 분 이상이면 경고가 표시됩니다. 
- 오프셋이 15분을 초과하면 디바이스가 오프라인 상태가 됩니다. 이 cmdlet을 시간을 강제로 동기화하는 데  계속 사용할 수 있습니다. 
- 그러나 오프셋이 15시간을 초과할 경우에는 시간을 강제 동기화할 수 없으며 오류 메시지가 표시됩니다.

**샘플 출력 – Sync-HcsTime을 사용하여 강제 시간 동기화**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>HcsPing 활성화 및 HcsPing cmdlet 비활성화를 사용하여 문제 해결
디바이스의 네트워크 인터페이스가 ICMP ping 요청에 응답하도록 이 cmdlet를 사용하세요. 기본적으로 StorSimple 네트워크 인터페이스는 ping 요청에 응답하지 않습니다. 이 cmdlet을 사용하는 것이 디바이스가 온라인 상태이고 연결할 수 있는지 확인할 수 있는 가장 쉬운 방법입니다.

**샘플 출력 – HcsPing 사용 및 HcsPing 사용 안함**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Trace-HcsRoute cmdle을 사용하여 문제해결
경로 추적 도구로 이 cmdlet을 사용합니다. 일정 기간 동안 최종 대상으로 각 라우터에 패킷이 전송을 전송하고 각 홉에서 반환되는 패킷에 기준으로 결과를 계산합니다. cmdlet이 특정 라우터 또는 링크의 패킷 손실의 정도를 표시하기 때문에 어떤 라우터 또는 링크가 네트워크 문제를 초래할 수 있는지 파악할 수 있습니다.

**Trace-HcsRoute를 사용하여 패킷의 경로 추적 방법을 보여주는 샘플 출력**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Get HcsRoutingTable cmdlet을 사용하여 문제해결
StorSimple 디바이스에 대한 라우팅 테이블을 보려면 이 cmdlet을 사용합니다. 라우팅 테이블은 IP (인터넷 프로토콜) 네트워크를 통해 이동하는 데이터 패킷이 어디로 이동하는지 결정하는 데 도움이 되는 규칙 집합입니다.

라우팅 테이블은 지정 네트워크에 데이터를 라우팅하는 인터페이스와 게이트웨이를 보여줍니다. 또한 특정 대상에 연결된 경로에 대한 의사 결정자인 라우팅 메트릭을 제공합니다. 라우팅 메트릭이 낮을수록 기본 설정이 높습니다.

예를 들면 네트워크 인터페이스가 2개(DATA 2, DATA 3) 있는 경우 데이터 2와 데이터 3을 인터넷에 연결합니다. 데이터 2 및 데이터 3 에 대한 라우팅 메트릭이 각각 15, 261인 경우에는 라우팅 메트릭이 가장 낮은 데이터 2는 인터넷에 연결하는데 사용되는 기본 인터페이스입니다.

StorSimple 디바이스에 업데이트 1을 실행하는 경우 데이터 0 네트워크 인터페이스에는 클라우드 트래픽에 대한 가장 높은 기본 설정을 합니다. 이는 다른 클라우드 가능 인터페이스가 있는 경우에도 클라우드 트래픽이 DATA 0 통해 라우팅되는 것을 의미합니다.

(다음 예와 같이) 매개 변수를 지정하지 않고 `Get-HcsRoutingTable` cmdlet을 실행하면 cmdlet에서 IPv4 및 IPv6 라우팅 테이블을 모두 출력합니다. 아니면 `Get-HcsRoutingTable -IPv4` 또는 `Get-HcsRoutingTable -IPv6`를 지정하여 관련 라우팅 테이블을 가져올 수 있습니다.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>단계별 StorSimple 문제 해결 예제
다음 예제에서는 StorSimple 배포의 단계별 문제 해결을 보여줍니다. 예제 시나리오에서 네트워크 설정 또는 DNS 이름이 올바르지 않음을 나타내는 오류 메시지와 함께 디바이스 등록이 실패합니다.

반환된 오류 메시지는 다음과 같습니다.

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

오류는 다음 중 하나가 원인일 수 있습니다.

* 잘못된 하드웨어 설치
* 잘못된 네트워크 인터페이스
* 잘못된 IP 주소, 서브넷 마스크, 게이트웨이, 기본 DNS 서버 또는 웹 프록시
* 잘못된 등록 키
* 잘못된 방화벽 설정

### <a name="to-locate-and-fix-the-device-registration-problem"></a>디바이스 등록 문제를 찾아 해결하려면
1. 디바이스 구성을 확인합니다. 활성 컨트롤러에서 `Invoke-HcsSetupWizard`를 실행합니다.
   
   > [!NOTE]
   > 설치 마법사는 활성 컨트롤러에서 실행해야 합니다. 활성 컨트롤러에 연결되어 있는지를 확인하려면 직렬 콘솔에 표시된 배너를 찾습니다. 배너는 컨트롤러 0 또는 컨트롤러 1에 연결되어있는지 여부 및 컨트롤러 활성 또는 수동인지를 나타냅니다. 자세한 내용은 [디바이스에서 활성 컨트롤러 식별](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)로 이동합니다.
   
2. 디바이스가 올바르게 연결되었는지 확인합니다. 디바이스 백플레인에서 네트워크 케이블을 확인합니다. 케이블은 디바이스 모델에 해당됩니다. 자세한 내용을 보려면 [StorSimple 8100 디바이스 설치](storsimple-8100-hardware-installation.md) 또는 [8600 StorSimple 디바이스 설치](storsimple-8600-hardware-installation.md)로 이동합니다.
   
   > [!NOTE]
   > 10 GbE 네트워크 포트를 사용하는 경우 제공된 QSFP-SFP 어댑터 및 SFP 케이블을 사용해야 합니다. 자세한 내용은 [10 GbE 포트에 대한 OEM 공급 업체에서 권장한 케이블, 스위치 및 트랜시버 목록](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)을 참조하세요.
  
3. 네트워크 인터페이스의 상태를 확인합니다.
   
   * Get-NetAdapter cmdlet를 사용하여 데이터 0에 대한 네트워크 인터페이스의 상태를 감지합니다. 
   * 링크 작동하지 않은 경우 **ifindex** 상태는 인터페이스가 작동 중단되었음을 나타냅니다. 그런 다음 기기 및 스위치에 대한 네트워크 연결을 확인해야 합니다. 잘못된 케이블을 제거해야 합니다. 
   * 활성 컨트롤러의 데이터 0 포트가 의심되는 경우, 컨트롤러 1의 데이터 0 포트에 연결하여 확인할 수 있습니다. 이를 확인하려면, 컨트롤러 0의 디바이스 뒷면에서 네트워크 케이블을 분리하고 컨트롤러 1에 케이블을 연결한 다음 Get-NetAdapter cmdlet을 다시 실행합니다.
     컨트롤러의 데이터 0 포트가 실패하면 다음 단계는 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. 시스템에서 컨트롤러를 교체해야할 수 있습니다.
4. 스위치에 연결을 확인합니다.
   
   * 기본 인클로저에서 컨트롤러 0과 컨트롤러 1의 데이터 0 네트워크 인터페이스가 동일한 서브넷에 있는지 확인합니다. 
   * 허브 또는 라우터를 확인합니다. 일반적으로 동일한 허브 또는 라우터에 두 컨트롤러를 연결해야 합니다. 
   * 연결에 사용할 스위치에는 동일한 vLAN의 두 컨트롤러에 대해 데이터 0이 있는지 확인합니다.
5. 모든 사용자 오류를 제거합니다.
   
   * 설치 마법사를 다시 실행하고( **Invoke-HcsSetupWizard**실행), 값을 다시 입력하여 오류가 없는지 확인합니다. 
   * 사용된 등록 확인 키를 확인합니다. 동일한 등록 키를 여러 디바이스에서 StorSimple 디바이스 관리자 서비스에 연결하는데 사용할 수 있습니다. [서비스 등록 키 받기](storsimple-8000-manage-service.md#get-the-service-registration-key) 의 절차를 사용하여 올바른 등록 키를 사용하고 있는지 확인합니다.
     
     > [!IMPORTANT]
     > 여러 서비스를 실행하는 경우 적절한 서비스를 위한 등록 키가 디바이스를 등록하는데 사용되는지 확인해야 합니다. 잘못된 StorSimple 디바이스 관리자 서비스로 디바이스를 등록한 경우 다음 단계는 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요. 디바이스의 공장 기본 설정(데이터가 손실될 수 있음)을 수행하여 원하는 서비스에 연결해야 합니다.
     > 
     > 
6. Test-Connection cmdlet을 사용하여 외부 네트워크에 연결했는지 확인합니다. 자세한 내용은 [Test-Connection cmdlet 문제 해결](#troubleshoot-with-the-test-connection-cmdlet)로 이동합니다.
7. 방화벽 간섭을 확인합니다. 가상 IP(VIP), 서브넷, 게이트웨이 및 DNS 설정이 모두 올바른지 확인하고 연결 문제가 계속 나타나면, 방화벽이 디바이스와 외부 네트워크 간의 통신을 차단하고 있을 수 있습니다. 80 및 443 포트가 아웃바운드 통신을 위한 StorSimple 디바이스에서 사용할 수 있는지 확인해야 합니다. 자세한 내용은 [StorSimple 디바이스에 대한 네트워킹 요구 사항](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)을 참조하세요.
8. 로그를 살펴봅니다. [문제 해결에 사용 가능한 지원 패키지 및 디바이스 로그](#support-packages-and-device-logs-available-for-troubleshooting)로 이동합니다.
9. 앞의 단계로 문제가 해결되지 않으면 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요.

## <a name="next-steps"></a>다음 단계
[진단 도구를 사용하여 StorSimple 디바이스 문제를 해결하는 방법을 알아봅니다](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
