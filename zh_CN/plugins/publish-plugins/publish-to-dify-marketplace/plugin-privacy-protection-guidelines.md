# 插件隐私政策准则

向 Dify Marketplace 提交插件上架申请时，您需要公开说明如何处理用户数据。以下是关于填写插件相关隐私问题和用户数据处理的指南。

插件隐私政策声明内容围绕以下问题展开：

**您的插件是否收集和使用用户个人数据？**&#x5982;有，请整理并列出具体类型。

> 个人数据指可识别特定个人的任何信息，包括单独或与其他信息结合后可用于识别、联系或定位个人的信息。

#### **1. 列出所收集的数据类型**

**类型一：直接识别信息**

* 姓名（全名、名、姓）
* 邮箱
* 手机号
* 住址
* 身份证件号（身份证、护照、驾照等）

\
**类型二：间接识别信息**

* 设备识别码（IMEI、MAC 地址、设备 ID）
* IP 地址
* 位置信息（GPS 坐标、城市、地区）
* 在线标识（cookies、广告 ID）
* 用户名
* 头像
* 生物特征（指纹、人脸识别）
* 浏览记录
* 购物记录
* 健康数据
* 财务信息

\
**类型三：被用于识别个人的组合信息**

* 年龄
* 性别
* 职业
* 兴趣爱好

即使您的插件本身不收集个人信息，也请确认插件使用的第三方服务是否涉及数据收集或处理。作为开发者，您需要公开所有数据收集活动，包括第三方服务的数据处理。请务必阅读第三方服务的隐私政策，确保在提交时声明插件涉及的所有数据收集情况。

例如，当你正在开发的插件涉及 Slack 服务时，请在插件的隐私政策声明文件中引用 [Slack 的隐私政策](https://slack.com/trust/privacy/privacy-policy)并声明数据收集情况。

#### 2. 填写最新版本的插件隐私政策

**插件隐私政策**必须包含：

* 收集的数据类型
* 数据用途
* 是否与第三方共享数据（如有，请列出第三方服务的名称及其隐私政策链接）
* 如果不熟悉隐私政策写法，可以参考由 Dify 官方维护插件内的隐私政策

#### **3. 在插件 Manifest 文件内引入隐私政策声明**

详细字段的填写说明请参考 [Manifest](../../schema-definition/manifest.md)。

### &#x20;**常见问题**

1. **什么是"收集和使用"用户个人数据？有哪些常见案例？**

“收集和使用”指对用户数据进行收集、传输、使用或共享。常见情况包括：

* 通过表单收集个人信息；
* 使用登录功能（含第三方认证）；
* 收集可能包含个人信息的输入或资源；
* 分析用户行为、互动和使用情况；
* 存储通讯内容，如消息、聊天记录、邮箱；
* 访问关联社交媒体的用户资料；
* 收集健康数据，如运动、心率、医疗信息；
* 保存搜索记录或浏览行为；
* 处理财务信息，如银行资料、信用分、交易记录。