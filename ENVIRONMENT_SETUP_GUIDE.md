# Technical Assessment — Dynamics (macOS / Visual Studio Code)

This guide provides **macOS instructions** for setting up a free **Dynamics 365 / Power Platform Developer Environment** and the **.NET 6+ Dataverse SDK** tools needed to complete the *Technical Assessment – Dynamics*.

You’ll use **Visual Studio Code**, **.NET 6 CLI**, and the **Power Platform CLI (PAC)** to build and register plugins, and optionally explore low-code/no-code (LCNC) options such as Power Automate.

##  1. Create a Free Power Platform Developer Environment

1. Go to: [https://powerapps.microsoft.com/developerplan](https://powerapps.microsoft.com/developerplan)
2. Click **“Get started free.”**
3. Sign in with a **Microsoft 365 or Outlook account**.  
   > If you don’t have one, create a free [Outlook.com](https://outlook.com) account first.
4. Complete setup. This creates your **personal Developer Environment** with Dataverse and Power Automate.
5. Open your environment at [https://make.powerapps.com](https://make.powerapps.com).
6. Verify you’re in your **Developer Environment** (top-right selector).
7. Navigate to **Tables → + New table** to begin creating entities.

## 2. Install Required Tools on macOS

### A. Install Homebrew (if not already installed)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### B. Install .NET 6 or .NET 8 SDK
```bash
brew install --cask dotnet-sdk
dotnet --version
```

### C. Install Power Platform CLI
```bash
brew install microsoft/powerplatform/powerplatform-cli
pac --version
```

### D. Install Visual Studio Code
```bash
brew install --cask visual-studio-code
```
Or download directly from: [https://code.visualstudio.com](https://code.visualstudio.com)

### E. Recommended VS Code Extensions
Open the Extensions tab (**⇧⌘X**) and install:
- **C#** (by Microsoft)
- **Power Platform Tools**
- **REST Client** (for testing HTTP requests)

## 3. Create and Configure a .NET Plugin Project

### A. Create Project
```bash
mkdir DynamicsAssessment.Plugins
cd DynamicsAssessment.Plugins
dotnet new classlib -f net6.0
code .
```

### B. Add SDK Packages
```bash
dotnet add package Microsoft.PowerPlatform.Dataverse.Client
dotnet add package Microsoft.PowerPlatform.Dataverse.Client.Extensions
```

### C. Example Plugin Code
Create a new file `RequestRoutingPlugin.cs` inside your project:

```csharp
using Microsoft.Xrm.Sdk;
using System;

namespace DynamicsAssessment.Plugins
{
    public class RequestRoutingPlugin : IPlugin
    {
        public void Execute(IServiceProvider serviceProvider)
        {
            var context = (IPluginExecutionContext)serviceProvider.GetService(typeof(IPluginExecutionContext));
            var serviceFactory = (IOrganizationServiceFactory)serviceProvider.GetService(typeof(IOrganizationServiceFactory));
            var service = serviceFactory.CreateOrganizationService(context.UserId);

            try
            {
                var target = (Entity)context.InputParameters["Target"];
                if (target.LogicalName != "ia_request") return;

                var category = target.GetAttributeValue<string>("ia_category");
                var topic = target.GetAttributeValue<string>("ia_topic");

                // TODO: Query Routing Rule table via Dataverse SDK
                // TODO: Assign Queue, set Routing Status, and create Routing Audit entry
            }
            catch (Exception ex)
            {
                throw new InvalidPluginExecutionException($"Routing plugin error: {ex.Message}", ex);
            }
        }
    }
}
```

Build the project:
```bash
dotnet build
```
You should now have a compiled DLL in:  
`bin/Debug/net6.0/DynamicsAssessment.Plugins.dll`

## 4. Connect to Dataverse and Register Plugin

### A. Authenticate to Your Environment
Replace the URL with your environment’s Dataverse URL (from **Settings → Developer Resources** in Power Apps):
```bash
pac auth create --url https://YOUR_ENVIRONMENT.crm.dynamics.com
```

### B. Create or Use an Existing Solution
```bash
pac solution list
pac solution create --name "DynamicsAssessment" --publisher-name "IronArch" --publisher-prefix "ia"
```

### C. Register Plugin Assembly
```bash
pac plugin add --assembly "bin/Debug/net6.0/DynamicsAssessment.Plugins.dll" --solution "DynamicsAssessment"
```

### D. Add Plugin Step
```bash
pac plugin step add --assembly "DynamicsAssessment.Plugins.dll"   --class "DynamicsAssessment.Plugins.RequestRoutingPlugin"   --message Create --entity ia_request --stage PostOperation --mode Async
```

### E. Validate
```bash
pac plugin list
```

## ✅ Summary

After setup, you should have:
- A working **Power Platform Developer Environment**
- A functional **.NET 6+ plugin** registered using Power Platform CLI
- Optional exploration of **Power Automate** as a low-code alternative
- A clear understanding of how to connect, extend, and register Dynamics 365 components from macOS using Visual Studio Code

You’re ready to build and demo your *Technical Assessment – Dynamics* solution.

---

**Tip:**  
If you encounter authentication or registration issues:
```bash
pac auth list
pac auth select --index 1
```
