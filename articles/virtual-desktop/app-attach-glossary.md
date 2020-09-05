---
title: Windows 가상 데스크톱 MSIX 앱 연결 용어집-Azure
description: MSIX 앱 연결 용어 및 개념을 설명 합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f3cc8495f673c8b428aa9e6ace2747a70c5b0847
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556289"
---
# <a name="msix-app-attach-glossary"></a>MSIX 앱 연결 용어집

이 문서는 MSIX 앱 첨부와 관련 된 주요 용어 및 개념에 대 한 정의의 목록입니다.

## <a name="msix-container"></a>MSIX 컨테이너

MSIX 컨테이너는 MSIX 개 앱이 실행 되는 위치입니다. 자세히 알아보려면 [Msix 컨테이너](/windows/msix/msix-container)를 참조 하세요.

## <a name="msix-application"></a>MSIX 응용 프로그램 

MSIX 파일에 저장 된 응용 프로그램입니다.

## <a name="msix-package"></a>MSIX 패키지 

MSIX 패키지는 MSIX 파일 또는 응용 프로그램입니다.

## <a name="msix-share"></a>MSIX 공유

MSIX 공유는 확장 된 MSIX 패키지를 포함 하는 네트워크 공유입니다. MSIX 공유는 SMB 3 이상을 지원 합니다. 응용 프로그램은 응용 프로그램 파일을 시스템 드라이브로 이동 하지 않고도이 MSIX 공유에서 준비 됩니다.

## <a name="repackage"></a>리

다시 패키지는 msix이 아닌 응용 프로그램을 사용 하 여 MSIX 패키징 도구 (.MPT)를 사용 하 여 MSIX으로 변환 합니다. 자세한 내용은 [Msix 패키징 도구 개요](/windows/msix/packaging-tool/tool-overview)를 참조 하세요.

## <a name="expand"></a>Expand

확장 MSIX 패키지는 여러 단계 프로세스입니다. MSIX 파일을 사용 하 여 해당 콘텐츠를 VHD (x) 또는 CIM 파일에 저장 합니다. 

MSIX 패키지를 확장 하려면 다음을 수행 합니다.

