---
title: StorSimple 8000 시리즈 보안 | Microsoft Docs
description: 온 프레미스 및 클라우드에서 StorSimple 서비스, 디바이스 및 데이터를 보호하는 보안 및 개인정보 보호 기능에 대해 설명합니다.
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
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 734b0cf9373ea98ab33c06b45ad53b46a3355dd6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62117028"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple 보안 및 데이터 보호

## <a name="overview"></a>개요

보안은 특히 기술이 기밀 또는 소유 데이터와 함께 사용 되는 경우 새로운 기술을 채택하는 사람들에게는 주요 관심사입니다. 다양한 기술을 평가하는 동안 데이터 보호에 대한 비용 및 위험 증가를 고려해야 합니다. Microsoft Azure StorSimple은 보안 및 개인정보 솔루션 데이터 보호 모두를 제공하며 다음을 수행할 수 있도록 돕습니다.

* **기밀성** – 권한 있는 엔터티만 데이터를 볼 수 있습니다.
* **무결성** – 권한 있는 엔터티만 데이터를 수정하거나 삭제할 수 있습니다.

Microsoft Azure StorSimple 솔루션은 서로 상호 작용 하는 네 가지 기본 구성 요소로 이루어져 있습니다.

* **Microsoft Azure에서 호스트되는 StorSimple 디바이스 관리자 서비스** – StorSimple 디바이스를 구성하고 프로비전하는 데 사용하는 관리 서비스입니다.
* **StorSimple 디바이스** – 데이터 센터에 설치된 물리적 디바이스입니다. StorSimple 디바이스에 연결하는 데이터를 생성하는 모든 호스트와 클라이언트이며, 해당 디바이스는 데이터를 관리하여 적절한 경우 Azure 클라우드로 이동합니다.
* **디바이스에 연결된 클라이언트/호스트** – StorSimple 디바이스에 연결되고 보호되어야 하는 데이터를 생성하는 인프라 내의 클라이언트입니다.
* **클라우드 저장소** – 데이터가 저장되는 Azure 클라우드의 위치입니다.

다음 섹션에서는 각 이러한 구성 요소와 구성요소에 저장된 데이터를 보호할 수 있는 StorSimple 보안 기능에 대해 설명합니다. 또한 Microsoft Azure StorSimple 보안 및 해당 답변에 대해 있을 수 있는 질문의 목록이 포함됩니다.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple 디바이스 관리자 서비스 보호

StorSimple 디바이스 관리자 서비스는 Microsoft Azure에서 호스트되고 조직에서 조달하는 모든 StorSimple 디바이스를 관리하는 데 사용되는 관리 서비스입니다. 웹 브라우저에서 조직 자격 증명을 사용하여 Azure Portal에 로그온하면 StorSimple 디바이스 관리자 서비스에 액세스할 수 있습니다.

StorSimple 디바이스 관리자 서비스에 액세스하려면 조직에 StorSimple을 포함하는 Azure 구독이 있어야 합니다. 구독은 Azure Portal에서 액세스할 수 있는 기능을 제어합니다. 조직에 Azure 구독이 없고 이에 대한 자세한 정보를 알려면 [조직으로 Azure에 등록](../active-directory/fundamentals/sign-up-organization.md)을 참조하세요.

StorSimple 디바이스 관리자 서비스는 Azure에서 호스트되므로 Azure 보안 기능으로 보호됩니다. Microsoft Azure에서 제공하는 보안 기능에 대한 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/security/)로 이동합니다.

## <a name="storsimple-device-protection"></a>StorSimple 디바이스 보호

StorSimple 디바이스는 SSD(반도체 드라이브) 및 HDD(하드 디스크 드라이브)를 포함하는 온-프레미스 하이브리드 저장소 디바이스로, 중복 컨트롤러 및 자동 장애 조치 기능을 함께 제공합니다. 컨트롤러는 현재 사용되는(또는 핫) 데이터를 로컬 저장소(StoreSimple 디바이스 또는 온-프레미스 서버)에 배치하고 덜 자주 사용되는 데이터는 클라우드로 이동하여 저장소 계층화를 관리합니다.

