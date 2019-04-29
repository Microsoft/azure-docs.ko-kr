---
title: Azure Migrate의 Collector 어플라이언스 업그레이드 | Microsoft Docs
description: Azure Migrate의 Collector 어플라이언스 업그레이드에 대한 정보를 제공합니다.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685922"
---
# <a name="collector-appliance-updates"></a>수집기 어플라이언스 업데이트

이 문서에서는 [Azure Migrate](migrate-overview.md)의 Collector 어플라이언스 업그레이드 정보를 간략하게 제공합니다.

Azure Migrate Collector는 Azure로 마이그레이션하기 전에 평가를 진행하기 위해 온-프레미스 vCenter 환경을 검색하는 데 사용되는 간편한 어플라이언스입니다. [자세히 알아보기](concepts-collector.md).

## <a name="how-to-upgrade-the-appliance"></a>어플라이언스를 업그레이드 하는 방법

OVA를 다시 다운로드하지 않고도 Collector를 최신 버전으로 업그레이드할 수 있습니다.

1. 닫기 및 모든 브라우저 창을 파일/폴더 기기에서 엽니다.
2. 이 문서의 아래에 언급 된 업데이트 목록에서 최신 업그레이드 패키지를 다운로드 합니다.
3. 다운로드 한 패키지 안전한 지 확인 하려면 관리자 권한 명령 창을 열고 ZIP 파일에 대 한 해시를 생성 하려면 다음 명령을 실행 합니다. 생성된 해시는 특정 버전에 대해 언급된 해시와 일치해야 합니다.

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    예제: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Collector 어플라이언스 VM에 zip 파일을 복사합니다.
5. zip 파일을 마우스 오른쪽 단추로 클릭하고 **압축 풀기**를 선택합니다.
6. **Setup.ps1**을 마우스 오른쪽 단추로 클릭  >  **PowerShell에서 실행**을 선택하고 설치 지침을 따릅니다.

## <a name="collector-update-release-history"></a>Collector 업데이트 릴리스 기록

### <a name="continuous-discovery-upgrade-versions"></a>연속 검색: 업그레이드 버전

#### <a name="version-101014-released-on-03292019"></a>(03/29/2019에 릴리스됨) 1.0.10.14 버전

몇 가지 UI 기능이 포함 되어 있습니다.

업그레이드에 대 한 값을 해시 [1.0.10.14 패키지](https://aka.ms/migrate/col/upgrade_10_14)

**알고리즘** | **해시 값**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>(03/13/2019에 릴리스됨) 1.0.10.12 버전

문제에 대 한 수정 프로그램이 포함 되어 클라우드 어플라이언스에 Azure를 선택 합니다.

업그레이드에 대 한 값을 해시 [1.0.10.12 패키지](https://aka.ms/migrate/col/upgrade_10_12)

**알고리즘** | **해시 값**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>일회성 검색(현재 사용 중단): 이전 업그레이드 버전

> [!NOTE]
> 일회성 검색 어플라이언스는 성능 데이터 지점 가용성에 대한 vCenter Server의 통계 설정에 의존하고 평균 성능 카운터를 수집함에 따라 Azure로 마이그레이션할 VM의 크기가 부족해진다는 이유로 현재는 사용되지 않습니다.

#### <a name="version-10916-released-on-10292018"></a>버전 1.0.9.16(2018년 10월 29 출시)

어플라이언스를 설정하는 동안 발생하는 PowerCLI 문제에 대한 수정 사항을 포함합니다.

업그레이드 [패키지 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)에 대한 해시 값

**알고리즘** | **해시 값**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>버전 1.0.9.14

업그레이드 [패키지 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)에 대한 해시 값

**알고리즘** | **해시 값**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>버전 1.0.9.13

업그레이드 [패키지 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)에 대한 해시 값

**알고리즘** | **해시 값**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>다음 단계

- Collector 어플라이언스에 대해 [자세히 알아봅니다](concepts-collector.md).
- VMware VM의 [평가를 실행](tutorial-assessment-vmware.md)합니다.
