# Azure Tags
Apply tags to Azure resources to logically organize them into a taxonomy. After tags are applied, it can be used to retrieve all the resources in a subscription with that tag name and value. Tags enable you to retrieve related resources from different resource groups. This approach is helpful to organize resources for billing or management.

The best practice is to create a Resource Group with Tags and then apply a Policy to enforce tagging rules and conventions. For example, create a Resource Group with the Tags defined.

```bash
# Create the Resource Group with tags enabled - https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest
az group create --name abs --location eastus --tags "EmployeeID=12345" "AD ID=abs123" "EmployeeName=Abhinab" "DepartmentName=HumanResource" "ProjectName=CommunityProject" "CostCode=HR-CP" --verbose
```

![Alt Text](/images/resource-group-tags.jpg)

Apply built-in policy "Inherit a tag from the resource group" on the Resource Group. It will add or replace the specified tag and value from the parent resource group when any resource is created or updated. Existing resources can be remediated by triggering a remediation task.

> You can also create your own custom policy & add to the list of in-built policies and enforce them on any Resource or Resource Group.

```bash
# Create a policy assignment - https://docs.microsoft.com/en-us/azure/governance/policy/assign-policy-azurecli
az policy assignment create --name <actual_name_of_policy_assignment> --display-name <display_name_of_policy_assignment> --policy <policy_name> --scope <scope_of_policy_assignment> --params <tagname_value> --assign-identity --location <location_vale>

# The policy definition name/id can be extracted using the below command
az policy definition list --query "[?displayName=='Inherit a tag from the resource group'].name"

# The scope for Resource Group can be found using the below command
az group list --query "[?name=='abs'].id"
```

See example below

```bash
# Create a policy assignment

# Assignment for Ad ID tag
az policy assignment create --name "inherit-AD-ID-tag-from-resource-group" --display-name "Inherit AD ID tag from the resource group" --policy cd3aa116-8754-49c9-a813-ad46512ece54 --scope /subscriptions/141b62fb-b57f-4854-be88-423368640cb1/resourceGroups/abs --params "{'tagName':{'value': 'AD ID'}}" --assign-identity --location eastus

# Assignment for EmployeeName tag
az policy assignment create --name "inherit-employee-name-tag-from-resource-group" --display-name "Inherit Employee Name tag from the resource group" --policy cd3aa116-8754-49c9-a813-ad46512ece54 --scope /subscriptions/141b62fb-b57f-4854-be88-423368640cb1/resourceGroups/abs --params "{'tagName':{'value': 'EmployeeName'}}" --assign-identity --location eastus

# Assignment for EmployeeID tag
az policy assignment create --name "inherit-employee-id-tag-from-resource-group" --display-name "Inherit Employee ID tag from the resource group" --policy cd3aa116-8754-49c9-a813-ad46512ece54 --scope /subscriptions/141b62fb-b57f-4854-be88-423368640cb1/resourceGroups/abs --params "{'tagName':{'value': 'EmployeeID'}}" --assign-identity --location eastus

# Assignment for DepartmentName tag
az policy assignment create --name "inherit-department-name-tag-from-resource-group" --display-name "Inherit DepartmentName tag from the resource group" --policy cd3aa116-8754-49c9-a813-ad46512ece54 --scope /subscriptions/141b62fb-b57f-4854-be88-423368640cb1/resourceGroups/abs --params "{'tagName':{'value': 'DepartmentName'}}" --assign-identity --location eastus

# Assignment for ProjectName tag
az policy assignment create --name "inherit-project-name-tag-from-resource-group" --display-name "Inherit ProjectName tag from the resource group" --policy cd3aa116-8754-49c9-a813-ad46512ece54 --scope /subscriptions/141b62fb-b57f-4854-be88-423368640cb1/resourceGroups/abs --params "{'tagName':{'value': 'ProjectName'}}" --assign-identity --location eastus

# Assignment for CostCode tag
az policy assignment create --name "inherit-cost-code-tag-from-resource-group" --display-name "Inherit CostCode tag from the resource group" --policy cd3aa116-8754-49c9-a813-ad46512ece54 --scope /subscriptions/141b62fb-b57f-4854-be88-423368640cb1/resourceGroups/abs --params "{'tagName':{'value': 'CostCode'}}" --assign-identity --location eastus
```

Once the policies are applied, any resource created in the Resource Group 'abs' will inherit the tags

![Alt Text](/images/resource-tags.jpg)

To get resource groups that have a specific tag, use the below command.
```bash
# Refer the documentation for details - https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources
az group list --tag DepartmentName=HumanResource
```

To get all the resources that have a particular tag and value, use the below command.
```bash
az resource list --tag DepartmentName=HumanResource
```
Deleting the Resource Group & all resources to avoid charges.
```bash
# Delete the Resource Group - https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest
az group delete --name abs --yes --no-wait
```