인증된 StorSimple 디바이스만 Azure 구독에서 만든 StorSimple 디바이스 관리자 서비스에 가입할 수 있습니다. 디바이스를 인증하려면 서비스 등록 키를 입력하여 StorSimple 디바이스 관리자 서비스에 등록해야 합니다. 서비스 등록 키는 Azure Portal에서 생성된 임의의 128비트 키입니다.

![서비스 등록 키](./media/storsimple-security/ServiceRegistrationKey.png)

서비스 등록 키를 얻는 방법을 알아보려면 [2 단계: 서비스 등록 키 가져오기](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)로 이동합니다.

서비스 등록 키는 100자 이상의 문자를 포함 하는 긴 키입니다. 키를 복사해서 안전한 위치에 텍스트 파일로 저장해두면 필요에 따라 추가 디바이스를 인증하는 데 사용할 수 있습니다. 첫 번째 디바이스 등록 후 서비스 등록 키를 손실한 경우 StorSimple 디바이스 관리자 서비스에서 새 키를 생성할 수 있습니다. 기존 디바이스의 작동에 영향을 주지 않습니다.

디바이스를 등록한 후에 토큰을 사용하여 Microsoft Azure와 통신할 수 있습니다. 디바이스를 등록한 후 서비스 등록 키는 사용되지 않습니다.

> [!NOTE]
> 사용한 후 항상 서비스 등록 키를 다시 생성하는 것이 좋습니다.


## <a name="protect-your-storsimple-solution-via-passwords"></a>암호를 통해 StorSimple 솔루션 보호

암호는 컴퓨터 보안의 중요한 사항으로, 권한이 있는 사용자만 데이터에 액세스할 수 있도록 하기 위해서 StorSimple 솔루션에서 광범위하게 사용됩니다. StorSimple을 사용하면 다음과 같은 암호를 구성할 수 있습니다.

* StorSimple 디바이스 관리자 암호
* 핸드셰이크 인증 프로토콜(CHAP) 초기자 및 대상 암호 문제
* StorSimple 스냅숏 관리자 암호

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>StorSimple용 Windows PowerShell 및 StorSimple 디바이스 관리자 암호

StorSimple용 Windows PowerShell은 StorSimple 디바이스를 관리하는 데 사용할 수 있는 명령줄 인터페이스입니다. StorSimple용 Windows PowerShell에는 디바이스를 등록하고, 디바이스에서 네트워크 인터페이스를 구성하고 특정 형식의 업데이트를 설치하고, 지원 세션에 액세스하여 디바이스 문제를 해결하고 디바이스 상태를 변경할 수 있는 기능이 있습니다. 디바이스의 직렬 콘솔에 연결하거나 Windows PowerShell 원격 기능을 사용하여 StorSimple용 Windows PowerShell에 액세스할 수 있습니다.

PowerShell 원격은 HTTPS 또는 HTTP를 통해 수행할 수 있습니다. HTTPS 통한 원격 관리를 사용하는 경우, 디바이스에서 원격 관리 인증서를 다운로드하고 원격 클라이언트에 설치해야 합니다. PowerShell 원격에 대한 자세한 내용을 보려면 [StorSimple 디바이스에 원격으로 연결](storsimple-8000-remote-connect.md)로 이동하세요.

StorSimple용 Windows PowerShell을 사용하여 디바이스에 연결 후 디바이스에 로그온하려면 디바이스 관리자 암호를 입력해야 합니다.

![디바이스 관리자 암호](./media/storsimple-security/DeviceAdminPW.png)

다음 모범 사례를 고려하세요.

