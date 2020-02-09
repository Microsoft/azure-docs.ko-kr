---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391720"
---
아니요: 서버 엔드포인트을 제거 하는 것은 서버를 다시 부팅 하는 것과 비슷합니다. 서버 엔드포인트을 제거 하 고 다시 만들면 동기화, 클라우드 계층화 또는 Azure File Sync의 다른 측면에서 발생 하는 문제를 해결 하기 위한 적절 한 솔루션은 거의 없습니다. 서버 엔드포인트 제거는 파괴적인 작업입니다. 계층화 된 파일이 서버 엔드포인트 네임 스페이스 외부에 존재 하는 경우 데이터 손실이 발생할 수 있습니다. 자세한 내용은 [서버 엔드포인트 네임 스페이스 외부에 계층화 된 파일이 존재 하는 이유](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) 를 참조 하세요. 또는 서버 엔드포인트 네임 스페이스 내에 있는 계층화 된 파일에 액세스할 수 없는 파일이 생성 될 수 있습니다. 이러한 문제는 서버 엔드포인트을 다시 만들 때 해결 되지 않습니다. 클라우드 계층화를 사용하도록 설정하지 않아도 계층화된 파일이 서버 엔드포인트 네임스페이스에 있을 수 있습니다. 따라서이 특정 폴더에서 Azure File Sync 사용을 중지 하거나 Microsoft 엔지니어가 명시적으로이 작업을 수행 하도록 지시 하지 않는 한 서버 엔드포인트을 제거 하지 않는 것이 좋습니다. 서버 엔드포인트 제거에 대한 자세한 내용은 [서버 엔드포인트 제거](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)를 참조하세요.    