1. MSIX 패키지 (MSIX 파일)를 가져옵니다.
2. MSIX 파일의 이름을 .zip 파일로 바꿉니다.
3. 폴더에 결과 .zip 파일의 압축을 풉니다.
4. 폴더와 동일한 크기의 VHD를 만듭니다.
5. VHD를 탑재 합니다.
6. 디스크를 초기화 합니다.
7. 파티션을 만듭니다.
8. 파티션을 포맷 합니다.
9. 압축을 푼 콘텐츠를 VHD에 복사 합니다.
10. MSIXMGR 도구를 사용 하 여 VHD의 내용에 Acl을 적용 합니다.
11. VHD (x) 또는 [CIM](#cim)을 분리 합니다.

## <a name="upload-an-msix-package"></a>MSIX 패키지 업로드 

MSIX 패키지를 업로드 하려면 확장 된 MSIX 패키지를 포함 하는 VHD (x) 또는 [CIM](#cim) 을 msix 공유에 업로드 해야 합니다.

Windows 가상 데스크톱에서 업로드는 MSIX 공유 당 한 번 수행 됩니다. 패키지를 업로드 하면 동일한 구독의 모든 호스트 풀에서 해당 패키지를 참조할 수 있습니다.

## <a name="publish-an-msix-package"></a>MSIX 패키지 게시

Windows 가상 데스크톱에서 MSIX 패키지를 게시 하면 원격 앱 또는 데스크톱에 연결 됩니다.

## <a name="assign-an-msix-package"></a>MSIX 패키지 할당 

Windows 가상 데스크톱에서 게시 된 MSIX 패키지는 Active Directory 도메인 서비스 (AD DS) 또는 Azure Active Directory (Azure AD) 사용자 또는 사용자 그룹에 할당 되어야 합니다.

## <a name="staging"></a>준비

준비에는 다음 두 가지 작업이 포함 됩니다.

- VM에 VHD (x) 또는 [CIM](#cim) 을 탑재 합니다.
- MSIX 패키지를 등록할 수 있음을 OS에 알립니다.

## <a name="registration"></a>등록

등록을 통해 사용자에 게 스테이징 된 MSIX 패키지를 사용할 수 있습니다. 등록은 사용자 단위로 설정 됩니다. 특정 사용자에 대해 앱을 명시적으로 등록 하지 않은 경우 앱을 실행할 수 없습니다.

등록에는 두 가지 유형 (일반 및 지연)이 있습니다.

### <a name="regular-registration"></a>정기 등록

정기적으로 등록 하는 경우 사용자에 게 할당 된 각 응용 프로그램이 완전히 등록 됩니다. 등록은 사용자가 세션에 로그인 하는 동안 발생 하며이는 Windows 가상 데스크톱을 사용 하 여 시작 하는 데 걸리는 시간에 영향을 줄 수 있습니다.

### <a name="delayed-registration"></a>지연 된 등록

지연 된 등록에서 사용자에 게 할당 된 각 응용 프로그램은 부분적 으로만 등록 됩니다. 부분 등록 이란 시작 메뉴 타일과 파일 연결을 두 번 클릭 하 여 등록 했음을 의미 합니다. 등록은 사용자가 자신의 세션에 로그인 하는 동안 발생 하므로 Windows 가상 데스크톱을 사용 하 여 시작 하는 데 걸리는 시간에 대 한 영향을 최소화 합니다. 등록은 사용자가 MSIX 패키지에서 응용 프로그램을 실행 하는 경우에만 완료 됩니다.

지연 등록은 현재 MSIX 앱 연결에 대 한 기본 구성입니다.

## <a name="deregistration"></a>등록

등록을 취소 하면 사용자에 대해 등록 되었지만 실행 되지 않은 MSIX 패키지가 제거 됩니다. 사용자가 세션에서 로그 아웃 하는 동안 등록 취소가 발생 합니다. 등록을 취소 하는 동안 MSIX 앱 연결은 사용자와 관련 된 응용 프로그램 데이터를 로컬 사용자 프로필에 푸시합니다.

## <a name="destage"></a>스테이징 취소

Destaging은 현재 실행 되 고 있지 않고 사용자에 대해 준비 되지 않은 MSIX 패키지 또는 응용 프로그램을 분리할 수 있음을 OS에 알립니다. 그러면 OS에서이에 대 한 모든 참조가 제거 됩니다.

## <a name="cim"></a>CIM

. CIM은 CimFS (복합 이미지 파일 시스템)와 연결 된 새 파일 확장명입니다. CIM 파일 탑재 및 탑재 해제는 VHD 파일 보다 더 빠릅니다. 또한 CIM은 VHD 보다 더 작은 CPU와 메모리를 사용 합니다.

다음 표는 VHD와 CimFS 간의 성능 비교입니다. 이러한 숫자는 DSv4 컴퓨터에서 실행 되는 각 형식의 800 MB 파일을 사용 하 여 테스트를 실행 한 결과입니다.

|  사양                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| 평균 탑재 시간     | 356 밀리초                     | 255 밀리초      |
| 평균 분리 시간   | 1615 밀리초                    | 36 밀리초       |
| 메모리 사용량 | 6% (8gb)                      | 2% (8gb)       |
| CPU (스파이크 수)          | 여러 번 최대값 | 영향 없음 |

## <a name="next-steps"></a>다음 단계

MSIX 앱 연결에 대해 자세히 알아보려면 [개요](what-is-app-attach.md) 및 [FAQ](app-attach-faq.md)를 확인 하세요. 그렇지 않으면 [앱 연결 설정](app-attach.md)을 시작 합니다.

