## Windows AKS nodes does not have SSH Username/password:

You can create windows SSH user/pass with azure CLI, this requires reimaging vmss instances

```bash
VMSS=<VMSS Name>
VMSSRG=<VMSS Resource Group>
WINADMINUSER=<Windows USer>
WINADMINPASS=<your complex 14 letters  password>
az vmss update --resource-group $VMSSRG --name $VMSS --set "virtualMachineProfile.osProfile.adminUsername=$WINADMINUSER"
az vmss update --resource-group $VMSSRG --name $VMSS --set "virtualMachineProfile.osProfile.adminPassword=$WINADMINPASS"
az vmss update-instances --instance-ids "*" --resource-group $VMSSRG --name $VMSS
#add --instance-id if you want to specify a specific image
#Example: az vmss reimage --instance-id 1 --resource-group $VMSSRG --name $VMSS
az vmss reimage --resource-group $VMSSRG --name $VMSS
```