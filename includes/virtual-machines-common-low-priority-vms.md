---
 title: include file
 description: include file
 services: virtual-machines
 author: cynthn
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 09/23/2019
 ms.author: cynthn
 ms.custom: include file
---

Using low-priority VMs allows you to take advantage of our unused capacity at a significant cost savings. At any point in time when Azure needs the capacity back, the Azure infrastructure will evict low-priority VMs. Therefore, low-priority VMs are great for workloads that can handle interruptions like batch processing jobs, dev/test environments, large compute workloads, and more.

The amount of available capacity can vary based on size, region, time of day, and more. When deploying low-priority VMs, Azure will allocate the VMs if there is capacity available, but there is no SLA for these VMs. A low-priority VM offers no high availability guarantees.

At any point in time when Azure needs the capacity back, the Azure infrastructure will evict low-priority VMs. Therefore, low-priority VMs are great for workloads that can handle interruptions like batch processing jobs, dev/test environments, large compute workloads, and more.



## Eviction Policy

When deploying low-priority VMs, Azure will allocate the VMs if there is capacity available, but there are no SLA guarantees. At any point in time when Azure needs the capacity back, we will evict low-priority VMs with 30 seconds notice. 

For the preview, VMs will be evicted based on capacity and the max price you set. For virtual machines, the eviction policy is set to *Deallocate*. The *Deallocate* policy moves your evicted VMs to the stopped-deallocated state, allowing you to redeploy the evicted VMs. However, there is no guarantee that the allocation will succeed. The deallocated VMs will count against your low-priority vCPU quota and you will be charged for your underlying disks. 

| Option | Outcome |
|--------|---------|
| Max price is set to >= the current price. | VM is deployed if capacity and quota are available. |
| Max price is set to < the current price. | The VM is not deployed. You will get an error message that the max price needs to be >= current price. |
| Restarting a stop/deallocate VM if the max price is >= the current price | If there is capacity and quota, then the VM is deployed. |
| Restarting a stop/deallocate VM if the max price is < the current price | You will get an error message that the max price needs to be >= current price. | 
| Price for the VM has gone up and is now > the max price. | The VM gets evicted. You get a 30s notification before actual eviction. | 
| After eviction the price for the VM goes back to being < the max price. | The VM will not be automatically re-started. You can restart the VM youself, and it will be charged at the current price. |
| If the max price is set to `-1` | The VM will not be evicted for pricing reasons. The max price will be the current price, up to the price for on-demand VMs. You will never be charged above the on-demand price.| 

## Limitations

The following VM sizes are not supported for low-priority VMs:
 - B-series
 - Promo versions of any size (like Dv2, NV, NC, H promo sizes)
 - The original A-series
 - The original D-series 
 
Low-priority VMs can be deployed to any region, except Microsoft Azure China and Department of Defense (DoD) in the Azure Government region.

## Pricing

Pricing for low-priority VMs is variable, based on region and SKU. For more information, see VM pricing for [Linux](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/) and [Windows](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/windows/). 


With variable pricing, you have option to set a max price, in USD, using up to 5 decimal places. For example, the value `0.98765`would be a max price of $0.98765 USD per hour. If you set the max price to be `-1`, the VM won't be evicted based on price. The price for the VM will be the current price for low-priority or the price for an on-demand VM, which ever is less, as long as there is capacity and quota available.

> [!IMPORTANT]
> For the early part of the public preview, you can set a max price, but it will be ignored. Low-priority VMs will have a fixed price, so there will not be any price-based evictions.

## Use the Azure CLI

The process to create a VM with low-priority using the Azure CLI is the same as detailed in the [quickstart article](/azure/virtual-machines/linux/quick-create-cli). Just add the '--priority Low' parameter and provide a max price or `-1`.

> [!IMPORTANT]
> For the early part of the public preview, you can set a max price, but it will be ignored. Low-priority VMs will have a fixed price, so there will not be any price-based evictions.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low \
	--max-price -1
```

## Use Azure PowerShell

Register the feature.

```powershell
Register-AzProviderFeature -FeatureName LowPrioritySingleVM -ProviderNamespace Microsoft.Compute
```

It takes a few minutes for the registration to complete. Use [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) to check on the status of the feature registration.

```powershell
Get-AzProviderFeature -FeatureName LowPrioritySingleVM -ProviderNamespace Microsoft.Compute
```

Create a low priority VM using [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) to create the configuration. Include `-Priority low` and set `-MaxPrice` to either:
- `-1` so the VM is not evicted based on price.
- a dollar amount, up to 5 digits. For example `-MaxPrice .98765` means that the VM will be deallocated once the price for a low priority VM goes about $.98765 per hour.

> [!IMPORTANT]
> For the early part of the public preview, you can set a max price, but it will be ignored. Low-priority VMs will have a fixed price, so there will not be any price-based evictions.


This example creates a low priority VM that will not be deallocated based on pricing (only when Azure needs the capacity back).

```azurepowershell-interactive
$resourceGroup = "myLowPriRG"
$location = "eastus"
$vmName = "myLowPriVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a low-priority VM
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Low" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

After the VM is created, you can query to see the max price for all of the VMs in the resource group.

```powershell
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## Use Azure Resource Manager Templates

For template deployments, use`"apiVersion": "2019-03-01"`. Add the `priority`, `evictionPolicy` and `billingProfile` properties to in your template: 

```json
                "priority": "Low",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```


> [!IMPORTANT]
> For the early part of the public preview, you can set a max price, but it will be ignored. Low-priority VMs will have a fixed price, so there will not be any price-based evictions.


Here is a sample template with the added properties for a low-priority VM. Replace the resource names with your own and `<password>` with a password for the local administrator account on the VM.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/lowpriVM/providers/Microsoft.Network/virtualNetworks/lowpriVM",
        "subnetName": "default",
        "networkInterfaceName": "lowpriVMNIC",
        "publicIpAddressName": "lowpriVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "lowpriVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragelowpri2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragelowpri2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "lowpriVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-10-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Low",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }				
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```


##  Frequently asked questions

**Q:** Once created, is a low-priority VM the same as regular on-demand VM?

**A:** Yes, except there is no SLA for low-priority VMs.


**Q:** What to do when you get evicted, but still need capacity?

**A:** We recommend you use on-demand VMs instead of low-priority VMs if you need capacity right away.


**Q:** How is quota managed for low-priority VMs?

**A:** Low-priority VMs and regular VMs will have separate quota pools. 

> [!IMPORTANT]
> For the early part of the public preview, regular VMs and low-priority VMs will share quota.


**Q:** Can I request for additional quota for low-priority?

**A:** Yes, you will be able to submit the request to increase your quota for low priority VMs through the automation tool.


**Q:** Can I use low-priority VMs with AKS?

**A:** Not yet. We are working with AKS team to introduce low-priority VM option in AKS.


**Q:** Where can I post questions?

**A:** You can post and tag your question with `azurelowpri` at [http://aka.ms/stackoverflow](https://stackoverflow.microsoft.com/questions/tagged/azurelowpri). 

