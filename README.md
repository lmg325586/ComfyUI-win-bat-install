# 🚀 ComfyUI ROCm 7.2 一键部署脚本

> 专为 Windows 11 + AMD GPU 用户设计的自动化部署方案，\\\*\\\*严格遵循 ROCm 7.2 官方要求（仅支持 Python 3.12）\\\*\\\*，5 分钟完成从零到 GPU 加速的 ComfyUI 部署。

\---

## ⚠️ 硬性系统要求（部署前必读）

|项目|要求|验证方法|
|-|-|-|
|**操作系统**|Windows 11 22H2+ (Build 22621+)|`winver` 命令|
|**AMD 驱动**|Adrenalin 26.1.1 或更高|AMD Software → 驱动版本|
|**GPU 型号**|gfx1100/gfx1101/gfx1200/gfx1201 架构：<br>• Radeon RX 9070/9060 XT/7900 XTX<br>• Ryzen AI 300 系列 APU|`dxdiag` → 显卡信息|
|**Python**|**仅支持 3.12**（脚本自动安装）|`python --version`|
|**磁盘空间**|≥15 GB（含模型缓存）|`dir F:\\\\`|
|**网络**|可访问 `repo.radeon.com`|`ping repo.radeon.com`|

> ❗ \\\*\\\*不满足任一条件将导致部署失败\\\*\\\*  
> - Windows 10 ❌ 不支持  
> - 驱动 < 26.1.1 ❌ 不支持  
> - Python 3.11 ❌ 不支持（ROCm wheel 严格绑定 cp312）

\---

## 📂 目录结构

部署完成后自动生成以下结构：

F:\\ComfyUI
├── comfyui-venv\\ ← Python 3.12 虚拟环境（含 ROCm PyTorch）
├── ComfyUI\\ ← 官方仓库（git clone）
│ ├── main.py ← ComfyUI 入口
│ ├── models\\ ← 模型目录（需手动下载）
│ │ ├── checkpoints\\ ← SD 模型（.safetensors）
│ │ └── ...
│ └── ...
├── deploy\_comfyui.bat ← 【首次运行】一键部署脚本
└── start\_comfyui.bat ← 【日常使用】一键启动脚本

\---

## 🚀 使用指南

### 步骤 1：首次部署（仅需 1 次）

1. 将 `install-ComfyUI(gfx120Xgfx110X)AMD9000系7000系.bat` 保存至任意文件夹
2. **右键 → 以管理员身份运行**
3. 脚本自动执行：

   * 检测 Python 3.12 → 不存在则用 Winget 安装
   * 创建虚拟环境
   * 安装 ROCm 7.2 SDK 组件（4 个必须项）
   * 安装 PyTorch ROCm 版本（3 个 wheel）
   * 克隆 ComfyUI 仓库
   * 安装 Python 依赖
4. 完成后提示：部署成功！
5.下一步：双击 start\_comfyui.bat 启动服务



> ⏱️ \\\*\\\*耗时\\\*\\\*：5-15 分钟（取决于网络下载速度）  
> 💡 \\\*\\\*提示\\\*\\\*：首次运行需联网下载约 2.5 GB 组件

### 步骤 2：日常启动（每次使用）

1. 双击 `start\\\_comfyui.bat`
2. 自动执行：
* 激活虚拟环境
* 启动 ComfyUI 服务（后台最小化运行）
* 检测 8188 端口就绪
* **自动打开浏览器** → `http://127.0.0.1:8188`
3. 浏览器中操作生成图像

> 💡 \\\*\\\*提示\\\*\\\*：  
> - 本窗口可直接关闭，\\\*\\\*不影响后台服务运行\\\*\\\*  
> - 终止服务：关闭最小化的 "ComfyUI Backend" 窗口 或 按 `Ctrl+C`

\---

## 🔧 故障排查

|问题|原因|解决方案|
|-|-|-|
|`not a supported wheel`|未先安装 ROCm SDK 组件|重新运行 `deploy\\\_comfyui.bat`（确保步骤 5 在步骤 6 之前）|
|`torch.cuda.is\\\_available() = False`|1. 驱动 < 26.1.1<br>2. GPU 不在支持列表<br>3. 未重启系统|1. 更新 AMD 驱动至 26.1.1+<br>2. 检查 [兼容性列表](https://rocm.docs.amd.com/projects/radeon-ryzen/en/latest/docs/compatibility/compatibilityrad/windows/windows_compatibility.html)<br>3. 安装驱动后**必须重启**|
|部署卡在 `Installing ROCm SDK`|网络无法访问 repo.radeon.com|1. 检查代理/防火墙<br>2. 尝试手机热点|
|`git clone failed`|未安装 Git|从 [git-scm.com](https://git-scm.com/) 安装 Git 并勾选 "Add to PATH"|
|浏览器未自动打开|8188 端口被占用|1. 检查其他程序占用 `netstat -ano|

\---

## ⚙️ 高级配置（可选）

### 修改启动参数

编辑 `start\\\_comfyui.bat` 中的启动命令：

```batch
:: 自定义端口
start "ComfyUI Backend" /min cmd /c "python main.py --port 8888"

---

模型下载指引
访问 Civitai 或 Hugging Face
下载 .safetensors 模型文件
放置路径：F:\\\\ComfyUI\\\\ComfyUI\\\\models\\\\checkpoints\\\\
刷新 ComfyUI UI 即可使用

---

📜 许可与声明
本脚本基于 AMD 官方 ROCm 文档 编写：
PyTorch 安装指南
ComfyUI 安装指南

---

💡 常见问题

Q：部署后能否移动目录？
A：可以，但需同时移动 comfyui-venv 和 ComfyUI 两个文件夹，且保持相对路径不变。

Q：Windows 10 能用吗？
A：不能。ROCm 7.2 官方仅支持 Windows 11，Windows 10 无任何 workaround。



