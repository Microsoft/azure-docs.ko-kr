---
title: Azure 서비스 중단이 Azure Key Vault에 영향을 주는 경우 수행할 작업 - Azure Key Vault | Microsoft Docs
description: Azure 서비스 중단이 Azure Key Vault에 영향을 주는 경우 수행할 작업에 대해 알아봅니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 35814f34550ac7bf4ad85a96d0838df62fe63be6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073185"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault 가용성 및 중복성

Azure Key Vault에는 서비스의 개별 구성 요소가 실패해도 애플리케이션에서 키 및 암호를 사용할 수 있도록 해주는 여러 계층의 중복성이 있습니다.

주요 자격 증명 모음의 내용은 키와 비밀의 높은 내구성을 유지하기 위해 지역 내에 복제되고 동일한 지리 내 최소 150마일 떨어진 보조 지역에도 복제됩니다. 특정 지역 쌍에 대한 자세한 내용은 [Azure 쌍을 이루는 지역](../../best-practices-availability-paired-regions.md) 문서를 참조하세요.

주요 자격 증명 모음 서비스 내에서 개별 구성 요소가 실패하면 기능이 저하되지 않도록 하기 위해 해당 지역 내의 대체 구성 요소가 요청을 처리하도록 개입됩니다. 이 프로세스를 시작하기 위해 어떤 작업도 수행할 필요가 없습니다. 이 프로세스는 자동으로 수행되며 사용자에게 투명하게 공개됩니다.

전체 Azure 지역을 사용할 수 없는 드문 경우, 해당 지역에 Azure Key Vault를 활용하는 요청이 보조 지역으로 자동 라우팅(*장애 조치*)됩니다. 기본 지역을 다시 사용할 수 있는 경우 요청은 주 지역으로 다시 라우팅(*장애 복구(failback)* )됩니다. 다시 한 번 말씀드리지만, 이 작업은 자동으로 이루어지므로 어떤 조치도 필요하지 않습니다.

이 고가용성 디자인을 통해 Azure Key Vault는 유지 관리 작업을 위한 가동 중지 시간이 필요하지 않습니다.

알고 있어야 하는 몇 가지 주의 사항이 있습니다.

* 지역 장애 조치 시 서비스를 장애 조치하는 데 몇 분 정도 걸릴 수 있습니다. 장애 조치(failover) 전에 이 시간 동안 이루어진 요청이 실패할 수 있습니다.
* 장애 조치(failover) 동안 주요 자격 증명 모음은 읽기 전용 모드입니다. 이 모드에서 지원되는 요청은 다음과 같습니다.
  * 인증서 나열
  * 인증서 가져오기
  * 암호 나열
  * 암호 가져오기
  * 키 나열
  * 키(키의 속성) 가져오기
  * Encrypt
  * 암호 해독
  * 래핑
  * 래핑 취소
  * 확인
  * 로그인
  * Backup
* 장애 조치(failover) 중에는 주요 자격 증명 모음 속성을 변경할 수 없습니다. 액세스 정책 또는 방화벽 구성 및 설정을 변경할 수 없습니다.
* 장애 조치가 장애 복구되면 모든 요청 유형( 읽기 *및* 쓰기 요청 포함)을 사용할 수 있습니다.

