---
title: Azure Storage에 AppSource 패키지 저장 및 SAS 키를 사용하여 URL 생성 | Microsoft Docs
description: AppSource 패키지를 업로드하고 보호하기 위해 수행해야 하는 단계를 자세히 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ad0e6eaae5c0fad74ea484827e0f8d535cfbf579
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750065"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Azure Storage에 AppSource 패키지 저장 및 SAS 키를 사용하여 URL 생성
=============================================================================

파일의 보안을 유지하려면 모든 파트너가 Azure Blob Storage 계정에 AppSource 패키지 파일을 저장하고 SAS 키를 사용하여 해당 파일을 공유해야 합니다. Microsoft는 Azure 저장소 위치에서 패키지 파일을 검색하여 인증을 하고 AppSource 평가판에 사용합니다.

Blob Storage에 패키지를 업로드하려면 다음 단계를 수행합니다.

1. <https://azure.microsoft.com>으로 이동하여 무료 평가판 또는 유료 계정을 만듭니다.

2. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

3. **+ 새로 만들기**를 클릭한 다음 **데이터 + 저장소** 계정으로 이동하여 새 저장소 계정을 만듭니다.

   ![Microsoft Azure Portal의 데이터 + 저장소 블레이드](media/CRMScreenShot7.png)

4. **이름** 및 **리소스 그룹** 이름을 입력하고 **만들기** 단추를 클릭합니다.

   ![Microsoft Azure 포털에서 저장소 계정 만들기](media/CRMScreenShot8.png)

5. 새로 만든 리소스 그룹으로 이동하여 새 Blob 컨테이너를 만듭니다.

   ![Microsoft Azure Portal을 사용하여 Blob으로 패키지 업로드](media/CRMScreenShot9.png)

6. Microsoft [Azure Storage 탐색기](https://storageexplorer.com/)를 아직 다운로드하여 설치하지 않은 경우 다운로드/설치합니다.

7. Storage 탐색기를 열고 아이콘을 사용하여 Azure 스토리지 계정에 연결합니다.

8. 앞에서 만든 Blob 컨테이너로 이동한 다음 **업로드**를 클릭하여 패키지 zip 파일을 추가합니다.

   ![Microsoft Storage 탐색기를 사용하여 패키지 업로드](media/CRMScreenShot10.png)

9. 파일을 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기**를 선택합니다.

   ![Azure 파일의 공유 액세스 서명 가져오기](media/CRMScreenShot11.png)

10. SAS가 1개월 동안 활성 상태로 유지되도록 **만료 시간**을 수정하고 **만들기**를 클릭합니다.

    ![Azure 파일의 SAS 만료 날짜 수정](media/CRMScreenShot12.png)

11. URL 필드의 내용을 복사한 다음 나중에 사용할 수 있도록 저장합니다. 연결된 제품을 만들 때 이 URL을 입력해야 합니다. 

    ![Azure 파일의 SAS URL 복사](media/CRMScreenShot13.png)

