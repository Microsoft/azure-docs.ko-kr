---
title: 어플라이언스를 사용하여 Azure로 데이터를 전송하는 옵션 | Microsoft Docs
description: Azure로 데이터를 전송할 적절한 어플라이언스를 선택하는 방법 알아보기
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: d727ed62f5a869f6eb67400281d2660607756abf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637504"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>StorSimple을 Azure 파일 동기화 및 Data Box Edge 데이터 전송 옵션과 비교 
 
이 문서에서는 온-프레미스 데이터를 Azure로 전송하는 옵션을 대략적으로 설명하고 Data Box Edge, Azure 파일 동기화, StorSimple 8000 시리즈를 비교합니다.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – Data Box Edge는 Azure와 데이터를 주고 받을 수 있는 온-프레미스 네트워크 디바이스로, 업로드하는 동안 데이터를 사전 처리하는 AI 지원 Edge 컴퓨팅을 제공합니다. Data Box Gateway는 동일한 데이터 전송 기능을 제공하는 가상 디바이스 버전입니다.
- **[Azure 파일 동기화](/azure/storage/files/storage-sync-files-deployment-guide)** – Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. Azure 파일 동기화의 일반 공급은 2018년 초에 발표되었습니다.
- **[StorSimple](/azure/storsimple/storsimple-overview)** – StorSimple은 기업에서 기본 스토리지, 데이터 보호, 보관 및 재해 복구를 위해 Azure 스토리지와 긴밀하게 통합하여 스토리지 인프라를 단일 솔루션에 통합할 수 있도록 도와주는 하이브리드 디바이스입니다. StorSimple의 제품 수명 주기는 [여기](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)서 찾을 수 있습니다.

## <a name="comparison-summary"></a>비교 요약

|                           |StorSimple 8000   |Azure 파일 동기화   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|개요         |계층화된 하이브리드 스토리지 및 보관|클라우드 계층화 및 다중 사이트 동기화를 사용하는 일반 파일 서버 스토리지.  |데이터를 사전 처리하여 네트워크를 통해 Azure로 보내는 스토리지 솔루션.        |
|시나리오        |파일 서버, 보관, 백업 대상 |파일 서버, 보관(다중 사이트)   |데이터 전송, 데이터 사전 처리(ML 추론, IoT, 보관 포함)    |
|Edge 컴퓨팅     |사용할 수 없음 |사용할 수 없음 |Azure IoT Edge를 사용한 컨테이너 실행 지원    |
|폼 팩터      |물리적 디바이스   |Windows Server에 에이전트 설치 |물리적 디바이스   |
|하드웨어         |서비스의 일부로 Microsoft에서 물리적 디바이스 제공 | 고객이 제공 |서비스의 일부로 Microsoft에서 물리적 디바이스 제공  |
|데이터 형식      |사용자 지정 형식   |파일         |Blob 또는 파일    |
|프로토콜 지원 |iSCSI          |SMB, NFS    | SMB 또는 NFS      |
|가격          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure 파일 동기화](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>다음 단계

- [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) 및 [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)에 대한 자세한 정보
- [Azure 파일 동기화](/azure/storage/files/storage-sync-files-deployment-guide)에 대한 자세한 정보
