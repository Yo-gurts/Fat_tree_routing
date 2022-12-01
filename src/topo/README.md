
# 创建拓扑的节点

1. 第一次开机后，设置hugepage，加载 openvswitch 模块

    ```bash
    dpdk-hugepages.py -p 1G --setup 25G

    modprobe openvswitch
    ```

2. 准备 ovsdpdk 镜像

3. `./topo create_topo` 注意修改镜像名称

4. `./topo add_flow` 添加流表

5. `./topo del_flow` 删除流表

6. `./topo destroy_topo` 销毁拓扑、容器
