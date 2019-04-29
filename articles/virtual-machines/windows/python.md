---
title: Python을 사용하여 Azure에서 Windows VM 만들기 및 관리 | Microsoft Docs
description: Python을 사용하여 Azure에서 Windows VM을 만들고 관리하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 748bc08e003d398e96ef55493e4f3b0bf6b7da28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034759"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Python을 사용하여 Azure에서 Windows VM 만들기 및 관리

[Azure VM(Virtual Machine)](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 몇 가지 지원 Azure 리소스가 필요합니다. 이 문서에서는 Python을 사용하여 VM 리소스 만들기, 관리 및 삭제에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Visual Studio 프로젝트 만들기
> * 패키지 설치
> * 자격 증명 만들기
> * 리소스 만들기
> * 관리 작업 수행
> * 리소스 삭제
> * 애플리케이션 실행

이러한 단계를 수행하려면 약 20분이 걸립니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)를 아직 설치하지 않았으면 설치합니다. 작업 페이지에서 **Python 개발**을 선택한 다음 **설치**를 클릭합니다. 요약하자면, **Python 3 64비트(3.6.0)** 가 자동으로 선택되는 것을 확인할 수 있습니다. Visual Studio를 이미 설치한 경우 Visual Studio 시작 관리자를 사용하여 Python 워크로드를 추가할 수 있습니다.
2. Visual Studio를 설치하고 시작한 후 **파일** > **신규** > **프로젝트**를 클릭합니다.
3. **템플릿** > **Python** > **Python 애플리케이션**을 클릭하고, 프로젝트의 이름에 사용할 *myPythonProject*를 입력한 후, 프로젝트의 위치를 선택하고, **확인**을 클릭합니다.

## <a name="install-packages"></a>패키지 설치

1. 솔루션 탐색기에서 *myPythonProject* 아래에 있는 **Python 환경**을 마우스 오른쪽 단추로 클릭한 다음, **가상 환경 추가**를 선택합니다.
2. 가상 환경 추가 화면에서 기본 이름 *env*를 수락하고, *Python 3.6(64비트)* 이 기본 인터프리터로 선택되어 있는지 확인한 후 **만들기**를 클릭합니다.
3. 만든 *env* 환경을 마우스 오른쪽 단추로 클릭하고, **Python 패키지 설치**를 클릭한 후, 검색 상자에 *azure*를 입력한 다음, Enter 키를 누릅니다.

azure 패키지가 성공적으로 설치되었음이 출력 창에 표시됩니다. 

## <a name="create-credentials"></a>자격 증명 만들기

이 단계를 시작하기 전에 [Active Directory 서비스 주체](../../active-directory/develop/howto-create-service-principal-portal.md)가 있는지 확인합니다. 또한 이후 단계에서 필요한 애플리케이션 ID, 인증 키 및 테넌트 ID를 기록해 두어야 합니다.

1. 만든 *myPythonProject.py* 파일을 열고, 애플리케이션을 실행하도록 활성화하는 다음 코드를 추가합니다.

    ```python
    if __name__ == "__main__":
    ```

2. 필요한 코드를 가져오려면 .py 파일의 맨 위에 다음 문을 추가합니다.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. 다음으로 .py 파일에 가져오기 문 뒤에 변수를 추가하여 이 코드에 사용되는 공통 값을 지정합니다.
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    **subscription-id**를 구독 ID로 바꿉니다.

4. 요청하는 데 필요한 Active Directory 자격 증명을 만들려면 .py 파일에 변수 뒤에 다음 함수를 추가합니다.

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    **application-id**, **authentication-key** 및 **tenant-id**를 Azure Active Directory 서비스 주체를 만들 때 이전에 수집한 값으로 바꿉니다.

5. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>리소스 만들기
 
### <a name="initialize-management-clients"></a>관리 클라이언트 초기화

관리 클라이언트는 Azure에서 Python SDK를 사용하여 리소스를 만들고 관리하는 데 필요합니다. 관리 클라이언트를 만들려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>VM 및 지원 리소스 만들기

