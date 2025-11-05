# 使用参数
| 名称              | 说明                                                         |替换参数              |
|-------------------|--------------------------------------------------------------|-------------------|
| **OCI_USER**      | Oracle Cloud 用户唯一标识符 |       |
| **OCI_TENANCY**   | Oracle Cloud 租户唯一标识符 |  **<COMPARTMENT_ID>**  |
| **OCI_FINGERPRINT** | API密钥指纹 |       |
| **OCI_PRIVATE_KEY** | API认证私钥（PEM格式，未加密） |       |
| **OCI_REGION**    | 区域标识符 |       |
| **SSH_PUBLIC_KEY**    | 用于 SSH 登录的公钥 |   **<SSH_PUBLIC_KEY>**    |



# 1. 列出并检查 VM.Standard.A1.Flex 实例
```bash
oci compute instance list \
--compartment-id <COMPARTMENT_ID> \
--query 'data[?shape==`VM.Standard.A1.Flex`].{id:id, name:"display-name", shape:shape, state:"lifecycle-state"}' \
--raw-output
```

手动测试：

替换 `<COMPARTMENT_ID>` 为你实际的 OCI 租户 ID。

执行此命令，获取所有形状为 `VM.Standard.A1.Flex` 的实例，并过滤掉生命周期状态为 TERMINATED 的实例。

# 2. 获取可用域（Availability Domain）
```bash
oci iam availability-domain list \
--compartment-id <COMPARTMENT_ID> \
--query 'data[0]."name"' \
--raw-output
```

手动测试：

将 `<COMPARTMENT_ID>` 替换为你的实际租户 ID。

执行命令，获取租户下的第一个可用域的名称(`<AD_NAME>`)。

# 3. 获取 Ubuntu 22.04 镜像 ID
```bash
oci compute image list \
--compartment-id <COMPARTMENT_ID> \
--operating-system "Canonical Ubuntu" \
--operating-system-version "22.04" \
--all \
--query 'data[*].{id: id, displayName: "display-name"}' \
--raw-output
```

手动测试：

将 `<COMPARTMENT_ID>` 替换为你的实际租户 ID。

执行此命令，查询操作系统为 Ubuntu 22.04 的所有镜像 ID(`<IMAGE_ID>`)。

# 4. 获取 VCN（虚拟云网络）ID
```bash
oci network vcn list \
--compartment-id <COMPARTMENT_ID> \
--query 'data[0]."id"' \
--raw-output
```

手动测试：

将 `<COMPARTMENT_ID>` 替换为你的实际租户 ID。

执行此命令，获取租户下第一个虚拟云网络的 ID(`<VCN_ID>`)。

# 5. 获取子网 ID
```bash
oci network subnet list \
--compartment-id <COMPARTMENT_ID> \
--vcn-id <VCN_ID> \
--query 'data[0]."id"' \
--raw-output
```

手动测试：

将 `<COMPARTMENT_ID>` 和 `<VCN_ID>` 替换为你实际的租户 ID 和虚拟云网络 ID。

执行此命令，获取子网 ID(`<SUBNET_ID>`)。

# 6. 创建新的 Ubuntu ARM 实例
```bash
oci compute instance launch \
--availability-domain <AD_NAME> \
--compartment-id <COMPARTMENT_ID> \
--shape "VM.Standard.A1.Flex" \
--shape-config '{"ocpus": 4, "memoryInGBs": 24}' \
--display-name <INSTANCE_NAME> \
--image-id <IMAGE_ID> \
--subnet-id <SUBNET_ID> \
--ssh-authorized-keys-file <(echo "<SSH_PUBLIC_KEY>") \
--assign-public-ip true
```

手动测试：

替换 `<AD_NAME>`, `<COMPARTMENT_ID>`, `<INSTANCE_NAME>`, `<IMAGE_ID>`, `<SUBNET_ID>`, 和 `<SSH_PUBLIC_KEY>` 为你的实际值。

`INSTANCE_NAME`是实例的名称可以自定义，例如：`ubuntu-arm-4c24g-20`

执行此命令，创建新的 `Ubuntu ARM` 实例。确保 `SSH_PUBLIC_KEY` 已经存在。