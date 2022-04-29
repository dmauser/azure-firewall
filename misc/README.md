# Commands and few tips on Azure Firewall


## Firewall Policy

Sample of creating a networking rule:

```bash
#Create firewall rules
fwpolicyname=NewPolicyTest #Firewall Policy Name
rg=FW-Policies # Set your Resource Group
az network firewall policy create --name $fwpolicyname --resource-group $rg
az network firewall policy rule-collection-group create --name NetworkRuleCollectionGroup --priority 200 --policy-name $fwpolicyname --resource-group $rg
# First rule always has to create a Collection group
az network firewall policy rule-collection-group collection add-filter-collection \
 --resource-group $rg \
 --policy-name $fwpolicyname \
 --name GenericCollection \
 --rcg-name NetworkRuleCollectionGroup \
 --rule-type NetworkRule \
 --rule-name AnyRFC1918only \
 --action Allow \
 --ip-protocols "Any" \
 --source-addresses 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16 \
 --destination-addresses  10.0.0.0/8 172.16.0.0/12 192.168.0.0/16 \
 --destination-ports "*" \
 --collection-priority 100

 #Additional rule to the same collection:
 az network firewall policy rule-collection-group collection rule add \
 --resource-group $rg \
 --collection-name GenericCollection \
 --name 10NetInternet-WebOnly \
 --policy-name $fwpolicyname \
 --rcg-name NetworkRuleCollectionGroup \
 --rule-type NetworkRule \
 --ip-protocols "TCP" \
 --source-addresses 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16 \
 --destination-addresses "*" \
 --destination-ports 80 443
```