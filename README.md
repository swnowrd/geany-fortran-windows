# Windows 上用 Geany 写 Fortran：从安装到运行的完整教程

> **适合人群**：完全零基础，刚接触 Fortran 或从 Linux 转到 Windows 的用户
>
> **测试环境**：Windows 10/11 x64 + Geany 2.1 + GNU Fortran 15.2.0 (via Miniconda)

---

## 目录

1. [为什么选 Geany？](#1-为什么选-geany)
2. [安装 gfortran 编译器](#2-安装-gfortran-编译器)
3. [安装 Geany](#3-安装-geany)
4. [配置 Geany 支持 Fortran 编译](#4-配置-geany-支持-fortran-编译)
5. [写第一个 Fortran 程序：Hello World](#5-写第一个-fortran-程序hello-world)
6. [常见问题 & 排坑指南](#6-常见问题--排坑指南)
7. [附录：完整配置文件参考](#7-附录完整配置文件参考)

---

## 1. 为什么选 Geany？

Geany 是一款轻量级的代码编辑器，适合做 Fortran 开发的原因：

- **轻量**：安装包不到 30 MB，启动秒开
- **内置编译支持**：可以直接在编辑器里按 F8 编译、F5 运行
- **语法高亮**：开箱支持 Fortran 90/95/2003/2018 关键字
- **免费开源**：跨平台，Windows/Linux/macOS 均可用

---

## 2. 安装 gfortran 编译器

Fortran 代码需要编译器才能运行。在 Windows 上，**推荐用 Miniconda 安装 gfortran**，这是最省事的方式。

### 方法一：通过 Miniconda 安装（推荐）

**第一步：安装 Miniconda**

前往 [https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html) 下载 Windows 版安装包，安装到非系统盘（如 `D:\Miniconda3`）。

**第二步：安装 gfortran**

打开 Anaconda Prompt，执行：

```bash
conda install -c conda-forge gfortran
```

等待安装完成（几分钟）。

**第三步：验证安装**

打开 PowerShell 或 CMD，输入：

```
gfortran --version
```

如果看到类似以下输出，说明安装成功：

```
GNU Fortran (conda-forge gcc 15.2.0-19) 15.2.0
```

> **注意**：记住 gfortran 的安装路径，后面配置 Geany 时需要用到。
> 通常在 `D:\Miniconda3\Library\bin\gfortran.exe`（根据你的安装位置调整）。

### 方法二：通过 MSYS2 安装（适合需要完整 Unix 工具链的用户）

```bash
# 在 MSYS2 终端里执行
pacman -S mingw-w64-x86_64-gcc-fortran
```

安装后 gfortran 通常在 `C:\msys64\mingw64\bin\gfortran.exe`。

---

## 3. 安装 Geany

前往 [https://www.geany.org/download/releases/](https://www.geany.org/download/releases/) 下载 Windows 版安装包（选 `.exe` 安装程序），按提示安装。

推荐安装到非系统盘，如 `D:\Program Files\Geany`。

---

## 4. 配置 Geany 支持 Fortran 编译

Geany 安装后需要手动配置编译命令。配置只需做一次。

### 4.1 找到配置文件位置

Geany 的用户配置文件在：

```
C:\Users\你的用户名\AppData\Roaming\geany\filedefs\
```

> **提示**：`AppData` 是隐藏文件夹。在资源管理器地址栏直接输入 `%APPDATA%\geany\filedefs` 回车即可打开。

### 4.2 创建或编辑 filetypes.fortran

在 `filedefs` 文件夹下，新建（或编辑）一个名为 `filetypes.fortran` 的文件，写入以下内容：

```ini
[settings]
extension=f90
mime_type=text/x-fortran
comment_single=!
comment_use_indent=false
context_action_cmd=

[indentation]
width=4
type=1

[build_settings]
# 编译：生成目标文件 .o（检查语法错误用）
compiler=gfortran -Wall -Wno-tabs -g -c "%f"

# 生成：编译并链接，生成可执行 .exe
# -static 让 exe 独立运行，不依赖外部 DLL
linker=gfortran -static -Wall -Wno-tabs -g "%f" -o "%e.exe"

# 运行：直接执行生成的 exe
run_cmd="%e.exe"

[build-menu]
FT_00_LB=编译(_C)
FT_00_CM=gfortran -Wall -Wno-tabs -g -c "%f"
FT_00_WD=
FT_01_LB=生成(_B)
FT_01_CM=gfortran -static -Wall -Wno-tabs -g "%f" -o "%e.exe"
FT_01_WD=
EX_00_LB=执行(_E)
EX_00_CM="%e.exe"
EX_00_WD=
```

> **注意**：上面的配置用的是 `gfortran`（不带路径），前提是 gfortran 已经在系统 PATH 中。
> 如果运行时报「找不到 gfortran」，把 `gfortran` 替换成完整路径，例如：
> `D:\Miniconda3\Library\bin\gfortran.exe`

### 4.3 重启 Geany

**改完配置文件后，必须重启 Geany 才能生效**（或用菜单「工具 → 重新加载配置」）。

---

## 5. 写第一个 Fortran 程序：Hello World

### 5.1 新建文件

打开 Geany，`Ctrl+N` 新建文件，`Ctrl+Shift+S` 另存为，文件名写 `hello.f90`（注意扩展名是 `.f90`）。

### 5.2 写代码

输入以下代码：

```fortran
program hello
    implicit none
    write(*,*) "Hello, Fortran!"
end program hello
```

保存文件（`Ctrl+S`）。

### 5.3 编译

按 **`F8`**（或点菜单「生成 → 编译」）。

Geany 底部的「编译器」标签页会显示编译输出。如果没有报错，说明编译成功。

> **关于乱码**：编译器输出窗口如果显示乱码，**忽略它即可**，这是 Windows cmd 的编码问题，不影响实际编译结果。只看是否有 `error:` 就行。

### 5.4 生成（链接）

按 **`Shift+F8`**（或点菜单「生成 → 生成」）。

这一步会生成 `hello.exe`，放在和源文件相同的目录里。

### 5.5 运行

按 **`F5`**（或点菜单「生成 → 执行」）。

会弹出一个 cmd 窗口，显示：

```
Hello, Fortran!
```

---

## 6. 常见问题 & 排坑指南

### 问题 1：F5 之后终端窗口一闪而过，看不到输出

**原因**：程序运行完就退出了，窗口来不及让你看到输出就关掉了。

**解决方法**：在代码末尾加一行等待用户输入：

```fortran
program hello
    implicit none
    write(*,*) "Hello, Fortran!"
    read(*,*)   ! 等待按 Enter 键，防止窗口关闭
end program hello
```

运行后按 Enter 关闭窗口。

---

### 问题 2：编译报错 `ld: cannot find -l...`（找不到库）

**原因**：`-static` 选项需要静态库文件，有时 Miniconda 环境里缺少。

**解决方法**：先去掉 `-static` 试试：

```ini
# 把配置里的 linker 改成：
linker=gfortran -Wall -Wno-tabs "%f" -o "%e.exe"
```

去掉 `-static` 后生成的 `.exe` 依赖几个 DLL，但通常在 Miniconda 环境里运行没问题。

---

### 问题 3：运行 .exe 时报错「找不到 DLL」（libgfortran-5.dll 等）

**原因**：Miniconda 的 gfortran 默认动态链接，生成的 exe 依赖运行时 DLL，这些 DLL 不在系统 PATH 里。

**解决方法一（推荐）**：加 `-static` 让编译器静态链接：

```
gfortran -static -Wall test.f90 -o test.exe
```

**解决方法二**：把 DLL 复制到和 exe 同一个目录。需要的 DLL 通常在 `D:\Miniconda3\Library\bin\` 里：
- `libgfortran-5.dll`
- `libgcc_s_seh-1.dll`
- `libwinpthread-1.dll`

---

### 问题 4：编译器输出窗口中文乱码

**原因**：Windows cmd 默认使用 GBK 编码，而 Geany 的编译器输出窗口使用 UTF-8，两者不匹配。

**解决方法**：**忽略它**。乱码只出现在 Geany 的编译器输出窗口里，不影响实际编译结果。

**不要尝试**通过修改 `chcp 65001` 或更改 Geany 的编码设置来解决——这会引发更多问题（cmd 控制台对 UTF-8 支持不完整）。

**根本解决方案**：代码里不要写中文（注释改用英文）。

---

### 问题 5：修改了配置文件但 Geany 没反应

**原因**：Geany 不会自动检测配置文件变化。

**解决方法**：重启 Geany，或者菜单「工具 → 重新加载配置」。

---

### 问题 6：gfortran 找不到（`gfortran: command not found`）

**原因**：gfortran 所在目录不在系统 PATH 里。

**解决方法**：在配置文件里写 gfortran 的**完整路径**：

```ini
FT_00_CM=D:\Miniconda3\Library\bin\gfortran.exe -Wall -Wno-tabs -g -c "%f"
FT_01_CM=D:\Miniconda3\Library\bin\gfortran.exe -static -Wall -Wno-tabs -g "%f" -o "%e.exe"
```

或者把 gfortran 目录加入 PATH：
1. 右键「此电脑」→「属性」→「高级系统设置」→「环境变量」
2. 在「系统变量」里找到 `Path`，点「编辑」
3. 新建一条：`D:\Miniconda3\Library\bin`（改成你自己的路径）
4. 重启 Geany

---

### 问题 7：配置文件改乱了，Geany 行为异常

**解决方法**：重装 Geany。

重装会清除 `%APPDATA%\geany\` 下的配置，让 Geany 回到默认状态。然后重新按本教程第 4 节配置一次即可。

---

## 7. 附录：完整配置文件参考

### filetypes.fortran（用于 .f90 文件）

路径：`%APPDATA%\geany\filedefs\filetypes.fortran`

```ini
[settings]
extension=f90
mime_type=text/x-fortran
comment_single=!
comment_use_indent=false
context_action_cmd=

[indentation]
width=4
type=1

[build_settings]
compiler=D:\Miniconda3\Library\bin\gfortran.exe -Wall -Wno-tabs -g -c "%f"
linker=D:\Miniconda3\Library\bin\gfortran.exe -static -Wall -Wno-tabs -g "%f" -o "%e.exe"
run_cmd="%e.exe"

[build-menu]
FT_00_LB=编译(_C)
FT_00_CM=D:\Miniconda3\Library\bin\gfortran.exe -Wall -Wno-tabs -g -c "%f"
FT_00_WD=
FT_01_LB=生成(_B)
FT_01_CM=D:\Miniconda3\Library\bin\gfortran.exe -static -Wall -Wno-tabs -g "%f" -o "%e.exe"
FT_01_WD=
EX_00_LB=执行(_E)
EX_00_CM="%e.exe"
EX_00_WD=
```

### 快捷键速查

| 操作 | 快捷键 |
|------|--------|
| 编译（生成 .o，检查语法） | `F8` |
| 生成（编译+链接，生成 .exe） | `Shift+F8` |
| 运行 | `F5` |
| 保存 | `Ctrl+S` |

---

## 贡献与反馈

如果你在按本教程操作时遇到了问题，欢迎提 Issue 或 PR。

特别感谢昨晚踩坑并总结经验的朋友——正是因为这些痛苦的经历，才有了这份教程。
author note:
没错昨晚踩坑的就是我，不过先说明两点：
第一miniconda是可选的：只需要安装Fortran配置就行。miniconda是我方便和ai交互安装的。可以直接从官网下载安装包安装，也可以使用conda安装。
第二：安装geany后请注意配置字体（特别是首选文件编码一定是gbk，因为cmd默认使用gbk中文，选用utf-8则输出中文乱码）。在geany->编辑->首选项->字体->首选文件编码->gbk。geany->编辑->首选项->界面->字体->修改“编辑器”“标记列表”“信息窗口”三个位置的字体（可以挨个测试是否支持显示中文：建议参数值：NSimSum,Microsoft JhengHei,Noto Sans SC）。
---

*最后更新：2026-06-04*