모든 리소스는 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md)에 포함되어야 합니다.

1. 리소스 그룹을 만들려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[가용성 집합](tutorial-availability-sets.md)은 애플리케이션에서 사용되는 가상 머신을 쉽게 유지 관리할 수 있도록 합니다.

1. 가용성 집합을 만들려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

[공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)는 가상 머신과 통신해야 합니다.

1. 가상 머신에 대한 공용 IP 주소를 만들려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

가상 머신은 [가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 서브넷에 있어야 합니다.

1. 가상 네트워크를 만들려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. 가상 네트워크에 대한 서브넷을 만들려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

가상 컴퓨터는 가상 네트워크에서 통신하기 위해 네트워크 인터페이스가 필요합니다.

1. 네트워크 인터페이스를 만들려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

모든 지원 리소스를 만들었으므로 가상 머신을 만들 수 있습니다.

1. 가상 머신을 만들려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > 이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 머신을 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 머신 탐색 및 선택](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
    > 
    > 

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>관리 작업 수행

가상 머신의 수명 주기 동안 가상 머신 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 코드를 만들 수도 있습니다.

### <a name="get-information-about-the-vm"></a>VM 관련 정보 가져오기

1. 가상 컴퓨터에 대한 정보를 가져오려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>VM을 중지합니다.

가상 머신을 중지하고 해당 설정을 모두 그대로 유지하면 계속 요금이 청구될 수 있습니다. 그렇지 않으려면 가상 머신을 중지하고 할당을 해제합니다. 가상 머신을 할당을 해제하면 연결된 모든 리소스의 할당이 취소되고 대금 청구가 끝납니다.

1. 가상 컴퓨터를 할당 취소 없이 중지하려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    가상 머신의 할당을 취소하려는 경우 power_off 호출을 이 코드로 변경합니다.

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>VM 시작

1. 가상 머신을 시작하려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>VM 크기 조정

가상 머신의 크기를 결정할 때 배포의 여러 측면을 고려해야 합니다. 자세한 내용은 [VM 크기](sizes.md)를 참조하세요.

1. 가상 머신의 크기를 변경하려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>VM에 데이터 디스크 추가

가상 머신에도 VHD로 저장되는 [데이터 디스크](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)가 하나 이상 있을 수 있습니다.

1. 가상 컴퓨터에 데이터 디스크를 추가하려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다. 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 항상 더 이상 필요하지 않은 리소스를 삭제하는 것이 좋습니다. 가상 머신 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

1. 리소스 그룹 및 모든 리소스를 삭제하려면 .py 파일에서 변수 뒤에 이 함수를 추가합니다.
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. 이전에 추가한 함수를 호출하려면 .py 파일의 끝에서 **if** 문 아래에 이 코드를 추가합니다.
   
    ```python
    delete_resources(resource_group_client)
    ```

3. *myPythonProject.py*를 저장합니다.

## <a name="run-the-application"></a>애플리케이션 실행

1. 콘솔 애플리케이션을 실행하려면 Visual Studio에서 **시작**을 클릭합니다.

2. 각 리소스의 상태가 반환된 후 **Enter** 키를 누릅니다. 상태 정보에 **Succeeded** 프로비저닝 상태가 표시됩니다. 가상 머신을 만든 후 만든 모든 리소스를 삭제할 기회가 있습니다. **Enter** 키를 눌러 리소스 삭제를 시작하기 전에 Azure Portal에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다. Azure Portal이 열려 있는 경우 새 리소스를 보려면 블레이드를 새로 고쳐야 할 수 있습니다.  

    이 콘솔 애플리케이션을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. 애플리케이션이 종료된 후 모든 리소스 및 리소스 그룹을 삭제하려면 몇 분 정도 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 배포에 문제가 있는 경우 다음 단계로서 [Azure Portal을 사용하여 리소스 그룹 배포 문제 해결](../../resource-manager-troubleshoot-deployments-portal.md)
- [Azure Python 라이브러리](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)에 대한 자세한 내용

