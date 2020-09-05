---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74895279"
---
Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대한 추가 제어를 위해 Blob 및 파일 데이터의 암호화에 사용할 고객 관리 키를 제공할 수 있습니다.

고객 관리 키는 Azure Key Vault에 저장 되어야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. 스토리지 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다. 암호화 및 키 관리 Azure Storage에 대 한 자세한 내용은 [미사용 데이터에 대 한 Azure Storage 암호화](../articles/storage/common/storage-service-encryption.md)를 참조 하세요. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../articles/key-vault/key-vault-overview.md)
