---
title: Container instance에서 entrypoint 재정의
description: Azure container instance를 배포할 때 컨테이너 이미지의 entrypoint를 재정의 하도록 명령줄 설정
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533414"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>컨테이너 인스턴스에서 명령줄을 설정 하 여 기본 명령줄 작업을 재정의 합니다.

컨테이너 인스턴스를 만들 때 필요에 따라 기본 명령줄 명령 구운를 컨테이너 이미지로 재정의 하는 명령을 지정 합니다. 이 동작은 `docker run`에 대한 `--entrypoint` 명령줄 인수와 비슷합니다.

컨테이너 인스턴스의 [환경 변수](container-instances-environment-variables.md) 를 설정 하는 것과 같이 시작 명령줄을 지정 하는 것은 작업 관련 구성을 사용 하 여 각 컨테이너를 동적으로 준비 해야 하는 batch 작업에 유용 합니다.

## <a name="command-line-guidelines"></a>명령줄 지침

* 기본적으로 명령줄은 컨테이너에서 *셸을 제외 하 고 시작 되는 단일 프로세스* 를 지정 합니다. 예를 들어 명령줄에서 Python 스크립트나 실행 파일을 실행할 수 있습니다. 프로세스에서 추가 매개 변수 또는 인수를 지정할 수 있습니다.

* 여러 명령을 실행 하려면 컨테이너 운영 체제에서 지원 되는 셸 환경을 설정 하 여 명령줄을 시작 합니다. 예시:

  |운영 체제  |기본 셸  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |알파인     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  셸의 규칙에 따라 여러 명령을 순서 대로 실행 합니다.

* 컨테이너 구성에 따라 명령줄 실행 파일 또는 인수에 대한 전체 경로를 설정 해야 할 수 있습니다.

* 명령줄에서 장기 실행 태스크를 지정 하는지 아니면 한 번 실행 태스크가 지정 되는지에 따라 컨테이너 인스턴스에 대해 적절 한 [다시 시작 정책을](container-instances-restart-policy.md) 설정 합니다. 예를 들어 `Never` 또는 `OnFailure`의 다시 시작 정책은 한 번 실행 작업에 권장 됩니다. 

* 컨테이너 이미지의 기본 entrypoint 집합에 대한 정보가 필요한 경우 [docker 이미지 검사](https://docs.docker.com/engine/reference/commandline/image_inspect/) 명령을 사용 합니다.

## <a name="command-line-syntax"></a>명령줄 구문

명령줄 구문은 인스턴스를 만드는 데 사용 되는 Azure API 또는 도구에 따라 달라 집니다. 셸 환경을 지정 하는 경우 셸의 명령 구문 규칙도 관찰 됩니다.

* [az container create][az-container-create] 명령: `--command-line` 매개 변수를 사용 하 여 문자열을 전달 합니다. 예: `--command-line "python myscript.py arg1 arg2"`).

* [Get-azurermcontainergroup][new-azurermcontainergroup] Azure PowerShell cmdlet: `-Command` 매개 변수를 사용 하 여 문자열을 전달 합니다. 예: `-Command "echo hello"`.

* Azure Portal: 컨테이너 구성의 **명령 재정의** 속성에서 쉼표로 구분 된 문자열 목록 (따옴표 포함 안 함)을 제공 합니다. 예: `python, myscript.py, arg1, arg2`). 

* 리소스 관리자 템플릿 또는 YAML 파일 또는 Azure Sdk 중 하나: 명령줄 속성을 문자열 배열로 지정 합니다. 예: JSON 배열은 리소스 관리자 템플릿에서 `["python", "myscript.py", "arg1", "arg2"]`. 

  [Dockerfile](https://docs.docker.com/engine/reference/builder/) 구문에 익숙한 경우이 형식은 CMD 명령의 *exec* 형식과 비슷합니다.

### <a name="examples"></a>예시

|    |  Azure CLI   | 포털 | Template | 
| ---- | ---- | --- | --- |
| 단일 명령 | `--command-line "python myscript.py arg1 arg2"` | **명령 재정의**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 여러 명령 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**명령 재정의**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 예제

예를 들어 셰익스피어의 *Hamlet* 텍스트를 분석 하 여 가장 자주 발생 하는 단어를 찾는 [microsoft/aci-wordcount][aci-wordcount] 컨테이너 이미지의 동작을 수정 합니다. *Hamlet*을 분석 하는 대신 다른 텍스트 원본을 가리키는 명령줄을 설정할 수 있습니다.

기본 텍스트를 분석할 때 [microsoft/aci-wordcount][aci-wordcount] 컨테이너의 출력을 보려면 다음 [az container create][az-container-create] 명령을 사용 하 여 실행 합니다. 시작 명령줄이 지정 되지 않았으므로 기본 컨테이너 명령이 실행 됩니다. 설명을 위해이 예제에서는 [환경 변수](container-instances-environment-variables.md) 를 설정 하 여 5 자 이상의 상위 3 개 단어를 찾습니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

컨테이너의 상태가 *종료* 된 것으로 표시 된 후 (상태를 확인 하려면 [az container show][az-container-show] 사용), 출력을 보려면 [az container logs][az-container-logs] 를 사용 하 여 로그를 표시 합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

출력

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

이제 다른 명령줄을 지정 하 여 다른 텍스트를 분석 하는 두 번째 예제 컨테이너를 설정 합니다. *Wordcount.py*컨테이너에서 실행 되는 Python 스크립트는 URL을 인수로 수락 하 고 기본값 대신 해당 페이지의 콘텐츠를 처리 합니다.

예를 들어 *Romeo 및 Juliet*에서 5 자 이상의 상위 3 개 단어를 확인 하려면 다음을 수행 합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

다시 한번 컨테이너가 *Terminated*가 되면 컨테이너의 로그를 표시하여 출력을 봅니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

출력

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>다음 단계

여러 컨테이너가 있는 큰 데이터 집합을 일괄 처리 하는 등의 작업 기반 시나리오는 런타임에 사용자 지정 명령줄을 사용 하 여 이점을 누릴 수 있습니다. 작업 기반 컨테이너를 실행 하는 방법에 대한 자세한 내용은 [컨테이너 화 된 작업을 다시 시작 정책으로 실행](container-instances-restart-policy.md)을 참조 하세요.

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
