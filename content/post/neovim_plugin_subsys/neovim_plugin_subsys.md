+++
title = "neovim 插件体系"
author = ["alilili"]
lastmod = 2023-09-12T00:51:33+08:00
draft = false
+++

:ID:       3a30186f-e3df-489d-838f-a5579852c39f
:ROAM_ALIASES: "neovim 插件体系"


## vim(neovim) plugin system {#vim--neovim--plugin-system}


### vim(neovim) 插件为外部提供的接口 {#vim--neovim--插件为外部提供的接口}

vim 插件和 vim 常见的交互接口(机制)如下. vim 插件通过这些接口, 接受 vim 的输入, 影响 vim 的行为, 并且最终实现特定的功能.

-   快捷键:
-   外部命令:
-   全局变量:
-   全局函数:
-   color scheme:
-   autocmd:
-   autoload
-   file type


### 以 LeaderF 插件为例 {#以-leaderf-插件为例}

以 LeaderF 插件为例来讲解上面的接口, LeaderF 是一个 neovim 下的模糊搜索插件.
以其搜索文件的功能为例.


#### vim 脚本全局变量 {#vim-脚本全局变量}

他暴露给 `LeaderF` 暴露给我们了一个名为 `Lf_ShortcutF` 的全局变量.

`Lf_ShortcutF` 变量代表模糊匹配文件查找功能的快捷键, 默认为 `<Leader>f`.
在配置文件的最后添加如下配置项 `let g:Lf_ShortcutF = "<C-P>"` 实现修改 LeaderF 文件查找功能的快捷键.
到这里, 我们遇到了插件和 vim 的第一个接口 ****全局变量****, 这里使用全局变量实现让用户自己修改快捷键的功能.
插件通过一些全局变量, 把一些简单的拓展点暴露给用户, 让用户自己修改.

在插件中的实现方式也很简单.


#### vim 插件定义快捷键 {#vim-插件定义快捷键}

插件里使用 nnoremap 命令把快捷键 `Lf_ShortcutF` 映射到 `LeaderfFile` 命令上.
也就说, 我们在 vim 里键入 'C-p' 上后会触发 vim 命令.

{{< highlight vimscript >}}
exec 'nnoremap <silent><unique> ' g:Lf_ShortcutF ':<C-U>LeaderfFile<CR>'
{{< /highlight >}}

修改配置文件之后, 会触发文件查找功能. 如下图:

{{< figure src="roam/vim(neovim)_plugin_system/20230903-222230_screenshot.png" >}}


#### vim 插件定义命令 {#vim-插件定义命令}

上面我们说快捷键实际上代表了 vim 的 ****命令**** `LeaderfFile`.
插件会把自己的功能定义成命令或者是函数让使用者调用.
相比快捷键, 命令或者函数会更灵活一点.

插件中使用如下脚本定义命令:

{{< highlight vimscript >}}
command! -bar -nargs=* -complete=dir LeaderfFile Leaderf file <args>
{{< /highlight >}}

其中:

-   `-bar`: 代表接受 '|' 和其他的命令.
-   `-nargs=*`: 代表接受任意参数.
-   `-complete=dir`: 表示自动补全. 如果使用 `Leaderf file` 则不会有自动补全. 如果使用 `:LeaderfFile` 命令时 + tab 键会自动补全为当前目录下 dir.

`Leaderf file` 实际上是一个命令 + 一个子命令的形式, 这里的 `Leaderf` 是命令 `file` 是子命令.
`Leaderf` 也是 `command` 定义的.

{{< highlight vimscript >}}
command! -nargs=* -bang -complete=customlist,leaderf#Any#parseArguments Leaderf call leaderf#Any#start(<bang>0, <q-args>)
{{< /highlight >}}

`-complete=customlist,leaderf#Any#parseArguments`: 表示用 `leaderf#Any#parseArguments` 函数做自动补全.


#### vim 插件定义函数 {#vim-插件定义函数}

我们可以搜到 `leaderf#Any#start` 这个函数. vim 插件函数签名前面的前缀可以理解为函数作用域.

{{< highlight vimscript >}}
function! leaderf#Any#start(bang, args) abort
    if a:args == ""

    else
        call leaderf#LfPy("anyHub.start(r''' ".a:args." ''', bang=".a:bang.")")
    endif
endfunction
{{< /highlight >}}

这个函数里调用到了 python 的 `anyHub.start` 函数. 这里不是要教大家写插件, 而是要简单讲一下各个接口的作用. 尽管耐心读下去.

在 start 函数里会把参数派发给 `fileExplManager`.

{{< highlight python >}}
            if category == "file":
                from .fileExpl import fileExplManager
                manager = fileExplManager
{{< /highlight >}}

之后会调用 `fileExplManager.startExplorer`, 之后会调用 `manager.py` 中的 `startExplorer`.

并调用如下函数创建窗口

{{< highlight python >}}
        self._getInstance().enterBuffer(win_pos, not isinstance(content, list))
{{< /highlight >}}

创建窗口前调用钩子函数.

{{< highlight python >}}
        self._before_enter()
{{< /highlight >}}


#### vim color scheme {#vim-color-scheme}

完成窗口创建之后调用如下命令调整 color scheme

{{< highlight python >}}
            if lfEval("get(g:, 'Lf_highlightDevIconsLoad', 0)") == '0' or lfEval("hlexists('Lf_hl_devIcons_c')") == '0':
                highlightDevIcons()
            lfCmd("augroup Lf_DevIcons_Colorscheme")
            lfCmd("autocmd! ColorScheme * call leaderf#highlightDevIcons()")
            lfCmd("augroup END")
{{< /highlight >}}

倒数三行是一种自动覆盖所有 Color Scheme 的方法. 我们暂时忽略.
只看 `highlightDevIcons` 是如何调整 color scheme 的.


### vim(neovim) 插件文件结构 {#vim--neovim--插件文件结构}

-   autoload:
-   plugin:
-   ftplugin:
-   doc:
