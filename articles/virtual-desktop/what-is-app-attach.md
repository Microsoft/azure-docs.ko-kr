---
title: Windows 가상 데스크톱 MSIX 앱 연결 개요-Azure
description: MSIX 앱 연결 이란? 이 문서에서 확인 하세요.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556281"
---
# <a name="what-is-msix-app-attach"></a>MSIX 앱 연결 이란?

MSIX은 모든 Windows 앱에 대 한 패키징 환경을 개선 하는 데 필요한 많은 기능을 제공 하는 새로운 패키징 형식입니다. MSIX에 대해 자세히 알아보려면 [msix 개요](/windows/msix/overview)를 참조 하세요.

MSIX 앱 연결은 물리적 컴퓨터와 가상 컴퓨터 모두에 MSIX 개의 응용 프로그램을 제공 하는 방법입니다. 그러나 MSIX 앱 연결은 Windows 가상 데스크톱에서 특히 이루어지기 때문에 regular MSIX과 다릅니다. 이 문서에서는 MSIX 앱 연결의 정의와 사용자를 위해 수행할 수 있는 작업을 설명 합니다.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Windows 가상 데스크톱의 응용 프로그램 제공 옵션

다음 방법 중 하나를 통해 Windows 가상 데스크톱에서 앱을 배달할 수 있습니다.

- 마스터 이미지에 앱 배치
- 중앙 관리를 위해 SCCM 또는 Intune과 같은 도구 사용
- 동적 앱 프로 비전 (AppV, VMWare AppVolumes 또는 Citrix AppLayering)
- Microsoft 및 타사 도구를 사용 하 여 사용자 지정 도구 또는 스크립트 만들기

## <a name="what-does-msix-app-attach-do"></a>MSIX 앱 연결에는 무엇이 필요 한가요?

Windows 가상 데스크톱 배포에서 MSIX 앱 연결은 다음과 같은 작업을 수행할 수 있습니다.

- [Msix 컨테이너](/windows/msix/msix-container)를 사용 하 여 사용자 데이터, OS 및 앱 간의 분리를 만듭니다.
- 응용 프로그램을 동적으로 제공할 때 다시 패키지에 대 한 필요성을 제거 합니다.
- 사용자가 로그인 하는 데 걸리는 시간을 줄입니다.
- 인프라 요구 사항 및 비용을 줄입니다.

MSIX 앱 연결은 VDI 또는 SBC 외부에서 적용할 수 있어야 합니다.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>MSIX 앱 연결에 비해 기존 앱 계층화

다음 표에서는 MSIX 앱 연결 및 앱 계층화의 주요 기능을 비교 합니다.

| 기능 | 기존 앱 계층화  | MSIX 앱 연결  |
|-----|-----------------------------|--------------------|
| 서식               | 다른 앱 계층화 기술에는 서로 다른 독자적인 형식이 필요 합니다. | Native MSIX 패키징 형식으로 작동 합니다.        |
| 다시 패키지 오버 헤드 | 소유 형식에는 업데이트 당 시퀀싱 및 다시 패키지가 필요 합니다.         | MSIX으로 게시 된 앱에는 다시 패키지가 필요 하지 않습니다. 그러나 MSIX 패키지를 사용할 수 없는 경우 다시 패키지 오버 헤드가 여전히 적용 됩니다. |
| 에코시스템            | 해당 없음 (예: 공급 업체는 App-v를 제공 하지 않음)  | MSIX은 Office와 같은 핵심 ISV 파트너 및 사내 앱을 채택 하는 Microsoft의 기본 기술입니다. 가상 데스크톱 및 물리적 Windows 컴퓨터에서 MSIX을 사용할 수 있습니다. |
| 인프라       | 추가 인프라 필요 (서버, 클라이언트 등) | 저장소 전용   |
| 관리       | 유지 관리 및 업데이트 필요   | 앱 업데이트 간소화 |
| 사용자 환경      | 사용자 로그인 시간에 영향을 줍니다. 경계는 OS 상태, 앱 상태 및 사용자 데이터 사이에 존재 합니다.  | 배달 된 앱은 로컬로 설치 된 응용 프로그램과 구분할 수 없습니다. |

## <a name="next-steps"></a>다음 단계

MSIX 앱 연결에 대해 자세히 알아보려면 [용어집](app-attach-glossary.md) 및 [FAQ](app-attach-faq.md)를 확인 하세요. 그렇지 않으면 [앱 연결 설정](app-attach.md)을 시작 합니다.
