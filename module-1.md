# 1. What is Cloud Shell?

Cloud Shell is used to interact, manage, or monitor Azure services like Network, Virtual Machines (VMs), and Storage without going to the Azure Portal, by using the CLI. It also provides storage to store scripts and files, which can be accessed while switching between different virtual machines.

---

# 2. When to use Cloud Shell?

Suppose you are not using your admin machine and you get an issue that needs to be fixed. At that time, you can log in to your Azure subscription through a browser and then use the CLI to perform the actions you need.

1. Open a secure command-line session from any browser-based device.
2. Interact with Azure resources without the need to install plug-ins or add-ons on your device.
3. Persist files between sessions for later use.
4. Use either Bash or PowerShell, whichever you prefer, to manage Azure resources.
5. Edit files (such as scripts) by using the Cloud Shell editor.

---

# 3. When can't you use it?

You shouldn't use Azure Cloud Shell if:

* You intend to leave a session open for more than 20 minutes for long-running scripts or activities. In these cases, your session is disconnected without warning, and the current state is lost.
* You need admin permissions, such as `sudo` access, from within the Azure CLI or PowerShell environment.
* You need to install tools that aren't supported in the limited Cloud Shell environment but instead require a custom virtual machine or container.
* You need storage from different regions. You might need to back up and synchronize this content since only one region can have the storage allocated to Azure Cloud Shell.
* You need to open multiple sessions at the same time. Azure Cloud Shell allows only one instance at a time and isn't suitable for concurrent work across multiple subscriptions or tenants.

---

# ARM Template Structure

## 1. What is IaC?

Infrastructure as Code (IaC) allows you to describe, through code, the infrastructure that you need for your application.

With Infrastructure as Code, you can maintain both your application code and everything you need to deploy your application in a central code repository.

The advantages of Infrastructure as Code are:

1. Consistent configurations
2. Improved scalability
3. Faster deployments
4. Better traceability

---

## What is an ARM Template?

ARM Templates are JavaScript Object Notation (JSON) files that define the infrastructure and configuration for your deployment. The template uses a declarative syntax, i.e., it describes what the resources should look like without describing the control flow.

ARM Templates allow you to declare what you intend to deploy without having to write the sequence of programming commands to create it. You can define and use the required resources and their properties.

---

## Benefits of ARM Templates

1. Allows you to automate deployments and practice Infrastructure as Code (IaC).
2. They are idempotent, i.e., no matter how many times you run the template, it will always produce the same result for the defined resources.
3. They have built-in validation.
4. If your deployments become more complex, you can break your ARM Templates into smaller, reusable components. You can link these smaller templates together at deployment time. You can also nest templates inside other templates.
5. You can review the deployment history from the Azure Portal along with all the parameters used during deployment.

---

## ARM Template Structure

1. **Schema:** Specifies the location of the JSON schema file.
2. **Content Version:** Specifies the version of the template.
3. **Parameters:** Provides values during deployment, which helps you use the same template across different environments.
4. **Variables:** Used to define values that can be reused throughout the template.
5. **Resources:** Contains the resources that you want to deploy or update.
6. **Outputs:** Used to define the values that will be returned after the deployment.

---

## Example of an ARM Template

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24,
      "metadata": {
        "description": "Storage account name"
      }
    }
  },
  "variables": {
    "uniqueStorName": "[concat(parameters('storageName'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[variables('uniqueStorName')]",
      "location": "East US",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true
      }
    }
  ],
  "outputs": {}
}
```

### Explanation

Here, we are creating a Storage Account whose name should be unique every time the template is deployed.

* **Parameter (`storageName`)**: Allows us to provide a different storage account name each time we deploy the template.
* **Variable (`uniqueStorName`)**: Reuses the parameter value and combines it with the `uniqueString(resourceGroup().id)` function to generate a unique storage account name.
* **Resource**: Creates an Azure Storage Account with the **Standard_LRS** SKU in the **East US** region.
* **Properties**: Enables HTTPS-only traffic for secure access.
* **Outputs**: Currently empty, but it can be used to return values (such as the storage account name or resource ID) after the deployment is completed.
