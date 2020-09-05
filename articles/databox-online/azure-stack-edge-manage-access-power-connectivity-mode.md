---
title: Azure Stack Edge 장치 액세스, 전원 및 연결 모드 | Microsoft Docs
description: Azure로 데이터를 전송 하는 데 도움이 되는 Azure Stack Edge 장치에 대 한 액세스, 기능 및 연결 모드를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 75e7a43d545bea63d553ad01836ed5c6ebc2dcc9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079798"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge"></a>Azure Stack Edge에 대 한 액세스, 기능 및 연결 모드 관리

이 문서에서는 Azure Stack Edge에 대 한 액세스, 기능 및 연결 모드를 관리 하는 방법을 설명 합니다. 이러한 작업은 로컬 웹 UI 또는 Azure Portal을 통해 수행합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 디바이스 액세스 관리
> * 연결 모드 관리
> * 전원 관리


## <a name="manage-device-access"></a>디바이스 액세스 관리

Azure Stack Edge 장치에 대 한 액세스는 장치 암호를 사용 하 여 제어 됩니다. 로컬 웹 UI를 통해 암호를 변경할 수 있습니다. Azure Portal에서 장치 암호를 다시 설정할 수도 있습니다.

### <a name="change-device-password"></a>디바이스 암호 변경

로컬 UI에서 다음 단계를 수행 하 여 장치 암호를 변경 합니다.

1. 로컬 웹 UI에서 **유지 관리 > 암호 변경**으로 이동합니다.
2. 현재 암호를 입력한 다음, 새 암호를 입력합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. 새 암호를 확인합니다.

    ![암호 변경](media/azure-stack-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. **암호 변경**을 선택합니다.
 
### <a name="reset-device-password"></a>장치 암호 다시 설정

재설정 워크플로에서는 사용자가 이전 암호를 기억할 필요가 없으며 암호를 분실했을 때 유용합니다. 이 워크플로는 Azure Portal에서 수행합니다.

1. Azure Portal에서 **개요 > 관리자 암호 재설정**으로 이동합니다.

    ![암호 재설정](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 새 암호를 입력하고 확인합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. **재설정**을 선택합니다.

    ![암호 재설정](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>리소스 액세스 관리

Azure Stack Edge/Data Box Gateway, IoT Hub 및 Azure Storage 리소스를 만들려면 리소스 그룹 수준에서 참가자 이상의 권한이 필요 합니다. 또한 해당 하는 리소스 공급자를 등록 해야 합니다. 정품 인증 키 및 자격 증명을 포함 하는 모든 작업의 경우에는 Microsoft Graph API에 대 한 사용 권한도 필요 합니다. 이러한 내용은 다음 섹션에 설명 되어 있습니다. 

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API 사용 권한 관리

Azure Stack Edge 장치에 대 한 정품 인증 키를 생성 하거나 자격 증명이 필요한 작업을 수행 하는 경우 Graph API Azure Active Directory 수 있는 권한이 있어야 합니다. 자격 증명을 필요로 하는 작업은 다음과 같습니다.

-  연결 된 저장소 계정을 사용 하 여 공유 만들기
-  장치에서 공유에 액세스할 수 있는 사용자 만들기

`User`가능 하면 Active Directory 테 넌 트에 대 한 액세스 권한이 있어야 합니다 `Read all directory objects` . 권한이 없는 게스트 사용자는 사용할 수 없습니다 `Read all directory objects` . 게스트 인 경우 활성화 키 생성, Azure Stack에 지 장치에서 공유 만들기, 사용자 만들기, Edge 계산 역할의 구성, 장치 암호 다시 설정 등의 작업이 모두 실패 합니다.

Microsoft Graph API에 대 한 사용자 액세스를 제공 하는 방법에 대 한 자세한 내용은 [Microsoft Graph 사용 권한 참조](https://docs.microsoft.com/graph/permissions-reference)를 참조 하세요.

### <a name="register-resource-providers"></a>리소스 공급자 등록

Azure에서 리소스를 프로 비전 하려면 (Azure Resource Manager 모델) 해당 리소스 만들기를 지 원하는 리소스 공급자가 필요 합니다. 예를 들어 가상 컴퓨터를 프로 비전 하려면 구독에서 ' Microsoft. Compute ' 리소스 공급자를 사용할 수 있어야 합니다.
 
리소스 공급자는 구독 수준에서 등록됩니다. 기본적으로 새 Azure 구독은 주로 사용되는 리소스 공급자 목록에 미리 등록됩니다. 이 목록에는 ' DataBoxEdge '에 대 한 리소스 공급자가 포함 되어 있지 않습니다.

사용자가 해당 리소스에 대 한 리소스 공급자가 이미 등록 되어 있는 경우 소유자 권한이 있는 리소스 그룹 내에서 ' DataBoxEdge ' 같은 리소스를 만들 수 있도록 구독 수준에 대 한 액세스 권한을 부여 하지 않아도 됩니다.

리소스를 만들기 전에 리소스 공급자가 구독에 등록 되어 있는지 확인 합니다. 리소스 공급자가 등록 되지 않은 경우 새 리소스를 만드는 사용자에 게 구독 수준에서 필요한 리소스 공급자를 등록할 수 있는 충분 한 권한이 있는지 확인 해야 합니다. 아직 수행 하지 않은 경우 다음과 같은 오류가 표시 됩니다.

*구독에 \<Subscription name> 리소스 공급자를 등록할 수 있는 권한이 없습니다. DataBoxEdge.*


현재 구독에서 등록 된 리소스 공급자 목록을 가져오려면 다음 명령을 실행 합니다.

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Azure Stack Edge 장치의 경우을 `Microsoft.DataBoxEdge` 등록 해야 합니다. 등록 하려면 `Microsoft.DataBoxEdge` 구독 관리자가 다음 명령을 실행 해야 합니다.

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

리소스 공급자를 등록 하는 방법에 대 한 자세한 내용은 [리소스 공급자 등록에 대 한 오류 해결](../azure-resource-manager/templates/error-register-resource-provider.md)을 참조 하세요.

## <a name="manage-connectivity-mode"></a>연결 모드 관리

기본 완전히 연결 된 모드와는 별도로 장치를 부분적으로 연결 된 모드 또는 완전히 연결이 끊어진 모드에서 실행할 수도 있습니다. 이러한 각 모드는 아래에 설명되어 있습니다.

- **모두 연결** 됨-장치가 작동 하는 일반적인 기본 모드입니다. 이 모드에서는 데이터의 클라우드 업로드 및 다운로드를 모두 사용할 수 있습니다. Azure Portal 또는 로컬 웹 UI를 사용 하 여 장치를 관리할 수 있습니다.

- **부분적으로 분리** 됨 –이 모드에서 장치는 공유 데이터를 업로드 하거나 다운로드할 수 없지만 Azure Portal를 통해 관리할 수 있습니다.

    이 모드는 일반적으로 요금제 위성 네트워크에서 사용되며 목표는 네트워크 대역폭 소비를 최소화하는 것입니다. 디바이스 모니터링 작업을 위해 최소한의 네트워크 사용량이 여전히 발생할 수 있습니다.

- **연결 끊김** – 이 모드에서는 디바이스가 클라우드에서 연결이 완전히 끊어지고 클라우드 업로드와 다운로드를 모두 사용할 수 없습니다. 디바이스는 로컬 웹 UI를 통해서만 관리할 수 있습니다.

    이 모드는 일반적으로 디바이스를 오프라인 상태로 만들 때 사용합니다.

디바이스 모드를 변경하려면 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에서 **구성 > 클라우드 설정**으로 이동합니다.
2. 드롭다운 목록에서 장치를 작동 하는 데 사용할 모드를 선택 합니다. **전체 연결 됨**, **부분적으로 연결 됨**및 **완전히 분리**됨에서 선택할 수 있습니다. 부분적으로 연결이 끊긴 모드에서 디바이스를 실행하려면 **Azure Portal 관리**를 사용하도록 설정합니다.

    ![연결 모드](media/azure-stack-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>전원 관리

로컬 웹 UI를 사용하여 물리적 디바이스를 종료하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 데이터 서버에서 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이 작업을 수행하면 데이터 손상 가능성이 최소화됩니다.

1. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. 수행 하려는 작업에 따라 **종료** 또는 **다시 시작** 을 선택 합니다.

    ![전원 설정](media/azure-stack-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 확인 메시지가 표시 되 면 **예** 를 선택 하 여 계속 합니다.

> [!NOTE]
> 물리적 장치를 종료 하는 경우 장치에서 전원 단추를 눌러 장치를 켜야 합니다.

## <a name="next-steps"></a>다음 단계

- [공유 관리](azure-stack-edge-manage-shares.md) 방법에 대해 알아봅니다.
