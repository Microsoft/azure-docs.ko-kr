---
title: Linux의 App Service에 대한 SSH 지원 - Azure | Microsoft Docs
description: Linux의 Azure App Service에서 SSH 사용에 대해 자세히 알아봅니다.
keywords: azure app service, 웹앱, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d84a4dd0b69ce9ca7fc594dffce3238c620c426
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852656"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Linux의 Azure App Service에 대한 SSH 지원

[SSH(Secure Shell)](https://wikipedia.org/wiki/Secure_Shell)는 주로 명령줄 터미널에서 원격으로 관리 명령을 실행하는 데 사용합니다. Linux의 App Service는 새 웹앱의 런타임 스택에 사용되는 기본 제공 Docker 이미지가 있는 앱 컨테이너에 SSH 지원을 제공합니다. 

![런타임 스택](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

사용자 지정 Docker 이미지의 경우 사용자 지정 이미지에서 SSH 서버를 구성하여 지원합니다.

SSH 및 SFTP를 사용하여 로컬 개발 컴퓨터에서 직접 컨테이너에 연결할 수도 있습니다.

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>사용자 지정 Docker 이미지를 사용한 SSH 지원 사용

참조 [구성 SSH 사용자 지정 컨테이너에서](configure-custom-container.md#enable-ssh)합니다.

## <a name="open-ssh-session-from-remote-shell"></a>원격 셸에서 SSH 세션 열기

> [!NOTE]
> 이 기능은 현재 미리 보기로 제공됩니다.
>

TCP 터널링을 사용하여 인증된 WebSocket 연결을 통해 개발 컴퓨터와 Web App for Containers 간의 네트워크 연결을 만들 수 있습니다. 선택한 클라이언트의 App Service에서 실행 중인 컨테이너를 사용하여 SSH 세션을 열 수 있습니다.

시작하려면 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치해야 합니다. Azure CLI를 설치하지 않고 작동 방식을 확인하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 엽니다. 

[az webapp 원격 연결 만들기](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) 명령을 사용하여 앱에 원격 연결을 엽니다. 지정할  _\<구독 id >_,  _\<그룹-이름 >_ 고 \_앱에 대 한 < 앱 이름 > _.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> 명령의 끝에 `&`은 Cloud Shell을 사용하는 경우 단지 편의를 위해 제공됩니다. 동일한 셸에서 다음 명령을 실행할 수 있도록 백그라운드에서 프로세스를 실행합니다.

명령 출력은 SSH 세션을 여는 데 필요한 정보를 제공합니다.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

로컬 포트를 사용하여 사용자가 선택한 클라이언트를 통해 컨테이너가 있는 SSH 세션을 엽니다. 다음 예제에서는 기본 [ssh](https://ss64.com/bash/ssh.html) 명령을 사용합니다.

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

메시지가 표시되면 `yes`을 입력하여 연결을 계속합니다. 그러면 암호를 입력하라는 메시지가 나타납니다. 앞부분에 표시된 `Docker!`을 사용합니다.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

인증되면 세션 시작 화면이 표시되어야 합니다.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

이제 커넥터에 연결되어 있습니다.  

[top](https://ss64.com/bash/top.html) 명령을 다시 실행합니다. 프로세스 목록에서 앱의 프로세스를 확인할 수 있어야 합니다. 아래 예제 출력에서는 `PID 263`이 있는 출력입니다.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>다음 단계

[Azure 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

컨테이너용 웹앱에 대한 자세한 내용은 다음을 참조하세요.

* [VS Code의 Azure App Service에서 Node.js 앱의 원격 디버깅 소개](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Web App for Containers에 사용자 지정 Docker 이미지를 사용하는 방법](quickstart-docker-go.md)
* [Linux의 Azure App Service에서 .NET Core 사용](quickstart-dotnetcore.md)
* [Linux의 Azure App Service에서 Ruby 사용](quickstart-ruby.md)
* [Containers용 Azure App Service Web App 관련 FAQ](app-service-linux-faq.md)
