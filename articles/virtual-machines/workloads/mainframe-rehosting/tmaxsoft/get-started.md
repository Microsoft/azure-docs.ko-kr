---
title: Azure Virtual Machines에서 TmaxSoft OpenFrame 시작
description: Azure Virtual Machines (Vm)에서 TmaxSoft OpenFrame 환경을 사용 하 여 IBM z/OS 메인프레임 워크 로드를 다시 호스트 합니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485539"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft OpenFrame 시작

기존 메인프레임 자산 가져오고 TmaxSoft OpenFrame를 사용 하 여 Microsoft Azure로 이동 합니다. 이 인기 있는 재호스팅 솔루션은 azure에서 응용 프로그램을 신속 하 게 마이그레이션할 수 있도록 에뮬레이션 환경을 만듭니다. 다시 포맷 하지가 필요 합니다.

## <a name="openframe-rehosting-environment"></a>OpenFrame 재호스팅 환경

Azure에서 개발, 데모, 테스트 또는 프로덕션 워크 로드에 대 한 OpenFrame 환경을 설정 합니다. 다음 그림에서 알 수 있듯이, OpenFrame 메인프레임 에뮬레이션 환경을 Azure에 생성 되는 여러 구성 요소를 포함 합니다. 예를 들어, 온라인 서비스 OpenFrame 메인프레임 미들웨어와 같은 IBM 고객 정보 제어 시스템 (CICS)를 대체합니다. OpenFrame 일괄 처리를 해당 TJES 구성 요소를 사용 하 여 IBM 메인프레임의 작업 항목 하위 시스템 (랭은 여기서 서쪽)를 대체합니다. 

![OpenFrame 재호스팅 프로세스](media/openframe-01.png)

> [!NOTE]
> OpenFrame 환경에서 Azure를 실행 하려면 유효한 제품 라이선스 또는 TmaxSoft에서 평가판 라이선스가 있어야 합니다.

## <a name="openframe-components"></a>OpenFrame 구성 요소

다음 구성 요소를 사용 하면 Azure에서 OpenFrame 환경의 일부인:

- **마이그레이션 도구** OFMiner, 메인프레임 자산을 분석 하 고 Azure로 마이그레이션함으로써는 솔루션을 포함 합니다.
- **컴파일러**, OFCOBOL에는 메인프레임 COBOL 프로그램과 해석 하는 컴파일러를 포함 하 여 OFPLI, 메인프레임의 PL를 해석 하는 / I 프로그램; 및 OFASM, 메인프레임의 어셈블러 프로그램을 해석 하는 컴파일러입니다.
- **프런트 엔드** Java Enterprise 사용자 솔루션 (JEUS), Java Enterprise Edition 6.OFGW 마다 인증 된 웹 응용 프로그램 서버 및 3270 수신기를 제공 하는 OpenFrame 게이트웨이 구성 요소를 포함 하 여 구성 합니다.
- **응용 프로그램** 환경입니다. OpenFrame 자료는 전체 시스템을 관리 하는 미들웨어입니다. OpenFrame 서버 유형 C (OSC) 메인프레임의 미들웨어 및 IBM CICS 바꿉니다.
- **관계형 데이터베이스**Tibero (표시), Oracle 데이터베이스, Microsoft SQL Server, IBM Db2 또는 MySQL입니다. OpenFrame 응용 프로그램 데이터베이스와 통신 하도록 열린 데이터베이스 연결 (ODBC) 프로토콜을 사용 합니다.
- **보안** TACF, 시스템 및 리소스에 대 한 사용자 액세스를 제어 하는 서비스 모듈을 통해. 
- **OFManager** 웹 환경에서 OpenFrame의 작업 및 관리 기능을 제공 하는 솔루션입니다.

![OpenFrame 아키텍처](media/openframe-02.png)

## <a name="next-steps"></a>다음 단계

- [Azure에서 TmaxSoft OpenFrame 설치](./install-openframe-azure.md)
