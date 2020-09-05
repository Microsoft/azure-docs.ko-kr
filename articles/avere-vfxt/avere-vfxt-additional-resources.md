---
title: Avere vFXT for Azure에 대한 추가 링크
description: Azure의 Avere vFXT에 대 한 추가 정보는 다음 리소스를 사용 하 여 Avere cluster 설명서 및 vFXT 관리 설명서를 참조 하세요.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271093"
---
# <a name="additional-documentation"></a>추가 설명서

이 문서에는 Avere 제어판 관리 인터페이스 및 관련 항목에 대한 추가 설명서 링크가 있습니다.

## <a name="avere-cluster-documentation"></a>Avere 클러스터 설명서

추가 Avere 클러스터 설명서는 <https://azure.github.io/Avere/> 웹 사이트에서 찾을 수 있습니다. 클러스터의 기능 및 설정을 구성하는 방법을 이해하는 데 도움이 되는 문서는 다음과 같습니다.

* [FXT 클러스터 만들기 가이드](<https://azure.github.io/Avere/#fxt_cluster>)는 물리적 하드웨어 노드로 구성된 클러스터를 위해 설계되었지만, 문서의 정보 중 일부는 vFXT 클러스터와도 관련이 있습니다. 특히 새 vFXT 클러스터 관리자는 다음 섹션을 참조하여 이점을 얻을 수 있습니다.
  * [지원 및 모니터링 설정 사용자 지정](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>)에서는 지원 업로드 설정을 사용자 지정하고, 원격 모니터링을 사용하도록 설정하는 방법을 설명합니다.
  * [VServers 및 글로벌 네임스페이스 구성](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>)에는 클라이언트 측 네임스페이스를 만드는 방법에 대한 정보가 나와 있습니다.
  * [Avere 클러스터에 대한 DNS를 구성](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>)에서는 라운드 로빈 DNS를 구성하는 방법을 설명합니다.
  * [백 엔드 저장소](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) 문서를 추가 하면 핵심 filers를 추가 하는 방법에 대해 설명 합니다.

* [클러스터 구성 가이드](<https://azure.github.io/Avere/#operations>)는 Avere 클러스터에 대한 설정 및 옵션에 대한 전체 참조입니다. vFXT 클러스터는 이러한 옵션의 하위 집합을 사용하지만, 동일한 구성 페이지의 대부분이 적용됩니다.

* [대시보드 가이드](<https://azure.github.io/Avere/#operations>)에서는 Avere 제어판의 클러스터 모니터링 기능을 사용하는 방법을 설명합니다.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT 만들기 및 관리 설명서

Vfxt.py 사용에 대 한 전체 가이드 인 스크립트 기반 클라우드 클러스터 만들기 및 관리 유틸리티는 GitHub: [클라우드 클러스터 관리 vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)를 참조 하세요.
