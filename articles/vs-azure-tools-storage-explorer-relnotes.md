---
title: Microsoft Azure Storage Explorer 릴리스 정보
description: Microsoft Azure Storage Explorer에 대한 릴리스 정보
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 1adfb59843150ffaa6ed76411d07d8ec6cf6a44b
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555258"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Microsoft Azure Storage Explorer 릴리스 정보

이 문서에는 이전 버전에 대한 릴리스 정보 뿐만 아니라 Azure Storage 탐색기에 대한 최신 릴리스 정보가 포함 되어 있습니다. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터를 쉽게 사용할 수 있게 하는 독립 실행형 앱입니다.

이전 버전의 Storage 탐색기을 다운로드 하려면 GitHub 리포지토리의 [릴리스 페이지](https://github.com/microsoft/AzureStorageExplorer/releases) 를 방문 하면 됩니다.

## <a name="version-1110"></a>버전 1.11.0
11/4/2019

### <a name="new"></a>신규
* Blob, ADLS Gen2 및 Managed Disks에 대한 작업은 통합 AzCopy를 사용 합니다. 구체적으로 말하면 AzCopy를 사용 하 여 다음 작업을 수행 합니다.
   * Blob
      * 편집용으로 열기 + 업로드
      * & 끌어서 놓기를 포함 하 여 업로드
      * 다운로드
      * 복사 & 붙여넣기 #1249
      * 삭제
   * ADLS Gen2 Blob
      * & 끌어서 놓기를 포함 하 여 업로드
      * 다운로드
      * 복사 & 붙여넣기
      * 삭제 (폴더 삭제 포함)
   * Managed Disks
      * 업로드
      * 다운로드
      * 복사 & 붙여넣기

   또한 몇 가지 자주 요청 되는 기능이 통합 AzCopy 환경에 추가 되었습니다.
   * 충돌 해결-충돌을 해결 하기 위해 전송 하는 동안 메시지가 표시 됩니다. #1455
   * 페이지 blob으로 업로드-AzCopy 및 .vhdx 파일을 페이지 blob으로 업로드 하는지 여부를 선택할 수 있습니다. #1164 및 #1601
   * 구성 가능한 AzCopy 매개 변수-AzCopy의 성능 및 리소스 사용량을 조정 하기 위해 여러 설정이 추가 되었습니다. 아래 세부 정보를 참조 하세요.

* ADLS Gen2 및 Blob 다중 프로토콜 액세스를 사용 하도록 설정 하 고 ADLS Gen2 환경을 강화 하기 위해 ADLS Gen2 계정에 대해 다음과 같은 기능을 추가 했습니다.
   * 이름을 사용 하 여 ACL 권한 설정 검색
   * $Logs 및 $web 같은 숨겨진 컨테이너를 봅니다.
   * 컨테이너 임대 획득 및 중단
   * Blob 임대 가져오기 및 중단 #848
   * 컨테이너 액세스 정책 관리
   * Blob 액세스 계층 구성
   * Blob 복사 & 붙여넣기

* 이 릴리스에서는 17 개의 추가 언어를 미리 보고 하 고 있습니다. 설정 페이지의 "응용 프로그램" → "국가별 설정" → "언어 (미리 보기)" 아래에서 원하는 언어로 전환할 수 있습니다. 계속 해 서 추가 문자열을 변환 하 고 변환 품질을 개선 하는 작업을 수행 하 고 있습니다. 번역에 대한 의견이 있거나 아직 변환 되지 않은 문자열이 있는 경우 [GitHub에서 문제를 여세요](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* 모든 릴리스에서는 Storage 탐색기을 세밀 하 게 설정 하기 위해 몇 가지 설정을 등록 하려고 합니다. 이 릴리스에서는 AzCopy 및 서비스 노드를 숨기는 것을 추가로 구성 하는 설정이 추가 되었습니다.
   * AzCopy 대역폭 제한-네트워크 AzCopy에서 사용 하는 크기를 제어 하는 데 도움이 됩니다. 이 설정은 "전송" → "AzCopy" → "최대 전송 속도"에서 찾을 수 있습니다. #1099
   * AzCopy MD5 확인-다운로드 시 엄격히 AzCopy에서 MD5 해시를 확인 하는 경우와 방법을 구성할 수 있습니다. 이 설정은 "전송" → "AzCopy" → "MD5 확인"에서 찾을 수 있습니다.
   * AzCopy concurrency 및 메모리 버퍼 크기-기본적으로 AzCopy는 컴퓨터를 분석 하 여 이러한 설정에 대한 적절 한 기본값을 결정 합니다. 그러나 성능 문제가 발생 하는 경우 이러한 고급 설정을 사용 하 여 컴퓨터에서 AzCopy가 실행 되는 방식을 추가로 조정할 수 있습니다. 이러한 설정은 "전송" → "AzCopy" 아래에서 찾을 수 있습니다. #994
   * 서비스 노드 표시 및 숨기기-이러한 설정은 Storage 탐색기에서 지 원하는 Azure 서비스를 표시 하거나 숨기는 옵션을 제공 합니다. 이러한 설정은 "서비스" 섹션에서 찾을 수 있습니다. #1877

* 관리 디스크의 스냅숏을 만들 때 이제 기본 이름이 제공 됩니다. #1847
* Azure AD와 연결 하는 경우 ADLS Gen2 Blob 컨테이너를 연결 하면 노드 옆에 "(ADLS Gen2)"가 표시 됩니다. #1861

### <a name="fixes"></a>수정 프로그램
* 대량 디스크를 복사, 업로드 또는 다운로드 하는 경우에는 Storage 탐색기 작업에 관련 된 디스크에 대한 액세스를 취소 하는 데 실패할 수 있습니다. 이 문제가 해결되었습니다. #2048
* 파티션 키 쿼리를 볼 때 테이블 통계가 실패 했습니다. 이 문제가 해결되었습니다. #1886

### <a name="known-issues"></a>알려진 문제
* 이제 Storage 탐색기 1.11.0에는 ADLS Gen2 컨테이너에 연결 하기 위한 DFS 엔드포인트 (예: "myaccount.dfs.core.windows.net")이 필요 합니다. 이전 버전의 Storage 탐색기에서는 blob 엔드포인트을 사용할 수 있었습니다. 이러한 첨부 파일은 1.11.0로 업그레이드 한 후 더 이상 작동 하지 않을 수 있습니다. 이 문제가 발생 하는 경우 DFS 엔드포인트을 사용 하 여 다시 연결 합니다.
* 숫자 설정은 올바른 범위에 있는지 여부를 확인 하지 않습니다. #2140
* 트리 보기에서 한 저장소 계정에서 다른 저장소 계정으로 blob 컨테이너를 복사 하지 못할 수 있습니다. 문제를 조사 하 고 있습니다. #2124
* 자동 새로 고침 설정은 아직 Blob 탐색기의 모든 작업에 영향을 주지 않습니다.
* 관리 디스크 기능은 Azure Stack에서 지원 되지 않습니다.
* 디스크를 업로드 하거나 붙여 넣을 수 없고 오류 전에 새 디스크가 생성 된 경우에는 Storage 탐색기에서 디스크를 삭제 하지 않습니다.
* 디스크 업로드 또는 붙여넣기를 취소 하는 경우에 따라 새 디스크를 손상 된 상태로 둘 수 있습니다. 이 경우 새 디스크를 삭제 하거나 디스크 Api를 수동으로 호출 하 여 디스크의 내용이 더 이상 손상 되지 않도록 해야 합니다.
* RBAC를 사용 하는 경우 저장소 리소스에 액세스 하기 위해 Storage 탐색기에 일부 관리 계층 권한이 필요 합니다. 자세한 내용은 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 를 참조 하세요.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
   * ADLS Gen2
   * Managed Disks
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux에서 Storage 탐색기를 실행 하려면 먼저 특정 종속성을 설치 해야 합니다. 자세한 내용은 Storage 탐색기 [문제 해결 가이드를 참조](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) 하세요.

## <a name="previous-releases"></a>이전 릴리스

* [버전 1.10.1](#version-1101)
* [버전 1.10.0](#version-1100)
* [버전 1.9.0](#version-190)
* [버전 1.8.1](#version-181)
* [버전 1.8.0](#version-180)
* [버전 1.7.0](#version-170)
* [버전 1.6.2](#version-162)
* [버전 1.6.1](#version-161)
* [버전 1.6.0](#version-160)
* [버전 1.5.0](#version-150)
* [버전 1.4.4](#version-144)
* [버전 1.4.3](#version-143)
* [버전 1.4.2](#version-142)
* [버전 1.4.1](#version-141)
* [버전 1.3.0](#version-130)
* [버전 1.2.0](#version-120)
* [버전 1.1.0](#version-110)
* [Version 1.0.0](#version-100)
* [버전 0.9.6](#version-096)
* [버전 0.9.5](#version-095)
* [버전 0.9.4 및 0.9.3](#version-094-and-093)
* [버전 0.9.2](#version-092)
* [버전 0.9.1 및 0.9.0](#version-091-and-090)
* [버전 0.8.16](#version-0816)
* [버전 0.8.14](#version-0814)
* [버전 0.8.13](#version-0813)
* [버전 0.8.12 및 0.8.11 및 0.8.10](#version-0812-and-0811-and-0810)
* [버전 0.8.9 및 0.8.8](#version-089-and-088)
* [버전 0.8.7](#version-087)
* [버전 0.8.6](#version-086)
* [버전 0.8.5](#version-085)
* [버전 0.8.4](#version-084)
* [버전 0.8.3](#version-083)
* [버전 0.8.2](#version-082)
* [버전 0.8.0](#version-080)
* [버전 0.7.20160509.0](#version-07201605090)
* [버전 0.7.20160325.0](#version-07201603250)
* [버전 0.7.20160129.1](#version-07201601291)
* [버전 0.7.20160105.0](#version-07201601050)
* [버전 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>버전 1.10.1
9/19/2019

### <a name="hotfix"></a>업데이트나
* ADLS Gen 1 계정에서 데이터를 보려고 시도 하는 동안 일부 사용자가 1.10.0에서 오류가 발생 했습니다. 이 오류가 발생 하 여 탐색기 패널이 제대로 렌더링 되지 못했습니다. 이 문제가 해결되었습니다. #1853 #1865

### <a name="new"></a>신규
* 이제 Storage 탐색기에 전용 설정 UI가 있습니다. 편집 → 설정에서 액세스 하거나 왼쪽 세로 도구 모음에서 설정 아이콘 (기어)을 클릭 하 여 액세스할 수 있습니다. 이 기능은 다양 한 [사용자 요청 설정을](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)제공 하는 첫 번째 단계입니다. 이 릴리스에서는 다음 설정이 지원 됩니다.
  * 테마
  * Proxy
  * 종료 시 로그 아웃 #6
  * 장치 코드 흐름 로그인 사용
  * 자동 새로 고침 #1526
  * AzCopy 사용
  * AzCopy SAS 기간 추가 된 것으로 표시 하려는 다른 설정이 있는 경우 확인할 설정을 설명 하는 [GitHub에서 문제를 여세요](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) .
* 이제 Storage 탐색기 Managed Disks를 지원 합니다. 다음과 같은 기능이 가능합니다.
  * 새 디스크에 온-프레미스 VHD 업로드
  * 디스크 다운로드
  * 리소스 그룹 및 지역에서 디스크 복사 및 붙여넣기
  * 디스크 삭제
  * 디스크의 스냅숏 만들기 디스크의 업로드, 다운로드 및 지역 간 복사는 AzCopy v10에 의해 구동 됩니다.
* 이제 Linux의 Snap 저장소를 통해 Storage 탐색기를 설치할 수 있습니다. Snap 저장소를 통해 설치 하는 경우 .NET Core를 포함 하 여 모든 종속성이 설치 됩니다. 현재 Storage 탐색기 Ubuntu 및 CentOS에서 제대로 실행 되는지 확인 했습니다. 다른 Linux 배포판의 Snap 저장소에서 설치 하는 동안 문제가 발생 하는 경우 [GitHub에서 문제를 여세요](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Snap 스토어에서 설치 하는 방법에 대한 자세한 내용은 [시작 가이드](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux)를 참조 하세요. #68
* ADLS Gen2 사용자에 게 더 유용한 기능을 제공 하는 Azure Active Directory (Azure AD)와 연결 하기 위한 두 가지 주요 변경 사항이 있습니다.
  * 이제 연결 중인 리소스가 있는 테넌트를 선택 합니다. 즉, 더 이상 리소스의 구독에 대한 RBAC 액세스 권한이 필요 하지 않습니다.
  * ADLS Gen2 Blob 컨테이너를 연결 하는 경우 이제 컨테이너의 특정 경로에 연결할 수 있습니다.
* ADLS Gen2 파일 및 폴더에 대한 Acl을 관리 하는 경우 이제 Storage 탐색기 ACL의 엔터티에 대한 이름을 표시 합니다. #957
* OID를 통해 ADLS Gen2 ACL에 추가 하는 경우 이제 Storage 탐색기는 OID가 테넌트의 유효한 엔터티에 속하는지 유효성을 검사 합니다. #1603
* 탭 간을 이동 하는 바로 가기 키에는 이제 표준 키 조합이 더 많이 사용 됩니다. #1018
* 이제 탭을 마우스 가운데 단추를 클릭 하면 닫힙니다. #1348
* AzCopy 전송에 contains 및 no 오류가 포함 되어 있으면 이제 건너뛰기가 발생 했음을 강조 표시 하는 경고 아이콘을 표시 Storage 탐색기 합니다. #1490
* 통합 AzCopy 버전 10.2.1로 업데이트 되었습니다. 또한 이제 정보 대화 상자에서 설치 된 AzCopy 버전을 확인할 수 있습니다. #1343

### <a name="fixes"></a>수정 프로그램
* 연결 된 저장소 계정으로 작업 하는 경우 많은 사용자가 "정의 되지 않은 버전을 읽을 수 없음" 또는 "정의 되지 않은 연결을 읽을 수 없음" 오류를 여러 사용자가 실행 했습니다. 여전히이 문제의 근본 원인을 조사 하 고 있지만, 1.10.0에서 연결 된 저장소 계정 로드에 대한 오류 처리가 향상 되었습니다. #1626, #985 및 #1532
* 탐색기 트리 (왼쪽)는 포커스가 위쪽 노드로 반복적으로 이동 하는 상태로 전환 될 수 있었습니다. 이 문제가 해결되었습니다. #1596
* Blob의 스냅숏을 관리할 때 없습니다는 스냅숏과 연결 된 타임 스탬프를 읽지 않습니다. 이 문제가 해결되었습니다. #1202
* MacOS의 프록시 설정이 인증 프로세스에서 사용 하도록 설정 되지 않았습니다. 이 문제가 해결되었습니다. #1567
* 소 버린 클라우드의 저장소 계정이 이름 및 키를 사용 하 여 연결 된 경우 AzCopy가 작동 하지 않습니다. 이 문제가 해결되었습니다. #1544
* 연결 문자열을 통해 연결 하는 경우 Storage 탐색기은 이제 후행 공백을 제거 합니다. #1387

### <a name="known-issues"></a>알려진 문제
* 자동 새로 고침 설정은 아직 Blob 탐색기의 모든 작업에 영향을 주지 않습니다.
* 관리 디스크 기능은 Azure Stack에서 지원 되지 않습니다.
* 디스크를 업로드 하거나 붙여 넣을 수 없고 오류 전에 새 디스크가 생성 된 경우에는 Storage 탐색기에서 디스크를 삭제 하지 않습니다.
* 디스크 업로드 또는 붙여넣기를 취소 하는 경우에 따라 새 디스크를 손상 된 상태로 둘 수 있습니다. 이 경우 새 디스크를 삭제 하거나 디스크 Api를 수동으로 호출 하 여 디스크의 내용이 더 이상 손상 되지 않도록 해야 합니다.
* 디스크 업로드 또는 붙여넣기를 취소 하는 경우에 따라 새 디스크를 손상 된 상태로 둘 수 있습니다. 이 경우 새 디스크를 삭제 하거나 디스크 Api를 수동으로 호출 하 여 디스크의 내용이 더 이상 손상 되지 않도록 해야 합니다.
* AzCopy 않는 Blob 다운로드를 수행 하는 경우에는 규모가 많은 파일에 대해 MD5를 확인 하지 않습니다. 이는 저장소 SDK의 버그로 인 한 것입니다. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC를 사용 하는 경우 저장소 리소스에 액세스 하기 위해 Storage 탐색기에 일부 관리 계층 권한이 필요 합니다. 자세한 내용은 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 를 참조 하세요.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
   * ADLS Gen2
   * Managed Disks
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux에서 Storage 탐색기를 실행 하려면 먼저 특정 종속성을 설치 해야 합니다. 자세한 내용은 Storage 탐색기 [문제 해결 가이드를 참조](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) 하세요.


## <a name="version-1100"></a>버전 1.10.0
9/12/2019

### <a name="new"></a>신규

* 이제 Storage 탐색기에 전용 설정 UI가 있습니다. 편집 → 설정에서 액세스 하거나 왼쪽 세로 도구 모음에서 설정 아이콘 (기어)을 클릭 하 여 액세스할 수 있습니다. 이 기능은 다양 한 [사용자 요청 설정을](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)제공 하는 첫 번째 단계입니다. 이 릴리스에서는 다음 설정이 지원 됩니다.
    * 테마
    * Proxy
    * 종료 시 로그 아웃 [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * 장치 코드 흐름 로그인 사용
    * 자동 새로 고침 [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * AzCopy 사용
    * AzCopy SAS 기간

    추가 하려는 다른 설정이 있는 경우 [GitHub에서 보려는 설정을 설명](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=)하는 문제를 여세요.
* 이제 Storage 탐색기 Managed Disks를 지원 합니다. 다음과 같은 기능이 가능합니다.
    * 새 디스크에 온-프레미스 VHD 업로드
    * 디스크 다운로드
    * 리소스 그룹 및 지역에서 디스크 복사 및 붙여넣기
    * 디스크 삭제
    * 디스크의 스냅숏 만들기

    디스크의 업로드, 다운로드 및 지역 간 복사는 AzCopy v10에 의해 구동 됩니다.
* 이제 Linux의 Snap 저장소를 통해 Storage 탐색기를 설치할 수 있습니다. Snap 저장소를 통해 설치 하는 경우 .NET Core를 포함 하 여 모든 종속성이 설치 됩니다. 현재 Storage 탐색기 Ubuntu 및 CentOS에서 제대로 실행 되는지 확인 했습니다. 다른 Linux 배포판의 Snap 저장소에서 설치 하는 동안 문제가 발생 하는 경우 [GitHub에서 문제를 여세요](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Snap 스토어에서 설치 하는 방법에 대한 자세한 내용은 [시작 가이드](https://aka.ms/storageexplorer/snapinformation)를 참조 하세요. [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* ADLS Gen2 사용자에 게 더 유용 하 게 기능을 제공 하기 위해 Azure Active Directory (Azure AD)와 연결 하기 위한 두 가지 주요 변경 사항이 있습니다. * 이제 연결 중인 리소스가 있는 테넌트를 선택 합니다. 즉, 더 이상 리소스의 구독에 대한 RBAC 액세스 권한이 필요 하지 않습니다.
        * ADLS Gen2 Blob 컨테이너를 연결 하는 경우 이제 컨테이너의 특정 경로에 연결할 수 있습니다.
* ADLS Gen2 파일 및 폴더에 대한 Acl을 관리 하는 경우 이제 Storage 탐색기 ACL의 엔터티에 대한 이름을 표시 합니다. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* OID를 통해 ADLS Gen2 ACL에 추가 하는 경우 이제 Storage 탐색기는 OID가 테넌트의 유효한 엔터티에 속하는지 유효성을 검사 합니다. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* 탭 간을 이동 하는 바로 가기 키에는 이제 표준 키 조합이 더 많이 사용 됩니다. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* 이제 탭을 마우스 가운데 단추를 클릭 하면 닫힙니다. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* AzCopy 전송에 contains 및 no 오류가 포함 되어 있으면 이제 건너뛰기가 발생 했음을 강조 표시 하는 경고 아이콘을 표시 Storage 탐색기 합니다. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* 통합 AzCopy 버전 10.2.1로 업데이트 되었습니다. 또한 이제 정보 대화 상자에서 설치 된 AzCopy 버전을 확인할 수 있습니다. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>수정 프로그램

* 연결 된 저장소 계정으로 작업 하는 경우 많은 사용자가 "정의 되지 않은 버전을 읽을 수 없음" 또는 "정의 되지 않은 연결을 읽을 수 없음" 오류를 여러 사용자가 실행 했습니다. 여전히이 문제의 근본 원인을 조사 하 고 있지만, 1.10.0에서 연결 된 저장소 계정 로드에 대한 오류 처리가 향상 되었습니다. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)및 [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* 탐색기 트리 (왼쪽)는 포커스가 위쪽 노드로 반복적으로 이동 하는 상태로 전환 될 수 있었습니다. 이 문제가 해결되었습니다. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Blob의 스냅숏을 관리할 때 없습니다는 스냅숏과 연결 된 타임 스탬프를 읽지 않습니다. 이 문제가 해결되었습니다. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* MacOS의 프록시 설정이 인증 프로세스에서 사용 하도록 설정 되지 않았습니다. 이 문제가 해결되었습니다. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* 소 버린 클라우드의 저장소 계정이 이름 및 키를 사용 하 여 연결 된 경우 AzCopy가 작동 하지 않습니다. 이 문제가 해결되었습니다. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* 연결 문자열을 통해 연결 하는 경우 Storage 탐색기은 이제 후행 공백을 제거 합니다. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>알려진 문제

* 자동 새로 고침 설정은 아직 Blob 탐색기의 모든 작업에 영향을 주지 않습니다.
* 관리 디스크 기능은 Azure Stack에서 지원 되지 않습니다.
* 디스크를 업로드 하거나 붙여 넣을 수 없고 오류 전에 새 디스크가 생성 된 경우에는 Storage 탐색기에서 디스크를 삭제 하지 않습니다.
* 디스크 업로드 또는 붙여넣기를 취소 하는 경우에 따라 새 디스크를 손상 된 상태로 둘 수 있습니다. 이 경우 새 디스크를 삭제 하거나 디스크 Api를 수동으로 호출 하 여 디스크의 내용이 더 이상 손상 되지 않도록 해야 합니다.
* AzCopy 않는 Blob 다운로드를 수행 하는 경우에는 규모가 많은 파일에 대해 MD5를 확인 하지 않습니다. 이는 저장소 SDK의 버그로 인 한 것입니다. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC를 사용 하는 경우 저장소 리소스에 액세스 하기 위해 Storage 탐색기에 일부 관리 계층 권한이 필요 합니다. 자세한 내용은 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 를 참조 하세요.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
   * ADLS Gen2
   * Managed Disks
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux에서 Storage 탐색기를 실행 하려면 먼저 특정 종속성을 설치 해야 합니다. 자세한 내용은 Storage 탐색기 [문제 해결 가이드를 참조](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) 하세요.

## <a name="version-190"></a>버전 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>다운로드 Azure Storage 탐색기 1.9.0
- [Windows 용 Azure Storage 탐색기 1.9.0](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Mac 용 Azure Storage 탐색기 1.9.0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Linux 용 Azure Storage 탐색기 1.9.0](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>신규

* 이제 Azure AD (RBAC 또는 ACL 권한)를 통해 Blob 컨테이너를 연결할 수 있습니다. 이 기능은 컨테이너에 액세스할 수 있지만 컨테이너가 있는 저장소 계정에는 액세스할 수 없는 사용자를 돕기 위한 것입니다. 이 기능에 대한 자세한 내용은 시작 가이드를 참조 하세요.
* 이제 RBAC에서 임대를 획득 하 고 중단 합니다. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* 액세스 정책을 관리 하 고 공용 액세스 수준을 설정 하면 이제 RBAC에서 작동 합니다. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* 이제 blob 폴더 삭제가 RBAC와 함께 작동 합니다. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* 이제 blob 액세스 계층 변경이 RBAC에서 작동 합니다. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* 이제 "도움말" → "다시 설정"을 통해 빠른 액세스를 빠르게 다시 설정할 수 있습니다. [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>미리 보기 기능

* 이제 장치 코드 흐름 로그인을 미리 볼 수 있습니다. 사용 하도록 설정 하려면 "미리 보기" → "장치 코드 흐름 로그인 사용"으로 이동 합니다. 이 기능을 사용 하기 위해 빈 로그인 창에서 문제가 발생 한 모든 사용자에 게 더 안정적인 형태의 로그인을 입증할 수 있는 것이 좋습니다.
* AzCopy와 통합 된 Storage 탐색기는 현재 미리 보기로 제공 됩니다. 이 기능을 사용 하도록 설정 하려면 "미리 보기" → "향상 된 Blob 업로드 및 다운로드를 위해 AzCopy 사용"으로 이동 합니다. AzCopy를 사용 하 여 완료 된 Blob 전송은 속도가 빠르고 성능이 더 우수 합니다.

### <a name="fixes"></a>수정 프로그램

* 한 계정에 대해 50 개 이상의 구독을 로드할 수 없는 문제를 수정 했습니다. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* 직접 링크가 실패할 때 표시 되는 정보 표시줄에서 작동 하지 않는 "로그인" 단추가 수정 되었습니다. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* MacOS에서 앱 파일을 업로드 하는 것이 수정 되지 않았습니다. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* 실패 한 blob 이름 바꾸기에 대해 작동 하지 않는 "모두 다시 시도"가 수정 되었습니다. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Blob을 여는 동안 "취소"가 작동 하지 않는 문제를 수정 했습니다. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* 제품 전체에서 여러 철자 및 도구 설명 문제를 수정 했습니다. 많은 사람들이 이러한 문제를 보고 해 주셔서 감사 합니다. [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>알려진 문제

* AzCopy 않는 Blob 다운로드를 수행 하는 경우에는 규모가 많은 파일에 대해 MD5를 확인 하지 않습니다. 이는 저장소 SDK의 버그로 인 한 것입니다. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC를 사용 하는 경우 저장소 리소스에 액세스 하기 위해 Storage 탐색기에 일부 관리 계층 권한이 필요 합니다. 자세한 내용은 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 를 참조 하세요.
* 프록시 뒤에 있는 ADLS Gen2 Blob에 액세스 하려고 하면 실패할 수 있습니다.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
   * ADLS Gen2
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux에서 Storage 탐색기를 실행 하려면 먼저 특정 종속성을 설치 해야 합니다. 자세한 내용은 Storage 탐색기 [문제 해결 가이드를 참조](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) 하세요.

## <a name="version-181"></a>버전 1.8.1
5/13/2019

### <a name="hotfixes"></a>핫픽스
* 경우에 따라 리소스 수준에서 "더 로드"를 클릭 하면 다음 리소스 페이지가 반환 되지 않습니다. 이 문제가 해결되었습니다. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* Windows에서 단일 파일 또는 폴더를 다운로드 하는 중에 파일 또는 폴더의 이름에 잘못 된 문자가 포함 된 경우 AzCopy 다운로드가 실패 합니다. 이 문제가 해결되었습니다. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* 매우 드문 경우 지만 파일 공유의 이름을 바꾸거나 파일 공유에서 이름을 바꾼 경우, 이름 바꾸기에 대한 복사본이 실패 했거나, 저장소 탐색이 Azure를 사용 하 여 복사본의 성공 여부를 확인할 수 없는 경우에는 o를 삭제할 Storage 탐색기 있습니다. 복사가 완료 되기 전에 파일을 riginal 합니다. 이 문제가 해결되었습니다.

### <a name="new"></a>신규

* Integrated AzCopy 버전이 버전 10.1.0로 업데이트 되었습니다.
* 이제 Ctrl/Cmd + R을 사용 하 여 현재 포커스가 있는 편집기를 새로 고칠 수 있습니다. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API 버전이 2017-04-17로 변경 되었습니다.
* ADLS Gen2에 대한 액세스 관리 대화 상자는 이제 다른 POSIX 권한 도구와 비슷한 방식으로 마스크를 동기화 상태로 유지 합니다. UI는 사용자 또는 그룹의 사용 권한이 마스크의 범위를 초과 하는 경우에도 경고를 표시 합니다. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* AzCopy 업로드의 경우 MD5 해시를 계산 하 고 설정 하는 플래그를 이제 사용할 수 있습니다. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>미리 보기 기능

* 이제 장치 코드 흐름 로그인을 미리 볼 수 있습니다. 사용 하도록 설정 하려면 "미리 보기" → "장치 코드 흐름 로그인 사용"으로 이동 합니다. 이 기능을 사용 하기 위해 빈 로그인 창에서 문제가 발생 한 모든 사용자에 게 더 안정적인 형태의 로그인을 입증할 수 있는 것이 좋습니다.
* AzCopy와 통합 된 Storage 탐색기는 현재 미리 보기로 제공 됩니다. 이 기능을 사용 하도록 설정 하려면 "미리 보기" → "향상 된 Blob 업로드 및 다운로드를 위해 AzCopy 사용"으로 이동 합니다. AzCopy를 사용 하 여 완료 된 Blob 전송은 속도가 빠르고 성능이 더 우수 합니다.

### <a name="fixes"></a>수정 프로그램

* 액세스 정책 대화 상자가 더 이상 만료 되지 않는 저장소 액세스 정책에 대한 만료 날짜를 설정 하지 않습니다. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Sas를 생성할 때 저장 된 액세스 정책이 올바르게 사용 되는지 확인 하기 위해 SAS 생성 대화 상자에 몇 가지 변경 내용이 적용 되었습니다. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* 512이 아닌 바이트 정렬 파일을 페이지 Blob에 업로드 하려고 할 때 Storage 탐색기 이제 더 적절 한 오류를 표시 합니다. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* 표시 이름을 사용 하는 Blob 컨테이너를 복사 하면 오류가 발생 합니다. 이제 Blob 컨테이너의 실제 이름이 사용 됩니다. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* 이름에 유니코드 문자가 있는 ADLS Gen2 폴더에서 특정 작업을 수행 하는 데 실패 합니다. 이제 모든 작업이 작동 합니다. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>알려진 문제

* AzCopy 않는 Blob 다운로드를 수행 하는 경우에는 규모가 많은 파일에 대해 MD5를 확인 하지 않습니다. 이는 저장소 SDK의 버그로 인 한 것입니다. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC를 사용 하는 경우 저장소 리소스에 액세스 하기 위해 Storage 탐색기에 일부 관리 계층 권한이 필요 합니다. 자세한 내용은 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 를 참조 하세요.
* 프록시 뒤에 있는 ADLS Gen2 Blob에 액세스 하려고 하면 실패할 수 있습니다.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
   * ADLS Gen2
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux에서 Storage 탐색기를 실행 하려면 먼저 특정 종속성을 설치 해야 합니다. 자세한 내용은 Storage 탐색기 [문제 해결 가이드를 참조](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) 하세요.

## <a name="version-180"></a>버전 1.8.0
5/1/2019

### <a name="new"></a>신규

* Integrated AzCopy 버전이 버전 10.1.0로 업데이트 되었습니다.
* 이제 Ctrl/Cmd + R을 사용 하 여 현재 포커스가 있는 편집기를 새로 고칠 수 있습니다. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API 버전이 2017-04-17로 변경 되었습니다.
* ADLS Gen2에 대한 액세스 관리 대화 상자는 이제 다른 POSIX 권한 도구와 비슷한 방식으로 마스크를 동기화 상태로 유지 합니다. UI는 사용자 또는 그룹의 사용 권한이 마스크의 범위를 초과 하는 경우에도 경고를 표시 합니다. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* AzCopy 업로드의 경우 MD5 해시를 계산 하 고 설정 하는 플래그를 이제 사용할 수 있습니다. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>미리 보기 기능

* 이제 장치 코드 흐름 로그인을 미리 볼 수 있습니다. 사용 하도록 설정 하려면 "미리 보기" → "장치 코드 흐름 로그인 사용"으로 이동 합니다. 이 기능을 사용 하기 위해 빈 로그인 창에서 문제가 발생 한 모든 사용자에 게 더 안정적인 형태의 로그인을 입증할 수 있는 것이 좋습니다.
* AzCopy와 통합 된 Storage 탐색기는 현재 미리 보기로 제공 됩니다. 이 기능을 사용 하도록 설정 하려면 "미리 보기" → "향상 된 Blob 업로드 및 다운로드를 위해 AzCopy 사용"으로 이동 합니다. AzCopy를 사용 하 여 완료 된 Blob 전송은 속도가 빠르고 성능이 더 우수 합니다.

### <a name="fixes"></a>수정 프로그램

* 액세스 정책 대화 상자가 더 이상 만료 되지 않는 저장소 액세스 정책에 대한 만료 날짜를 설정 하지 않습니다. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Sas를 생성할 때 저장 된 액세스 정책이 올바르게 사용 되는지 확인 하기 위해 SAS 생성 대화 상자에 몇 가지 변경 내용이 적용 되었습니다. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* 512이 아닌 바이트 정렬 파일을 페이지 Blob에 업로드 하려고 할 때 Storage 탐색기 이제 더 적절 한 오류를 표시 합니다. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* 표시 이름을 사용 하는 Blob 컨테이너를 복사 하면 오류가 발생 합니다. 이제 Blob 컨테이너의 실제 이름이 사용 됩니다. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* 이름에 유니코드 문자가 있는 ADLS Gen2 폴더에서 특정 작업을 수행 하는 데 실패 합니다. 이제 모든 작업이 작동 합니다. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>알려진 문제

* AzCopy 않는 Blob 다운로드를 수행 하는 경우에는 규모가 많은 파일에 대해 MD5를 확인 하지 않습니다. 이는 저장소 SDK의 버그로 인 한 것입니다. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* RBAC를 사용 하는 경우 저장소 리소스에 액세스 하기 위해 Storage 탐색기에 일부 관리 계층 권한이 필요 합니다. 자세한 내용은 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 를 참조 하세요.
* 프록시 뒤에 있는 ADLS Gen2 Blob에 액세스 하려고 하면 실패할 수 있습니다.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
   * ADLS Gen2
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux에서 Storage 탐색기를 실행 하려면 먼저 특정 종속성을 설치 해야 합니다. 자세한 내용은 Storage 탐색기 [문제 해결 가이드를 참조](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) 하세요.

## <a name="version-170"></a>1\.7.0 버전
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>다운로드 Azure Storage 탐색기 1.7.0
- [Windows 용 Azure Storage 탐색기 1.7.0](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Mac 용 Azure Storage 탐색기 1.7.0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Linux 용 Azure Storage 탐색기 1.7.0](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>신규

* 이제 ADLS Gen2 컨테이너, 파일 또는 폴더에 대한 액세스를 관리할 때 소유자 및 소유 그룹을 변경할 수 있습니다.
* Windows에서는 제품 내에서 Storage 탐색기를 업데이트 하는 것이 이제 증분 설치입니다. 이로 인해 업데이트 환경이 더 빨라집니다. 새로 설치 하려는 경우 설치 [관리자](https://azure.microsoft.com/features/storage-explorer/) 를 직접 다운로드 한 다음 수동으로 설치할 수 있습니다. #1089

### <a name="preview-features"></a>미리 보기 기능

* 이제 장치 코드 흐름 로그인을 미리 볼 수 있습니다. 사용 하도록 설정 하려면 "미리 보기" → "장치 코드 흐름 로그인 사용"으로 이동 합니다. 이 기능을 사용 하기 위해 빈 로그인 창에서 문제가 발생 한 모든 사용자에 게 더 안정적인 형태의 로그인을 입증할 수 있는 것이 좋습니다. #938
* AzCopy와 통합 된 Storage 탐색기는 현재 미리 보기로 제공 됩니다. 이 기능을 사용 하도록 설정 하려면 "미리 보기" → "향상 된 Blob 업로드 및 다운로드를 위해 AzCopy 사용"으로 이동 합니다. AzCopy를 사용 하 여 완료 된 Blob 전송은 속도가 빠르고 성능이 더 우수 합니다.

### <a name="fixes"></a>수정 프로그램

* 이제 AzCopy가 사용 하도록 설정 된 경우에는 업로드 하려는 blob 유형을 선택할 수 있습니다. #1111
* 이전에는 ADLS Gen2 저장소 계정에 대한 정적 웹 사이트를 사용 하도록 설정 하 고 이름 및 키를 사용 하 여 연결 했다면 계층적 네임 스페이스를 사용 하는 것이 감지 되지 않았습니다 Storage 탐색기. 이 문제가 해결되었습니다. #1081
* Blob 편집기에서 남은 보존 일 수 또는 상태를 기준으로 정렬 하는 중입니다. 이 문제가 해결되었습니다. #1106
* 1\.5.0 후에는 이름 바꾸기 또는 복사 & 붙여넣기 중에 성공 여부를 보고 하기 전에 Storage 탐색기 서버 쪽 복사본이 완료 될 때까지 더 이상 기다리지 않습니다. 이 문제가 해결되었습니다. #976
* 실험적 AzCopy 기능을 사용 하는 경우 "명령 클립보드로 복사"를 클릭 한 후 복사 된 명령은 항상 자체적으로 실행할 수 없습니다. 이제는 전송을 수동으로 실행 하는 데 필요한 모든 명령이 복사 됩니다. #1079
* 이전에는 프록시 뒤에 있는 경우 ADLS Gen2 blob에 액세스할 수 없습니다. 이는 저장소 SDK에서 사용 하는 새 네트워킹 라이브러리의 버그로 인 한 것입니다. 1\.7.0에서는이 문제를 완화 하려고 했지만 일부 사용자가 계속 해 서 문제를 볼 수 있습니다. 전체 픽스는 향후 업데이트에서 출시 될 예정입니다. #1090
* 1\.7.0에서 파일 저장 대화 상자는 이제 파일을 저장 한 마지막 위치를 정확 하 게 기억 합니다. #16
* 속성 패널에서 저장소 계정의 SKU 계층이 계정 종류로 표시 되었습니다. 이 문제가 해결되었습니다. #654
* Blob의 이름을 올바르게 입력 했더라도 blob의 임대를 중단 하는 것이 불가능 한 경우도 있습니다. 이 문제가 해결되었습니다. #1070

### <a name="known-issues"></a>알려진 문제

* RBAC를 사용 하는 경우 저장소 리소스에 액세스 하기 위해 Storage 탐색기에 일부 관리 계층 권한이 필요 합니다. 자세한 내용은 [문제 해결 가이드](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) 를 참조 하세요.
* 프록시 뒤에 있는 ADLS Gen2 Blob에 액세스 하려고 하면 실패할 수 있습니다.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>버전 1.6.2
1/9/2019

### <a name="hotfixes"></a>핫픽스
* 1\.6.1에서 사용자가 아닌 ObjectId에 따라 ADLS Gen2 ACL에 추가된 엔터티는 항상 그룹으로 추가되었습니다. 이제, 그룹만 그룹으로 추가되고, 엔터프라이즈 애플리케이션 및 서비스 주체와 같은 엔터티는 사용자로 추가됩니다. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* ADLS Gen2 스토리지 계정에 컨테이너가 없고 이름 및 키를 사용하여 연결된 경우 Storage Explorer는 스토리지 계정이 ADLS Gen2임을 감지하지 못합니다. 이 문제가 해결되었습니다. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* 1\.6.0에서는 복사 및 붙여넣기 동안 발생한 충돌을 해결할 필요가 없습니다. 대신, 충돌한 복사가 단순히 실패합니다. 이제 첫 번째 충돌 시 해결 방법을 묻는 메시지가 표시됩니다. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* API 제한으로 인해 액세스 관리 대화 상자의 모든 ObjectId 유효성 검사를 사용할 수 없습니다. 이제 사용자 UPN에 대해서만 유효성 검사가 수행됩니다. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* ADLS Gen2 액세스 관리 대화 상자에서 그룹에 대한 권한을 수정할 수 없습니다. 이 문제가 해결되었습니다. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* 끌어서 놓기 업로드 지원이 ADLS Gen2 편집기에 추가되었습니다. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* ADLS Gen2 파일 및 폴더의 속성 대화 상자에 포함된 URL 속성에서 가끔 '/'가 누락되었습니다. 이 문제가 해결되었습니다. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* ADLS Gen2 컨테이너, 파일 또는 폴더의 현재 사용 권한을 가져오지 못할 경우 이제 활동 로그에 오류가 제대로 표시됩니다. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Windows에서 MAX_PATH보다 긴 경로 만들 가능성을 줄이기 위해 파일을 열 때 만드는 임시 경로를 단축했습니다. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* 이제 로그인한 사용자가 없고 연결된 리소스가 없는 경우 연결 대화 상자가 올바르게 표시됩니다. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 1\.6.0에서는 비 HNS Blob 및 파일의 속성을 저장하면 모든 속성의 값이 인코딩되었습니다. 이로 인해 ASCII 문자만 포함하는 값이 불필요하게 인코딩되었습니다. 이제 비 ASCII 문자를 포함하는 값만 인코딩됩니다. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* SAS가 사용되고 SAS에 읽기 권한이 없으면 HNS가 아닌 Blob 컨테이너에 폴더를 업로드하지 못했습니다. 이 문제가 해결되었습니다. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* AzCopy 전송을 취소할 수 없었습니다. 이 문제가 해결되었습니다. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* 폴더 이름에 공백이 있을 때 ADLS Gen2 Blob 컨테이너에서 폴더를 다운로드하려고 하면 AzCopy가 실패합니다. 이 문제가 해결되었습니다. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* 1\.6.0에서는 CosmosDB 편집기가 제대로 작동되지 않았습니다. 이제 이 문제가 해결되었습니다. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>신규

* 이제 Storage Explorer를 사용하여 [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)를 통해 Blob 데이터에 액세스할 수 있습니다. 로그인했으며 Storage Explorer가 사용자의 Storage 계정용 키를 검색할 수 없으면 데이터와 상호 작용하면서 인증 작업에 OAuth 토큰이 사용됩니다.
* Storage Explorer는 이제 ADLS Gen2 Storage 계정을 지원합니다. Storage Explorer가 Storage 계정에 대해 계층적 네임스페이스가 사용하도록 설정되어 있음을 감지하면 Storage 계정 이름 옆에 "(ADLS Gen2 미리 보기)"가 표시됩니다. Storage Explorer는 사용자가 로그인될 때 또는 Storage 계정에 이름과 키를 첨부한 경우 계층 구조 네임스페이스가 사용하도록 설정되는지 여부를 검색할 수 있습니다. ADLS Gen2 Storage 계정의 경우 Storage Explorer를 사용하여 다음 작업을 수행할 수 있습니다.
  * 컨테이너 만들기 및 삭제
  * 컨테이너 속성 및 사용 권한 관리(왼쪽)
  * 컨테이너 내 데이터 보기 및 탐색
  * 새 폴더 만들기
  * 파일 및 폴더 업로드, 다운로드, 이름 바꾸기 및 삭제
  * 파일 및 폴더 속성과 사용 권한 관리(오른쪽)
    
    일시 삭제, 스냅샷 등의 기타 일반적인 Blob 기능은 현재 사용할 수 없습니다. 권한 관리는 로그인한 경우에만 사용할 수 있습니다. 또한 ADLS Gen2 Storage 계정에서 작업하는 경우 Storage Explorer는 모든 업로드 및 다운로드에 AzCopy를 사용하고, 사용 가능한 경우 모든 작업에 기본적으로 이름 및 키 자격 증명을 사용합니다.
* 강력한 사용자 의견이 제공된 후에는 임대 차단을 한 번 더 사용하여 여러 Blob의 임대를 한꺼번에 차단할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

* ADLS Gen2 Storage 계정에서 다운로드하는 경우 전송 중인 파일 중 하나라도 이미 있는 경우 AzCopy가 때때로 충돌합니다. 이 문제는 향후 핫픽스에서 수정될 예정입니다.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>버전 1.6.1
12/18/2018

### <a name="hotfixes"></a>핫픽스
* API 제한으로 인해 액세스 관리 대화 상자의 모든 ObjectId 유효성 검사를 사용할 수 없습니다. 이제 사용자 UPN에 대해서만 유효성 검사가 수행됩니다. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* ADLS Gen2 액세스 관리 대화 상자에서 그룹에 대한 권한을 수정할 수 없습니다. 이 문제가 해결되었습니다. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* 끌어서 놓기 업로드 지원이 ADLS Gen2 편집기에 추가되었습니다. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* ADLS Gen2 파일 및 폴더의 속성 대화 상자에 포함된 URL 속성에서 가끔 '/'가 누락되었습니다. 이 문제가 해결되었습니다. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* ADLS Gen2 컨테이너, 파일 또는 폴더의 현재 사용 권한을 가져오지 못할 경우 이제 활동 로그에 오류가 제대로 표시됩니다. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Windows에서 MAX_PATH보다 긴 경로 만들 가능성을 줄이기 위해 파일을 열 때 만드는 임시 경로를 단축했습니다. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* 이제 로그인한 사용자가 없고 연결된 리소스가 없는 경우 연결 대화 상자가 올바르게 표시됩니다. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 1\.6.0에서는 비 HNS Blob 및 파일의 속성을 저장하면 모든 속성의 값이 인코딩되었습니다. 이로 인해 ASCII 문자만 포함하는 값이 불필요하게 인코딩되었습니다. 이제 비 ASCII 문자를 포함하는 값만 인코딩됩니다. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* SAS가 사용되고 SAS에 읽기 권한이 없으면 HNS가 아닌 Blob 컨테이너에 폴더를 업로드하지 못했습니다. 이 문제가 해결되었습니다. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* AzCopy 전송을 취소할 수 없었습니다. 이 문제가 해결되었습니다. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* 폴더 이름에 공백이 있을 때 ADLS Gen2 Blob 컨테이너에서 폴더를 다운로드하려고 하면 AzCopy가 실패합니다. 이 문제가 해결되었습니다. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* 1\.6.0에서는 CosmosDB 편집기가 제대로 작동되지 않았습니다. 이제 이 문제가 해결되었습니다. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>신규

* 이제 Storage Explorer를 사용하여 [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)를 통해 Blob 데이터에 액세스할 수 있습니다. 로그인했으며 Storage Explorer가 사용자의 Storage 계정용 키를 검색할 수 없으면 데이터와 상호 작용하면서 인증 작업에 OAuth 토큰이 사용됩니다.
* Storage Explorer는 이제 ADLS Gen2 Storage 계정을 지원합니다. Storage Explorer가 Storage 계정에 대해 계층적 네임스페이스가 사용하도록 설정되어 있음을 감지하면 Storage 계정 이름 옆에 "(ADLS Gen2 미리 보기)"가 표시됩니다. Storage Explorer는 사용자가 로그인될 때 또는 Storage 계정에 이름과 키를 첨부한 경우 계층 구조 네임스페이스가 사용하도록 설정되는지 여부를 검색할 수 있습니다. ADLS Gen2 Storage 계정의 경우 Storage Explorer를 사용하여 다음 작업을 수행할 수 있습니다.
  * 컨테이너 만들기 및 삭제
  * 컨테이너 속성 및 사용 권한 관리(왼쪽)
  * 컨테이너 내 데이터 보기 및 탐색
  * 새 폴더 만들기
  * 파일 및 폴더 업로드, 다운로드, 이름 바꾸기 및 삭제
  * 파일 및 폴더 속성과 사용 권한 관리(오른쪽)
    
    일시 삭제, 스냅샷 등의 기타 일반적인 Blob 기능은 현재 사용할 수 없습니다. 권한 관리는 로그인한 경우에만 사용할 수 있습니다. 또한 ADLS Gen2 Storage 계정에서 작업하는 경우 Storage Explorer는 모든 업로드 및 다운로드에 AzCopy를 사용하고, 사용 가능한 경우 모든 작업에 기본적으로 이름 및 키 자격 증명을 사용합니다.
* 강력한 사용자 의견이 제공된 후에는 임대 차단을 한 번 더 사용하여 여러 Blob의 임대를 한꺼번에 차단할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

* ADLS Gen2 Storage 계정에서 다운로드하는 경우 전송 중인 파일 중 하나라도 이미 있는 경우 AzCopy가 때때로 충돌합니다. 이 문제는 향후 핫픽스에서 수정될 예정입니다.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>버전 1.6.0
2018/12/5

### <a name="new"></a>신규

* 이제 Storage Explorer를 사용하여 [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409)를 통해 Blob 데이터에 액세스할 수 있습니다. 로그인했으며 Storage Explorer가 사용자의 Storage 계정용 키를 검색할 수 없으면 데이터와 상호 작용하면서 인증 작업에 OAuth 토큰이 사용됩니다.
* Storage Explorer는 이제 ADLS Gen2 Storage 계정을 지원합니다. Storage Explorer가 Storage 계정에 대해 계층적 네임스페이스가 사용하도록 설정되어 있음을 감지하면 Storage 계정 이름 옆에 "(ADLS Gen2 미리 보기)"가 표시됩니다. Storage Explorer는 사용자가 로그인될 때 또는 Storage 계정에 이름과 키를 첨부한 경우 계층 구조 네임스페이스가 사용하도록 설정되는지 여부를 검색할 수 있습니다. ADLS Gen2 Storage 계정의 경우 Storage Explorer를 사용하여 다음 작업을 수행할 수 있습니다.
  * 컨테이너 만들기 및 삭제
  * 컨테이너 속성 및 사용 권한 관리(왼쪽)
  * 컨테이너 내 데이터 보기 및 탐색
  * 새 폴더 만들기
  * 파일 및 폴더 업로드, 다운로드, 이름 바꾸기 및 삭제
  * 파일 및 폴더 속성과 사용 권한 관리(오른쪽)
    
    일시 삭제, 스냅샷 등의 기타 일반적인 Blob 기능은 현재 사용할 수 없습니다. 권한 관리는 로그인한 경우에만 사용할 수 있습니다. 또한 ADLS Gen2 Storage 계정에서 작업하는 경우 Storage Explorer는 모든 업로드 및 다운로드에 AzCopy를 사용하고, 사용 가능한 경우 모든 작업에 기본적으로 이름 및 키 자격 증명을 사용합니다.
* 강력한 사용자 의견이 제공된 후에는 임대 차단을 한 번 더 사용하여 여러 Blob의 임대를 한꺼번에 차단할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

* ADLS Gen2 Storage 계정에서 다운로드하는 경우 전송 중인 파일 중 하나라도 이미 있는 경우 AzCopy가 때때로 충돌합니다. 이 문제는 향후 핫픽스에서 수정될 예정입니다.
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>버전 1.5.0
2018/10/29

### <a name="new"></a>신규

* 이제 [AzCopy v10(미리 보기)](https://github.com/Azure/azure-storage-azcopy)를 사용하여 Blob을 업로드하고 다운로드할 수 있습니다. 이 기능을 사용하도록 설정하려면 "실험적" 메뉴로 이동한 다음, "향상된 Blob 업로드 및 다운로드를 위해 AzCopy 사용"을 클릭합니다. 사용하도록 설정하면 AzCopy는 다음 시나리오에서 사용됩니다.
   * 도구 모음 또는 끌어서 놓기를 통해 폴더 및 파일을 Blob 컨테이너에 업로드합니다.
   * 도구 모음 또는 팝업 메뉴를 통해 폴더 및 파일을 다운로드합니다.

* 또한 AzCopy를 사용하는 경우:
   * 클립보드에 대한 전송을 실행하는 데 사용된 AzCopy 명령을 복사할 수 있습니다. 활동 로그에서 "AzCopy 명령을 클립보드로 복사"를 클릭하면 됩니다.
   * 업로드한 후에 Blob 편집기를 수동으로 새로 고쳐야 합니다.
   * blob을 추가하기 위해 파일을 업로드하도록 지원되지 않으며 vhd 파일은 페이지 Blob으로 업로드되고 다른 모든 파일은 블록 Blob으로 업로드됩니다.
   * 업로드 또는 다운로드하는 동안 발생하는 오류 및 충돌은 업로드 또는 다운로드가 완료될 때까지 표시되지 않습니다.

마지막으로, 파일 공유에서 AzCopy를 사용하는 지원은 향후 제공될 예정입니다.
* 이제 Storage Explorer는 Electron 2.0.11 버전을 사용합니다.
* 임대를 해제하는 작업은 이제 한 번에 하나의 Blob에서만 수행될 수 있습니다. 또한 중단한 임대의 Blob 이름을 입력해야 합니다. 특히 VM의 경우 실수로 임대를 중단할 가능성을 줄이려고 이렇게 변경했습니다. #394
* 로그인 문제가 발생하면 이제 인증을 다시 설정하도록 시도할 수 있습니다. "도움말" 메뉴로 이동하고 "다시 설정"을 클릭하여 이 기능에 액세스합니다. #419

### <a name="fix"></a>해결

* 사용자 피드백을 통해 기본 에뮬레이터 노드가 다시 사용하도록 설정되었습니다. 연결 대화 상자를 통해 추가 에뮬레이터 연결을 계속 추가할 수 있습니다. 하지만 에뮬레이터가 기본 포트를 사용하도록 구성된 경우 &quot;로컬 및 연결된/스토리지 계정&quot;에서 &quot;에뮬레이터 * 기본 포트&quot; 노드를 사용할 수도 있습니다. #669
* Storage Explorer를 통해 더 이상 선행 또는 후행 공백이 있는 Blob 메타데이터 값을 설정할 수 없습니다. #760
* 연결 대화 상자의 동일한 페이지에서 언제든지 "로그인" 단추를 사용하도록 설정했습니다. 이제 적절한 경우 사용하지 않도록 설정합니다. #761
* 빠른 액세스 항목이 추가되지 않으면 빠른 액세스는 더 이상 콘솔에서 오류를 발생시키지 않습니다.

### <a name="known-issues"></a>알려진 문제

* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 #537을 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 이 문제에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다. Blob 컨테이너 간에 업로드하거나 다운로드하는 경우 이 문제를 해결하기 위해 경우 실험적 AzCopy 기능을 사용할 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack에서는 다음과 같은 기능을 지원하지 않습니다. Azure Stack 리소스를 사용하는 동안 이러한 기능을 사용하려고 시도하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>버전 1.4.4
2018년 10월 15일

### <a name="hotfixes"></a>핫픽스
* Azure US Government 사용자의 차단을 해제하기 위해 Azure Resource Management API 버전이 롤백되었습니다. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* 회전자가 로드되면 이제 CSS 애니메이션을 사용하여 Storage Explorer에서 사용하는 GPU 양을 줄일 수 있습니다. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>신규
* SAS 연결 및 에뮬레이터와 같은 외부 리소스 연결이 크게 향상되었습니다. 이제 다음을 수행할 수 있습니다.
   * 연결하는 리소스의 표시 이름을 사용자 지정합니다. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 다른 포트를 사용하여 여러 로컬 에뮬레이터에 연결합니다. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 연결된 리소스를 빠른 액세스에 추가합니다. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 이제 Storage Explorer에서 일시 삭제를 지원합니다. 다음과 같은 기능이 가능합니다.
   * Storage 계정에 대한 Blob 컨테이너 노드를 마우스 오른쪽 단추로 클릭하여 일시 삭제 정책을 구성합니다.
   * 탐색 모음 옆에 있는 드롭다운에서 "활성 및 삭제된 Blob"을 선택하여 Blob 편집기에서 일시 삭제된 Blob을 봅니다.
   * 일시 삭제된 Blob을 삭제 취소합니다.

### <a name="fixes"></a>수정 프로그램
* Premium Storage 계정이 CORS를 지원하지 않으므로 "CORS 설정 구성" 작업은 Premium Storage 계정에서 더 이상 사용할 수 없습니다. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* 이제 SAS 연결 서비스에 대한 공유 액세스 서명 속성이 있습니다. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* &quot;기본 액세스 계층 설정&quot; 작업은 이제 빠른 액세스에 고정된 Blob 및 GPV2 스토리지 계정에 대해 사용할 수 있습니다. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 경우에 따라 Storage Explorer에서 클래식 스토리지 계정을 표시하지 못할 수도 있습니다. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>알려진 문제
* Azure Storage 에뮬레이터 또는 Azurite와 같은 에뮬레이터를 사용하는 경우 해당 기본 포트에 대한 연결을 수신하도록 해야 합니다. 그렇지 않으면 Storage Explorer에서 연결할 수 없습니다.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/97)에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>버전 1.4.3
2018년 10월 11일

### <a name="hotfixes"></a>핫픽스
* Azure US Government 사용자의 차단을 해제하기 위해 Azure Resource Management API 버전이 롤백되었습니다. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* 회전자가 로드되면 이제 CSS 애니메이션을 사용하여 Storage Explorer에서 사용하는 GPU 양을 줄일 수 있습니다. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>신규
* SAS 연결 및 에뮬레이터와 같은 외부 리소스 연결이 크게 향상되었습니다. 이제 다음을 수행할 수 있습니다.
   * 연결하는 리소스의 표시 이름을 사용자 지정합니다. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 다른 포트를 사용하여 여러 로컬 에뮬레이터에 연결합니다. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 연결된 리소스를 빠른 액세스에 추가합니다. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 이제 Storage Explorer에서 일시 삭제를 지원합니다. 다음과 같은 기능이 가능합니다.
   * Storage 계정에 대한 Blob 컨테이너 노드를 마우스 오른쪽 단추로 클릭하여 일시 삭제 정책을 구성합니다.
   * 탐색 모음 옆에 있는 드롭다운에서 "활성 및 삭제된 Blob"을 선택하여 Blob 편집기에서 일시 삭제된 Blob을 봅니다.
   * 일시 삭제된 Blob을 삭제 취소합니다.

### <a name="fixes"></a>수정 프로그램
* Premium Storage 계정이 CORS를 지원하지 않으므로 "CORS 설정 구성" 작업은 Premium Storage 계정에서 더 이상 사용할 수 없습니다. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* 이제 SAS 연결 서비스에 대한 공유 액세스 서명 속성이 있습니다. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* &quot;기본 액세스 계층 설정&quot; 작업은 이제 빠른 액세스에 고정된 Blob 및 GPV2 스토리지 계정에 대해 사용할 수 있습니다. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 경우에 따라 Storage Explorer에서 클래식 스토리지 계정을 표시하지 못할 수도 있습니다. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>알려진 문제
* Azure Storage 에뮬레이터 또는 Azurite와 같은 에뮬레이터를 사용하는 경우 해당 기본 포트에 대한 연결을 수신하도록 해야 합니다. 그렇지 않으면 Storage Explorer에서 연결할 수 없습니다.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/97)에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>버전 1.4.2
2018년 9월 24일

### <a name="hotfixes"></a>핫픽스
* 새 Azure Storage 계정 종류에 대한 지원을 추가하기 위해 Azure Resource Management API 버전이 2018-07-01로 업데이트되었습니다. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>신규
* SAS 연결 및 에뮬레이터와 같은 외부 리소스 연결이 크게 향상되었습니다. 이제 다음을 수행할 수 있습니다.
   * 연결하는 리소스의 표시 이름을 사용자 지정합니다. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 다른 포트를 사용하여 여러 로컬 에뮬레이터에 연결합니다. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 연결된 리소스를 빠른 액세스에 추가합니다. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 이제 Storage Explorer에서 일시 삭제를 지원합니다. 다음과 같은 기능이 가능합니다.
   * Storage 계정에 대한 Blob 컨테이너 노드를 마우스 오른쪽 단추로 클릭하여 일시 삭제 정책을 구성합니다.
   * 탐색 모음 옆에 있는 드롭다운에서 "활성 및 삭제된 Blob"을 선택하여 Blob 편집기에서 일시 삭제된 Blob을 봅니다.
   * 일시 삭제된 Blob을 삭제 취소합니다.

### <a name="fixes"></a>수정 프로그램
* Premium Storage 계정이 CORS를 지원하지 않으므로 "CORS 설정 구성" 작업은 Premium Storage 계정에서 더 이상 사용할 수 없습니다. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* 이제 SAS 연결 서비스에 대한 공유 액세스 서명 속성이 있습니다. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* &quot;기본 액세스 계층 설정&quot; 작업은 이제 빠른 액세스에 고정된 Blob 및 GPV2 스토리지 계정에 대해 사용할 수 있습니다. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 경우에 따라 Storage Explorer에서 클래식 스토리지 계정을 표시하지 못할 수도 있습니다. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>알려진 문제
* Azure Storage 에뮬레이터 또는 Azurite와 같은 에뮬레이터를 사용하는 경우 해당 기본 포트에 대한 연결을 수신하도록 해야 합니다. 그렇지 않으면 Storage Explorer에서 연결할 수 없습니다.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/97)에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>버전 1.4.1
2018년 08월 28일

### <a name="hotfixes"></a>핫픽스
* Storage Explorer는 처음 시작할 때 중요한 데이터를 암호화하는 데 사용되는 키를 생성할 수 없었습니다. 이로 인해 빠른 액세스를 사용하고 리소스를 연결할 때 문제가 발생할 수 있습니다. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* 계정에서 홈 테넌트에 대해 MFA를 요구하지 않았지만 다른 일부 테넌트에 대해서는 MFA를 요구한 경우 Storage Explorer는 구독을 나열할 수 없습니다. 이제 이러한 계정으로 로그인하면 Storage Explorer에서 자격 증명을 다시 입력하고 MFA를 수행하도록 요청합니다. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer에서 Azure Germany 및 Azure US Government의 리소스를 연결할 수 없습니다. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* 이메일 주소가 동일한 두 개의 계정에 로그인하면 경우에 따라 Storage Explorer에서 리소스를 트리 보기에 표시하지 못할 수도 있습니다. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* 느린 Windows 머신에서 경우에 따라 시작 화면이 표시되는 데 상당한 시간이 걸릴 수 있습니다. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* 연결된 계정 또는 서비스가 있는 경우에도 연결 대화 상자가 표시됩니다. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>신규
* SAS 연결 및 에뮬레이터와 같은 외부 리소스 연결이 크게 향상되었습니다. 이제 다음을 수행할 수 있습니다.
   * 연결하는 리소스의 표시 이름을 사용자 지정합니다. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 다른 포트를 사용하여 여러 로컬 에뮬레이터에 연결합니다. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 연결된 리소스를 빠른 액세스에 추가합니다. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 이제 Storage Explorer에서 일시 삭제를 지원합니다. 다음과 같은 기능이 가능합니다.
   * Storage 계정에 대한 Blob 컨테이너 노드를 마우스 오른쪽 단추로 클릭하여 일시 삭제 정책을 구성합니다.
   * 탐색 모음 옆에 있는 드롭다운에서 "활성 및 삭제된 Blob"을 선택하여 Blob 편집기에서 일시 삭제된 Blob을 봅니다.
   * 일시 삭제된 Blob을 삭제 취소합니다.

### <a name="fixes"></a>수정 프로그램
* Premium Storage 계정이 CORS를 지원하지 않으므로 "CORS 설정 구성" 작업은 Premium Storage 계정에서 더 이상 사용할 수 없습니다. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* 이제 SAS 연결 서비스에 대한 공유 액세스 서명 속성이 있습니다. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* &quot;기본 액세스 계층 설정&quot; 작업은 이제 빠른 액세스에 고정된 Blob 및 GPV2 스토리지 계정에 대해 사용할 수 있습니다. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 경우에 따라 Storage Explorer에서 클래식 스토리지 계정을 표시하지 못할 수도 있습니다. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>알려진 문제
* Azure Storage 에뮬레이터 또는 Azurite와 같은 에뮬레이터를 사용하는 경우 해당 기본 포트에 대한 연결을 수신하도록 해야 합니다. 그렇지 않으면 Storage Explorer에서 연결할 수 없습니다.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/97)에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>버전 1.3.0
07/09/2018

### <a name="new"></a>신규
* 정적 웹 사이트에서 사용하는 $web 컨테이너 액세스가 현재 지원됩니다. 이제 웹 사이트에서 사용되는 파일 및 폴더를 손쉽게 업로드하고 관리할 수 있습니다. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* macOS에서 앱 표시줄이 재구성되었습니다. 변경 내용에는 파일 메뉴, 일부 바로 가기 키 변경 및 앱 메뉴의 몇 가지 새로운 명령이 포함됩니다. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Azure US Government에 로그인하기 위한 인증 기관 엔드포인트가 https://login.microsoftonline.us/ 로 변경되었습니다.
* 접근성: 화면 판독기가 활성 상태인 경우 키보드 탐색이 이제 오른쪽에 항목을 표시하는 데 사용되는 테이블과 함께 작동합니다. 화살표 키를 사용하여 행과 열을 탐색하고, Enter 키를 사용하여 기본 작업 항목을 호출하고, 바로 가기 메뉴 키를 사용하여 항목에 대한 바로 가기 메뉴를 열고, Shift 키 또는 컨트롤을 사용하여 다중 선택할 수 있습니다. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>수정 프로그램
*  일부 머신에서 자식 프로세스를 시작하려면 오랜 시간이 소요되었습니다. 이 현상이 발생하는 경우 “자식 프로세스가 적시에 시작하지 못함” 오류가 나타납니다. 자식 프로세스를 시작하는 데 할당된 시간이 이제 20초에서 90초로 증가되었습니다. 이 문제가 아직 해결되지 않은 경우 연결된 GitHub 문제에 의견을 남겨주세요. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* 읽기 권한이 없는 SAS를 사용하는 경우 큰 blob을 업로드할 수 없었습니다. 업로드에 대한 논리가 이 시나리오에서 작동하도록 수정되었습니다. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* 컨테이너에 대한 공용 액세스 수준 설정에서 모든 액세스 정책을 제거하고, 그 반대의 경우도 마찬가지입니다. 이제 공용 액세스 수준 및 액세스 정책은 둘 중 하나를 설정하는 경우 유지됩니다. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* 속성 대화 상자에서 “AccessTierChangeTime”이 잘렸습니다. 이 문제가 해결되었습니다. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* "Microsoft Azure Storage Explorer -" 접두사가 새 디렉터리 만들기 대화 상자에서 누락되었습니다. 이 문제가 해결되었습니다. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* 접근성: 엔터티 추가 대화 상자가 VoiceOver를 사용하는 경우 탐색하기 어려웠습니다. 개선이 이루어졌습니다. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* 접근성: 작업 및 속성 창에 대한 축소/확장 단추의 배경색이 고대비 검정 테마의 유사한 UI 컨트롤과 불일치합니다. 색상이 변경되었습니다. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* 접근성: 고대비 검정 테마에서 속성 대화 상자의 ‘X’ 단추에 대한 포커스 스타일링이 표시되지 않습니다. 이 문제가 해결되었습니다. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* 접근성: 작업 및 속성 탭에서 수준 이하의 화면 판독기 환경에서 발생하는 몇 가지 aria 값이 누락되었습니다. 이제 누락된 aria 값이 추가되었습니다. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* 접근성: 왼쪽에 축소된 트리 노드에 false의 aria 확장 값이 부여되지 않았습니다. 이 문제가 해결되었습니다. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>알려진 문제
* Blob 컨테이너와 같은 SAS URI를 통해 연결된 리소스에서 분리하면 다른 첨부 파일이 올바르게 표시되지 않도록 하는 오류가 발생할 수 있습니다. 이 문제를 해결하려면 그룹 노드를 새로 고칩니다. 자세한 내용은 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/537)를 참조하세요.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/97)에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 다음 기능을 지원하지 않으며, Azure Stack에서 작업하는 동안 이러한 기능을 사용하려고 하면 예기치 않은 오류가 발생할 수 있습니다.
   * 파일 공유
   * 액세스 계층
   * 일시 삭제
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>버전 1.2.0
2018/06/12

### <a name="new"></a>신규
* Storage Explorer가 테넌트의 하위 집합에서만 하위 구독을 로드하지 못하는 경우 성공적으로 로드된 모든 구독은 실패한 테넌트에 대한 오류 메시지와 함께 표시됩니다. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Windows에서 업데이트를 사용할 수 있는 경우 이제 "종료 시 업데이트"하도록 선택할 수 있습니다. 이 옵션을 선택하면 Storage Explorer를 닫은 후 업데이트에 대한 설치 관리자가 실행됩니다. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* 파일 공유 스냅샷을 볼 때 파일 공유 편집기의 바로 가기 메뉴에 복원 스냅샷이 추가되었습니다.[#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* 큐 지우기 단추는 이제 항상 활성화되어 있습니다.[#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* ADFS Azure Stack에 로그인에 대한 지원이 다시 활성화되었습니다. Azure Stack 버전 1804 이상이 필요합니다. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>수정 프로그램
* 이름이 동일한 스토리지 계정에서 다른 파일 공유의 접두사인 파일 공유에 대한 스냅샷을 본 경우 다른 파일 공유에 대한 스냅샷도 나열됩니다. 이 문제가 해결되었습니다. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* SAS를 통해 연결된 경우 파일 공유 스냅샷에서 파일 복원은 오류가 발생합니다. 이 문제가 해결되었습니다. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Blob에 대한 스냅샷을 볼 때 스냅샷 승격 작업은 기본 Blob 및 단일 스냅샷이 선택된 경우 활성화되었습니다. 작업은 이제 단일 스냅샷이 선택된 경우에만 활성화됩니다. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* 단일 작업(예: Blob 다운로드)이 시작되고 나중에 실패한 경우 동일한 형식의 다른 작업을 시작할 때까지 자동으로 다시 시도하지 않습니다. 모든 작업은 이제 대기 중인 작업의 수와 관계 없이 자동 다시 시도되어야 합니다.
* GPV2 및 Blob Storage 계정에서 새롭게 만든 Blob 컨테이너에 대해 열린 편집기에는 액세스 계층 열이 없습니다. 이 문제가 해결되었습니다. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* 액세스 계층 열은 경우에 따라 Storage 계정 또는 Blob 컨테이너가 SAS를 통해 연결된 경우 나타나지 않습니다. 열은 이제 항상 표시되지만 액세스 계층 집합이 없는 경우에 빈 값으로 표시됩니다. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* 새로 업로드된 블록 Blob의 액세스 계층에 대한 설정이 비활성화되었습니다. 이 문제가 해결되었습니다. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* 키보드를 사용하여 "탭 열어 두기" 단추가 호출된 경우 키보드 포커스가 손실됩니다. 이제 포커스는 열어 둔 탭으로 이동합니다. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* 쿼리 작성기에서 쿼리의 경우 VoiceOver는 현재 연산자에 대한 사용 가능한 설명을 제공하지 않습니다. 이제 좀 더 구체적이 되었습니다. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* 다양한 편집기에 대한 페이지 매김 링크가 설명되지 않았습니다. 잘 설명하도록 변경되었습니다. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* 엔터티 추가 대화 상자에서 VoiceOver는 입력 요소가 일부인 열을 발표하지 않았습니다. 이제 현재 열의 이름은 요소의 설명에 포함되었습니다. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* 포커스가 지정되었을 때 라디오 단추와 확인란에 테두리 표시가 없었습니다. 이 문제가 해결되었습니다. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>알려진 문제
* Azure Storage 에뮬레이터 또는 Azurite와 같은 에뮬레이터를 사용하는 경우 해당 기본 포트에 대한 연결을 수신하도록 해야 합니다. 그렇지 않으면 Storage Explorer에서 연결할 수 없습니다.
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/97)에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>버전 1.1.0
05/09/2018

### <a name="new"></a>신규
* Storage Explorer에서 Azurite 사용이 지원됩니다. 참고: Azurite에 대한 연결은 기본 개발 엔드포인트로 하드코드됩니다.
* Storage Explorer에서 Blob 전용 및 GPV2 스토리지 계정에 대한 액세스 계층이 지원됩니다. 액세스 계층에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)를 참조하세요.
* SAS를 생성할 때 시작 시간이 더 이상 필요하지 않습니다.

### <a name="fixes"></a>수정 프로그램
* 미국 정부 계정에 대한 구독 검색이 끊겼었습니다. 이 문제가 해결되었습니다. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* 액세스 정책의 만료 시간이 올바르게 저장되지 않았습니다. 이 문제가 해결되었습니다. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* 컨테이너에 있는 항목에 대한 SAS URL을 생성할 때 해당 항목의 이름이 URL에 추가되지 않았습니다. 이 문제가 해결되었습니다. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* SAS를 만들 때 과거의 만료 시간이 기본값이 되는 경우가 있습니다. 이 문제는 Storage Explorer에서 마지막으로 사용된 시작 시간과 만료 시간이 기본값으로 사용되기 때문입니다. 이제는 SAS 대화 상자를 열 때마다 새로운 기본값이 생성됩니다. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* 스토리지 계정 간에 복사하는 경우 24시간 SAS가 생성됩니다. 복사본이 24시간 넘게 유지된 경우에는 복사가 실패합니다. SAS가 만료되어 복사가 실패할 가능성을 줄이기 위해 SAS가 1주일간 지속되도록 시간을 늘렸습니다. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* 일부 작업에 대해 "취소"를 클릭해도 작동하지 않는 경우가 있습니다. 이 문제가 해결되었습니다. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* 일부 작업에 대한 전송 속도가 틀렸습니다. 이 문제가 해결되었습니다. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* 보기 메뉴에 있는 "이전의 영문인 Previous"의 철자가 틀렸습니다. 이제 철자가 수정되었습니다. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Windows Installer의 마지막 페이지에 "다음" 단추가 있었습니다. 이것이 "마침" 단추로 변경되었습니다. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* HC 검정 테마를 사용하는 경우 대화 상자의 단추에 탭 포커스가 표시되지 않았습니다. 이제 표시됩니다.[#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* 활동 로그의 작업에 대한 "자동 해결의 영문인 Auto-Resolve"의 대/소문자가 잘못되었습니다. 이제 수정되었습니다. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* 테이블에서 엔터티를 삭제할 때 확인을 요청하는 대화 상자에 오류 아이콘이 표시되었습니다. 이제 대화 상자에 경고 아이콘이 사용됩니다. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>알려진 문제
* Mac용 VS를 사용하고 사용자 지정 AAD 구성을 만든 적이 있으면 로그인이 불가능할 수 있습니다. 이 문제를 해결하려면 ~/.IdentityService/AadConfigurations의 내용을 삭제합니다. 이렇게 해도 차단이 해제되지 않으면 [이 문제](https://github.com/Microsoft/AzureStorageExplorer/issues/97)에 의견을 남겨주세요.
* Azurite는 모든 스토리지 API에 아직 완전히 구현되지 않았습니다. 그래서 개발 스토리지에 Azurite를 사용하는 경우 예상치 못한 오류나 동작이 발생할 수 있습니다.
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* NodeJS의 버그로 인해 OneDrive 폴더에서 업로드가 수행되지 않습니다. 이 버그는 수정되었지만 Electron에 아직 통합되지 않았습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Version 1.0.0
2018/04/16

### <a name="new"></a>신규
* Storage Explorer에서 Visual Studio 2017과 동일한 계정을 사용할 수 있는 향상된 인증입니다. 이 기능을 사용하려면 계정에 다시 로그인하여 필터링된 구독을 다시 설정해야 합니다.
* AAD에서 지원하는 Azure Stack 계정의 경우 이제 '대상 Azure Stack'을 사용하도록 설정하면 Storage Explorer가 Azure Stack 등록을 검색합니다. 더 이상 사용자 지정 로그인 환경을 만들 필요가 없습니다.
* 보다 빠른 탐색이 가능하도록 여러 바로 가기가 추가되었습니다. 그 중에는 다양한 패널을 전환하고 편집기 간에 이동하는 바로 가기도 포함됩니다. 자세한 내용은 보기 메뉴를 참조하세요.
* 이제 Storage Explorer 피드백이 GitHub에 있습니다. 왼쪽 아래에 있는 [피드백] 단추를 클릭하거나 [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues)로 이동하여 문제 페이지를 열 수 있습니다. 자유롭게 의견을 제안하고, 문제를 보고하고, 궁금한 점을 질문하거나 기타 형태의 피드백을 남겨주세요.
* SSL 인증서 문제가 발생했는데 잘못된 인증서를 찾을 수 없는 경우 이제 명령줄에서 `--ignore-certificate-errors` 플래그로 Storage Explorer를 시작할 수 있습니다. 이 플래그로 시작하면 Storage Explorer가 SSL 인증서 오류를 무시합니다.
* 이제 BLOB 및 파일 항목의 상황에 맞는 메뉴에 '다운로드' 옵션이 있습니다.
* 향상된 내게 필요한 옵션 및 화면 판독기가 지원됩니다. 내게 필요한 옵션 기능을 사용하는 경우 [내게 필요한 옵션 설명서](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility)에서 자세한 내용을 참조하세요.
* 이제 Storage Explorer가 Electron 1.8.3 사용

### <a name="breaking-changes"></a>주요 변경 내용
* Storage Explorer가 새로운 인증 라이브러리로 전환되었습니다. 라이브러리 전환 과정의 일부로, 계정에 다시 로그인하여 필터링된 구독을 다시 설정해야 합니다.
* 중요한 데이터를 암호화하는 데 사용되는 방법이 변경되었습니다. 이로 인해 빠른 실행 항목 중 일부를 다시 추가하고/하거나 연결된 리소스 중 일부를 다시 연결해야 할 수도 있습니다.

### <a name="fixes"></a>수정 프로그램
* 프록시 뒤에 있는 일부 사용자는 '확인할 수 없습니다' 오류 메시지와 함께 그룹 BLOB 업로드 또는 다운로드가 중단되었습니다. 이 문제가 해결되었습니다.
* 직접 연결을 사용하는 동안 로그인이 필요한 경우 '로그인' 프롬프트를 클릭하면 빈 대화 상자가 나타났습니다. 이 문제가 해결되었습니다.
* Linux에서 GPU 프로세스 크래시 때문에 Storage Explorer를 시작할 수 없는 경우 이제 크래시가 발생했으니 '--disable-gpu' 스위치를 사용하라고 사용자에게 알려주며, 스위치를 사용하도록 설정하면 Storage Explorer가 자동으로 다시 시작됩니다.
* 액세스 정책 대화 상자에서 잘못된 액세스 정책을 식별하기가 어려웠습니다. 이제는 쉽게 알아볼 수 있도록 잘못된 액세스 정책 ID의 테두리가 빨간색으로 표시됩니다.
* 활동 로그가 활동의 여러 부분 사이에 있는 공백을 많이 차지하는 경우가 종종 있었습니다. 이 문제가 해결되었습니다.
* 테이블 쿼리 편집기에서, 잘못된 상태의 타임스탬프 절에서 나가서 다른 절을 수정하려고 하면 편집기가 동결되었습니다. 이제 다른 절에서 변경 내용이 감지되면 편집기가 타임스탬프 절을 마지막 유효한 상태로 복원합니다.
* 트리 뷰에서 검색 쿼리에 입력하는 동안 일시 중지하면 검색이 시작되고 텍스트 상자에서 포커스가 사라졌습니다. 이제는 'Enter' 키를 누르거나 [검색 시작] 단추를 클릭하여 명시적으로 검색을 시작해야 합니다.
* 파일 공유에서 파일을 마우스 오른쪽 단추로 클릭하면 가끔 'Get Shared Access Signature' 명령이 비활성화되었습니다. 이 문제가 해결되었습니다.
* 검색하는 동안 포커스가 있는 리소스 트리 노드를 필터링하면 리소스 트리를 탭하고 화살표 키를 사용하여 리소스 트리를 탐색할 수 없었습니다. 이제 포커스가 있는 리소스 트리 노드를 숨기면 리소스 트리의 첫 번째 노드로 포커스가 자동으로 이동합니다.
* 편집기 도구 모음에서 추가 구분 기호가 보이는 경우가 종종 있었습니다. 이 문제가 해결되었습니다.
* 이동 경로 탐색 텍스트 상자가 가끔 오버플로되었습니다. 이 문제가 해결되었습니다.
* 많은 파일을 한꺼번에 업로드하면 BLOB 및 파일 공유 편집기가 계속 새로 고침되는 경우가 가끔 있었습니다. 이 문제가 해결되었습니다.
* '폴더 통계' 기능은 파일 공유 스냅샷 관리 보기에서 아무런 목적도 없었습니다. 이 기능이 비활성화되었습니다.
* Linux에서 파일 메뉴가 표시되지 않았습니다. 이 문제가 해결되었습니다.
* 폴더를 파일 공유에 업로드할 때 기본적으로 폴더의 콘텐츠만 업로드되었습니다. 이제 폴더의 콘텐츠를 파일 공유의 일치하는 폴더에 업로드하는 것이 기본 동작입니다.
* 여러 대화 상자에서 단추 순서가 거꾸로 바뀌었습니다. 이 문제가 해결되었습니다.
* 다양한 보안 관련 문제가 수정되었습니다.

### <a name="known-issues"></a>알려진 문제
* 드물지만, 트리 포커스가 빠른 액세스에 고정될 수 있습니다. 모두 새로 고침을 사용하여 고정을 풀 수 있습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux 사용자의 경우 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)을 설치해야 합니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>버전 0.9.6
02/28/2018

### <a name="fixes"></a>수정 프로그램
* 문제로 인해 BLOB/파일이 편집기에 나열되지 않았습니다. 이 문제가 해결되었습니다.
* 문제로 인해 스냅샷 뷰 사이를 전환하면 항목이 잘못 표시되었습니다. 이 문제가 해결되었습니다.

### <a name="known-issues"></a>알려진 문제
* Storage Explorer는 ADFS 계정을 지원하지 않습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. [여기](https://github.com/Azure/azure-storage-node/issues/317)에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 계정 설정 패널에 구독을 필터링하기 위해 자격 증명을 다시 입력하라고 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>버전 0.9.5
02/06/2018

### <a name="new"></a>신규

* 파일 공유 스냅샷에 대한 지원:
    * 파일 공유의 스냅샷을 만들고 관리합니다.
    * 탐색할 때 파일 공유의 스냅샷 간에 보기를 쉽게 전환합니다.
    * 파일의 이전 버전을 복원합니다.
* Azure Data Lake Store에 대한 미리 보기 지원:
    * 여러 계정에서 ADLS 리소스에 연결합니다.
    * ADL URI를 사용하여 ADLS 리소스에 연결하고 공유합니다.
    * 기본 파일/폴더 작업을 재귀적으로 수행합니다.
    * 개별 폴더를 빠른 실행에 고정합니다.
    * 폴더 통계를 표시합니다.

### <a name="fixes"></a>수정 프로그램
* 시작 성능 개선
* 다양한 버그 수정

### <a name="known-issues"></a>알려진 문제
* Storage Explorer는 ADFS 계정을 지원하지 않습니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 계정 설정 패널에 구독을 필터링하기 위해 자격 증명을 다시 입력하라고 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>버전 0.9.4 및 0.9.3
2018년 1월 21일

### <a name="new"></a>신규
* 다음과 같은 경우에 기존 Storage Explorer 창을 다시 사용합니다.
    * Storage Explorer에 생성된 직접 링크를 여는 경우
    * 포털에서 Storage Explorer를 여는 경우
    * Azure Storage VS Code 확장(출시 예정)에서 Storage Explorer를 여는 경우
* Storage Explorer 내에서 새 Storage Explorer 창을 여는 기능이 추가되었습니다.
    * Windows의 경우 파일 메뉴 아래 및 작업 표시줄의 상황에 맞는 메뉴 아래에 '새 창' 옵션이 있습니다.
    * Mac의 경우 앱 메뉴 아래에 '새 창' 옵션이 있습니다.

### <a name="fixes"></a>수정 프로그램
* 보안 문제가 해결되었습니다. 가장 빠른 편의를 위해 0.9.4로 업그레이드하세요.
* 이전 작업은 적절하게 정리되지 않았습니다. 그러면 장기 실행 작업의 성능에 영향을 주었습니다. 이제 올바르게 정리되었습니다.
* 많은 수의 파일 및 디렉터리를 포함하는 작업으로 인해 경우에 따라 Storage Explorer가 중지됩니다. 시스템 리소스 사용을 제한하기 위해 파일 공유를 위한 Azure에 대한 요청이 제한됩니다.

### <a name="known-issues"></a>알려진 문제
* Storage Explorer는 ADFS 계정을 지원하지 않습니다.
* "탐색기 보기" 및 "계정 관리 보기"에 대한 바로 가기 키는 각각 Ctrl/Cmd+Shift+E 및 Ctrl/Cmd+Shift+A입니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 계정 설정 패널에 구독을 필터링하기 위해 자격 증명을 다시 입력하라고 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer --disable-gpu
    ```

* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>0\.9.2 버전
11/01/2017

### <a name="hotfixes"></a>핫픽스
* 현지 표준 시간대에 따라 테이블 엔터티에 대한 Edm.DateTime 값을 편집할 때, 예기치 않은 데이터 변경이 가능합니다. 이제 편집기는 Edm.DateTime 값에 대해 정확하고 일관된 제어를 제공하는 일반 텍스트 상자를 사용합니다.
* 이름과 키로 첨부될 때 BLOB 그룹 업로드/다운로드가 시작되지 않습니다. 이 문제가 해결되었습니다.
* 이전에 Microsoft Azure Storage Explorer는 계정 구독을 하나 이상 선택한 경우 다만 오래된 계정을 다시 인증하도록 메시지를 표시합니다. 이제 Microsoft Azure Storage Explorer는 계정이 완벽하게 필터링되었는지 묻는 메시지가 표시됩니다.
* Azure US Government에 대한 엔드포인트 도메인이 잘못 되었습니다. 이 문제가 해결되었습니다.
* Manage Accounts 패널에서 [적용] 단추는 때로 클릭하기 힘듭니다. 이 문제가 더 이상 일어나지 않을 것입니다.

### <a name="new"></a>신규
* Azure Cosmos DB에 대한 미리 보기 지원:
    * [온라인 설명서](./cosmos-db/storage-explorer.md)
    * 데이터베이스 및 컬렉션 만들기
    * 데이터 조작
    * 문서 쿼리, 만들기 또는 삭제
    * 저장 프로시저, 사용자 정의 함수 또는 트리거 업데이트
    * 연결 문자열을 사용하여 데이터베이스 연결 및 관리
* 여러 개의 작은 Blob을 업로드/다운로드하는 성능 개선
* Blob 업로드 그룹이나 Blob 다운로드 그룹에 오류가 발생하는 경우 "모두 다시 시도" 작업 추가
* 이제 네트워크 연결이 손실된 것을 감지하면 Blob 업로드/다운로드 중에 Storage Explorer가 반복을 일시 중지합니다. 그런 다음 네트워크 연결이 다시 설정되면 반복을 재개할 수 있습니다.
* 팝업 메뉴를 통해 "모두 닫기", "다른 항목 닫기" 및 "닫기" 탭에 대한 기능 추가
* 이제 Storage Explorer는 기본 대화 상자 및 네이티브 팝업 메뉴를 사용합니다.
* 이제 Storage Explorer에 보다 쉽게 액세스할 수 있습니다. 향상된 기능은 다음과 같습니다.
    * Windows에서는 NVDA, Mac에서는 VoiceOver에 대한 화면 판독기 지원 향상
    * 고대비 테마 설정 향상
    * 키보드 탭 및 키보드 포커스 수정

### <a name="fixes"></a>수정 프로그램
* 잘못된 Windows 파일 이름을 포함한 Blob을 열거나 다운로드하려고 하면 작업에 실패합니다. 이제 Storage Explorer는 Blob 이름이 잘못되었는지를 감지하고 Blob을 인코딩할지 아니면 건너뛸지를 묻습니다. 또한 Storage Explorer는 파일 이름이 인코딩되어 표시되는지를 감지하고 업로드하기 전에 디코딩할지를 묻습니다.
* Blob 업로드 중에 대상 Blob 컨테이너의 편집기는 경우에 따라 올바르게 새로 고쳐지지 않습니다. 이 문제가 해결되었습니다.
* 여러 형태의 연결 문자열 및 SAS URI에 대한 지원이 회귀되었습니다. 모든 알려진 문제를 해결했지만 추가 문제가 발생하면 사용자 의견을 보내주세요.
* 0\.9.0를 사용하는 일부 사용자에게는 업데이트 알림이 중단되었습니다. 이 문제를 해결했습니다. 버그의 영향을 받는 경우 [여기](https://azure.microsoft.com/features/storage-explorer/)에서 최신 버전의 Microsoft Azure Storage Explorer를 수동으로 다운로드할 수 있습니다.

### <a name="known-issues"></a>알려진 문제
* Storage Explorer는 ADFS 계정을 지원하지 않습니다.
* "탐색기 보기" 및 "계정 관리 보기"에 대한 바로 가기 키는 각각 Ctrl/Cmd+Shift+E 및 Ctrl/Cmd+Shift+A입니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 계정 설정 패널에 구독을 필터링하기 위해 자격 증명을 다시 입력하라고 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer --disable-gpu
    ```

* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>버전 0.9.1 및 0.9.0
10/20/2017
### <a name="new"></a>신규
* Azure Cosmos DB에 대한 미리 보기 지원:
    * [온라인 설명서](./cosmos-db/storage-explorer.md)
    * 데이터베이스 및 컬렉션 만들기
    * 데이터 조작
    * 문서 쿼리, 만들기 또는 삭제
    * 저장 프로시저, 사용자 정의 함수 또는 트리거 업데이트
    * 연결 문자열을 사용하여 데이터베이스 연결 및 관리
* 여러 개의 작은 Blob을 업로드/다운로드하는 성능 개선
* Blob 업로드 그룹이나 Blob 다운로드 그룹에 오류가 발생하는 경우 "모두 다시 시도" 작업 추가
* 이제 네트워크 연결이 손실된 것을 감지하면 Blob 업로드/다운로드 중에 Storage Explorer가 반복을 일시 중지합니다. 그런 다음 네트워크 연결이 다시 설정되면 반복을 재개할 수 있습니다.
* 팝업 메뉴를 통해 "모두 닫기", "다른 항목 닫기" 및 "닫기" 탭에 대한 기능 추가
* 이제 Storage Explorer는 기본 대화 상자 및 네이티브 팝업 메뉴를 사용합니다.
* 이제 Storage Explorer에 보다 쉽게 액세스할 수 있습니다. 향상된 기능은 다음과 같습니다.
    * Windows에서는 NVDA, Mac에서는 VoiceOver에 대한 화면 판독기 지원 향상
    * 고대비 테마 설정 향상
    * 키보드 탭 및 키보드 포커스 수정

### <a name="fixes"></a>수정 프로그램
* 잘못된 Windows 파일 이름을 포함한 Blob을 열거나 다운로드하려고 하면 작업에 실패합니다. 이제 Storage Explorer는 Blob 이름이 잘못되었는지를 감지하고 Blob을 인코딩할지 아니면 건너뛸지를 묻습니다. 또한 Storage Explorer는 파일 이름이 인코딩되어 표시되는지를 감지하고 업로드하기 전에 디코딩할지를 묻습니다.
* Blob 업로드 중에 대상 Blob 컨테이너의 편집기는 경우에 따라 올바르게 새로 고쳐지지 않습니다. 이 문제가 해결되었습니다.
* 여러 형태의 연결 문자열 및 SAS URI에 대한 지원이 회귀되었습니다. 모든 알려진 문제를 해결했지만 추가 문제가 발생하면 사용자 의견을 보내주세요.
* 0\.9.0를 사용하는 일부 사용자에게는 업데이트 알림이 중단되었습니다. 이 문제를 해결했습니다. 버그의 영향을 받는 경우 [여기](https://azure.microsoft.com/features/storage-explorer/)에서 최신 버전의 Storage Explorer를 수동으로 다운로드할 수 있습니다.

### <a name="known-issues"></a>알려진 문제
* Storage Explorer는 ADFS 계정을 지원하지 않습니다.
* "탐색기 보기" 및 "계정 관리 보기"에 대한 바로 가기 키는 각각 Ctrl/Cmd+Shift+E 및 Ctrl/Cmd+Shift+A입니다.
* Azure Stack을 대상으로 지정할 때 특정 파일을 추가 Blob으로 업로드하는 데 실패할 수 있습니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 여기에 설명된 취소 필터 해결 방법을 사용하기 때문입니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 계정 설정 패널에 구독을 필터링하기 위해 자격 증명을 다시 입력하라고 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Storage Explorer에서 사용하는 전자 셸에는 GPU(그래픽 처리 단위) 하드웨어 가속과 관련된 문제가 발생합니다. Storage Explorer가 빈(비어 있는) 주 창을 표시하는 경우 `--disable-gpu` 스위치를 추가하여 명령줄에서 Storage Explorer를 시작하고 GPU 가속을 사용하지 않도록 설정할 수 있습니다.

    ```
    ./StorageExplorer --disable-gpu
    ```

* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>0\.8.16 버전
8/21/2017

### <a name="new"></a>신규
* Blob을 열 때 Storage Explorer는 변경 내용이 감지될 경우 다운로드한 파일을 업로드할지 묻는 메시지를 표시합니다.
* 향상된 Azure Stack 로그인 환경
* 여러 개의 작은 파일을 동시에 업로드/다운로드하는 성능 개선


### <a name="fixes"></a>수정 프로그램
* 일부 blob 형식의 경우 업로드 중에 "바꾸기"를 선택하면 충돌이 발생해서 업로드가 다시 시작될 수 있습니다.
* 버전 0.8.15에서 업로드는 경우에 따라 99%에서 중단됩니다.
* 파일을 파일 공유에 업로드할 때 아직 존재하지 않는 디렉터리로 업로드하도록 선택하면 업로드가 실패합니다.
* Storage Explorer가 공유 액세스 서명 및 테이블 쿼리에 대한 타임스탬프를 잘못 생성합니다.


### <a name="known-issues"></a>알려진 문제
* 현재 이름 및 키 연결 문자열을 사용할 수 없습니다. 이 문제는 다음 릴리스에서 해결될 것입니다. 그때까지는 이름 및 키로 연결 기능을 사용할 수 있습니다.
* 잘못된 Windows 파일 이름을 갖는 파일을 열려고 하면 다운로드 시 파일을 찾을 수 없음 오류가 표시됩니다.
* 작업에서 "취소"를 클릭한 후 해당 작업이 취소될 때까지 시간이 걸릴 수 있습니다. 이것은 Azure Storage 노드 라이브러리의 제한 사항입니다.
* Blob 업로드를 완료한 후 업로드를 시작한 탭이 새로 고쳐집니다. 이러한 현상은 이전과 다르게 변경된 동작이며, 해당 동작으로 인해 현재 표시되어 있는 컨테이너의 루트로 다시 이동하게 됩니다.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 계정 설정 패널에 구독을 필터링하기 위해 자격 증명을 다시 입력하라고 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Ubuntu 14.04 사용자의 경우 GCC가 최신 상태인지 확인해야 합니다. 이를 위해 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Ubuntu 17.04 사용자의 경우에는 GConf를 설치해야 합니다. 이렇게 하려면 다음 명령을 실행한 후 컴퓨터를 다시 시작합니다.

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>버전 0.8.14
06/22/2017

### <a name="new"></a>신규

* 여러 중요 보안 업데이트를 활용하기 위해 Electron 버전이 1.7.2로 업데이트되었습니다.
* 이제 도움말 메뉴에서 온라인 문제 해결 가이드에 빠르게 액세스할 수 있습니다.
* Storage 탐색기 문제 해결 [가이드][2]
* Azure Stack 구독에 연결 하는 방법에 대한 [지침][3]

### <a name="known-issues"></a>알려진 문제

* Linux에서 마우스를 클릭해도 폴더 삭제 확인 대화 상자의 단추가 등록되지 않습니다. 이 문제를 해결하려면 Enter 키를 사용하세요.
* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 4개 이상의 Blob 또는 파일 그룹을 동시에 업로드하면 오류가 발생할 수 있습니다.
* 계정 설정 패널에 구독을 필터링하려면 자격 증명을 다시 입력하라는 메시지가 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Ubuntu 14.04 설치 시에는 gcc 버전을 업데이트하거나 업그레이드해야 합니다. 업그레이드 단계는 다음과 같습니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>버전 0.8.13
05/12/2017

#### <a name="new"></a>신규

* Storage 탐색기 문제 해결 [가이드][2]
* Azure Stack 구독에 연결 하는 방법에 대한 [지침][3]

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 파일 업로드 시의 높은 메모리 부족 오류 발생 가능성이 수정되었습니다.
* 수정됨: 이제 PIN/스마트 카드를 사용하여 로그인할 수 있습니다.
* 수정 됨: 이제 포털에서 열기는 Azure 중국 21Vianet, Azure 독일, Azure 미국 정부 및 Azure Stack와 함께 작동 합니다.
* 수정됨: Blob 컨테이너에 폴더를 업로드하는 중에 가끔씩 "잘못된 작업입니다."라는 오류가 발생하는 현상이 해결되었습니다.
* 수정됨: 스냅샷을 관리하는 동안 모두 선택 기능이 사용할 수 없도록 설정되었습니다.
* 수정됨: 기본 Blob의 메타데이터 스냅샷 속성을 확인하고 나면 해당 메타데이터가 덮어쓰여질 수 있습니다.

#### <a name="known-issues"></a>알려진 문제

* 잘못된 PIN/스마트 카드 인증서를 선택하는 경우 해당 선택을 취소하려면 Storage Explorer를 다시 시작해야 합니다.
* 확대하거나 축소하는 동안 확대/축소 수준이 잠시 동안 기본 수준으로 다시 설정될 수 있습니다.
* 4개 이상의 Blob 또는 파일 그룹을 동시에 업로드하면 오류가 발생할 수 있습니다.
* 계정 설정 패널에 구독을 필터링하려면 자격 증명을 다시 입력하라는 메시지가 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Ubuntu 14.04 설치 시에는 gcc 버전을 업데이트하거나 업그레이드해야 합니다. 업그레이드 단계는 다음과 같습니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>버전 0.8.12 및 0.8.11 및 0.8.10
04/07/2017

#### <a name="new"></a>신규

* 이제 업데이트 알림에서 업데이트를 설치할 때 Storage Explorer가 자동으로 닫힙니다.
* 현재 위치 빠른 액세스 기능이 자주 액세스하는 리소스 사용을 위한 향상된 환경을 제공합니다.
* 이제 Blob 컨테이너 편집기에서 임대한 Blob가 속하는 가상 컴퓨터를 확인할 수 있습니다.
* 이제 왼쪽 가로 패널을 축소할 수 있습니다.
* 이제 검색이 다운로드와 동시에 실행됩니다.
* Blob 컨테이너, 파일 공유 및 테이블 편집기에서 통계를 사용하여 리소스 또는 선택 항목의 크기를 확인할 수 있습니다.
* 이제 Azure Stack 계정을 기준으로 AAD(Azure Active Directory)에 로그인할 수 있습니다.
* 이제 Premium Storage 계정에 32MB가 넘는 보관 파일을 업로드할 수 있습니다.
* 손쉬운 사용 옵션 지원이 개선되었습니다.
* 이제 편집 -&gt; SSL 인증서 -&gt; 인증서 가져오기로 이동하여 신뢰할 수 있는 Base64 인코딩 X.509 SSL 인증서를 추가할 수 있습니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 계정의 자격 증명을 새로 고친 후 트리 뷰가 가끔씩 자동으로 새로 고쳐지지 않는 현상이 수정되었습니다.
* 수정됨: 에뮬레이터 큐 및 테이블에 대해 SAS를 생성하면 잘못된 URL이 생성되는 현상이 수정되었습니다.
* 수정됨: 이제 프록시가 사용하도록 설정된 상태에서 Premium Storage 계정을 확장할 수 있습니다.
* 수정됨: 계정을 1개 또는 0개를 선택하면 계정 관리 페이지의 적용 단추가 작동하지 않는 현상이 수정되었습니다.
* 수정됨: 충돌을 해결해야 하는 Blob 업로드가 실패할 수 있는 현상이 버전 0.8.11에서 수정되었습니다.
* 수정됨: 버전 0.8.11에서 피드백 전송이 중단되는 현상이 버전 0.8.12에서 수정되었습니다.

#### <a name="known-issues"></a>알려진 문제

* 0\.8.10으로 업그레이드한 후에는 모든 자격 증명을 새로 고쳐야 합니다.
* 확대하거나 축소하는 동안 확대/축소 수준이 잠시 동안 기본 수준으로 다시 설정될 수 있습니다.
* 4개 이상의 Blob 또는 파일 그룹을 동시에 업로드하면 오류가 발생할 수 있습니다.
* 계정 설정 패널에 구독을 필터링하려면 자격 증명을 다시 입력하라는 메시지가 표시될 수 있습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. Blob, 파일 및 엔터티의 기타 모든 속성과 메타데이터는 이름을 바꾸어도 보존됩니다.
* Azure Stack은 현재 파일 공유를 지원하지 않지만, 연결된 Azure Stack 스토리지 계정에는 파일 공유 노드가 계속 표시됩니다.
* Ubuntu 14.04 설치 시에는 gcc 버전을 업데이트하거나 업그레이드해야 합니다. 업그레이드 단계는 다음과 같습니다.

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>버전 0.8.9 및 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>신규

* Storage Explorer 0.8.9는 업데이트를 위해 최신 버전을 자동으로 다운로드합니다.
* 핫픽스: 포털에서 생성한 SAS URI를 사용하여 Storage 계정을 연결하면 오류가 발생합니다.
* 이제 Blob 스냅샷을 만들고 관리하고 수준을 올릴 수 있습니다.
* 이제 Azure China 21Vianet, Azure 독일 및 Azure 미국 정부 계정에 로그인할 수 있습니다.
* 이제 확대/축소 수준을 변경할 수 있습니다. 보기 메뉴의 옵션을 사용하여 확대, 축소 및 확대/축소 다시 설정을 수행할 수 있습니다.
* 이제 Blob 및 파일의 사용자 메타데이터에서 유니코드 문자가 지원됩니다.
* 내게 필요한 옵션이 개선되었습니다.
* 업데이트 알림에서 다음 버전의 릴리스 정보를 확인할 수 있습니다. 또한 도움말 메뉴에서 현재 릴리스 정보를 볼 수도 있습니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 이제 Windows의 제어판에서 버전 번호가 올바르게 표시됩니다.
* 수정됨: 검색 가능한 노드 수가 더 이상 50,000개로 제한되지 않습니다.
* 수정됨: 대상 디렉터리가 없으면 파일 공유로의 업로드가 무한 반복되는 현상이 수정되었습니다.
* 수정됨: 오랫동안 실행되는 업로드와 다운로드의 안정성이 개선되었습니다.

#### <a name="known-issues"></a>알려진 문제

* 확대하거나 축소하는 동안 확대/축소 수준이 잠시 동안 기본 수준으로 다시 설정될 수 있습니다.
* 빠른 액세스는 구독 기반 항목에만 작동합니다. 로컬 리소스 또는 키나 SAS 토큰을 통해 연결된 리소스는 이번 릴리스에서 지원되지 않습니다.
* 보유하고 있는 리소스 수에 따라 빠른 액세스 기능으로 대상 리소스에 액세스하는 데 몇 초 정도 걸릴 수 있습니다.
* 4개 이상의 Blob 또는 파일 그룹을 동시에 업로드하면 오류가 발생할 수 있습니다.

12/16/2016
### <a name="version-087"></a>버전 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>신규

* 이제 활동 창에서 업데이트, 다운로드 또는 복사 세션을 시작할 때 발생하는 충돌을 해결하는 방법을 선택할 수 있습니다.
* Storage 리소스의 전체 경로를 보려면 탭 위로 마우스를 가져갑니다.
* 탭을 클릭하면 왼쪽 탐색 창의 해당 위치와 동기화됩니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 이제 Mac에서도 Storage Explorer를 신뢰할 수 있는 앱으로 사용할 수 있습니다.
* 수정됨: Ubuntu 14.04가 다시 지원됩니다.
* 수정됨: 구독을 로드할 때 계정 추가 UI가 가끔씩 깜박이는 현상이 수정되었습니다.
* 수정됨: 가끔씩 왼쪽 탐색 창에 일부 Storage 리소스가 나열되지 않는 현상이 수정되었습니다.
* 수정됨: 작업 창에 가끔씩 빈 작업이 표시되는 현상이 수정되었습니다.
* 수정됨: 이제 마지막에 닫은 세션의 창 크기가 유지됩니다.
* 수정됨: 상황에 맞는 메뉴를 사용하는 동일한 리소스에 대해 여러 탭을 열 수 있습니다.

#### <a name="known-issues"></a>알려진 문제

* 빠른 액세스는 구독 기반 항목에만 작동합니다. 로컬 리소스 또는 키나 SAS 토큰을 통해 연결된 리소스는 이번 릴리스에서 지원되지 않습니다.
* 보유하고 있는 리소스 수에 따라 빠른 액세스 기능으로 대상 리소스로 이동하는 데 몇 초 정도 걸릴 수 있습니다.
* 4개 이상의 Blob 또는 파일 그룹을 동시에 업로드하면 오류가 발생할 수 있습니다.
* 검색 기능이 약 50,000개 노드에서 검색을 처리하지만 이러한 검색 후에 성능이 저하되거나 처리되지 않은 예외가 발생할 수 있습니다.
* macOS에서 Storage Explorer를 처음 사용할 때 사용자의 키 집합 액세스 권한을 묻는 메시지가 여러 번 표시될 수 있습니다. 확인 메시지가 다시 표시되지 않도록 항상 허용을 선택하는 것이 좋습니다.

11/18/2016
### <a name="version-086"></a>버전 0.8.6

#### <a name="new"></a>신규

* 쉬운 탐색을 위해 가장 자주 사용되는 서비스를 빠른 액세스 기능에 고정할 수 있습니다.
* 이제 다른 탭에 있는 여러 편집기를 열 수 있습니다. 한 번 클릭 하면 임시 탭이 열립니다. 영구적 탭을 열려면 두 번 클릭 합니다. 임시 탭을 클릭 하 여 영구 탭으로 만들 수도 있습니다.
* 특히 빠른 컴퓨터에서 큰 파일에 대해 업로드 및 다운로드를 수행할 때 성능 및 안정성이 크게 개선되었습니다.
* 이제 blob 컨테이너에 빈 "가상" 폴더를 만들 수 있음
* 새롭게 향상된 하위 문자열 검색에 범위 지정 검색 기능이 다시 도입되었으므로 이제 검색 시에 다음의 두 가지 옵션을 사용할 수 있습니다.
    * 전체 검색 - 검색 입력란에 검색 용어만 입력하면 됩니다.
    * 범위 지정 검색 - 노드 옆의 돋보기 아이콘을 클릭하고 경로 끝에 검색 용어를 추가하거나 마우스 오른쪽 단추를 클릭하고 "여기에서 검색"을 선택합니다.
* 밝게(기본값), 어둡게, 고대비 검정 및 고대비 흰색 등의 다양한 테마를 추가했습니다. 편집 -&gt; 테마로 이동하여 테마 기본 설정을 변경할 수 있습니다.
* Blob 및 파일 속성을 수정할 수 있습니다.
* 이제 인코딩된 큐 메시지(Base64)와 인코딩되지 않은 큐 메시지가 모두 지원됩니다.
* 이제 Linux에서는 64비트 OS를 사용해야 합니다. 이번 릴리스에서는 64비트 Ubuntu 16.04.1 LTS만 지원됩니다.
* 로고가 업데이트되었습니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 화면 중단 문제
* 수정됨: 향상된 보안
* 수정됨: 가끔씩 연결된 계정이 중복으로 표시되는 현상이 수정되었습니다.
* 수정됨: Blob의 콘텐츠 형식이 정의되어 있지 않으면 예외가 발생할 수 있는 현상이 수정되었습니다.
* 수정됨: 빈 테이블에서 쿼리 패널을 열 수 없는 현상이 수정되었습니다.
* 수정됨: 검색의 버그가 확인 및 수정되었습니다.
* 수정됨: "추가 로드"를 클릭하면 로드되는 리소스의 수가 50개에서 100개로 늘어났습니다.
* 수정됨: 이제는 첫 실행 시 계정에 로그인하면 기본적으로 해당 계정의 모든 구독이 선택됩니다.

#### <a name="known-issues"></a>알려진 문제

* Ubuntu 14.04에서는 이 Storage Explorer 릴리스가 실행되지 않습니다.
* 같은 리소스에 대해 여러 탭을 열려는 경우 같은 리소스를 계속해서 클릭하지 마세요. 다른 리소스를 클릭한 후에 다시 돌아가서 원래 리소스를 클릭하여 다른 탭에서 다시 열어야 합니다.
* 빠른 액세스는 구독 기반 항목에만 작동합니다. 로컬 리소스 또는 키나 SAS 토큰을 통해 연결된 리소스는 이번 릴리스에서 지원되지 않습니다.
* 보유하고 있는 리소스 수에 따라 빠른 액세스 기능으로 대상 리소스로 이동하는 데 몇 초 정도 걸릴 수 있습니다.
* 4개 이상의 Blob 또는 파일 그룹을 동시에 업로드하면 오류가 발생할 수 있습니다.
* 검색 기능이 약 50,000개 노드에서 검색을 처리하지만 이러한 검색 후에 성능이 저하되거나 처리되지 않은 예외가 발생할 수 있습니다.

10/03/2016
### <a name="version-085"></a>버전 0.8.5

#### <a name="new"></a>신규

* 이제 Portal에서 생성된 SAS 키를 사용하여 Storage 계정 및 리소스에 연결할 수 있습니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 검색 중에 경합 상태가 발생하면 가끔씩 노드를 확장할 수 없게 되는 현상이 수정되었습니다.
* 수정됨: 계정 이름과 키를 사용하여 Storage 계정에 연결할 때 "HTTP 사용"이 작동하지 않는 현상이 수정되었습니다.
* 수정됨: SAS 키(구체적으로는 Portal에서 생성된 키)에서 "후행 슬래시" 오류가 반환되는 현상이 수정되었습니다.
* 수정됨: 테이블 가져오기 문제가 수정되었습니다.
    * 가끔씩 파티션 키와 행 키가 서로 바뀌는 문제
    * "null" 파티션 키를 읽을 수 없는 문제

#### <a name="known-issues"></a>알려진 문제

* 검색 기능이 약 50,000개 노드에서 검색을 처리하지만 이러한 검색 후에 성능이 저하될 수 있습니다.
* Azure Stack은 현재 파일을 지원하지 않으므로 파일을 확장하려고 하면 오류가 표시됩니다.

09/12/2016
### <a name="version-084"></a>버전 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>신규

* 리소스를 공유하고 쉽게 액세스하기 위해 Storage 계정, 컨테이너, 큐, 테이블 또는 파일 공유에 대한 직접 링크를 생성할 수 있습니다(Windows 및 Mac OS 지원).
* 검색 상자에서 Blob 컨테이너, 테이블, 큐, 파일 공유 또는 스토리지 계정 검색
* 이제 테이블 쿼리 작성기에서 절을 그룹화할 수 있습니다.
* SAS에 연결된 계정 및 컨테이너 내에서 Blob 컨테이너, 파일 공유, 테이블, Blob, Blob 폴더, 파일 및 디렉터리 복사/붙여넣기 및 이름 바꾸기를 수행할 수 있습니다.
* 이제 Blob 컨테이너와 파일 공유의 이름 바꾸기 및 복사 시에 속성과 메타데이터가 보존됩니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 부울 또는 이진 속성을 포함하는 테이블 엔터티를 편집할 수 없는 문제가 수정되었습니다.

#### <a name="known-issues"></a>알려진 문제

* 검색 기능이 약 50,000개 노드에서 검색을 처리하지만 이러한 검색 후에 성능이 저하될 수 있습니다.

08/03/2016
### <a name="version-083"></a>버전 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>신규

* 컨테이너, 테이블, 파일 공유의 이름을 바꿀 수 있습니다.
* 쿼리 작성기 환경이 개선되었습니다.
* 쿼리를 저장하고 로드할 수 있습니다.
* 리소스를 공유하고 쉽게 액세스하기 위해 Storage 계정, 컨테이너, 큐, 테이블 또는 파일 공유에 대한 직접 링크를 생성할 수 있습니다(Windows 전용/Mac OS는 조만간 지원될 예정).
* CORS 규칙을 관리 및 구성하는 기능

#### <a name="fixes"></a>수정 프로그램

* 수정됨: Microsoft 계정을 8-12시간마다 다시 인증해야 하는 문제가 수정되었습니다.

#### <a name="known-issues"></a>알려진 문제

* 가끔씩 UI가 중지된 것처럼 보일 수 있습니다. 창을 최대화하면 이 문제를 해결할 수 있습니다.
* macOS 설치 시 관리자 권한이 필요할 수 있습니다.
* 계정 설정 패널에 구독을 필터링하려면 자격 증명을 다시 입력하라는 메시지가 표시될 수 있습니다.
* 파일 공유, Blob 컨테이너 및 테이블의 이름을 바꾸면 파일 공유 할당량, 공용 액세스 수준, 액세스 정책 등 컨테이너에 대한 메타데이터나 기타 속성이 보존되지 않습니다.
* blob 이름을 바꿀 경우(개별적으로 또는 이름이 바뀐 blob 컨테이너 내에서) 스냅샷을 보존되지 않습니다. blob, 파일 및 엔터티에 대한 다른 모든 속성 및 메타데이터는 이름을 바꾸는 동안 유지됩니다.
* SAS에 연결된 계정 내에서는 리소스 복사 또는 이름 바꾸기가 작동하지 않습니다.

07/07/2016
### <a name="version-082"></a>버전 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>신규

* Storage 계정이 구독별로 그룹화됩니다. 키 또는 SAS를 통해 연결된 개발 Storage 및 리소스는 (로컬 및 연결됨) 노드 아래에 표시됩니다.
* "Azure 계정 설정" 패널에서 계정을 로그오프할 수 있습니다.
* 로그인을 사용 및 관리하도록 프록시 설정을 구성할 수 있습니다.
* Blob 임대를 작성/중단할 수 있습니다.
* 클릭 한 번으로 Blob 컨테이너, 큐, 테이블 및 파일을 열 수 있습니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: .NET 또는 Java 라이브러리와 함께 삽입된 큐 메시지가 Base64에서 올바르게 디코드되지 않는 현상이 수정되었습니다.
* 수정됨: $metrics 테이블이 Blob Storage 계정에 대해 표시되지 않는 현상이 수정되었습니다.
* 수정됨: 로컬(개발) 스토리지에 대해 테이블 노드가 작동하지 않는 현상이 수정되었습니다.

#### <a name="known-issues"></a>알려진 문제

* macOS 설치 시 관리자 권한이 필요할 수 있습니다.

06/15/2016
### <a name="version-080"></a>버전 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>신규

* 파일 공유 지원: 파일 및 디렉터리 보기/업로드/다운로드/복사, SAS URI 만들기 및 연결이 지원됩니다.
* SAS URI 또는 계정 키를 사용하여 Storage에 연결하기 위한 사용자 환경이 개선되었습니다.
* 테이블 쿼리 결과를 내보낼 수 있습니다.
* 테이블 열 다시 정렬 및 사용자 지정이 가능합니다.
* 메트릭이 사용하도록 설정된 Storage 계정의 $logs Blob 컨테이너와 $metrics 테이블을 확인할 수 있습니다.
* 내보내기 및 가져오기 동작이 개선되어 이제 속성 값 형식이 포함됩니다.

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 큰 Blob를 업로드하거나 다운로드하면 업로드/다운로드가 완전히 진행되지 않는 현상이 수정되었습니다.
* 수정됨: 숫자 문자열 값("1")이 포함된 엔터티를 편집하거나 추가하거나 가져오면 해당 값이 double로 변환되는 현상이 수정되었습니다.
* 수정됨: 로컬 개발 환경에서 테이블 노드를 확장할 수 없는 현상이 수정되었습니다.

#### <a name="known-issues"></a>알려진 문제

* $metrics 테이블이 Blob Storage 계정에 대해 표시되지 않습니다.
* Base64 인코딩을 사용하여 인코딩된 큐 메시지를 프로그래밍 방식으로 추가하면 올바르게 표시되지 않을 수 있습니다.

05/17/2016
### <a name="version-07201605090"></a>버전 0.7.20160509.0

#### <a name="new"></a>신규

* 앱 작동 중단 시의 오류가 보다 효율적으로 처리됩니다.

#### <a name="fixes"></a>수정 프로그램

* 로그인 자격 증명이 필요할 때 정보 표시줄 메시지가 가끔씩 표시되지 않는 버그가 수정되었습니다.

#### <a name="known-issues"></a>알려진 문제

* 테이블: "1" 또는 "1.0"과 같이 모호한 숫자 값이 포함된 속성이 있는 엔터티를 추가하거나 편집하거나 가져올 때 사용자가 해당 엔터티를 `Edm.String`으로 보내려고 하면 값이 클라이언트 API를 통해 Edm.Double로 반환됩니다.

03/31/2016

### <a name="version-07201603250"></a>버전 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>신규

* 테이블 지원: 엔터티 보기/쿼리/내보내기/가져오기 및 CRUD 작업이 지원됩니다.
* 큐 지원: 메시지 보기, 추가, 큐에서 제거가 지원됩니다.
* Storage 계정에 대해 SAS URI를 생성할 수 있습니다.
* SAS URI를 사용하여 Storage 계정에 연결할 수 있습니다.
* 이후 Storage Explorer 업데이트를 위한 업데이트 알림이 표시됩니다.
* 디자인이 업데이트되었습니다.

#### <a name="fixes"></a>수정 프로그램

* 성능과 안정성이 개선되었습니다.

### <a name="known-issues-amp-mitigations"></a>알려진 문제 &amp; 완화 방법

* 큰 Blob 파일 다운로드가 제대로 작동하지 않습니다. 이 문제를 해결하는 방법이 제공될 때까지는 AzCopy를 사용하는 것이 좋습니다.
* 홈 폴더를 찾을 수 없거나 홈 폴더에 쓸 수 없는 경우 계정 자격 증명이 검색되거나 캐시되지 않습니다.
* "1" 또는 "1.0"과 같이 모호한 숫자 값이 포함된 속성이 있는 엔터티를 추가하거나 편집하거나 가져올 때 사용자가 해당 엔터티를 `Edm.String`으로 보내려고 하면 값이 클라이언트 API를 통해 Edm.Double로 반환됩니다.
* 여러 줄 된 레코드가 포함된 CSV 파일을 가져올 때 데이터가 잘리거나 암호화될 수 있습니다.

02/03/2016

### <a name="version-07201601291"></a>버전 0.7.20160129.1

#### <a name="fixes"></a>수정 프로그램

* Blob 업로드, 다운로드 및 복사 시의 전반적인 성능이 개선되었습니다.

01/14/2016

### <a name="version-07201601050"></a>버전 0.7.20160105.0

#### <a name="new"></a>신규

* Linux 지원이 추가되었습니다(OSX에 대한 패리티 기능).
* SAS(공유 액세스 서명) 키를 사용하여 Blob 컨테이너를 추가할 수 있습니다.
* Azure China 21Vianet의 Storage 계정을 추가할 수 있습니다.
* 사용자 지정 엔드포인트가 있는 Storage 계정을 추가할 수 있습니다.
* 내용 텍스트 및 그림 blob 열기 및 보기
* blob 속성 및 메타데이터 보기 및 편집

#### <a name="fixes"></a>수정 프로그램

* 수정됨: 500개가 넘는 많은 Blob를 업로드하거나 다운로드할 때 가끔씩 앱에 흰색 화면이 표시될 수 있는 현상이 수정되었습니다.
* 수정됨: Blob 컨테이너 공용 액세스 수준을 설정할 때 컨테이너에서 포커스를 다시 설정할 때까지 새 값이 업데이트되지 않습니다. 또한 대화 상자에서는 실제 현재 값이 아닌 "공용 액세스 권한 없음"이 항상 기본값으로 표시됩니다.
* 전반적인 키보드/손쉬운 사용 및 UI 지원이 향상되었습니다.
* 이동 경로 기록 텍스트가 길면 공백이 추가되어 줄이 바뀝니다.
* SAS 대화 상자에서 입력 유효성 검사가 지원됩니다.
* 사용자 자격 증명이 만료되어도 로컬 스토리지는 계속 사용할 수 있습니다.
* 열었던 Blob 컨테이너를 삭제하면 오른쪽의 Blob 탐색기가 닫힙니다.

#### <a name="known-issues"></a>알려진 문제

* Ubuntu 설치 시에는 gcc 버전을 업데이트하거나 업그레이드해야 합니다. 업그레이드 단계는 다음과 같습니다.
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>버전 0.7.20151116.0

#### <a name="new"></a>신규

* macOS 및 Windows 버전이 지원됩니다.
* 조직 계정, Microsoft 계정, 2FA 등을 사용하여 로그인해 Storage 계정을 확인할 수 있습니다.
* 로컬 개발 스토리지가 제공됩니다(Storage 에뮬레이터 사용, Windows 전용).
* Azure Resource Manager 및 클래식 리소스가 지원됩니다.
* Blob, 큐 또는 테이블을 만들고 삭제할 수 있습니다.
* 특정 Blob, 큐 또는 테이블을 검색할 수 있습니다.
* Blob 컨테이너의 내용을 탐색할 수 있습니다.
* 디렉터리를 보고 디렉터리 간을 이동할 수 있습니다.
* Blob와 폴더를 업로드, 다운로드 및 삭제할 수 있습니다.
* blob 속성 및 메타데이터 보기 및 편집
* SAS 키를 생성할 수 있습니다.
* SAP(저장된 액세스 정책)를 관리하고 만들 수 있습니다.
* 접두사별로 blob 검색
* 파일을 끌어서 놓는 방식으로 업로드하거나 다운로드할 수 있습니다.

#### <a name="known-issues"></a>알려진 문제

* Blob 컨테이너 공용 액세스 수준을 설정할 때 컨테이너에서 포커스를 다시 설정할 때까지 새 값이 업데이트되지 않습니다.
* 공용 액세스 수준을 설정하기 위해 대화 상자를 열면 실제 현재 값이 아닌 "공용 액세스 권한 없음"이 항상 기본값으로 표시됩니다.
* 다운로드한 Blob 이름을 바꿀 수 없습니다.
* 오류 발생 시 활동 로그 항목이 가끔씩 진행 중인 상태에서 "중단"되고 오류가 표시되지 않습니다.
* 많은 Blob를 업로드하거나 다운로드하려고 할 때 가끔씩 작동이 중단되거나 완전히 흰색 화면으로 바뀔 수 있습니다.
* 가끔씩 복사 작업을 취소할 수 없습니다.
* 컨테이너(Blob/테이블/큐)를 만드는 동안 잘못된 이름을 입력하고 다른 컨테이너 형식으로 다른 컨테이너 만들기를 진행하는 경우 새 형식에 포커스를 설정할 수 없습니다.
* 새 폴더를 만들거나 폴더 이름을 바꿀 수 없습니다.




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
