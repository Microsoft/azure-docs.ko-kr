---
title: 서버 수준 방화벽 규칙
description: 서버 수준 방화벽 규칙
keywords: SQL 연결,연결 문자열
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202157"
---
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽에 있는 목록에서 **모든 서비스**를 선택합니다.

3. 스크롤하여 **SQL 서버**를 선택합니다.

    ![포털에서 Azure SQL Database 서버를 찾습니다.][b21-FindServerInPortal]
5. 필터 텍스트 상자에 서버 이름을 입력하기 시작합니다. 해당 행이 표시됩니다.

6. 서버에 대한 행을 선택합니다. 서버 블레이드가 표시됩니다.

7. 서버 블레이드에서 **설정**을 선택합니다.

8. **방화벽**을 선택합니다.

    ![설정 > 방화벽 선택][b31-SettingsFirewallNavig]
9. **클라이언트 IP 추가**를 선택합니다. 첫 번째 텍스트 상자에 새 규칙의 이름을 입력합니다.

10. 사용하도록 설정하려는 범위의 하한 및 상한 IP 주소 값을 입력합니다.

    * 하한 값은 **.0**으로 끝나고 상한 값은 **.255**로 끝나도록 하면 편리할 수 있습니다.

11. **저장**을 선택합니다.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
