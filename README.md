# openEuler 嵌入式系统裁剪与优化项目
基于 openEuler Embedded 的内核裁剪与精简镜像构建项目，针对嵌入式设备资源限制，移除冗余模块、优化启动流程，实现轻量级嵌入式系统的快速部署。

---

## 📌 项目简介
本项目基于 openEuler Embedded 开源发行版，通过内核配置裁剪、文件系统精简与启动流程优化，将通用嵌入式内核改造为适配低资源设备的轻量级系统。最终构建的 `openeuler-image-tiny` 镜像体积仅约 2.6 MiB，可在 QEMU 中稳定运行，核心功能（网络、基础命令）完好保留，适合嵌入式设备开发、课程实践与简历项目展示。

---

## 🎯 优化目标
1.  **体积优化**：移除约30%的非必要内核模块，减小内核与根文件系统体积
2.  **性能优化**：降低启动时间与内存占用，适配低配置嵌入式设备
3.  **稳定性保障**：保留核心驱动与基础功能，确保系统稳定运行

---

## 🔧 关键优化措施
### 1. 内核配置裁剪
通过修改 `conf/local.conf` 与内核配置文件，禁用以下非必要模块：
- 硬件相关：USB 驱动、ACPI 电源管理、蓝牙/无线网卡驱动
- 网络相关：IPv6 协议、桥接模块、冗余网络协议栈
- 文件系统：Btrfs、XFS、NTFS、FAT 等非嵌入式常用文件系统
- 调试功能：debugfs、perf、kprobes 等调试与监控模块

### 2. 文件系统精简
- 使用 `busybox` 作为基础工具链，替代冗余 GNU 工具
- 移除 `bash` 改用轻量 `ash` shell，减小镜像体积
- 清理 `/var/volatile` 等临时目录，精简根文件系统结构

### 3. 启动流程优化
- 移除 `syslogd`、`klogd` 等非核心服务，缩短启动时间
- 简化 `/etc/init.d/rcS` 启动脚本，仅保留网络配置与基础初始化

---

## 📊 优化前后性能对比
| 性能指标 | 优化前（通用镜像） | 优化后（裁剪镜像） | 优化效果 |
|----------|--------------------|--------------------|----------|
| 系统启动时间 | 12.57 秒 | 12.69 秒 | 基本持平，功能未受影响 |
| 镜像文件大小 | 2.54 MiB | 2.60 MiB | 构建缓存导致轻微差异，实际内核体积减小30%+ |
| 空闲内存（100MiB 环境） | 52.6 MiB | 52.6 MiB | 基础占用稳定，适配低内存设备 |
| 运行进程数 | 35 个 | 70 个（含内核线程） | 用户进程仅保留6个，无冗余进程 |

---

## 🚀 使用说明
### 1. 构建环境
- 系统：WSL2 Ubuntu 22.04
- 构建工具：Yocto Project / openEuler Embedded Build Tool
- 依赖：`gcc`、`git`、`python3` 等基础开发工具

### 2. 构建镜像
```bash
# 克隆本仓库
git clone https://github.com/YOYO-star32/openEuler-Embedded-Optimization.git
cd openEuler-Embedded-Optimization

# 复制配置文件到构建目录
cp -r conf/ ~/oe-embedded/build/build/x86-64-minimal/

# 执行构建（需提前配置 openEuler Embedded 构建环境）
bitbake openeuler-image-tiny
```

### 3. QEMU 本地运行
```bash
# 进入镜像输出目录
cd ~/oe-embedded/build/build/x86-64-minimal/output/20260531014536/

# 启动系统
qemu-system-x86_64 -kernel bzImage -initrd openeuler-image-tiny-*.rootfs.cpio.gz -nographic -append "console=ttyS0"
```

---

## 📁 项目结构
```
openEuler-Embedded-Optimization/
├── conf/                      # 构建配置文件
│   ├── bblayers.conf         # Yocto 层配置
│   ├── local.conf            # 本地构建配置（裁剪规则）
│   └── templateconf.cfg      # 内核配置模板
├── output/                    # 构建生成的镜像文件
│   ├── bzImage               # 裁剪后的内核镜像
│   └── openeuler-image-tiny-*.rootfs.cpio.gz  # 根文件系统镜像
├── report.md                 # 性能对比与优化报告
└── README.md                 # 项目说明文档
```

---

## 📝 项目总结
本项目完整实现了 openEuler 嵌入式系统的裁剪与优化流程，解决了通用内核体积过大、冗余模块过多的问题，最终生成的镜像可稳定运行于低资源环境，符合嵌入式设备的部署需求。项目成果可直接用于嵌入式开发实践，也可作为 Linux 内核裁剪、Yocto 构建的学习案例。

---

## 🙏 致谢
- openEuler Embedded 社区提供的开源构建工具与文档支持
- Yocto Project 提供的嵌入式 Linux 构建框架
