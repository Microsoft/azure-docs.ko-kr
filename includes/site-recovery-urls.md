---
title: 포함 파일
description: 포함 파일
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67182728"
---
속성 | 상용 URL | 정부 URL | Description
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Azure Active Directory를 사용한 액세스 제어 및 ID 관리에 사용됩니다. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | 복제 데이터 전송 및 조정에 사용됩니다.
복제 | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | 복제 관리 작업 및 조정에 사용됩니다.
스토리지 | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다.
원격 분석(선택 사항) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | 원격 분석에 사용됩니다.
시간 동기화 | ``time.windows.com`` | ``time.nist.gov`` | 모든 배포에서 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.


