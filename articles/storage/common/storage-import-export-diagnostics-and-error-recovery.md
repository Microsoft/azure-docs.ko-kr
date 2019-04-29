---
title: Azure Import/Export 작업에 대한 진단 및 오류 복구 | Microsoft Docs
description: Microsoft Azure Import/Export 서비스 작업에 대해 자세한 정보 로깅을 설정하는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306b94fbe23e0ae92dcd59f7a87b7bb58ef7c3b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478813"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Azure Import/Export 작업에 대한 진단 및 오류 복구
처리된 각 드라이브의 경우 Azure Import/Export 서비스가 연결된 저장소 계정에 오류 로그를 만듭니다. [작업 배치](/rest/api/storageimportexport/jobs) 또는 [업데이트 작업 속성](/rest/api/storageimportexport/jobs) 작업을 호출할 때 `LogLevel` 속성을 `Verbose`로 설정하여 자세한 정보 로깅을 사용하도록 설정할 수도 있습니다.

 기본적으로 로그는 `waimportexport`라는 컨테이너에 기록됩니다. `Put Job` 또는 `Update Job Properties` 조작을 호출할 때 `DiagnosticsPath` 속성을 설정하여 다른 이름을 지정할 수 있습니다. 로그는 다음 명명 규칙을 사용하여 블록 Blob으로 저장됩니다. `waies/jobname_driveid_timestamp_logtype.xml`.

 [작업 가져오기](/rest/api/storageimportexport/jobs) 작업을 호출하여 작업의 로그 URI를 검색할 수 있습니다. 자세한 정보 로그의 URI는 각 드라이브의 `VerboseLogUri` 속성에서 반환되는 반면 오류 로그의 URI는 `ErrorLogUri` 속성에서 반환됩니다.

로깅 데이터를 사용하여 다음 문제를 식별할 수 있습니다.

## <a name="drive-errors"></a>드라이브 오류

다음 항목은 드라이브 오류로 분류됩니다.

-   매니페스트 파일 액세스 또는 읽기 오류

-   잘못된 BitLocker 키

-   드라이브 읽기/쓰기 오류

## <a name="blob-errors"></a>Blob 오류

다음 항목은 Blob 오류로 분류됩니다.

-   잘못되었거나 충돌하는 Blob 또는 이름

-   누락된 파일

-   Blob을 찾을 수 없음

-   잘린 파일(디스크의 파일이 매니페스트에 지정된 것보다 작음)

-   손상된 파일 콘텐츠(가져오기 작업의 경우 MD5 체크섬 불일치로 감지됨)

-   손상된 Blob 메타데이터 및 속성 파일(MD5 체크섬 불일치로 감지됨)

-   Blob 속성 및/또는 메타데이터 파일에 대한 잘못된 스키마

가져오기 또는 내보내기 작업의 일부가 성공적으로 완료되지 않은 상태에서 전체 작업은 여전히 완료되는 경우가 있을 수 있습니다. 이 경우 누락된 데이터를 네트워크를 통해 업로드 또는 다운로드하거나 새 작업을 만들어 데이터를 전송할 수 있습니다. 네트워크를 통해 데이터를 복구하는 방법을 알아보려면 [Azure Import/Export 도구 참조](storage-import-export-tool-how-to-v1.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
