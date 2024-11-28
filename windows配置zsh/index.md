# Windows配置zsh


<!--more-->

## 安装 git bash

为了在 Windows 上使用 zsh，需要一个类 Unix 环境。推荐使用 Git Bash，您可以从[官网](https://gitforwindows.org/)下载并安装

## 下载 zsh

[下载 zsh](https://packages.msys2.org/packages/zsh?repo=msys)

{{< image src="./image.webp" caption="下载 zsh" height="1562" width="2450">}}

将下载的内容解压到 git 的根目录中

设置 git 使用 zsh，在`.bashrc`中配置

```shell
if [ -t 1 ]; then
  exec zsh
fi
```

再次启动 git bash，光标和提示在同一行说明成功进入了 zsh

在你的`$USERPROFILE`下新建`.zshrc`作为 zsh 的启动脚本

```shell

```

### conda 设置（可选的）

如果未经配置在命令行中使用 conda 可能会遇到 LF 的问题

在用户目录下新建一个 conda 初始化脚本`conda-shell-zsh-hook.zsh`，粘贴以下内容，将 `CONDA_EX` 和 `CONDA_PYTHON_EXE` 中的路径修改为你的 Conda 路径

```shell
export PYTHONIOENCODING=UTF-8
export CONDA_EXE="$(cygpath 'D:\Workspace\Environment\python\anaconda3\Scripts\conda.exe')"
export _CE_M=''
export _CE_CONDA=''
export CONDA_PYTHON_EXE="$(cygpath 'D:\Workspace\Environment\python\anaconda3\python.exe')"

# Copyright (C) 2012 Anaconda, Inc
# SPDX-License-Identifier: BSD-3-Clause
__conda_exe() (
    "$CONDA_EXE" $_CE_M $_CE_CONDA "$@" | tr -d '\r'
)

__conda_hashr() {
    if [ -n "${ZSH_VERSION:+x}" ]; then
        \rehash
    elif [ -n "${POSH_VERSION:+x}" ]; then
        : # pass
    else
        \hash -r
    fi
}

__conda_activate() {
    if [ -n "${CONDA_PS1_BACKUP:+x}" ]; then
        # Handle transition from shell activated with conda <= 4.3 to a subsequent activation
        # after conda updated to >= 4.4. See issue #6173.
        PS1="$CONDA_PS1_BACKUP"
        \unset CONDA_PS1_BACKUP
    fi
    \local ask_conda
    ask_conda="$(PS1="${PS1:-}" __conda_exe shell.posix "$@")" || \return
    \eval "$ask_conda"
    __conda_hashr
}

__conda_reactivate() {
    \local ask_conda
    ask_conda="$(PS1="${PS1:-}" __conda_exe shell.posix reactivate)" || \return
    \eval "$ask_conda"
    __conda_hashr
}

conda() {
    \local cmd="${1-__missing__}"
    case "$cmd" in
    activate | deactivate)
        __conda_activate "$@"
        ;;
    install | update | upgrade | remove | uninstall)
        __conda_exe "$@" || \return
        __conda_reactivate
        ;;
    *)
        __conda_exe "$@"
        ;;
    esac
}

if [ -z "${CONDA_SHLVL+x}" ]; then
    \export CONDA_SHLVL=0
    # In dev-mode CONDA_EXE is python.exe and on Windows
    # it is in a different relative location to condabin.
    if [ -n "${_CE_CONDA:+x}" ] && [ -n "${WINDIR+x}" ]; then
        PATH="$(\dirname "$CONDA_EXE")/condabin${PATH:+":${PATH}"}"
    else
        PATH="$(\dirname "$(\dirname "$CONDA_EXE")")/condabin${PATH:+":${PATH}"}"
    fi
    \export PATH

    # We're not allowing PS1 to be unbound. It must at least be set.
    # However, we're not exporting it, which can cause problems when starting a second shell
    # via a first shell (i.e. starting zsh from bash).
    if [ -z "${PS1+x}" ]; then
        PS1=
    fi
fi

# conda activate base

```

在`.zshrc`中激活此脚本

```shell
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
if [ -f '/d/Workspace/Environment/python/anaconda3/Scripts/conda.exe' ]; then
    export PYTHONIOENCODING=UTF-8
    source ~/conda-shell-zsh-hook.zsh
fi

if [ -f "/d/Workspace/Environment/python/anaconda3/etc/profile.d/mamba.sh" ]; then
    . "/d/Workspace/Environment/python/anaconda3/etc/profile.d/mamba.sh"
fi
# <<< conda initialize <<<
```

## 下载 oh-my-zsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 配置插件&主题

### 添加 zsh-autosuggestions 插件

`zsh-autosuggestions`可根据输入历史对命令进行补全提示，我们从将代码 clone 到`.oh-my-zsh/custom/plugins`目录里

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

在.zshrc 中添加新的插件配置

```shell
plugins=(git zsh-autosuggestions)
```

`zsh-syntax-highlighting`可对输入的命令做语法校验

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

在.zshrc 中添加新的插件配置

```shell
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

### 配置 starship

您可在 `.zshrc` 中设置 `ZSH_THEME` 可更改主题，例如`ZSH_THEME="ys"`

Starship 是一个独立的、跨 shell 的终端用户界面，它不仅改变提示符的外观，还可以显示更多的信息模块，如 Git 分支、环境变量、包裹管理器状态等。

此处使用了 [starship](https://starship.rs/) 美化 zsh，可从[release](https://github.com/starship/starship/releases)安装或者直接从 rust 构建。

```shell
# 选择一种安装方式
cargo install starship --locked
choco install starship
conda install -c conda-forge starship
scoop install starship
winget install --id Starship.Starship
```

安装完成后只需在`.zshrc`尾部设置启动命令即可

```shell
eval "$(starship init zsh)"
```

## 在 vscode 中设置默认终端

`Ctrl+Shift+P`输入 `setting`，打开用户设置 JSON 文件

添加 `Git Bash` 选项并修改其为默认终端

```json
{
  // other configurations
  "terminal.integrated.profiles.windows": {
    // other configurations
    "Git Bash": {
      "source": "Git Bash"
    }
  },
  "terminal.integrated.defaultProfile.windows": "Git Bash"
}
```

## 其他可选配置

### 代理设置

```shell
set_proxy() {
    if [ "$1" = "on" ]; then
        export http_proxy="http://127.0.0.1:20171"
        export https_proxy="https://127.0.0.1:20171"
    elif [ "$1" = "off" ]; then
        unset http_proxy
        unset https_proxy
    fi
}

edit_zshrc() {
    vim ~/.zshrc
}
```

## 完整配置

.zshrc

```shell
export ZSH="/c/Users/30349/.oh-my-zsh"
# export PATH=$HOME/bin:/usr/local/bin:$PATH

ZSH_THEME="ys"
ZSH_DISABLE_COMPFIX=true

plugins=(git zsh-autosuggestions)

source $ZSH/oh-my-zsh.sh

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
if [ -f '/d/Workspace/Environment/python/anaconda3/Scripts/conda.exe' ]; then
    export PYTHONIOENCODING=UTF-8
    source ~/conda-shell-zsh-hook.zsh
fi

if [ -f "/d/Workspace/Environment/python/anaconda3/etc/profile.d/mamba.sh" ]; then
    . "/d/Workspace/Environment/python/anaconda3/etc/profile.d/mamba.sh"
fi
# <<< conda initialize <<<

eval "$(starship init zsh)"
```

## 参考文章

- [Windows 安装 Zsh 终端 - fetips 的文章 - 知乎](https://zhuanlan.zhihu.com/p/625583037)

- [在 Zsh 环境下配置 Conda/Mamba 踩坑记录](https://hairlessvillager.github.io/2024/09/10/a-problem-in-windows-conda-zsh/)

- [Starship 快速上手](https://starship.rs/zh-CN/guide/)

