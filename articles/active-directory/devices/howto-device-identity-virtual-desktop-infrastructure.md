---
title: 장치 id 및 데스크톱 가상화-Azure Active Directory
description: VDI와 Azure AD 장치 id를 함께 사용 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0313c27666cda785b24f23436f6ad727f337ca8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259356"
---
# <a name="device-identity-and-desktop-virtualization"></a>장치 id 및 데스크톱 가상화

관리자는 일반적으로 조직에서 Windows 운영 체제를 호스팅하는 VDI (가상 데스크톱 인프라) 플랫폼을 배포 합니다. 관리자는 VDI를 다음에 배포 합니다.

- 관리를 간소화 합니다.
- 리소스를 통합 하 고 중앙 집중화 하 여 비용을 절감 합니다.
- 언제 어디서 나 모든 장치에서 가상 데스크톱에 액세스할 수 있는 최종 사용자 이동성 및 자유를 제공 합니다.

가상 데스크톱에는 다음과 같은 두 가지 기본 유형이 있습니다.

- 영구적
- 비영구적

영구 버전은 각 사용자 또는 사용자 풀에 고유한 데스크톱 이미지를 사용 합니다. 이러한 고유한 데스크톱은 나중에 사용할 수 있도록 사용자 지정 하 고 저장할 수 있습니다. 

비영구 버전은 사용자가 필요에 따라에 액세스할 수 있는 데스크톱 컬렉션을 사용 합니다. 이러한 비영구 데스크톱은 원래 상태로 되돌아갑니다. Windows 현재<sup>1</sup> 의 경우 가상 컴퓨터가 종료/다시 시작/OS 다시 설정 프로세스를 통과 하 고 windows 하위 수준<sup>2</sup> 의 경우 사용자가 로그 아웃 하면이 오류가 발생 합니다.

원격 작업은 새로운 일반 작업으로 지속 되므로 비영구 VDI 배포가 증가 했습니다. 고객이 비영구 VDI를 배포 하는 경우 장치 수명 주기 관리에 대 한 적절 한 전략이 없어도 장치 등록이 자주 발생 했을 때 발생할 수 있는 장치 변동 (code churn)을 관리 하는 것이 중요 합니다.

> [!IMPORTANT]
> 장치 변동 관리에 실패 하면 테 넌 트 할당량을 초과 하는 경우 테 넌 트 할당량 사용 사용량과 잠재적 서비스 중단 위험이 발생할 수 있습니다. 이러한 상황을 방지 하려면 비영구 VDI 환경을 배포할 때 아래에 설명 된 지침을 따라야 합니다.

이 문서에서는 관리자에 게 장치 id 및 VDI에 대 한 지원을 제공 하는 Microsoft의 지침을 설명 합니다. 장치 id에 대 한 자세한 내용은 [장치 Id 정의](overview.md)문서를 참조 하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

VDI 환경에 대해 Azure AD에서 장치 id를 구성 하기 전에 지원 되는 시나리오를 숙지 합니다. 다음 표에서는 지원 되는 프로 비전 시나리오를 보여 줍니다. 이 컨텍스트에서 프로 비전 하는 것은 관리자가 최종 사용자 개입 없이도 장치 id를 대규모로 구성할 수 있음을 의미 합니다.

| 장치 id 유형 | ID 인프라 | Windows 디바이스 | VDI 플랫폼 버전 | 지원됨 |
| --- | --- | --- | --- | --- |
| 하이브리드 Azure AD 가입 | 페더레이션된<sup>3</sup> | Windows 현재 및 Windows 하위 수준 | 영구적 | 예 |
|   |   | Windows 현재 | 비영구 | 예<sup>5</sup> |
|   |   | Windows 하위 수준 | 비영구 | 예<sup>6</sup> |
|   | 관리<sup>4</sup> | Windows 현재 및 Windows 하위 수준 | 영구적 | 예 |
|   |   | Windows 현재 | 비영구 | 예 |
|   |   | Windows 하위 수준 | 비영구 | 예<sup>6</sup> |
| Azure AD 가입 | 페더레이션 | Windows 현재 | 영구적 | 예 |
|   |   |   | 비영구 | 예 |
|   | 관리형 | Windows 현재 | 영구적 | 예 |
|   |   |   | 비영구 | 예 |
| Azure AD 등록 | 페더레이션된/관리 | Windows 현재/Windows 하위 수준 | 영구/비영구 | 해당 없음 |

<sup>1</sup> **windows 현재** 장치는 Windows 10, Windows Server 2016 및 windows server 2019를 나타냅니다.

