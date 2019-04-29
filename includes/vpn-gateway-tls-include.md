---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 07/27/2018
ms.date: 01/21/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 0d5c3b55d20be19d4aeb92b82d6e44d417259a7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320106"
---
1. **명령 프롬프트**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택하여 상승된 권한으로 명령 프롬프트를 엽니다.
2. 명령 프롬프트에서 다음 명령을 실행합니다.

   ```
   reg add HKLM\SYSTEM\CurrentControlSet\Services\RasMan\PPP\EAP\13 /v TlsVersion /t REG_DWORD /d 0xfc0
   reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   if %PROCESSOR_ARCHITECTURE% EQU AMD64 reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   ```

3. 다음 업데이트를 설치합니다.
  
   * [KB3140245](https://www.catalog.update.microsoft.com/search.aspx?q=kb3140245)
   * [KB2977292](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2977292)

4. 컴퓨터를 다시 부팅합니다.
5. VPN에 연결합니다.

> [!NOTE]
> 이전 버전의 Windows 10(10240)을 실행 중인 경우 위의 레지스트리 키를 설정해야 합니다.
>
