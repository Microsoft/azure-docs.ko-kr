---
title: Azure Container Instances에서 실행 중인 컨테이너에서 명령 실행
description: Azure Container Instances에서 현재 실행 중인 컨테이너에서 명령을 실행하는 방법에 대해 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60579749"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>실행 중인 Azure Container Instances에서 명령 실행

Azure Container Instances는 실행 중인 컨테이너에서 명령을 실행하도록 지원합니다. 시작한 컨테이너에서 명령을 실행하면 애플리케이션 개발 및 문제 해결 중에 특히 유용합니다. 이 기능은 일반적으로 실행 중인 컨테이너에서 문제를 디버깅할 수 있도록 대화형 셸을 실행하는 데 사용됩니다.

## <a name="run-a-command-with-azure-cli"></a>Azure CLI를 사용하여 명령 실행

[Azure CLI][azure-cli]에서 실행 중인 컨테이너에서 [az container exec][az-container-exec]으로 명령을 실행합니다.

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

예를 들어 Nginx 컨테이너에서 Bash 셸을 시작하려면 다음을 수행합니다.

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

다음 예제 출력에서 Bash 셸은 실행 중인 Linux 컨테이너에서 시작되어 `ls`를 실행할 터미널을 제공합니다.

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

이 예제에서 명령 프롬프트는 실행 중인 Nanoserver 컨테이너에서 실행됩니다.

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>다중 컨테이너 그룹

[컨테이너 그룹](container-instances-container-groups.md)에 애플리케이션 컨테이너 및 로깅 사이드카 등의 여러 컨테이너가 있는 경우 `--container-name`을 사용하여 명령을 실행할 컨테이너의 이름을 지정합니다.

예를 들어 컨테이너 그룹에서 *mynginx*는 *nginx-app* 및 *logger*라는 두 개의 컨테이너입니다. *nginx-app* 컨테이너에서 셸을 시작하려면:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>제한

현재 Azure Container Instances에서는 [az container exec][az-container-exec]를 사용하여 단일 프로세스를 시작하도록 지원합니다. 또한 명령 인수를 전달할 수 없습니다. 예를 들어 `sh -c "echo FOO && echo BAR"`에서 명령을 연결하거나 `echo FOO`를 실행할 수 없습니다.

## <a name="next-steps"></a>다음 단계

다른 문제 해결 도구 및 일반적인 배포 문제에 대한 자세한 내용은 [Azure Container Instances에서 컨테이너 및 배포 문제 해결](container-instances-troubleshooting.md)에서 자세히 알아봅니다.

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure