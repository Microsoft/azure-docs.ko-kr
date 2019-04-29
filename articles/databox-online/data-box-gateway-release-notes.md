---
title: Azure 데이터 상자 게이트웨이 일반 공급 릴리스 정보 | Microsoft Docs
description: 일반 공급 릴리스를 실행 하는 Azure 데이터 상자 게이트웨이에 대 한 중요 한 미해결 문제 및 해결책을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754202"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure 데이터 상자에 지/Azure 데이터 상자 게이트웨이 일반 공급 릴리스 정보

## <a name="overview"></a>개요

다음 릴리스 정보는 중요 한 미해결 문제를 식별 하 고 Azure 데이터 가장자리가 상자 및 Azure 데이터 상자 게이트웨이에 대 한 해결된 된 문제에 대 한 GA (일반 공급) 릴리스 합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. 데이터 상자 Edge/데이터 상자 게이트웨이 배포 하기 전에 릴리스 정보에 포함 된 정보를 주의 깊게 검토 합니다.

GA 릴리스 소프트웨어 버전에 해당 됩니다.

- **데이터 상자 게이트웨이 1903 (1.5.814.447)**
- **데이터 상자 가장자리 1903 (1.5.814.447)**


## <a name="whats-new"></a>새로운 기능

- **새 가상 디스크 이미지** -새 VHDX 및 VMDK 이제 Azure portal에 표시 됩니다. 프로 비전, 구성 및 새 GA 데이터 상자 게이트웨이 장치를 배포 하려면 이러한 이미지를 다운로드 합니다. 이전 미리 보기 릴리스에서 만든 Data Box Gateway 디바이스는 이 버전으로 업데이트할 수 없습니다. 자세한 내용은 [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)를 참조하세요.
- **NFS 지원** -NFS 지원은 현재 미리 보기로 제공 되며 v3.0 및 v4.1에 대 한 사용 가능한 데이터 상자 가장자리와 데이터 상자 게이트웨이 장치에 액세스 하는 클라이언트입니다.
- **저장소 복원 력** -Your 데이터 가장자리가 상자의 장치 저장소 복원 력 기능을 사용 하 여 하나 이상의 데이터 디스크 오류를 견딜 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다. 저장소 복구를 선택 하 여 설정할 수 있습니다.는 **복원** 옵션을 **저장소 설정** 로컬 웹 UI입니다.


## <a name="known-issues-in-ga-release"></a>GA 릴리스의 알려진된 문제

다음 표에서 알려진된 문제가 요약 되어 데이터 상자 게이트웨이에 대 한 릴리스를 실행 합니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |파일 형식 | 파일 형식은 지원 되지 않습니다: 파일, 파일 차단, 소켓, 파이프, 기호화 된 링크 문자입니다.  |이 파일을 복사하면 길이가 0인 파일이 NFS 공유에 만들어집니다. 이들 파일은 오류 상태로 남아 있으며 또한 *error.xml*에 보고됩니다. <br> 디렉터리로 이동하는 바로 가기 링크로 인해 디렉터리가 오프라인으로 표시되지 않습니다. 따라서 디렉터리가 오프라인이며 모든 관련 콘텐츠가 Azure에 완전히 업로드되었음을 나타내는 회색 십자 표시가 디렉터리에 표시되지 않을 수 있습니다. |
| **2.** |삭제 | 이 릴리스의 버그로 인해 NFS 공유를 삭제하는 경우 공유가 삭제되지 않을 수 있습니다. 공유 상태가 *삭제 중*으로 표시됩니다.  |이 오류는 공유에서 지원되지 않는 파일 이름을 사용하는 경우에 발생합니다. |
| **3.** |복사 | 데이터 복사가 실패하고  "파일 시스템 제한으로 인해 요청한 작업을 완료할 수 없습니다." 오류가 표시됩니다.  |대체 데이터 Stream (광고) 128KB 보다 큰 파일 크기와 관련 된 지원 되지 않습니다.   |


## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md).
- [Azure 데이터 가장자리가 상자의 배포 준비](data-box-edge-deploy-prep.md)합니다.
