---
title: Azure Import/Export 도구 가져오기 작업 명령에 대한 빠른 참조 - v1 | Microsoft Docs
description: 자주 사용되는 가져오기 작업 명령에 대한 Azure Import/Export 도구 명령 참조 Import/Export 도구 v1을 나타냅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 44ee23a510bc15d5cb52a338be1652180b922db9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478439"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>가져오기 작업에 자주 사용 되는 명령에 대한 빠른 참조
이 섹션에서는 몇 가지 자주 사용하는 명령에 대한 빠른 참조를 제공합니다. 자세한 사용법은 [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md)를 참조하세요.  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>데이터가 하드 드라이브에 이미 복사된 경우 하드 드라이브 준비
 다음 명령은 데이터가 하드 드라이브에 이미 복사되었으나 아직 BitLocker로 암호화되지 않은 경우 하드 드라이브를 준비합니다.  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>하드 드라이브에 단일 디렉터리 복사  
 다음 명령은 BitLocker로 아직 암호화되지 않은 하드 드라이브에 단일 원본 디렉터리를 복사합니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>하드 드라이브에 2개의 디렉터리 복사  
 두 개의 원본 디렉터리를 드라이브에 복사하려면 다음 명령을 사용합니다.  
  
 첫 번째 명령은 로그 디렉터리, 저장소 계정 키, 대상 드라이브 문자 및 `format/encrypt` 요구 사항 및 일반적인 매개 변수를 지정합니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 두 번째 명령은 저널 파일, 새 세션 ID 및 소스와 대상 위치를 지정합니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>두 번째 복사 세션에서 하드 드라이브에 큰 파일 복사  
 다음 명령은 이전 복사 세션에서 준비된 하드 드라이브에 하나의 큰 파일을 복사합니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>다음 단계

* [가져오기 작업을 위한 하드 드라이브를 준비하는 샘플 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
