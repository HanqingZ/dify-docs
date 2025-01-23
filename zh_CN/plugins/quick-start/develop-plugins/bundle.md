# Bundle 插件包

Bundle 插件包是多个插件的集合。它可以将多个插件打包在一个插件内，以达到批量安装插件的效果，同时提供更强大的服务。

你可以通过 Dify cli 工具将多个插件打包为 Bundle。Bundle 插件包提供三种类型，分别为：

* `Marketplace` 类型。储存了插件的 id 与版本信息。导入时会通过 Dify Marketplace 下载具体的插件包。
* `GitHub` 类型。储存了 GitHub 的仓库地址、 release 版本号和 asset 文件名。导入时 Dify 会访问对应的 GitHub 仓库下载插件包。
* `Package` 类型。插件包会直接被储存在 Bundle 中。它不储存引用源，但可能会造成 Bundle 包体积较大的问题。

### 前置准备

* Dify 插件脚手架工具
* Python 环境，版本号 ≥ 3.10

关于如何准备插件开发的脚手架工具，详细说明请参考[初始化开发工具](initialize-development-tools.md)。

### 创建 Bundle 项目

在当前路径下，运行脚手架命令行工具，创建一个新的插件包项目。

```bash
./dify-plugin-darwin-arm64 bundle init
```

如果你已将该二进制文件重命名为了 `dify` 并拷贝到了 `/usr/local/bin` 路径下，可以运行以下命令创建新的插件项目：

```bash
dify bundle init
```

#### 1. 填写插件信息

按照提示配置插件名称、作者信息与插件描述。如果你是团队协作，也可以将作者填写为组织名。

> 名称长度必须为 1-128 个字符，并且只能包含字母、数字、破折号和下划线。

![Bundle basic information](https://assets-docs.dify.ai/2024/12/03a1c4cdc72213f09523eb1b40832279.png)

填写信息后敲击回车，将自动创建 Bundle 插件项目目录。

![](https://assets-docs.dify.ai/2024/12/356d1a8201fac3759bf01ee64e79a52b.png)

#### 2. 添加依赖

* **Marketplace**

执行以下命令：

```bash
dify-plugin bundle append marketplace . --marketplace_pattern=langgenius/openai:0.0.1
```

其中 marketplace\_pattern 为插件在 marketplace 中的引用，格式为 `组织名/插件名:版本号`。

* **Github**

执行以下命令：

```bash
dify-plugin bundle append github . --repo_pattern=langgenius/openai:0.0.1/openai.difypkg
```

其中 repo\_pattern 为插件在 github 中的引用，格式为 `组织名/仓库名:release/附件名`。

* **Package**

执行以下命令：

```bash
dify-plugin bundle append package . --package_path=./openai.difypkg
```

其中 package\_path 为插件包的目录。

### 打包 Bundle 项目

运行以下命令打包 Bundle 插件：

```bash
dify-plugin bundle package ./bundle
```

执行命令后，当前目录下将自动创建 `bundle.difybndl` 文件，该文件即为最后的打包结果。
