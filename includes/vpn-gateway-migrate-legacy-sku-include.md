> [!NOTE]
> 从旧的 SKU 迁移到新的 SKU 时，VPN 网关公共 IP 地址会更改。
> 

不能直接在旧的 SKU 和新的 SKU 系列之间调整 Azure VPN 网关大小。 如果你在 Resource Manager 部署模型中的 VPN 网关使用旧版 SKU，则可迁移到新的 SKU。 若要进行迁移，请删除虚拟网络的现有 VPN 网关，然后创建一个新的。

迁移工作流：

1. 删除到虚拟网关的任何连接。
2. 删除旧的 VPN 网关。
3. 创建新的 VPN 网关。
4. 使用新的 VPN 网关 IP 地址更新本地 VPN 设备（适用于站点到站点连接）。
5. 更新将连接到本网关的任何 VNet 到 VNet 本地网关的网关 IP 地址值。
6. 下载适用于 P2S 客户端（通过此 VPN 网关连接到虚拟网络）的新客户端 VPN 配置包。
7. 重新创建到虚拟网关的连接。