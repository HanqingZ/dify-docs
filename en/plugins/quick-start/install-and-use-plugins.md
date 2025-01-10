# Install and Use Plugins

Click "Plugins" in the top right corner of the Dify platform to go to the plugin management page of your current Workspace. You can install plugins through these three methods:

* **Marketplace**
* **GitHub**
* **Local Upload**

![Install plugins](https://assets-docs.dify.ai/2024/12/41cdde928c3898a04c6d70cd0543ae4d.png)

### Install Plugins

**Marketplace**&#x20;

The Dify Marketplace contains officially maintained and community-contributed models and tools. Simply click the "Install" button on a plugin to easily install it into your current Workspace.

<figure><img src="https://assets-docs.dify.ai/2024/12/3c19e702158e09941d5783f8dfafd941.png" alt=""><figcaption><p>Install plugins via marketplace</p></figcaption></figure>

**GitHub**&#x20;

You can install plugins directly through GitHub repository links. When installing through this method, ensure the plugin meets code standards. The plugin repository must create a Release and include the plugin package file as an attachment. For detailed requirements, please refer to [Publishing Plugins: GitHub](../publish-plugins/).

<figure><img src="https://assets-docs.dify.ai/2024/12/3c2612349c67e6898a1f33a7cc320468.png" alt=""><figcaption><p>GitHub Installatio</p></figcaption></figure>

**Local Upload**&#x20;

After [packaging a plugin](../publish-plugins/package-the-plugin-file-and-publish-it.md), you'll get a file with a `.difypkg` extension, commonly used in offline or testing environments, allowing installation of plugin files outside the official marketplace. Organizations can develop and maintain internal plugins and install them through local upload to avoid exposing sensitive information.

<figure><img src="https://assets-docs.dify.ai/2024/12/8c31c4025a070f23455799f942b91a57.png" alt=""><figcaption></figcaption></figure>

### **Plugin Authorization**&#x20;

Some third-party service plugins may require API Keys or other forms of authorization. After installation, manual authorization is needed for normal use.&#x20;

> API Keys are sensitive information, and authorization is only valid for the current user. Other team members will need to manually enter their authorization keys when using the plugin.

<figure><img src="https://assets-docs.dify.ai/2024/11/972de4c9fa00f792a1ab734b080aafdc.png" alt=""><figcaption><p>Plugin <strong>Authorization</strong></p></figcaption></figure>

After [installing a plugin](install-and-use-plugins.md) to your Workspace, you can use it within Dify applications. Below is a brief introduction to different usage methods for each type of plugin.

### **Use Plugins**

Taking `OpenAI` as an example, after installing a model type plugin, click on **Profile Picture → Settings → Model Providers** in the top right corner, and configure the API Key to activate the model provider. After authorization, you can select and use this large language model in all application types.

Authorization allows you to select and use this large language model within all application types.

<figure><img src="https://assets-docs.dify.ai/2024/12/4a38b1ea534ca68515839c518c250d2f.png" alt=""><figcaption><p>Using model type plugins</p></figcaption></figure>

### **Tool Type Plugins**&#x20;

Tool type plugins can be used in Chatflow, Workflow, and Agent application types. This section will demonstrate usage in these application types using the `Google` tool plugin as an example.&#x20;

> Some tool plugins require API Key authorization before use, so you can configure them after installation for subsequent use.

#### **Agent**&#x20;

After creating an Agent application, find the **"Tools"** option at the bottom of the application orchestration page. Select the installed tool plugin.&#x20;

When using the application, input tool usage instructions. For example, entering "today's news" will invoke the plugin to use Google search engine for online content retrieval.

<figure><img src="https://assets-docs.dify.ai/2024/12/78f833811cb0c3d5cbbb1a941cffc769.png" alt=""><figcaption><p>Agent Tools</p></figcaption></figure>

#### **Chatflow / Workflow**&#x20;

Chatflow and Workflow type applications share the same workflow orchestration canvas, so the method for using tool type plugins is consistent.&#x20;

You can click the + sign at the end of a node, select the installed Google plugin tool, and connect it to upstream nodes.

<figure><img src="https://assets-docs.dify.ai/2024/12/7e7bcf1f9e3acf72c6917ea9de4e4613.png" alt=""><figcaption><p>Chatflow / Workflow Tools</p></figcaption></figure>

In the plugin's input variables, fill in the user's input query variable or other information that needs online retrieval.

<figure><img src="https://assets-docs.dify.ai/2024/12/a67c4cffd8fdf33297d462b2e6d01d27.png" alt=""><figcaption><p>Tools input</p></figcaption></figure>

Different tool plugins have different usage methods. Please refer to each plugin's introduction page for specific parameter configuration methods.

### **Extension Type Plugins**&#x20;

Extension type plugins are highly customizable applications with independent request endpoints, generally not requiring integration with Dify applications.&#x20;

Different extension type plugins vary greatly in functionality; please refer to each plugin's instructions for detailed usage methods.&#x20;

<figure><img src="https://assets-docs.dify.ai/2024/12/d12c7d40f2a20ebae1d4a441e46689a7.png" alt=""><figcaption><p>Extension plugins usage</p></figcaption></figure>

### **Bundles Type Plugins**&#x20;

Bundles plugins are mainly used for batch installation of multiple plugins simultaneously. A bundle may contain model, tool, and extension type plugins. Specific usage methods should follow each plugin's instructions.
