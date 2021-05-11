---
title: Azure Red Hat OpenShift에서 보안 컨텍스트 제약 조건 관리 | Microsoft Docs
description: Azure Red Hat OpenShift 클러스터 관리자에 대한 보안 컨텍스트 제약 조건
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 977504c1faec9bd8134646a8cbe31f9eea665edd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636206"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift의 보안 컨텍스트 제약 조건 관리

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11은 2022년 6월 30일부터 사용이 중지됩니다. 새로운 Azure Red Hat OpenShift 3.11 클러스터 만들기 지원은 2020년 11월 30일까지 계속됩니다. 사용 중지 후에는 보안 취약성을 방지하기 위해 남아 있는 Azure Red Hat OpenShift 3.11 클러스터가 종료됩니다.
> 
> 이 가이드를 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md).
> 궁금한 점은 [문의하세요](mailto:arofeedback@microsoft.com).

SCCs(보안 컨텍스트 제약 조건)를 사용하면 클러스터 관리자가 Pod에 대한 사용 권한을 제어할 수 있습니다. 이 API 형식에 대해 자세히 알아보려면 [SCCs에 대한 아키텍처 설명서](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html)를 참조하세요. CLI를 사용하여 인스턴스에서 SCCs를 일반 API 개체로 관리할 수 있습니다.

## <a name="list-security-context-constraints"></a>보안 컨텍스트 제약 조건 나열

SCCs의 현재 목록을 가져오려면 다음 명령을 사용합니다. 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>개체에서 보안 컨텍스트 제약 조건을 검사합니다

특정 SCC를 검사하려면 `oc get`, `oc describe`, 또는 `oc edit`를 사용합니다.  예를 들어 **제한된** SCC를 검사하려면 다음 명령을 사용합니다.
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 
