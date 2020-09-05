---
title: 자격 증명 모음을 다른 리소스 그룹으로 이동 하는 Azure Key Vault | Microsoft Docs
description: 키 자격 증명 모음을 다른 리소스 그룹으로 이동 하는 방법에 대 한 지침입니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: fe8051d551077666c06ac033f22303fd643ac602
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585736"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>리소스 그룹 간에 Azure Key Vault 이동

## <a name="overview"></a>개요

키 자격 증명 모음을 리소스 그룹 간에 이동 하는 것은 지원 되는 주요 자격 증명 모음 기능입니다. 리소스 그룹 간에 키 자격 증명 모음을 이동 해도 키 자격 증명 모음 방화벽이 나 액세스 정책 구성에는 영향을 주지 않습니다. 연결 된 응용 프로그램 및 서비스 주체는 의도 한 대로 계속 작동 해야 합니다.

## <a name="design-considerations"></a>디자인 고려 사항

조직에서는 리소스 그룹 수준에서 적용 또는 제외를 사용 하 Azure Policy를 구현 했을 수 있습니다. 현재 키 자격 증명 모음이 있는 리소스 그룹 및 주요 자격 증명 모음을 이동 하는 리소스 그룹에는 다른 정책 할당 집합이 있을 수 있습니다. 정책 요구 사항의 충돌로 인해 애플리케이션이 중단될 수 있습니다.

### <a name="example"></a>예제

2년 동안 유효한 인증서를 생성하는 키 자격 증명 모음에 연결된 애플리케이션이 있습니다. 키 자격 증명 모음을 이동 하려는 리소스 그룹에는 1 년 이상 유효한 인증서 생성을 차단 하는 정책 할당이 있습니다. 키 자격 증명 모음을 새 리소스 그룹으로 이동한 후 2 년 동안 유효한 인증서를 만드는 작업은 Azure 정책 할당에 의해 차단 됩니다.

### <a name="solution"></a>솔루션

Azure Portal의 Azure Policy 페이지로 이동 하 고, 이동 하는 리소스 그룹 뿐만 아니라 현재 리소스 그룹에 대 한 정책 할당을 확인 하 고 불일치가 없는지 확인 하십시오.

## <a name="procedure"></a>프로시저

1. Azure Portal에 로그인
2. 키 자격 증명 모음으로 이동
3. “개요” 탭을 클릭
4. “이동” 단추를 선택
5. 드롭다운 옵션에서 "다른 리소스 그룹으로 이동" 선택
6. 키 자격 증명 모음을 이동할 리소스 그룹을 선택
7. 리소스 이동과 관련된 경고 승인
8. "확인" 선택

이제 Key Vault에서 리소스 이동의 유효성을 평가 하 고 오류를 경고 합니다. 오류가 발견 되지 않으면 리소스 이동이 완료 됩니다. 
