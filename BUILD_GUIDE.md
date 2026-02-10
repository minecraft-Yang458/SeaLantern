# Sea Lantern 构建指南

## 构建发行版

### 一键构建命令

```bash
npm run tauri build
```

这个命令会：
1. 运行 TypeScript 类型检查
2. 构建 Vue 前端（生产优化）
3. 编译 Rust 后端（Release 模式）
4. 打包成 Windows 安装程序

### 构建产物位置

构建完成后，安装包位于：

```
src-tauri/target/release/bundle/
├── msi/              # Windows MSI 安装包
│   └── sea-lantern_0.1.0_x64_zh-CN.msi
└── nsis/             # Windows NSIS 安装包（如果启用）
    └── sea-lantern_0.1.0_x64-setup.exe
```

可执行文件位于：
```
src-tauri/target/release/
└── sea-lantern.exe   # 便携版可执行文件
```

##  构建选项

### 仅构建可执行文件（不打包安装程序）

```bash
npm run tauri build -- --no-bundle
```

这将只生成 `sea-lantern.exe`，可以直接运行，无需安装。

### 指定构建目标

```bash
# 仅构建 MSI
npm run tauri build -- --bundles msi

# 仅构建 NSIS
npm run tauri build -- --bundles nsis

# 构建多个格式
npm run tauri build -- --bundles msi,nsis
```

### 调试模式构建

```bash
npm run tauri build -- --debug
```

## 构建前检查清单

1. ✅ **确保依赖已安装**
   ```bash
   npm install
   ```

2. ✅ **检查 Rust 工具链**
   ```bash
   rustc --version  # 应该显示版本号
   ```

3. ✅ **测试开发版本**
   ```bash
   npm run tauri dev  # 确保没有错误
   ```

4. ✅ **更新版本号**（如果需要）
   - 编辑 `src-tauri/tauri.conf.json` 中的 `version`
   - 编辑 `package.json` 中的 `version`
   - 编辑 `src-tauri/Cargo.toml` 中的 `version`

## 完整构建流程

```bash
# 1. 进入项目目录
cd sea-lantern

# 2. 清理旧的构建产物（可选）
rm -rf src-tauri/target/release/bundle
rm -rf dist

# 3. 安装/更新依赖
npm install

# 4. 构建发行版
npm run tauri build

# 5. 查看构建结果
ls -lh src-tauri/target/release/bundle/msi/
```

## 构建时间参考

- **首次构建**：约 5-15 分钟（需要编译所有 Rust 依赖）
- **后续构建**：约 2-5 分钟（增量编译）

## 优化构建大小

如果想减小安装包大小，可以编辑 `src-tauri/Cargo.toml`：

```toml
[profile.release]
strip = true      # 移除调试符号
lto = true        # 链接时优化
opt-level = "z"   # 优化文件大小
codegen-units = 1 # 减少并行代码生成单元
```

## 常见构建问题

### 问题 1: "npm run tauri build" 失败
**解决方案**：
```bash
# 先单独构建前端
npm run build

# 再构建 Tauri
npm run tauri build
```

### 问题 2: Rust 编译错误
**解决方案**：
```bash
# 清理 Rust 缓存
cd src-tauri
cargo clean
cd ..

# 重新构建
npm run tauri build
```

### 问题 3: 缺少 WebView2（Windows）
Tauri 需要 WebView2 运行时。构建的安装包会自动包含或下载 WebView2。

## 发布检查清单

构建完成后，发布前请确认：

- [ ] 在干净的系统上安装并测试
- [ ] 检查所有功能是否正常
- [ ] 确认设置可以保存和加载
- [ ] 测试服务器的创建、启动、停止
- [ ] 验证控制台日志显示正常
- [ ] 检查应用图标和窗口标题

## 快速发布

如果一切正常，安装包位于：
```
src-tauri/target/release/bundle/msi/sea-lantern_0.1.0_x64_zh-CN.msi
```

可以直接分发这个 MSI 文件给用户安装使用！
