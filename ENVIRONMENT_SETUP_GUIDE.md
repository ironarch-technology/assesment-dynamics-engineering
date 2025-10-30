# ⚙️ IronArch Dynamics Challenge — Environment Setup Guide (macOS / Visual Studio Code)

This guide provides **macOS instructions** for setting up a free **Dynamics 365 / Power Platform Developer Environment** and the **.NET 6+ Dataverse SDK** tools needed to complete the IronArch Dynamics Challenge.

You’ll use **Visual Studio Code**, **.NET 6 CLI**, and the **Power Platform CLI (PAC)** to build and register plugins, and Power Automate for low-code workflows.

---

## 🧱 1. Create a Free Power Platform Developer Environment

1. Go to:  
   👉 [https://powerapps.microsoft.com/developerplan](https://powerapps.microsoft.com/developerplan)
2. Click **“Get started free.”**
3. Sign in with a **Microsoft 365 or Outlook account**.  
   > If you don’t have one, create a free [Outlook.com](https://outlook.com) account first.
4. Complete setup. This creates your **personal Developer Environment** with Dataverse and Power Automate.
5. Open your environment at [https://make.powerapps.com](https://make.powerapps.com).
6. Verify you’re in your **Developer Environment** (see the top-right selector).
7. Navigate to **Tables → + New table** to start creating entities.

---

## 💻 2. Install Required Tools on macOS

### A. Install Homebrew (if not already installed)
Open Terminal and run:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### B. Install .NET 6 or .NET 8 SDK
```bash
brew install --cask dotnet-sdk
```
Verify:
```bash
dotnet --version
```

### C. Install Power Platform CLI
```bash
brew install microsoft/powerplatform/powerplatform-cli
```
Check install:
```bash
pac --version
```

### D. Install Visual Studio Code
```bash
brew install --cask visual-studio-code
```
Or download directly from: [https://code.visualstudio.com](https://code.visualstudio.com)

### E. Install VS Code Extensions
In VS Code, open the Extensions tab (⇧⌘X) and install:
- **C#** (by Microsoft)
- **Power Platform Tools** (optional)
- **REST Client** (for testing HTTP requests)

---

## ⚙️ 3. Create and Configure a .NET Plugin Project

### A. Create Project
In Terminal, navigate to your workspace folder:
```bash
mkdir IronArch.DynamicsChallenge.Plugins
cd IronArch.DynamicsChallenge.Plugins
dotnet new classlib -f net6.0
code .
```

### B. Add SDK Packages
In the VS Code terminal, install dependencies:
```bash
dotnet add package Microsoft.PowerPlatform.Dataverse.Client
dotnet add package Microsoft.PowerPlatform.Dataverse.Client.Extensions
```

### C. Example Plugin Code
Create a new file `RequestRoutingPlugin.cs` inside the project:

```csharp
using Microsoft.Xrm.Sdk;
using System;

namespace IronArch.DynamicsChallenge.Plugins
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

                // TODO: Lookup Routing Rule and assign Queue
                // TODO: Create Routing Audit entry
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
```
bin/Debug/net6.0/IronArch.DynamicsChallenge.Plugins.dll
```

---

## 🔗 4. Connect to Dataverse and Register Plugin

### A. Authenticate
Replace the URL with your environment’s Dataverse URL (found under **Settings → Developer Resources** in Power Apps):
```bash
pac auth create --url https://YOUR_ENVIRONMENT.crm.dynamics.com
```

### B. Create or Use an Existing Solution
```bash
pac solution list
pac solution create --name "IronArchDynamicsChallenge" --publisher-name "IronArch" --publisher-prefix "ia"
```

### C. Register Plugin Assembly
```bash
pac plugin add --assembly "bin/Debug/net6.0/IronArch.DynamicsChallenge.Plugins.dll" --solution "IronArchDynamicsChallenge"
```

### D. Add a Plugin Step
```bash
pac plugin step add --assembly "IronArch.DynamicsChallenge.Plugins.dll"   --class "IronArch.DynamicsChallenge.Plugins.RequestRoutingPlugin"   --message Create --entity ia_request --stage PostOperation --mode Async
```

### E. Validate Registration
```bash
pac plugin list
```

---

## ⚙️ 5. Power Automate Flow Setup

1. Go to [https://make.powerautomate.com](https://make.powerautomate.com).
2. Ensure your **Developer Environment** is selected.
3. Create a new **Automated Cloud Flow**:
   - Trigger: “When a row is added, modified or deleted (V3)”
   - Table: **Request**
4. Add actions to:
   - Query **Routing Rule**
   - Update the **Request** Queue and Routing Status
   - Create a **Routing Audit** record
5. Test the flow by creating a new Request record in Power Apps.

---

## 🌐 6. Test External API Integration

Use [https://webhook.site](https://webhook.site) to create a test endpoint.

Example HTTP action body (Power Automate):
```http
POST https://webhook.site/your-unique-id
Content-Type: application/json

{
  "requestId": "@{triggerOutputs()?['body/ia_requestid']}",
  "status": "Resolved"
}
```

You’ll see the POST payload logged live in your webhook.site dashboard.

---

## 🧹 7. Cleanup (Optional)

Remove the plugin assembly if desired:
```bash
pac plugin delete --assembly "IronArch.DynamicsChallenge.Plugins.dll"
```

You can keep your Developer Environment — it’s **free and renewable**.

---

## ✅ Summary

After setup, you should have:
- A working **Power Platform Developer Environment**
- Power Automate connected to Dataverse
- A .NET 6+ plugin built and registered from **macOS**
- A mock endpoint ready to simulate API notifications

You’re ready to build and demo your **IronArch Dynamics Challenge** solution on macOS using VS Code.

---

**Tip:**  
If you encounter errors with authentication or registration, verify your environment context:
```bash
pac auth list
pac auth select --index 1
```
