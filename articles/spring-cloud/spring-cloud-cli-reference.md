---
title: az 스프링 cloud
description: Azure CLI를 사용 하 여 Azure 스프링 클라우드 관리
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 869a47469f8e0429d3726651c28f5a58acfcb856
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279035"
---
# <a name="az-spring-cloud"></a>az 스프링-cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure 스프링 클라우드 관리

>[!Note]
> Azure Spring Cloud는 현재 미리 보기로 제공되고 있습니다.  이러한 명령은 이후 릴리스에서 변경 되거나 제거 될 수 있습니다.

| az 스프링-cloud |  |
|------|------:|
| [az 스프링-cloud create](#az-spring-cloud-create) | Azure 스프링 클라우드 인스턴스를 만듭니다. |
| [az 스프링-cloud delete](#az-spring-cloud-delete) | Azure 스프링 클라우드 인스턴스를 삭제 합니다. |
| [az 스프링-cloud list](#az-spring-cloud-list) | 지정 된 리소스 그룹의 모든 Azure 스프링 클라우드 인스턴스를 나열 하 고, 그렇지 않은 경우 구독 Id를 나열 합니다. |
| [az 스프링-cloud show](#az-spring-cloud-show) | Azure 스프링 클라우드의 세부 정보를 표시 합니다. |

| az 스프링-cloud app | Azure 스프링 클라우드에서 앱을 관리 하는 명령입니다.  |
| ---- | ----: |
| [az 스프링-cloud app create](#az-spring-cloud-app-create) | Azure 스프링 클라우드의 기본 배포를 사용 하 여 새 앱을 만듭니다. |
| [az 스프링-cloud app delete](#az-spring-cloud-app-delete) | Azure 스프링 클라우드에서 앱을 삭제 합니다. |
| [az 스프링-cloud app deploy](#az-spring-cloud-app-deploy) | 소스 코드 또는 미리 빌드된 이진 파일에서 앱으로 배포 하 고 관련 구성을 업데이트 합니다. |
| [az 스프링-cloud app list](#az-spring-cloud-app-list) | Azure 스프링 클라우드의 모든 앱을 나열 합니다. |
| [az 스프링-cloud app restart](#az-spring-cloud-app-restart) | 프로덕션 배포 기본값을 사용 하 여 앱 인스턴스를 다시 시작 합니다. |
| [az 스프링-cloud app scale](#az-spring-cloud-app-scale) | 앱 또는 해당 배포를 수동으로 확장 합니다. |
| [az 스프링-cloud app set-deployment](#az-spring-cloud-app-set-deployment) | 앱의 프로덕션 배포를 설정 합니다. |
| [az 스프링-cloud app show](#az-spring-cloud-app-show) | Azure 스프링 클라우드의 앱에 대 한 세부 정보를 표시 합니다. |
| [az 스프링-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | 원본에서 최신 배포에 대 한 빌드 로그를 표시 합니다. 기본값은 프로덕션 배포입니다. |
| [az 스프링-cloud app start](#az-spring-cloud-app-start) | 프로덕션 배포 기본값을 사용 하 여 앱의 인스턴스를 시작 합니다. |
| [az 스프링-cloud app stop](#az-spring-cloud-app-stop) | 프로덕션 배포 기본값을 사용 하 여 앱의 인스턴스를 중지 합니다. |
| [az 스프링-cloud app update](#az-spring-cloud-app-update) | 지정 된 앱의 구성을 업데이트 합니다. |

| az 스프링-cloud app binding | Azure Data Services를 사용 하 여 바인딩을 관리 하는 명령입니다.  이러한 설정이 적용 되기 전에 앱을 다시 시작 해야 합니다. |
| --- | ---: |
| [az 스프링-cloud app binding list](#az-spring-cloud-app-binding-list) | 앱의 모든 서비스 바인딩을 나열 합니다. |
| [az 스프링-cloud app binding remove](#az-spring-cloud-app-binding-remove) | 앱에서 서비스 바인딩을 제거 합니다. |
| [az 스프링-cloud app binding show](#az-spring-cloud-app-binding-show) | 서비스 바인딩의 세부 정보를 표시 합니다. |
| [az 스프링-cloud app binding cosmos add](#az-spring-cloud-app-binding-cosmos-add) | 앱을 사용 하 여 Azure CosmosDB를 바인딩합니다. |
| [az 스프링-cloud app binding cosmos update](#az-spring-cloud-app-binding-cosmos-update) | Azure CosmosDB 서비스 바인딩을 업데이트 합니다. |
| [az 스프링-cloud app binding mysql 추가](#az-spring-cloud-app-binding-mysql-add) | 앱에 Azure Database for MySQL을 바인딩합니다. |
| [az 스프링-cloud app binding mysql 업데이트](#az-spring-cloud-app-binding-mysql-update) | Azure Database for MySQL 서비스 바인딩을 업데이트 합니다. |
| [az 스프링-cloud app binding redis add](#az-spring-cloud-app-binding-redis-add) | 앱을 사용 하 여 Redis 용 Azure 캐시를 바인딩합니다. |
| [az 스프링-cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | Redis 서비스 바인딩을 위해 Azure 캐시를 업데이트 합니다. |

| az 스프링-cloud app deployment | Azure 스프링 클라우드의 앱 배포 수명 주기를 관리 하는 명령입니다. |
| --- | ---: |
| [az 스프링-cloud app deployment create](#az-spring-cloud-app-deployment-create) | 앱에 대 한 스테이징 배포를 만듭니다. |
| [az 스프링-cloud app deployment delete](#az-spring-cloud-app-deployment-delete) | 앱의 배포를 삭제 합니다. |
| [az 스프링-cloud app deployment list](#az-spring-cloud-app-deployment-list) | 앱의 모든 배포를 나열 합니다. |
| [az 스프링-cloud app deployment show](#az-spring-cloud-app-deployment-show) | 배포에 대 한 세부 정보를 표시 합니다. |

| az 스프링-cloud config-server | Azure 스프링 클라우드 구성 서버를 관리 하는 명령입니다. |
| --- | ---: |
| [az 스프링-cloud config-server clear](#az-spring-cloud-config-server-clear) | 구성 서버에서 모든 설정을 지웁니다. |
| [az 스프링-cloud config-server set](#az-spring-cloud-config-server-set) | YAML 파일에서 구성 서버를 정의 합니다. |
| [az 스프링-cloud config-server show](#az-spring-cloud-config-server-show) | 구성 서버 구성을 표시 합니다. |
| [az 스프링-클라우드 구성 서버 git 집합](#az-spring-cloud-config-server-git-set) | 구성 서버에 대 한 git 속성을 정의 합니다.  이전 값을 덮어씁니다. |
| [az 스프링-클라우드 구성 서버 git 리포지토리 추가](#az-spring-cloud-config-server-git-repo-add) | 구성 서버에 새 git 리포지토리 구성을 추가 합니다. |
| [az 스프링-클라우드 구성 서버 git 리포지토리 목록](#az-spring-cloud-config-server-git-repo-list) | 구성 서버에 대 한 모든 git 리포지토리 configs를 나열 합니다. |
| [az 스프링-클라우드 구성 서버 git 리포지토리 제거](#az-spring-cloud-config-server-git-repo-remove) | 구성 서버에서 지정 된 git 리포지토리를 제거 합니다. |

| az 스프링-cloud test-엔드포인트 | Azure 스프링 클라우드에서 엔드포인트 테스트를 관리 하는 명령 |
| --- | ---: |
| [az 스프링-클라우드 테스트-엔드포인트 사용 안 함](#az-spring-cloud-test-endpoint-disable) | 테스트 엔드포인트을 사용 하지 않습니다. |
| [az 스프링-클라우드 테스트-엔드포인트 사용](#az-spring-cloud-test-endpoint-enable) | 테스트 엔드포인트을 사용 하도록 설정 합니다. |
| [az 스프링-cloud test-엔드포인트 목록](#az-spring-cloud-test-endpoint-list) | 테스트 엔드포인트 키를 나열 합니다. |
| [az 스프링-클라우드 테스트-엔드포인트 갱신-키](#az-spring-cloud-test-endpoint-renew-key) | 테스트 엔드포인트 키를 다시 생성 합니다. |

## <a name="az-spring-cloud-create"></a>az 스프링-cloud create

Azure 스프링 클라우드의 기본 배포를 사용 하 여 새 앱을 만듭니다.

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 이 Azure 스프링 클라우드 인스턴스의 이름입니다. |
| --resource-group -g | 이 앱에 대 한 리소스 그룹을 지정 합니다.  `az configure --defaults group=<name>`를 사용 하 여 기본 그룹 구성 |

| 선택적 매개 변수 | |
| --- | ---: |
| --location -l | 이 앱의 서버 위치를 지정 합니다.  `az account list-locations`를 사용 하 여 올바른 위치 찾기 |
| --no-wait | 장기 실행 작업이 완료 되는 것은 아닙니다.

### <a name="examples"></a>예시

WestUS에서 새 Azure 스프링 클라우드 만들기

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az 스프링-cloud delete

Azure 스프링 클라우드 인스턴스를 삭제 합니다.

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 삭제할 Azure 스프링 클라우드 인스턴스의 이름입니다. |
| --resource-group -g | Azure 스프링 클라우드가 속한 리소스 그룹의 이름입니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| -대기 안 함 | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |

### <a name="example"></a>예

' MyResourceGroup '에서 ' MyService ' 이라는 Azure 스프링 클라우드 인스턴스를 삭제 합니다.

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az 스프링-cloud list

지정 된 리소스 그룹과 연결 된 모든 Azure 스프링 클라우드 인스턴스를 나열 합니다. 리소스 그룹이 지정 되지 않은 경우 구독 Id를 나열 합니다.

```cli
az spring-cloud list --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --resource-group -g | 리소스 그룹의 이름입니다. |

## <a name="az-spring-cloud-show"></a>az 스프링-cloud show

지정 된 Azure 스프링 클라우드 인스턴스에 대 한 세부 정보를 표시 합니다.

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | Azure 스프링 클라우드 인스턴스의 이름입니다. |
| --resource-group -g | Azure 스프링 클라우드 인스턴스가 속한 리소스 그룹의 이름입니다.

## <a name="az-spring-cloud-app-create"></a>az 스프링-cloud app create

Azure 스프링 클라우드에서 새 앱을 만듭니다.

```cli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --cpu | 인스턴스당 가상 코어 수입니다.  기본값: 1. |
| --사용-영구적-저장소 | 부울 값입니다.  True 이면 기본 경로를 사용 하 여 50GB 디스크를 탑재 합니다. |
| --instance-count | 인스턴스 수입니다.  기본값: 1. |
| --is-public | 부울 값입니다.  True 이면 공용 도메인을 할당 합니다. |
| --memory | 인스턴스당 GB의 메모리 수입니다.  기본값: 1. |

### <a name="examples"></a>예시

기본 구성을 사용 하 여 앱을 만듭니다.

```cli
az spring-cloud app create -n MyApp -s MyService
```

3 개 인스턴스를 사용 하 여 공개적으로 액세스할 수 있는 앱을 만듭니다.  각 인스턴스에는 3gb의 메모리와 2 개의 CPU 코어가 있습니다.

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az 스프링-cloud app delete

Azure 스프링 클라우드에서 앱을 삭제 합니다.

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-deploy"></a>az 스프링-cloud app deploy

소스 코드 또는 미리 빌드된 이진 파일에서 Azure 스프링 클라우드에 앱을 배포 하 고 관련 구성을 업데이트 합니다.

```cli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --cpu | 인스턴스당 가상 CPI 코어 수입니다. |
| --배포-d | 기존 앱 배포의 이름입니다.  지정 하지 않으면 프로덕션 배포가 기본값으로 지정 됩니다. |
| --env | ' Key [= value] ' 형식의 공백으로 구분 된 환경 변수입니다. |
| --instance-count | 인스턴스 수입니다. |
| --jar-경로 | 제공 된 경우 지정 된 경로에서 jar을 배포 합니다. 그렇지 않으면 현재 폴더를 tar로 배포 합니다. |
| --jvm-옵션 | JVM 옵션을 포함 하는 문자열입니다.  셸 구문 분석 오류를 방지 하려면 ' ' 대신 ' = '를 사용 하세요. 예: `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | 인스턴스당 GB의 메모리 수입니다. |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |
| --runtime-버전 | 앱에서 사용 되는 언어의 런타임 버전입니다.  허용 되는 값: `Java_11`, `Java_8`. |
| --target-모듈 | 배포할 자식 모듈입니다.  소스 코드에서 여러 jar 패키지를 작성 하는 경우에 필요 합니다. |
| --version | 배포 버전.  설정 하지 않으면 변경 되지 않습니다. |

### <a name="examples"></a>예시

소스 코드를 앱에 배포 합니다. 이렇게 하면 현재 디렉터리를 압축 하 고 Pivotal Build 서비스를 사용 하 여 이진 파일을 빌드한 다음 앱에 배포 합니다.

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

JVM 옵션 및 환경 변수를 사용 하 여 미리 작성 된 jar을 앱에 배포 합니다.

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

응용 프로그램의 특정 배포에 소스 코드를 배포 합니다.

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az 스프링-cloud app list

Azure 스프링 클라우드 인스턴스의 모든 앱을 나열 합니다.

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|필수 매개 변수 | |
| --- | ---: |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-restart"></a>az 스프링-cloud app restart

앱 인스턴스를 다시 시작 합니다.  프로덕션 배포에 대 한 기본값입니다.

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포-d | 응용 프로그램의 기존 배포 이름입니다.  지정 하지 않으면 프로덕션 배포가 기본값으로 지정 됩니다. |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |

## <a name="az-spring-cloud-app-scale"></a>az 스프링-cloud app scale

앱 또는 해당 배포를 수동으로 확장 합니다.

```cli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --cpu | 앱 인스턴스당 가상 CPU 코어 수입니다. |
| --배포-d | 응용 프로그램의 기존 배포 이름입니다.  지정 하지 않으면 프로덕션 배포가 기본값으로 지정 됩니다. |
| --instance-count | 이 앱의 인스턴스 수입니다. |
| --memory | 앱 인스턴스당 GB의 메모리 수입니다. |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |

### <a name="examples"></a>예시

앱을 4 CPU 코어 및 인스턴스당 8gb의 메모리로 확장 합니다.

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

앱 배포를 5 개의 인스턴스로 확장 합니다.

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az 스프링-cloud app set-deployment

앱의 프로덕션 배포에 대 한 구성 옵션을 설정 합니다.

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --배포-d | 기존 앱 배포의 이름입니다. |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |

### <a name="examples"></a>예시

앱의 스테이징 배포를 프로덕션으로 바꿉니다.

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az 스프링-cloud app show

Azure 스프링 클라우드의 앱에 대 한 세부 정보를 표시 합니다.

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az 스프링-cloud app show-deploy-log

소스 코드에서 마지막 배포의 빌드 로그를 표시 합니다.  기본값은 프로덕션 환경입니다.

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포-d | 기존 앱 배포의 이름입니다.  프로덕션 환경에 대 한 기본값입니다. |

## <a name="az-spring-cloud-app-start"></a>az 스프링-cloud app start

앱의 인스턴스를 시작 합니다.  기본값은 프로덕션 환경입니다.

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포-d | 기존 앱 배포의 이름입니다.  프로덕션 환경에 대 한 기본값입니다. |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |

## <a name="az-spring-cloud-app-stop"></a>az 스프링-cloud app stop

앱의 인스턴스를 중지 합니다.  프로덕션 환경에 대 한 기본값입니다.

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포-d | 기존 앱 배포의 이름입니다.  프로덕션 환경에 대 한 기본값입니다. |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |

## <a name="az-spring-cloud-app-update"></a>az 스프링-cloud app update

앱의 저장 된 구성을 업데이트 합니다.

```cli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| 필수 매개 변수 | |
| --- | ---: |
| --name -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포-d | 기존 앱 배포의 이름입니다.  프로덕션 환경에 대 한 기본값입니다. |
| --사용-영구적-저장소 | Boolean입니다.  True 이면 기본 경로를 사용 하 여 50GB 디스크를 탑재 합니다. |
| --env | ' Key [= value] ' 형식의 공백으로 구분 된 환경 변수입니다. |
| --is-public | Boolean입니다.  True 이면 앱에 공용 도메인을 할당 합니다. |
| --jvm-옵션 | JVM 옵션을 포함 하는 문자열입니다.  셸 구문 분석 오류를 방지 하려면 ' ' 대신 ' = '를 사용 하세요. 예: `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |
| --runtime-버전 | 앱에서 사용 되는 언어의 런타임 버전입니다.  허용 되는 값: `Java_11`, `Java_8`. |

### <a name="example"></a>예

앱에 대 한 환경 변수를 추가 합니다.

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az 스프링-cloud app binding list

앱의 모든 서비스 바인딩을 나열 합니다.

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-binding-remove"></a>az 스프링-cloud app binding remove

앱에서 서비스 바인딩을 제거 합니다.

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 제거할 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-binding-show"></a>az 스프링-cloud app binding show

서비스 바인딩의 세부 정보를 표시 합니다.

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az 스프링-cloud app binding cosmos add

앱에 Azure Cosmos DB을 바인딩합니다.

```cli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --api-version | 다음 값 중 하나를 사용 하 여 API 유형을 지정 합니다. cassandra, gremlin, mongo, sql, table |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

|선택적 매개 변수 | |
| --- | ---: |
| --컬렉션 이름 | 컬렉션의 이름입니다.  Gremlin를 사용할 때 필요 합니다. |
| --데이터베이스 이름 | 데이터베이스의 이름입니다.  Mongo, SQL 및 Gremlin를 사용할 때 필요 합니다. |
| --키-공백 | Cassandra 키-공간.  Cassandra를 사용할 때 필요 합니다. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az 스프링-cloud app binding cosmos update

```cli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

|선택적 매개 변수 | |
| --- | ---: |
| --컬렉션 이름 | 컬렉션의 이름입니다.  Gremlin를 사용할 때 필요 합니다. |
| --데이터베이스 이름 | 데이터베이스의 이름입니다.  Mongo, SQL 및 Gremlin를 사용할 때 필요 합니다. |
| --키-공백 | Cassandra 키-공간.  Cassandra를 사용할 때 필요 합니다. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az 스프링-cloud app binding mysql 추가

```cli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --데이터베이스 이름 | 데이터베이스의 이름입니다. |
| --key | 서비스의 API 키입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --리소스 id | 바인딩할 서비스의 Azure 리소스 ID입니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |
| --username | 데이터베이스 액세스에 대 한 사용자 이름입니다. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az 스프링-cloud app binding mysql 업데이트

앱에 대 한 서비스 바인딩 연결을 Azure Database for MySQL로 업데이트 합니다.

```cli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --데이터베이스 이름 | 데이터베이스의 이름입니다. |
| --key | 서비스의 API 키입니다. |
| --username | 데이터베이스 액세스에 대 한 사용자 이름입니다. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az 스프링-cloud app binding redis add

앱을 사용 하 여 Redis 용 Azure 캐시를 바인딩합니다.

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --리소스 id | 바인딩하려는 서비스의 Azure 리소스 ID입니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --사용 안 함-ssl | SSL을 사용 하지 않습니다. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az 스프링-cloud app binding redis update

Redis 용 Azure 캐시에 대 한 서비스 바인딩을 업데이트 합니다.

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --사용 안 함-ssl | SSL을 사용 하지 않습니다. |

## <a name="az-spring-cloud-app-deployment-create"></a>az 스프링-cloud app deployment create

앱에 대 한 스테이징 배포를 만듭니다.

코드를 배포 하거나 기존 배포에 대 한 설정을 업데이트 하려면 `az spring-cloud app deploy --deployment <staging-deployment>` 또는 ' az 스프링-cloud app update--deployment <staging deployment>를 사용 합니다.

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --cpu | 인스턴스당 가상 CPU 코어 수입니다.  기본값: 1 |
| --env | ' Key [= value] ' 형식의 공백으로 구분 된 환경 변수입니다. |
| --instance-count | 인스턴스 수입니다. 기본값: 1. |
| --jar-경로 | 제공 되는 경우 jar을 배포 합니다.  그렇지 않으면 현재 폴더를 tar로 배포 합니다. |
| --jvm-옵션 | JVM 옵션을 포함 하는 문자열입니다.  셸 구문 분석 오류를 방지 하려면 ' ' 대신 ' = '를 사용 하세요. 예: `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | 인스턴스당 GB의 메모리 수입니다. |
| --no-wait | 장기 실행 작업이 완료 될 때까지 기다리지 마세요. |
| --runtime-버전 | 앱에서 사용 되는 언어의 런타임 버전입니다.  허용 되는 값: `Java_11`, `Java_8`. |
| --복제본 건너뛰기-설정 | 현재 프로덕션 배포 설정을 복제 하 여 스테이징 배포를 만듭니다. |
| --target-모듈 | 배포할 자식 모듈입니다.  소스 코드에서 여러 jar 패키지를 작성 하는 경우에 필요 합니다. |
| --version | 배포 버전.  설정 하지 않으면 변경 되지 않습니다. |

### <a name="examples"></a>예시

응용 프로그램의 새 배포에 소스 코드를 배포 합니다.  이렇게 하면 현재 디렉터리를 압축 하 고 Pivotal 빌드 시스템을 사용 하 여 이진 파일을 빌드한 다음 배포 합니다.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

JVM 옵션 및 환경 변수를 사용 하 여 미리 작성 된 jar을 앱에 배포 합니다.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az 스프링-cloud app deployment delete

앱의 배포를 삭제 합니다.

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 배포의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-deployment-list"></a>az 스프링-cloud app deployment list

앱의 모든 배포를 나열 합니다.

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-deployment-show"></a>az 스프링-cloud app deployment show

배포에 대 한 세부 정보를 표시 합니다.

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 배포의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스-s | Azure 스프링 클라우드의 이름입니다.  `az configure --defaults spring-cloud=<name>`를 사용 하 여 기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-config-server-clear"></a>az 스프링-cloud config-server clear

구성 서버에서 모든 구성 설정을 지웁니다.

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-config-server-set"></a>az 스프링-cloud config-server set

YAML 파일을 사용 하 여 구성 서버에서 구성 설정을 설정 합니다.

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --config-file | 구성 서버 구성에 대 한 YAML 매니페스트의 파일 경로입니다. |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --no-wait | 장기 실행 작업이 완료 되는 것은 아닙니다.

## <a name="az-spring-cloud-config-server-show"></a>az 스프링-cloud config-server show

구성 서버 설정을 표시 합니다.

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-config-server-git-set"></a>az 스프링-cloud config-server git set

구성 서버에 대 한 git 속성을 설정 합니다.  이렇게 하면 기존의 모든 git 속성이 덮어쓰여집니다.

```cli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --uri | 추가 된 구성의 URI입니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --지연 | Azure에 전송 하는 대신 로컬 캐시에 개체를 임시로 저장 합니다.  `az cache`를 사용 하 여 보거나 지우십시오. |
| --호스트 키 | 추가 된 구성의 호스트 키입니다. |
| --호스트-키-알고리즘 | 추가 된 구성에 대 한 호스트 키 알고리즘입니다. |
| --레이블 | 추가 된 구성의 레이블입니다. |
| --password | 추가 된 구성의 암호입니다. |
| --개인 키 | 추가 된 구성의 개인 키입니다. |
| --검색 경로 | 추가 된 구성의 경로를 검색 합니다.  여러 경로에 쉼표 구분 기호를 사용 합니다. |
| --strict-키-검사 | 추가 된 구성의 엄격한 호스트 키 검사를 사용 하도록 설정 합니다. |
| --username | 추가 된 구성의 사용자 이름입니다. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az 스프링-클라우드 구성-서버 git 리포지토리 추가

```cli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --리포지토리-이름 | 리포지토리의 URI입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --uri | 추가 된 구성의 URI입니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --지연 | Azure에 전송 하는 대신 로컬 캐시에 개체를 임시로 저장 합니다.  `az cache`를 사용 하 여 보거나 지우십시오. |
| --호스트 키 | 추가 된 구성의 호스트 키입니다. |
| --호스트-키-알고리즘 | 추가 된 구성에 대 한 호스트 키 알고리즘입니다. |
| --레이블 | 추가 된 구성의 레이블입니다. |
| --password | 추가 된 구성의 암호입니다. |
| --패턴 | 리포지토리의 패턴입니다.  여러 경로에 쉼표 구분 기호를 사용 합니다.|
| --개인 키 | 추가 된 구성의 개인 키입니다. |
| --검색 경로 | 추가 된 구성의 경로를 검색 합니다.  여러 경로에 쉼표 구분 기호를 사용 합니다. |
| --strict-키-검사 | 추가 된 구성의 엄격한 호스트 키 검사를 사용 하도록 설정 합니다. |
| --username | 추가 된 구성의 사용자 이름입니다. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az 스프링-클라우드 구성-서버 git 리포지토리 목록

구성 서버에 정의 된 모든 git 리포지토리를 나열 합니다.

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --지연 | Azure에 전송 하는 대신 로컬 캐시에 개체를 임시로 저장 합니다.  `az cache`를 사용 하 여 보거나 지우십시오. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az 스프링-클라우드 구성-서버 git 리포지토리 제거

구성 서버에서 기존 git 리포지토리 구성을 제거 합니다.

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --리포지토리-이름 | 리포지토리의 URI입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --지연 | Azure에 전송 하는 대신 로컬 캐시에 개체를 임시로 저장 합니다.  `az cache`를 사용 하 여 보거나 지우십시오. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az 스프링-클라우드 테스트-엔드포인트 사용 안 함

Azure 스프링 클라우드의 테스트 엔드포인트 사용 안 함

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az 스프링-클라우드 테스트-엔드포인트 사용

Azure 스프링 클라우드에 대해 테스트 엔드포인트을 사용 하도록 설정 합니다. 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az 스프링-cloud test-엔드포인트 목록 

Azure 스프링 클라우드에 대해 사용 가능한 테스트 엔드포인트 키를 나열 합니다.

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --배포-d | 기존 앱 배포의 이름입니다.  지정 되지 않은 경우 기본적으로 production로 설정 됩니다. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az 스프링-클라우드 테스트-엔드포인트 갱신-키

Azure 스프링 클라우드의 테스트 엔드포인트 키를 다시 생성 합니다.

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --형식 | 테스트 엔드포인트 키의 형식입니다.  허용 되는 값: Primary, Secondary |
