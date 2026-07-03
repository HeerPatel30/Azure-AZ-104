# 1.What is cloud Shell ?
Well cloud shell is used to interact , manage or monitor the azure services like the network, vm and storage with going to the portol via CLI. It also provide the storage to store the script, files which can be accessing 
while switching between the different virtual machine. 

## 

# 2. When to use th cloud shell ? 
Suppose you are not using your Admin machine and you got an issue which you need to fixed it. So at that you can login to your subscription throw browser and then you can use the CLI to perform the action you need to do. 
1.Open a secure command-line session from any browser-based device.
2.Interact with Azure resources without the need to install plug-ins or add-ons to your device.
3.Persist files between sessions for later use.
4.Use either Bash or PowerShell, whichever you prefer, to manage Azure resources.
5.Edit files (such as scripts) via the Cloud Shell editor.


# 3. When you can't use it ?
You shouldn't use Azure Cloud Shell if:

You intend to leave a session open for more than 20 minutes for long running scripts or activities. In these cases, your session is disconnected without warning, and the current state is lost.
You need admin permissions, such as sudo access, from within the Azure CLI or PowerShell environment.
You need to install tools that aren't supported in the limited Cloud Shell environment, but instead require an environment such as a custom virtual machine or container.
You need storage from different regions. You might need to back up and synchronize this content since only one region can have the storage allocated to Azure Cloud Shell.
You need to open multiple sessions at the same time. Azure Cloud Shell allows only one instance at time and isn't suitable for concurrent work across multiple subscriptions or tenants.
##

# ARM template structure
##
# 1. what is IAC ? 
Infrastructure as code allows you to describe, through code, the infrastructure that you need for your application.
With infrastructure as code, you can maintain both your application code and everything you need to deploy your application in a central code repository.
The advantages to infrastructure as code are:
1.Consistent configurations
2.Improved scalability
3.Faster deployments
4.Better traceability

##

# What is ARM Template ? 
ARM templates are JavaScript Object Notation (JSON) files that define the infrastructure and configuration for your deployment. The template uses a declarative syntax. i.e what resources look like without describing the control flow.
ARM templates allow you to declare what you intend to deploy without having to write the sequence of programming commands to create it. You can mention and use the resources and properties.

##

# Benefits of the ARM Templates ? 
1. Allows you to automate the deployment and practice of the IAC.
2. They are idempotent i.e. no matter how many time you run the file it will always give same output as the resources.
3. They have inbuilt Validation.
4. If your deployments become more complex, you can break your ARM templates into smaller, reusable components. You can link these smaller templates together at deployment time. You can also nest templates inside other templates.
5. You can review the history from the azure portal of your deployment and all parameter used in it.

 
