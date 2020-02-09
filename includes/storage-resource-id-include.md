---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249939"
---
Azure AD 리소스 ID는 발급 된 토큰을 사용 하 여 Azure 리소스에 대 한 액세스를 제공 하는 데 사용할 수 있는 대상을 나타냅니다. Azure Storage의 경우 리소스 ID는 단일 저장소 계정에 특정 될 수도 있고 모든 저장소 계정에 적용 될 수도 있습니다. 다음 표에서는 리소스 ID에 대해 제공할 수 있는 값에 대해 설명 합니다.

|리소스 ID  |설명  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 지정 된 저장소 계정에 대 한 서비스 엔드포인트입니다. 이 값을 사용 하 여 특정 Azure Storage 계정 및 서비스만 요청에 대 한 권한을 부여 하는 토큰을 가져옵니다. 괄호 안의 값을 저장소 계정의 이름으로 바꿉니다.      |
|`https://storage.azure.com/`     | 를 사용 하 여 Azure Storage 계정에 대 한 요청을 승인 하는 토큰을 가져옵니다.        |