<sup>2</sup> **windows 하위 수준** 장치는 windows 7, Windows 8.1, windows Server 2008 r2, Windows Server 2012 및 windows server 2012 r 2를 나타냅니다. Windows 7에 대 한 지원 정보 [는 windows 7 지원 종료](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)를 참조 하세요. Windows Server 2008 r 2에 대 한 지원 정보는 [Windows server 2008 지원 종료](https://www.microsoft.com/cloud-platform/windows-server-2008)를 참조 하세요.

<sup>3</sup> **페더레이션** id 인프라 환경은 AD FS 또는 기타 타사 IDP와 같은 id 공급자를 사용 하는 환경을 나타냅니다.

<sup>4</sup> **관리 되** 는 id 인프라 환경은 [원활한 Single Sign-On](../hybrid/how-to-connect-sso.md)를 사용 하 여 Azure AD가 [phs (암호 해시 동기화](../hybrid/whatis-phs.md) ) 또는 [pta (통과 인증)](../hybrid/how-to-connect-pta.md) 로 배포 된 id 공급자로 Azure AD를 사용 하는 환경을 나타냅니다.

<sup>5</sup> **Windows 현재에 대 한 지 속성 지원을 지원 하지 않는** 경우 아래 지침 섹션에서 설명 하는 추가 고려 사항이 필요 합니다. 이 시나리오에는 Windows 10 1803, Windows Server 2019 또는 Windows Server (반기 채널) 버전 1803이 필요 합니다.

<sup>6</sup> **Windows 하위 수준에 대 한 지 속성 지원을** 사용 하려면 아래 지침 섹션에 설명 된 대로 추가로 고려해 야 합니다.


## <a name="microsofts-guidance"></a>Microsoft 지침

관리자는 하이브리드 Azure AD 조인을 구성 하는 방법을 알아보려면 id 인프라에 따라 다음 문서를 참조 해야 합니다.

- [페더레이션된 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
- [관리 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

비영구 VDI를 배포 하는 경우 IT 관리자가 아래 지침을 구현 하는 것이 좋습니다. 이렇게 하지 않으면 영구적이 지 않은 VDI 플랫폼에서 등록 된 오래 된 하이브리드 Azure AD 조인 장치가 많은 디렉터리에 있게 됩니다. 그러면 테 넌 트 할당량에 대 한 압력이 증가 하 고 테 넌 트 할당량이 부족 하 여 서비스 중단이 발생 합니다.

- 시스템 준비 도구 (sysprep.exe)를 사용 하 고 설치에 Windows 10 1809 이전 이미지를 사용 하는 경우, 하이브리드 Azure AD에 가입 된 Azure AD에 이미 등록 된 장치에서 이미지를 사용 하 고 있지 않은지 확인 합니다.
- VM (가상 머신) 스냅숏을 사용 하 여 추가 Vm을 만드는 경우, Azure AD에 이미 등록 된 VM의 스냅숏이 하이브리드 Azure AD 조인으로 사용 되지 않는지 확인 합니다.
- 데스크톱을 비영구 VDI 기반으로 표시 하는 컴퓨터의 표시 이름 (예: NPVDI)에 대 한 접두사를 만들고 사용 합니다.
- Windows 하위 수준:
   - **Autoworkplacejoin/leave** 명령을 로그 오프 스크립트의 일부로 구현 합니다. 이 명령은 사용자의 컨텍스트에서 트리거 되어야 하며, 사용자가 완전히 로그 오프 하 고 네트워크에 연결 되어 있는 동안에는 실행 해야 합니다.
- 페더레이션된 환경의 Windows current의 경우 (예: AD FS):
   - VM 부팅 시퀀스의 일부로 **dsregcmd.exe/join** 을 구현 합니다.
   - VM 종료/다시 시작 프로세스의 일부로 dsregcmd.exe/leave를 실행 **하지 마십시오** .
- [오래 된 장치를 관리](manage-stale-devices.md)하는 프로세스를 정의 하 고 구현 합니다.
   - 영구적이 지 않은 하이브리드 Azure AD 조인 장치를 식별 하는 전략이 있으면 (예: 컴퓨터 표시 이름 접두사 사용), 이러한 장치를 정리 하는 방법에 따라 디렉터리가 오래 된 많은 장치에서 사용 되지 않도록 해야 합니다.
   - Windows 현재 및 하위 수준에서 비영구 VDI를 배포 하는 경우 **ApproximateLastLogonTimestamp** 가 15 일 보다 오래 된 장치를 삭제 해야 합니다.
 
## <a name="next-steps"></a>다음 단계

[페더레이션된 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