* 원격 관리는 기본적으로 꺼져 있습니다. StorSimple 디바이스 관리자 서비스를 사용하여 사용하도록 설정할 수 있습니다. 실제로 필요한 기간 동안에만 원격 액세스를 사용하는 것이 좋습니다.
* 암호를 변경하는 경우 예기치 않은 연결 끊김을 겪지 않도록 모든 원격 액세스 사용자에게 알려야 합니다.
* StorSimple 디바이스 관리자 서비스는 기존 암호를 검색할 수 없습니다. 재설정만 가능합니다. 잊어버린 경우 암호를 재설정할 필요가 없도록 모든 암호를 안전한 장소에 저장하는 것이 좋습니다. 암호를 재설정해야 하는 경우 재설정 하기 전에 모든 사용자에게 알려야 합니다.

디바이스에 대한 직렬 연결을 사용하여 Windows PowerShell 인터페이스에 액세스할 수 있습니다. 또한 보안이 강화된 HTTP 또는 HTTPS를 사용하여 원격으로 액세스할 수도 있습니다. HTTPS는 직렬 또는 HTTP 연결보다 더 높은 수준의 보안을 제공합니다. 그러나 HTTPS를 사용하려면 먼저 디바이스에 액세스하는 클라이언트 컴퓨터에 인증서를 설치해야 합니다. StorSimple 디바이스 관리자 서비스의 디바이스 구성 페이지에서 원격 액세스 인증서를 다운로드할 수 있습니다. 원격 액세스에 대한 인증서를 분실한 경우 새 인증서를 다운로드하고 원격 관리를 사용할 수 있는 권한이 있는 모든 클라이언트에 전파해야 합니다.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>핸드셰이크 인증 프로토콜(CHAP) 초기자 및 대상 암호 문제

CHAP는 StorSimple 디바이스에서 원격 클라이언트의 ID를 확인하는 데 사용하는 인증 체계입니다. 확인은 공유 암호를 기반으로 합니다. CHAP는 일방(단방향)이거나 상호적(양방향)일 수 있습니다. 단방향 CHAP의 대상(StorSimple 디바이스)은 초기자(호스트)를 인증합니다. 상호 또는 역방향 CHAP는 대상이 초기자를 인증한 다음 초기자에서 대상을 인증해야 합니다. Azure StorSimple은 두 방법 중 하나를 사용하여 구성할 수 있습니다.

CHAP를 구성하는 경우 다음 사항에 유의하세요.

* CHAP 사용자 이름은 233 미만의 문자를 포함해야 합니다.
* CHAP 암호는 12 ~ 16 자 사이여야 합니다. 더 긴 사용자 이름이나 암호를 사용하려고 하면 Windows 호스트에서 인증 오류가 발생합니다.
* CHAP 초기자와 CHAP 대상 모두에 대해 동일한 암호를 사용할 수 없습니다.
* 암호를 설정한 후 변경할 수 있지만 검색할 수 없습니다. 암호가 변경되는 경우 StorSimple 디바이스에 연결할 수 있도록 모든 원격 액세스 사용자에게 알려야 합니다.

CHAP에 대한 자세한 내용 및 StorSimple을 구성하는 방법을 보려면 [StorSimple 디바이스에 대한 CHAP 구성](storsimple-8000-configure-chap.md)으로 이동하세요.

### <a name="storsimple-snapshot-manager-password"></a>StorSimple 스냅숏 관리자 암호

StorSimple 스냅숏 관리자는 볼륨 그룹 및 Windows 볼륨 섀도 복사본 서비스를 사용하여 애플리케이션에 일관된 백업을 생성하는 Microsoft Management Console (MMC) 스냅인입니다. 또한 백업 일정 및 복제를 만들거나 볼륨을 복원하려면 StorSimple 스냅숏 관리자를 사용할 수 있습니다.

StorSimple 스냅숏 관리자를 사용하여 디바이스를 구성할 때 StorSimple Snapshot Manager 암호를 제공해야 합니다. 이 암호는 등록 중 StorSimple에 대한 Windows PowerShell에서 먼저 설정됩니다. StorSimple 디바이스 관리자 서비스에서 암호를 설정하고 변경할 수도 있습니다. 이 암호는 StorSimple Snapshot Manager 디바이스를 인증합니다.

