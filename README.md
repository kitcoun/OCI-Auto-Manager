
# OCI ARM 实例自动管理脚本

## 功能简介
- 每小时自动检查租户是否存在实例
- 若无实例则自动创建新的 Ubuntu ARM 实例（4C24G）
- 操作系统：Canonical Ubuntu 22.04 Minimal aarch64
- 实例类型：VM.Standard.A1.Flex（Oracle Cloud 始终免费）

## 限制

免费限制：[说明](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm?utm_source=chatgpt.com)

示例说明
- 假设你有一个非 A1 形状的实例：
- 如果 7 天内 95% 的时间 CPU 利用率为 15%，同时网络利用率为 18% → 会被视为闲置
- 如果 7 天内 90% 的时间 CPU 利用率为 15%，5% 的时间为 25% → 不会被视为闲置
- 如果 CPU 利用率始终低于 20%，但网络利用率始终高于 20% → 不会被视为闲置

回收方式：通常是先停止实例，而不是直接删除，用户可以手动重启

## 工作流程说明

### 触发条件
- **自动触发**：每小时整点执行一次
- **手动触发**：可通过 GitHub Actions 界面手动启动

### 执行逻辑
1. 检查租户中是否存在任何实例（所有状态都会检查）
2. 如果发现`VM.Standard.A1.Flex`实例存在：
	- 立即终止工作流
	- 不进行任何实例操作
3. 如果确认没有`VM.Standard.A1.Flex`实例：
	- 创建新的 Ubuntu ARM 实例
	- 配置 4 OCPU 和 24GB 内存
	- 分配公网 IP
	- 注入 SSH 密钥

---

## 所需 GitHub Secrets 说明
| 名称              | 说明                                                         | 示例/获取方式 |
|-------------------|--------------------------------------------------------------|--------------|
| **OCI_USER**      | Oracle Cloud 用户唯一标识符<br>控制台 → 用户设置 → 用户OCID  | `ocid1.user.oc1..aaaaaaaaxxxxxxxx` |
| **OCI_TENANCY**   | Oracle Cloud 租户唯一标识符<br>控制台 → 租户信息 → 租户OCID | `ocid1.tenancy.oc1..aaaaaaaaxxxxxxxx` |
| **OCI_FINGERPRINT** | API密钥指纹<br>控制台 → 个人资料 → API密钥                  | `aa:bb:cc:dd:...` |
| **OCI_PRIVATE_KEY** | API认证私钥（PEM格式，未加密）<br>使用 openssl 生成         | `-----BEGIN RSA PRIVATE KEY----- ...` |
| **OCI_REGION**    | 区域标识符<br>推荐：us-ashburn-1, ap-seoul-1, eu-frankfurt-1 | `us-ashburn-1` |
| **SSH_PUBLIC_KEY** | 用于 SSH 登录的公钥<br>使用 ssh-keygen 生成                 | `ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAB...` |

> 获取方式可参考 Oracle Cloud 控制台相关页面。

---

## 快速链接（Oracle Cloud 控制台）

以下链接可直接打开 Oracle Cloud 控制台相关页面，用于查找或生成上表提到的参数：

- OCI 用户（User OCID）：https://cloud.oracle.com/identity/domains/my-profile
- 租户（Tenancy OCID）：https://cloud.oracle.com/tenancy
- API 密钥与指纹（Fingerprint / Private Key）：https://cloud.oracle.com/identity/domains/my-profile/auth-tokens
- 区域列表（Regions）：https://cloud.oracle.com/regions

提示：控制台页面可能需要先登录 Oracle Cloud 帐户。

---

## 手动测试 [示例](https://github.com/kitcoun/OCI-Auto-Manager/wiki)
> 在Oracle Cloud 控制台-实例-右上角开发人员工具-Cloud Shell

> 输入OCI代码

如需详细操作步骤或遇到问题，请参考 Oracle Cloud 官方文档。
