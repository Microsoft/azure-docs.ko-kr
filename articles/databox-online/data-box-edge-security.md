---
title: Azure 가장자리가 상자의 데이터 보안 | Microsoft Docs
description: Azure 데이터 가장자리가 상자의 장치, 서비스 및 데이터 온-프레미스를 보호 하는 보안 및 개인 정보 보호 기능에 설명 하 고 클라우드에서 합니다.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756236"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure 데이터 상자에 지 보안 및 데이터 보호

보안은 주요 관심사는 새로운 기술을 채택 하는 경우 특히 기술이 기밀 또는 소유 데이터를 사용 하 여 사용 되는 경우입니다. Azure 데이터 가장자리가 상자의 도와 줍니다 엔터티 권한 있는 볼 수 있습니다, 그리고 수정 또는 데이터를 삭제 합니다.

이 문서는 각 솔루션 구성 요소 및 그 안에 저장 된 데이터를 보호 하는 데 도움이 되는 데이터 가장자리가 상자의 보안 기능을 설명 합니다.

Azure 데이터 가장자리가 상자의 서로 상호 작용 하는 네 가지 주요 구성 요소가 구성 됩니다.

- **Azure에서 호스트 되는 데이터 가장자리가 상자의 서비스**합니다. 장치 순서를 만드는 데 사용할 수 있는 관리 리소스는 장치를 구성 하 고 완료 될 때까지 순서를 추적 합니다.
- **데이터 상자에 지 장치**합니다. Azure로 온-프레미스 데이터를 가져올 수 있도록 할 제공 되는 전송 장치입니다.
- **장치에 연결 된 클라이언트/호스트**합니다. 데이터 상자 Edge 장치에 연결 하 고 보호 해야 하는 데이터를 포함 하는 인프라 내의 클라이언트입니다.
- **클라우드 저장소**합니다. Azure 클라우드 플랫폼에서 데이터가 저장 되는 위치입니다. 이 위치는 일반적으로 사용자가 만든 데이터 가장자리가 상자의 리소스에 연결 된 저장소 계정.

## <a name="data-box-edge-service-protection"></a>데이터 가장자리가 상자의 서비스 보호

가장자리가 상자의 데이터 서비스는 Azure에서 호스트 되는 관리 서비스입니다. 서비스 구성 및 장치 관리에 사용 됩니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>데이터 상자에 지 장치 보호

데이터 상자 가장자리 로컬로 처리 한 다음 Azure에 전송 하 여 데이터를 변환 하는 데 도움이 되는 온-프레미스 장치는 합니다. 장치:

- 가장자리가 상자의 데이터 서비스에 액세스 하는 정품 인증 키를 해야 합니다.
- 항상 장치 암호로 보호 합니다.
- 잠긴 장치가입니다. 장치 BMC 및 BIOS가 암호로 보호 합니다. BIOS는 제한 된 사용자 액세스로 보호 됩니다.
- 보안 부팅이 있었습니다.
- Windows Defender Device Guard를 실행합니다. Device Guard를 사용 하면 코드 무결성 정책에서 정의 하는 신뢰할 수 있는 응용 프로그램만 실행할 수 있습니다.

### <a name="protect-the-device-via-activation-key"></a>정품 인증 키를 통해 장치를 보호 합니다.

인증 된 데이터 상자 Edge 장치를 Azure 구독에서 만든 가장자리가 상자의 데이터 서비스에 가입할 수 있습니다. 장치를 인증 하는 정품 인증 키를 사용 하 여 가장자리가 상자의 데이터 서비스를 사용 하 여 장치를 활성화 해야 합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

자세한 내용은 [정품 인증 키 가져오기](data-box-edge-deploy-prep.md#get-the-activation-key)합니다.

### <a name="protect-the-device-via-password"></a>암호를 통해 장치를 보호 합니다.

암호는 권한이 있는 사용자만 데이터에 액세스할 수 있는지 확인 합니다. 데이터 상자에 지 장치 부팅 잠긴 상태에서입니다.

다음을 수행할 수 있습니다.

- 로컬 웹 UI는 브라우저를 통해 장치에 연결한 후 장치에 로그인 하는 데 암호를 제공 합니다.
- HTTP를 통해 장치 PowerShell 인터페이스에 원격으로 연결 합니다. 원격 관리 기본적으로 켜져 있습니다. 그런 다음 장치에 로그인 할 장치 암호를 제공할 수 있습니다. 자세한 내용은 [데이터 상자 Edge 장치에 원격으로 연결](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 로컬 웹 UI를 사용 하 여 [암호를 변경](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)합니다. 암호를 변경 하는 경우에 사용자에 게 알리는 모든 원격 액세스 문제에 로그인 하지 못하도록 해야 합니다.

## <a name="protect-your-data"></a>데이터 보호

이 섹션에서는 전송 및 저장 된 데이터를 보호 하는 데이터 가장자리가 상자의 보안 기능을 설명 합니다.

### <a name="protect-data-at-rest"></a>휴지 상태의 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>이동 중인 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>저장소 계정을 통해 데이터 보호

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 회전 차례로 [저장소 계정 키를 동기화](data-box-edge-manage-shares.md#sync-storage-keys) 정기적으로 보호 하기 위해 저장소 계정에서 권한이 없는 사용자입니다.

## <a name="manage-personal-information"></a>개인 정보 관리

가장자리가 상자의 데이터 서비스는 다음과 같은 시나리오에서 개인 정보를 수집합니다.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

단계에 따라 공유를 삭제 하거나 액세스할 수 있는 사용자의 목록을 보려면 [데이터 상자 가장자리에서 공유 관리](data-box-edge-manage-shares.md)합니다.

자세한 내용은 Microsoft 개인 정보 취급 방침을 검토 합니다 [보안 센터](https://www.microsoft.com/trustcenter)합니다.

## <a name="next-steps"></a>다음 단계

[데이터 상자 Edge 장치 배포](data-box-edge-deploy-prep.md)