![StorSimple 스냅숏 관리자 암호](./media/storsimple-security/SnapshotMgrPassword.png)

StorSimple 스냅숏 관리자 암호는 14 ~ 15자여야 하며 대문자, 소문자, 숫자 및 특수 문자의 조합을 3개 이상 포함해야 합니다. StorSimple 스냅숏 관리자 암호를 설정한 후 변경될 수 있지만 검색할 수 없습니다. 암호를 변경하는 경우에 모든 원격 사용자에게 알려야 합니다.

StorSimple 스냅숏 관리자에 대한 자세한 내용을 보려면 [StorSimple 스냅숏 관리자란?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>암호 모범 사례

StorSimple 암호를 강력하고 잘 보호되도록 만들기 위해서 다음 지침을 사용하는 것이 좋습니다.

* 3개월 마다 암호를 변경합니다. 매년 암호 변경이 강제로 적용됩니다.
* 강력한 암호를 사용합니다. 자세한 내용을 보려면 [보다 강력한 암호 만들기 및 보호](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/)로 이동하세요.
* 서로 다른 액세스 메커니즘에 대해 항상 다른 암호를 사용합니다. 지정한 각 암호는 고유해야 합니다.
* StorSimple 디바이스에 액세스할 권한이 없는 사용자와 암호를 공유하지 않습니다.
* 다른 사람 앞에서 암호에 대해 얘기하거나 암호 형식에 대한 힌트를 제공하지 마세요.
* 계정이나 암호가 손상된 것 같은 경우 정보 보안 부서에 보고합니다.
* 모든 암호는 중요한 기밀 정보로 취급됩니다. 

## <a name="storsimple-data-protection"></a>StorSimple 데이터 보호

이 섹션은 전송 중인 데이터와 저장된 데이터를 보호하는 StorSimple 보안 기능을 설명합니다.

다른 섹션에서 설명한 것처럼 암호는 StorSimple 솔루션에 대한 액세스 권한을 얻기 전에 사용자를 인증하고 권한을 부여하는데 사용됩니다. 다른 보안 고려 사항은 저장하는 동안 및 저장소 시스템 간에 전송되는 동안 권한이 없는 사용자로부터 데이터를 보호하기 위한 것입니다. 다음 섹션은 StorSimple에 제공되는 데이터 보호 기능을 설명합니다.

> [!NOTE]
> 중복 제거는 StorSimple 디바이스에 및 Microsoft Azure 저장소에 저장된 데이터에 대한 추가 보호를 제공합니다. 데이터가 중복 제거된 경우, 데이터 개체는 이들 항목에 매핑 및 액세스하는데 사용되는 메타데이터와 별도로 저장됩니다. 볼륨 구조, 파일 시스템 또는 파일 이름에 따라 데이터를 다시 구성하는 사용 가능한 저장소 수준의 컨텍스트가 없습니다.


## <a name="protect-data-flowing-through-the-service"></a>서비스를 통해 흐르는 데이터 보호

StorSimple 디바이스 관리자 서비스의 주요 목적은 StorSimple 디바이스를 관리하고 구성하는 것입니다. StorSimple 디바이스 관리자 서비스는 Microsoft Azure에서 실행됩니다. Azure Portal을 사용하여 디바이스 구성 데이터를 입력한 후, Microsoft Azure가 StorSimple 디바이스 관리자 서비스를 사용하여 디바이스에 데이터를 보냅니다. StorSimple은 비대칭 키 쌍 시스템을 사용하여 Azure 서비스의 손상으로 인해 저장된 정보가 손상되지 않도록 지원합니다.

![기내 데이터 암호화](./media/storsimple-security/DataEncryption.png)

비대칭 키 시스템은 다음과 같이 서비스를 통해 흐르는 데이터를 보호할 수 있습니다.

1. 비대칭 공개 및 개인 키 쌍 디바이스를 사용하는 데이터 암호화 인증서가 디바이스에 생성되고 데이터를 보호하는 데 사용됩니다. 키는 첫 번째 디바이스를 등록하는 경우에 생성됩니다.
2. 데이터 암호화 인증서 키는 등록 중 첫 번째 디바이스에서 임의로 생성된 강력한 128비트 키인 서비스 데이터 암호화 키로 보호되는 개인정보 교환(.pfx) 파일로 내보내집니다.
3. 인증서의 공개 키는 StorSimple 디바이스 관리자 서비스에 대해 안전하게 사용할 수 있으며 개인 키는 디바이스와 같이 그대로 유지됩니다.
4. 서비스를 입력하는 데이터는 공용 키를 사용하여 암호화되며 디바이스에 저장된 개인 키를 사용하여 암호가 해독되어, Azure 서비스는 디바이스에 이동하는 데이터를 암호 해독할 수 없습니다.

서비스 데이터 암호화 키는 서비스에 등록된 첫 번째 디바이스에만 생성됩니다. 서비스에 등록되는 모든 후속 디바이스는 동일한 서비스 데이터 암호화 키를 사용해야 합니다.

> [!IMPORTANT]
> 서비스 데이터 암호화 키의 복사본을 만들어 안전한 위치에 저장하는 것이 매우 중요합니다. 서비스 데이터 암호화 키의 복사본은 권한 있는 사용자가 액세스하고 디바이스 관리자에게 쉽게 전달될 수 있는 방식으로 저장되어야 합니다.
> 
> 서비스 데이터 암호화 키를 분실한 경우 Microsoft 지원 담당자가 온라인 상태에서 장비가 있는 경우 검색을 도와줄 수 있습니다. 검색 후 서비스 데이터 암호화 키를 변경하는 것이 좋습니다.

서비스 데이터 암호화 키 및 해당 데이터 암호화 인증서를 변경하려면 [Microsoft Azure StorSimple 디바이스 관리자 서비스에 대한 서비스 데이터 암호화 키 변경](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)의 단계를 수행합니다. 암호화 키를 변경하면 새 키로 모든 디바이스를 업데이트해야 합니다. 따라서 모든 디바이스가 온라인 상태이면 키를 변경하는 것이 좋습니다. 디바이스가 오프라인 상태인 경우 다른 시간에 해당 키를 변경할 수 있습니다. 오래된 키와 디바이스로 여전히 백업을 실행할 수 있지만 키가 업데이트 될 때까지 데이터를 복원할 수 없습니다.

서비스 데이터 암호화 키 및 데이터 암호화 인증서가 만료되지 않았습니다. 그러나 키 손상을 방지하려면 매년 서비스 데이터 암호화 키를 변경하는 것이 좋습니다.

## <a name="protect-data-at-rest"></a>휴지 상태의 데이터 보호

StorSimple 디바이스는 사용 빈도에 따라 데이터를 로컬과 클라우드 계층에 저장하여 관리합니다. 디바이스에 연결된 모든 호스트 컴퓨터는 데이터를 디바이스로 보낸 다음, 적절한 경우 클라우드로 이동합니다. 데이터는 인터넷을 통해 디바이스에서 클라우드로 안전하게 전송됩니다. 각 디바이스에는 해당 디바이스의 모든 공유 볼륨을 표시하는 하나의 iSCSI 대상이 있습니다. 클라우드 저장소에 전송되기 전에 모든 데이터는 암호화 됩니다. 

![클라우드 저장소 암호화 키](./media/storsimple-security/CloudStorageEncryption.png)

클라우드로 이동하는 데이터의 보안 및 무결성 보장을 지원하기 위해서 StorSimple은 다음과 같이 클라우드 저장소 암호화 키를 정의하도록 허용합니다.

* 볼륨 컨테이너를 만들 때 클라우드 저장소 암호화 키를 지정합니다. 키를 수정하거나 나중에 추가할 수 없습니다.
* 볼륨 컨테이너의 모든 볼륨은 동일한 암호화 키를 공유합니다. 특정 볼륨에 대해 다른 형식의 암호화를 원하는 경우, 해당 볼륨을 호스트하는 새 볼륨 컨테이너를 만드는 것이 좋습니다.
* StorSimple 디바이스 관리자 서비스에서 클라우드 저장소 암호화 키를 입력하면 서비스 데이터 암호화 키의 공개 부분을 사용하여 키가 암호화된 후 디바이스로 전송됩니다.
* 클라우드 스토리지 암호화 키는 서비스에 저장되지 않으며 디바이스에만 알려집니다.
* 클라우드 저장소 암호화 키 지정은 선택 사항입니다. 디바이스에 대한 호스트에서 암호화된 데이터를 보낼 수 있습니다.

### <a name="additional-security-best-practices"></a>추가 보안 모범 사례

* 트래픽 분할: 완전히 분리된 네트워크를 배포하고 물리적 격리가 옵션이 아닌 VLAN을 사용하여 회사 LAN의 사용자 트래픽에서 iSCSI SAN을 격리합니다. iSCSI 저장소에 대한 전용 네트워크는 주요 비즈니스 데이터의 안전 및 성능을 보장합니다. 회사 LAN을 통한 저장소 및 사용자 트래픽 혼합은 권장하지 않으며 대기 시간이 증가하고 네트워크 오류를 일으킬 수 있습니다.
* 호스트 측 네트워크 보안은 TCP/IP 오프로드 엔진(TOE)을 지원하는 네트워크 인터페이스를 사용합니다. TOE는 네트워크 어댑터에서 TCP를 처리하여 CPU 부하를 줄입니다.

## <a name="protect-data-via-storage-accounts"></a>저장소 계정을 통해 데이터 보호

각 Microsoft Azure 구독에 하나 이상의 저장소 계정을 만들 수 있습니다. (저장소 계정은 Azure 클라우드에 저장 된 데이터로 작업하기 위한 고유한 네임스페이스를 제공합니다 .) 저장소 계정에 대한 액세스는 해당 저장소 계정과 연결된 구독 및 선택키를 통해 제어됩니다.

저장소 계정을 만들 때 Microsoft Azure는 두 개의 512비트 저장소 액세스 키를 생성하며, 이 중 하나는 StorSimple 디바이스가 저장소 계정에 액세스할 때 인증에 사용됩니다. 이 키 중 하나만 사용 중입니다. 주기적으로 키를 회전하도록 다른 키를 예약 상태로 둡니다. 키를 회전하려면 보조키를 활성화한 다음, 기본 키를 삭제합니다. 그런 후 다음 회전 시 사용할 새 키를 만들 수 있습니다. (보안상의 이유로 많은 데이터 센터 키 회전이 필요합니다.)

키 회전에 대해 이 모범 사례를 따르는 것이 좋습니다.

* 권한이 없는 사용자가 저장소 계정에 액세스하지 않도록 정기적으로 저장소 계정 키를 회전해야 합니다.
* Azure 관리자는 정기적으로 Azure Portal의 [스토리지] 섹션을 사용해 직접 Storage 계정에 액세스하여 기본 또는 보조 키를 변경하거나 다시 생성해야 합니다.

## <a name="protect-data-via-encryption"></a>암호화를 통해 데이터 보호

StorSimple는 StorSimple 솔루션의 구성 요소 간 이동되는 또는 저장된 데이터를 보호하기 위해 다음 암호화 알고리즘을 사용합니다.

| 알고리즘 | 키 길이 | 프로토콜/응용 프로그램/주석 |
| --- | --- | --- |
| RSA |2048 |Azure Portal은 RSA PKCS 1 v1.5를 사용하여 디바이스에 전송되는 구성 데이터(예: 저장소 계정 자격 증명, StorSimple 디바이스 구성, 클라우드 저장소 암호화 키)를 암호화합니다. |
| AES |256 |서비스 데이터 암호화 키의 공개 부분은 StorSimple 디바이스에서 Azure Portal로 전송되기 전에 CBC가 포함된 AES를 사용하여 암호화됩니다. 또한 클라우드 스토리지 계정에 데이터를 보내기 전에 데이터를 암호화하도록 StorSimple 디바이스에서 사용됩니다. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance 보안

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>개인 정보 관리

실제 및 가상 계열 둘 다에 대한 StorSimple 디바이스 관리자는 다음과 같은 키 인스턴스에서 개인 정보를 수집합니다.

- 사용자의 이메일 주소가 구성된 사용자 설정을 변경합니다. 이 정보는 관리자가 볼 수 있고 지울 수 있습니다. StorSimple 8000 시리즈 디바이스 및 StorSimple 가상 배열 둘 다에 적용됩니다.
  * StorSimple 8000 시리즈에 대한 설정을 보고 지우려면 [StorSimple 경고 보기 및 관리](storsimple-8000-manage-alerts.md#configure-alert-settings) 단계 수행
  * StorSimple 가상 배열에 대한 설정을 보고 지우려면 [StorSimple 경고 보기 및 관리](storsimple-virtual-array-manage-alerts.md#configure-alert-settings) 단계 수행
- 공유 폴더에 있는 데이터에 액세스할 수 있는 사용자입니다. 공유 데이터에 액세스할 수 있는 사용자의 목록을 표시하고 볼 수 있습니다. 공유가 삭제되는 경우 이 목록도 삭제됩니다. 이는 StorSimple 가상 배열에만 적용됩니다.
  * 액세스할 수 있는 사용자 목록을 보거나 공유를 삭제하려면 [StorSimple 가상 배열에서 공유 관리](storsimple-virtual-array-manage-shares.md) 단계 수행

자세한 내용은 [보안 센터](https://www.microsoft.com/trustcenter)에서 Microsoft 개인 정보 취급 방침을 검토합니다.

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

보안 및 Microsoft Azure StorSimple에 대한 질문과 답변은 다음과 같습니다.

**Q:** 서비스가 손상되었습니다. 다음 단계에서 어떻게 해야 합니까?

**A:** 데이터를 계층화하는데 사용되는 스토리지 계정에 대한 서비스 데이터 암호화 키 및 스토리지 계정 키를 즉시 변경해야 합니다. 자세한 내용은 다음을 참조하세요.

* [서비스 데이터 암호화 키 변경](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [저장소 계정의 키 회전](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** 서비스 등록 키를 요청하는 새 StorSimple 디바이스가 있습니다. 어떻게 장치를 검색하나요?

**A:** StorSimple 디바이스 관리자 서비스를 처음 만들 때 이 키가 생성됩니다. StorSimple 디바이스 관리자 서비스를 사용하여 디바이스에 연결하는 경우 서비스 빠른 시작 페이지를 사용하여 서비스 등록 키를 보거나 다시 생성할 수 있습니다. 새 서비스 등록 키를 생성해도 기존에 등록된 디바이스에는 영향을 주지 않습니다. 자세한 내용은 다음을 참조하세요.

* [서비스 등록 키 보기 또는 다시 생성](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**Q:** 서비스 데이터 암호화 키를 잃어버렸습니다. 어떻게 해야 합니까?

**A:** Microsoft 지원에 문의하세요. Microsoft 지원 담당자가 디바이스의 지원 세션에 로그온하여 키를 검색할 수 있습니다(한 대 이상의 디바이스가 온라인 상태인 경우). 서비스 데이터 암호화 키를 가져온 직후에 새 키를 사용자만 알 수 있도록 키를 변경해야 합니다. 자세한 내용은 다음을 참조하세요.

* [서비스 데이터 암호화 키 변경](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Q:**  서비스 데이터 암호화 키 변경을 위해 디바이스를 인증했지만 키 변경 프로세스를 시작하지 못했습니다. 어떻게 해야 하나요?

**A:** 제한 시간이 만료된 경우 서비스 데이터 암호화 키 변경에 대한 디바이스를 다시 인증하고 프로세스를 다시 시작해야 합니다.

**Q:**  서비스 데이터 암호화 키를 변경했지만 4시간 내에 다른 디바이스를 업데이트할 수 없습니다. 다시 시작해야 하나요?

**A:** 4시간이라는 기간은 변경을 초기화하기 위한 시간입니다. 인증된 StorSimple 디바이스에서 업데이트 프로세스를 시작한 후 모든 디바이스가 업데이트 될 때까지 인증은 유효합니다.

**Q:** StorSimple 관리자가 회사를 그만두었습니다. 어떻게 해야 하나요?

**A:** StorSimple 디바이스에 대한 액세스를 허용하는 암호를 변경하고 재설정하고, 서비스 데이터 암호화 키를 변경하여 새 정보가 인증되지 않은 사람에게 알려지지 않도록 합니다. 자세한 내용은 다음을 참조하세요.

* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 암호 변경](storsimple-8000-change-passwords.md)
* [서비스 데이터 암호화 키 변경](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [StorSimple 디바이스에 대한 CHAP 구성](storsimple-8000-configure-chap.md)

**Q:** StorSimple 디바이스에 연결하는 호스트에 StorSimple Snapshot Manager 암호를 입력하고 싶지만 암호가 없습니다. 어떻게 해야 하나요?

**A:** 암호를 잊어버린 경우 새 암호를 만들어야 합니다. 그런 다음 기존의 모든 사용자에게 암호가 변경되었으며 클라이언트를 업데이트해야 새 암호를 사용할 수 있음을 알려야 합니다. 자세한 내용은 다음을 참조하세요.

* [StorSimple 스냅숏 관리자 암호 변경](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [디바이스 인증](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** StorSimple용 Windows PowerShell에 대한 원격 액세스에 대한 인증서가 디바이스에서 변경되었습니다. 내 원격 액세스 클라이언트를 업데이트하려면 어떻게 해야 하나요?

**A:** StorSimple 디바이스 관리자 서비스에서 새 인증서를 다운로드하여 원격 액세스 클라이언트의 인증서 저장소에 설치될 수 있도록 제공합니다. 자세한 내용은 다음을 참조하세요.

* [가져오기-Certificate cmdlet](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**Q:** StorSimple 디바이스 관리자 서비스가 손상되어도 데이터가 보호되나요?

**A:** 서비스 구성 데이터는 웹 브라우저에서 볼 때 항상 공용 키로 암호화됩니다. 서비스가 개인 키에 액세스 할 수 없으므로 서비스는 어떠한 데이터도 볼 수 없습니다. StorSimple 디바이스 관리자 서비스가 손상되어도 StorSimple 디바이스 관리자 서비스에 저장된 키가 없기 때문에 영향이 없습니다.

**Q:** 다른 사람이 데이터 암호화 인증서에 대한 액세스 권한을 얻은 경우, 내 데이터가 손상되나요?

**A:** Microsoft Azure는 고객의 데이터 암호화 키(.pfx 파일)를 암호화된 형식으로 저장합니다. .pfx 파일은 암호화되어 있으므로 StorSimple 서비스는.pfx 파일을 해독하는 서비스 데이터 암호화 키가 없으므로 .pfx 파일에 대한 액세스 권한을 갖는 것만으로는 비밀이 노출되지 않습니다.

**Q:** 정부 기관이 Microsoft에 내 데이터를 요청하는 경우 어떻게 되나요?

**A:** 서비스에서 모든 데이터가 암호화되고 개인 키가 디바이스에 보관되므로 정부 기관은 고객에게 데이터를 요청해야 합니다.



## <a name="next-steps"></a>다음 단계

[StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)

