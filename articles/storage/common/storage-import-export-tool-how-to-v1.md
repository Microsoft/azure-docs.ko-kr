---
title: Azure Import/Export 도구 사용 - v1 | Microsoft Docs
description: Import/Export 도구를 사용하여 가져오기 작업을 위한 하드 드라이브 준비, 가져오기 작업 복구 또는 내보내기 작업 복구 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 1/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6116212ae81eb334386eebab057af967309370d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478541"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Azure Import/Export 도구 사용(클래식 배포 모델)

Azure Import/Export 도구(WAImportExport.exe)는 Azure Import/Export 서비스의 작업을 만들고 관리하는 데 사용되어 Azure Blob Storage 내부 또는 외부로 대량의 데이터를 전송할 수 있게 해줍니다.

이 설명서는 Azure Import/Export 도구의 클래식 배포 모델에 대한 것입니다. 최신 버전의 도구를 사용하는 방법에 대한 내용은 [Azure Import/Export 도구 사용](../storage-import-export-tool-how-to.md)을 참조하세요.

다음 문서에서는 해당 방법을 보여 줍니다.

- Import/Export 도구를 설치 및 설정합니다.
- 드라이브에서 Azure Blob Storage로 데이터를 가져오는 작업을 위한 하드 드라이브를 준비합니다.
- 로그 파일 복사를 사용하여 작업 상태를 검토합니다. 
- 가져오기 작업을 복구합니다. 
- 내보내기 작업을 복구합니다. 
- 프로세스 중에 문제가 발생한 경우 Azure Import/Export 도구 문제를 해결합니다. 

## <a name="next-steps"></a>다음 단계

* [WAImportExport 도구 설정](../storage-import-export-tool-how-to.md)
