# 使用不到，代码和示例有问题暂不修复。
# 使用参数
| 名称              | 说明                                                         |替换参数              |
|-------------------|--------------------------------------------------------------|-------------------|
| **OCI_USER**      | Oracle Cloud 用户唯一标识符 |       |
| **OCI_TENANCY**   | Oracle Cloud 租户唯一标识符 |  **<COMPARTMENT_ID>**  |
| **OCI_FINGERPRINT** | API密钥指纹 |       |
| **OCI_PRIVATE_KEY** | API认证私钥（PEM格式，未加密） |       |
| **OCI_REGION**    | 区域标识符 |       |

# 1. 列出所有实例
```bash
oci compute instance list \
--compartment-id <COMPARTMENT_ID> \
--query "data[].[id]" \
--output json
```

手动测试：

将 `<COMPARTMENT_ID>` 替换为你的实际值。

运行此命令将列出指定 compartment 中所有实例的 ID(`<INSTANCE_ID>`)。

# 2. 获取实例的详细信息
```bash
# 获取CPU核心
oci compute instance get \
--instance-id <INSTANCE_ID> \
--output json | jq '.data."shape-config".vcpus'
```

手动测试：

替换 `<INSTANCE_ID>` 为你自己的实例 ID。

查询实例的 CPU核心。

# 3. 获取 CPU 使用率（过去 7 天）
```bash
oci monitoring metric-data summarize-metrics-data \
--compartment-id <compartment_OCID> \
--namespace oci_computeagent \
--query-text "CpuUtilization[1m]{resourceId = \"<instance_OCID>\"}.max()" \
--start-time $(date -u -d '7 days ago' +'%Y-%m-%dT%H:%M:%SZ') \
--end-time $(date -u +'%Y-%m-%dT%H:%M:%SZ')
```

手动测试：

将 `<COMPARTMENT_ID>`, `<INSTANCE_ID>` 替换为实际值。你可以使用 date 命令来获取合适的日期格式。

运行此命令，获取指定实例过去 7 天内的 CPU 使用率数据。

# 4. 获取网络使用率（过去 7 天）
```bash
oci monitoring metric data query --namespace oci_compute --compartment-id <COMPARTMENT_ID> --resource-id <INSTANCE_ID> --metric-name NetworkUtilization --start-time $(date -d '7 days ago' --utc +%FT%TZ) --end-time $(date --utc +%FT%TZ) --query "data[0].value[95]" --output json
```

手动测试：

替换 `<COMPARTMENT_ID>`, `<INSTANCE_ID>`为实际值。

执行此命令，获取过去 7 天内的网络使用率数据。

# 5. 获取内存使用率（过去 7 天）
```bash
oci monitoring metric data query --namespace oci_compute --compartment-id <COMPARTMENT_ID> --resource-id <INSTANCE_ID> --metric-name MemoryUtilization --start-time $(date -d '7 days ago' --utc +%FT%TZ) --end-time $(date --utc +%FT%TZ) --query "data[0].value[95]" --output json
```

手动测试：

替换 `<COMPARTMENT_ID>`, `<INSTANCE_ID>` 为实际值。

执行此命令，查询指定实例的内存使用情况。

# 6. 列出虚拟网络接口（VNIC）
```bash
oci iam availability-domain list --compartment-id <COMPARTMENT_ID> --query 'data[0]."name"' --raw-output
```

手动测试：

将 `<INSTANCE_ID>` 替换为实际值。

运行此命令，获取指定实例的公共 IP 地址(`<INSTANCE_IP>`)。

# 7. 远程执行 SSH 命令
```bash
ssh -i <PRIVATE_KEY_PATH> <OCI_USERNAME>@<INSTANCE_IP> <<EOF
  docker run -d --name lookbusy --hostname lookbusy \
    -e TZ=Asia/Shanghai \
    -e CPU_UTIL="20-30" \
    -e MEM_UTIL=20 \
    -e SPEEDTEST_INTERVAL=10 \
    fogforest/lookbusy:latest
EOF
```

手动测试：

替换 `<PRIVATE_KEY_PATH>`, `<OCI_USERNAME>`, 和 `<INSTANCE_IP>` 为实际的 SSH 私钥路径、OCI 用户名和实例 IP 地址。

`<OCI_USERNAME>`为SSH登录用户名，比如ubuntu

执行此命令，使用 SSH 远程执行命令启动 Docker 容器。

# 8. 删除 Docker 容器
```bash
ssh -i <PRIVATE_KEY_PATH> <OCI_USERNAME>@<INSTANCE_IP> <<EOF
  docker rm -f lookbusy
EOF
```

手动测试：

将 `<PRIVATE_KEY_PATH>`, `<OCI_USERNAME>`, 和 `<INSTANCE_IP>` 替换为你的实际 SSH 私钥路径、OCI 用户名和实例 IP 地址。

`<OCI_USERNAME>`为SSH登录用户名，比如ubuntu

执行此命令，通过 SSH 删除名为 lookbusy 的 Docker 容器。