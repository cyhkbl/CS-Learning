# DotFile

如果有多台开发设备，我们需要在不同的电脑上保持同样的配置，同步dotfile就变得非常重要了。下面介绍一些管理和同步Dotfile的工具。

## Chezmoi

### 安装

```nushell
scoop install main/chezmoi
```
### 配置

如果是初次使用的话，先使用`chezmoi init`在~/.local/share/chezmoi创建一个git仓库，这个是local的Repo。

我们还需要一个remote的repo，我们可以在Github上创建一个私有的Repo来存储自己的dotfiles。一般命名为`dotfiles`。这样在其他的设备上初始化的时候直接`chezmoi init --apply username`即可，chezmoi会自动到Github名字为username的repo下寻找`dotfiles`的Repo来进行初始化，比较方便。如果使用其他名字，在其他设备上初始化的时候，需要指定Github Repo的全地址：`chezmoi init https://github.com/username/dotfiles.git`。

### 使用

- `chezmoi add <file>`：将文件添加到chezmoi的管理中。例如`chezmoi add 'C:\Users\Admin\.wezterm.lua'`。

- `chezmoi edit <file>`编辑dotfile。当然，也可以直接到`~/.local/share/chezmoi`直接编辑文件，效果是一样的。

- `chezmoi diff` ：查看修改的部分。

- `chezmoi apply`：将修改应用到实际的文件中。

- `chezmoi update`：从远程仓库拉取最新的dotfiles并应用到本地。

## 不同系统差异化配置

以Nushell的配置文件为例，Windows系统的配置目录在`"C:\Users\<username>\AppData\Roaming\nushell\"`，而Linux系统的配置目录在`~/.config/nushell/`。

我们可以维护一份主要配置文件，在Chezmoi中，让Windows配置目录里的文件和Linux配置目录里的文件都去“寻找”这份主要配置文件里的内容，配置都写在主要配置文件里。

可以在Chezmoi源文件夹里创建`\AppData\Roaming\nushell\config.nu`，写入`source ($nu.home-dir | path join ".config" "nushell" "commonconfig.nu")`

创建`\dot_config\nushell\config.nu`，写入`source ~/.config/nushell/commonconfig.nu`

最后在`~/.config/nushell/commonconfig.nu`里写入主要的配置内容即可。