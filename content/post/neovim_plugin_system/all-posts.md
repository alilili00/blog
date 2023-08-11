+++
author = ["alilili"]
lastmod = 2023-09-18T00:27:05+08:00
draft = false
+++

## C语言使用变长参数 {#C_language_va_list}

{{< highlight C >}}
void
die(const char *errstr, ...)
{
	va_list ap;

	va_start(ap, errstr);
	vfprintf(stderr, errstr, ap);
	va_end(ap);
	exit(1);
}
{{< /highlight >}}


## POSIX 定时器 {#posix_timer}

-   `time_t` 秒级定时
-   `timespec` 纳秒级定时
-   `timeval` 毫秒级定时


## 关于开发者测试与测试框架 {#design_testing}


### 什么是一个好的测试 {#什么是一个好的测试}

1.  ****应该是独立可重复的****. 每一个测试的环节应该是独立而且是可解耦的. 一个环节没跑通不应该影响后续环节. 一个环节的错误也不应该向后传, 一个环节不符合预期应该立刻报出来. 一个环节出了问题, 可以反复测试这个环节而不用再把其他环节再跑一遍.
2.  ****测试代码本身应该有良好的结构****. 不仅代码要有良好的结构, 测试代码也要有良好的结构. 相同的测试环节之间应该构成一个模块. 代码应该是平台无关的, 代码应该是可以复用的.
3.  ****打印尽可能多的信息****. 测试中不需要考虑打印信息太多, 造成阅读困难这一个问题. 而且测试的目的本来就是希望再测试的过程中发现尽可能多的问题.


### xUnit {#xunit}


### CUnit {#cunit}


#### 介绍 {#介绍}

1.  继Junit CppUnit的成功后， c语言环境下也出现了开放源码的白盒测试用例CUnit。CUnit以静态库的形式提供给用户使用，用户编写程序的时候直接链接此静态库就可以了。它提供了一个简单的单元测试框架，并且为常用的数据类型提供了丰富的断言语句支持。
2.  静态测试，方便操作。
3.  CUnit可以结合gcov lcov等使用
4.  可以看出Cunit也是有组织的，呵呵，主要分几个角色，Registry，Suite及Test方法。可以通过下面例子，体会到这种组织关系。

按官方文档说明，使用Cunit的主要步骤有：

1.  Write functions for tests (and suite init/cleanup if necessary).
2.  Initialize the test registry - `CU_initialize_registry()`
3.  Add suites to the test registry - `CU_add_suite()`
4.  Add tests to the suites - `CU_add_test()`
5.  Run tests using an appropriate interface, e.g. `CU_console_run_tests`
6.  Cleanup the test registry - `CU_cleanup_registry`


#### 获取 {#获取}

[CUnit网址](http://cunit.sourceforge.net/)


### google test {#google-test}


#### 获取 {#获取}

googlttest github 地址： [googletest github](https://github.com/google/googletest)


#### 简介 {#简介}

是一个跨平台的C/C++单元测试框架. 包括GoogleTest和GoogleMock两个部分.

关于单元测试具体可以参考:
[xUnit](https://en.wikipedia.org/wiki/XUnit)

特性:

-   丰富断言接口
-   自定义断言接口
-   死亡测试
-   致命和非致命测试
-   值-参数化测试
-   类型-参数化测试
-   运行测试的各种选项
-   导出XML测试报告


#### GoogleMock {#googlemock}

是一个GoogleMock用来生产模拟(Mock)类的框架.

主要包括一下功能:

-   使用一些简单的宏描述你想要模拟的接口，他们将扩展到你的mock类的实现;
-   创建一些模拟对象，并使用直观的语法指定其期望和行为;
-   练习使用模拟对象的代码。 Google Mock会在出现任何违反期望的情况时立即处理。


#### GTest Runner {#gtest-runner}

<!--list-separator-->

-  简介

    是一个基于Qt5的自动化运行测试和图形化用户界面. 支持Windows和Linux平台.


#### GoogleTest UI {#googletest-ui}

<!--list-separator-->

-  简介

    图形化运行二进制文件的框架.


#### GTest TAP Listener {#gtest-tap-listener}

<!--list-separator-->

-  简介

    监听GoogleTest事件, 来充填TAP来实现测试结果输出.

    [TAP protocol](https://en.wikipedia.org/wiki/Test_Anything_Protocol)介绍.


#### gtest parellel {#gtest-parellel}

<!--list-separator-->

-  简介

    并行运行程序实现, 二进制文件测试加速.


#### GoogleTest Adapter {#googletest-adapter}

<!--list-separator-->

-  简介

    是一个VS Code可视化GoogleTest的VS Code插件.


### catch2 {#catch2}


## C 语言符号可见性 {#c_visibility}

{{< highlight C >}}
  __attribute__((visibility("default")))
  __attribute__ ((visibility("hidden")))
{{< /highlight >}}

`main.cc:`

{{< highlight C >}}
extern "C" void fun1();
extern "C" void fun2();
int main()
{
  fun1();
  fun2();
  return 0;
}
{{< /highlight >}}

`Makefile:`

{{< highlight makefile >}}
all:test
test:main.o libvisibility.so
	g++ -o test main.o -lvisibility -L .
main.o::main.cc
	g++ -c main.cc
libvisibility.so:visibility1.o visibility2.o
	g++ -shared -o libvisibility.so visibility1.o visibility2.o
visibility1.o:visibility1.cc
	g++ -fvisibility=hidden -fPIC -c visibility1.cc
visibility2.o:visibility2.cc
	g++ -fvisibility=hidden -fPIC -c visibility2.cc
clean:
	rm -f *.o *.so test
{{< /highlight >}}

`visibility1.cc:`

{{< highlight C >}}
  #include <stdio.h>
  extern "C" void fun1()
  {
    printf("in %s\n",__FUNCTION__);
  }
  __attribute__ ((visibility("hidden"))) void fun1();
  //若编译此文件时使用了参数-fvisibility=hidden，则此行可以省略
{{< /highlight >}}

`visibility2.cc:`

{{< highlight C >}}
  #include <stdio.h>
  extern "C" void fun1();
  extern "C" void fun2()
  {
    fun1();
    printf("in %s\n",__FUNCTION__);
  }
  __attribute__ ((visibility("default"))) void fun2();
  //若编译此文件时没有使用参数-fvisibility或设置参数-fvisibility=default，则此行可以省略
{{< /highlight >}}


## KDE 的配色 {#kde_color_scheme}

除了 KDE/plasma 所控制的主题颜色之外. 还有 `qt` 也控制着应用的颜色. qt5 base 的应用需要使用 ****qt 设置**** 来改变默认的背景颜色.


## yay 换源 {#yay_change_software_download_server}

{{< highlight shell >}}
yay --save --aururl aur.tuna.tsinghua.edu.cn
{{< /highlight >}}


## Linux Kerel 物理内存管理快速参考 {#linux_kernel_phy_mem_quick_reference}


### 概览 {#概览}

UMA 计算机, 是指不存在本地和远程核的计算机. NUMA 是指存在本地和远程核的计算机, 一个核访问本地内存的速度和远程核内存的速度是不同的.

{{< figure src="/ox-hugo/uma.svg" >}}

{{< figure src="/ox-hugo/numa.svg" >}}

FLATMEM(平坦), DISCONTIGMEM(非连续), SPARSEMEM(离散).

在实际处理的时候内核把 UMA 系统当作 NUMA 的一个特例.

每一块 NUMA 中的内存被称作节点, 代码中用 `pg_data_t` 来管理.

每一个节点又被分为多个互斥的内存域, 如:

-   `ZONE_DAM`
-   `ZONE_NORMAL`
-   `ZONE_HIGHMEM`

`ZONE_MOVABLE`, 是一个伪内存区域, 用来防止物理内存碎片.

关于高端地址: 高端地址是一个物理地址空间的概念.


### 内存节点 {#内存节点}

每个物理内存页都用 `struct page` 来管理. 每个 ZONE 维护了一个物理内存页的页表.
每个节点都提供了一个备用的列表 `struct zonelist`, 越靠后越不适合分配.

{{< highlight C >}}
typedef struct pglist_data {
  struct zone node_zones[MAX_NR_ZONES]; /* 内存区域 */
  struct zonelist node_zonelists[MAX_ZONELISTS]; /* 备用内存区域 */
  int nr_zones; /* 内存区域数目 */
  struct page *node_mem_map; /* page实例数组的指针 */
  struct bootmem_data *bdata; /* 在mm子系统初始化之前的内存分配器 */
  unsigned long node_start_pfn; /* 节点的第一个页帧编号 */
  unsigned long node_present_pages; /* 物理内存页的总数 */
  unsigned long node_spanned_pages; /* 物理内存页的总长度,包含洞在内 */
  int node_id; /* 全局节点ID */
  struct pglist_data *pgdat_next; /* 下一个内存节点 */
  wait_queue_head_t kswapd_wait; /* 交换内存等待队列 */
  struct task_struct *kswapd;
  int kswapd_max_order;
} pg_data_t;
{{< /highlight >}}

内核维护了一个节点状态图.

{{< highlight C >}}
<nodemask.h>
enum node_states {
   N_POSSIBLE,
   /* 结点在某个时候可能变为联机 */
   N_ONLINE,
   /* 结点是联机的 */
   N_NORMAL_MEMORY,
   /* 结点有普通内存域 */
#ifdef CONFIG_HIGHMEM
   N_HIGH_MEMORY,
   /* 结点有普通或高端内存域 */
#else
   N_HIGH_MEMORY = N_NORMAL_MEMORY,
#endif
   N_CPU,
   /* 结点有一个或多个CPU */
   NR_NODE_STATES
 };
{{< /highlight >}}

状态 N_POSSIBLE、N_ONLINE 和 N_CPU 用于 CPU 和内存的热插拔.
如果结点有普通或高端内存则使用 `N_HIGH_MEMORY`, 仅当结点没有高端内存才设置 `N_NORMAL_MEMORY` 。


### 内存区域 {#内存区域}

内存域用 `<mmzone.h> struct zone` 结构体来描述, 供 lru 和页分配器来访问.

{{< highlight C >}}
<MMZONE.h>
struct zone {
  /*通常由页分配器访问的字段 */
  unsigned long
  pages_min, pages_low, pages_high;
  unsigned long
  lowmem_reserve[MAX_NR_ZONES];
  struct per_cpu_pageset pageset[NR_CPUS];
  /*
   * 不同长度的空闲区域
   */
  spinlock_t lock;
  struct free_area free_area[MAX_ORDER];
  ZONE_PADDING(_pad1_)
  /* 通常由页面收回扫描程序访问的字段 */
    spinlock_t
    lru_lock;
  struct list_head
  active_list;
  struct list_head
  inactive_list;
  unsigned long
  nr_scan_active;
  unsigned long
  nr_scan_inactive;
  unsigned long
  pages_scanned; /* 上一次回收以来扫描过的页 */
  unsigned long
  flags; /* 内存域标志,见下文 */
  /* 内存域统计量 */
  atomic_long_t
  vm_stat[NR_VM_ZONE_STAT_ITEMS];
  int prev_priority;
  ZONE_PADDING(_pad2_)
  /* 很少使用或大多数情况下只读的字段 */
    wait_queue_head_t * wait_table;
  unsigned long wait_table_hash_nr_entries;
  unsigned long wait_table_bits;
  /* 支持不连续内存模型的字段。 */
  struct pglist_data *zone_pgdat;
  unsigned long zone_start_pfn;
  unsigned long spanned_pages; /* 总长度,包含空洞 */
  unsigned long present_pages; /* 内存数量(除去空
                                  洞) */
  char *name;
} ____cacheline_maxaligned_in_smp;
{{< /highlight >}}

添加 `____cacheline_maxaligned_in_smp` 由编译器确保最佳的对齐方式, 使用　`ZONE_PADDING` 生成充填, 以确保每个自旋锁都处在该结构体的缓存行中.

-   如果空闲页多于 `pages_high`,则内存域的状态是理想的。
-   如果空闲页的数目低于 `pages_low`,则内核开始将页换出到硬盘。
-   如果空闲页的数目低于 `pages_min`,那么页回收工作的压力就比较大,
-   `lowmem_reserve` 数组分别为各种内存域指定了若干页,用于一些

无论如何都不能失败的关键 性内存分配。

-   pageset 是一个数组,用于实现每个 CPU 的热/冷页帧列表。内核使用这些列表来保存可用于 满足实现的“新鲜”页。
-   free_area 是同名数据结构的数组,用于实现伙伴系统。
-   `active_list` 是活动页的集合,而 `inactive_list` 则不活动页的集合(page 实例)
-   `nr_scan_active` 和 `nr_scan_inactive` 指定在回收内存时需要扫描的活动和不活动页的数目。
-   `pages_scanned` 指定了上次换出一页以来,有多少页未能成功扫描。
-   flags 描述内存域的当前状态。允许使用下列标志:
    -   `ZONE_ALL_UNRECLAIMABLE` 所有的页都已经“钉”住
    -   `ZONE_RECLAIM_LOCKED` 防止并发回收(一个内存区域已经开始往外存搬运了但是每搬完)
    -   `ZONE_OOM_LOCKED` 内存区域可以被回收
-   `prev_priority` 存储了上一次扫描操作扫描该内存域的优先级. 用来判断是否需要交换出映射的页.
-   `wait_table`, `wait_table_bits` 和 `wait_table_hash_nr_entries` 实现了一个等待队列, 可供等待某一页变为可用的进程使用。
-   `pglist_data` 区域所对应的节点.
-   `zone_start_pfn` 是内存域第一个页帧的索引.


#### 内存区域中的水线 {#内存区域中的水线}

用户层可通过文件 `/proc/sys/vm/min_free_kbytes` 来读取和修改该设置。


#### 冷热页 {#冷热页}

struct zone 的 pageset 成员用于实现冷热分配器(hot-n-cold allocator). 热的意味着页已经加载到 CPU 高速缓存, 每个 CPU 是独立管理的.

{{< highlight C >}}
<mmzone.h>
struct per_cpu_pageset {
struct per_cpu_pages pcp[2]; /* 索引0对应热页,索引1对应冷页 */
} ____cacheline_aligned_in_smp;
{{< /highlight >}}

{{< highlight C >}}
<mmzone.h>
struct per_cpu_pages {
  int count;　/* 列表中的页数 */
  int high; /* 页数上线水线 */
  int batch; /* 添加删除多页时, 块的大小 */
  struct list_head list; /* 页的列表 */
};
{{< /highlight >}}

如有可能,CPU 的高速缓存不是用单个页来填充的,而是用多个页组成的块。


#### 页帧 {#页帧}

页帧代表系统内存的最小单位,对内存中的每个页都会创建 struct page 的一个实例。

如果一页用于 slub 分配器, 那么可以确保只有内核会使用该页.

{{< highlight C >}}
<mm.h>
struct page {
  unsigned long flags; /* 原子标志， 有些时候会用于异步更新 */
  atomic_t _count; /* 使用计数 */
  union {
    atomic_t _mapcount; /* 表示页被映射的次数的计数 */
    unsigned int inuse; /* 对象的数目 */
  };
  union {
    struct {
      unsigned long private; /* 由映射私有, 不透明数据:
                              * 如果设置了PagePrivate 通常用于buffer_heads;
                              * 如果设置了PageSwapCache, 则用于swp_entry_t;
                               * 如果设置了PG_buddy, 则用于表示伙伴系统中的阶
                               */
      struct address_space *mapping; /* 如果最地位伪0则指向inode, address_space或为NULL. 如果页映射为匿名内存, 最低为置位, 而且该指针指向anon_vma对象 */
    };
    struct kmem_cache *slab; /* 用于SLUB分配器, 指向slab指针 */
    struct page *first_page; /* 用于复合页的尾页, 指向首页 */
  };
  union {
    pgoff_t index; /* 在映射内的偏移量 */
    void *freelist; /* SLUB: freelist */
  };
  struct list_head lru; /* 换出页列表, 例如由zone->lru_lock保护的active_list! */
  void *virtual; /* 内核虚拟地址(如果没有映射为NULL, 既高端内存) */
}
{{< /highlight >}}

-   flags 存储了体系结构无关的标志,用于描述页的属性.
-   count 是一个使用计数,表示内核中引用该页的次数。
-   mapcount 表示在页表中有多少项指向该页
-   lru 是一个表头,用于在各种链表上维护该页,
-   内核可以将多个毗连的页合并为较大的复合页(compound page) 。分组中的第一个页称作首页(head page),而所有其余各页叫做尾页(tail page)。
-   mapping 指定了页帧所在的地址空间。
-   index 是页帧在映射内部的偏移量。
-   private 是一个指向“私有”数据的指针,虚拟内存管理会忽略该数据。
-   virtual 用于高端内存区域中的页,换言之,即无法直接映射到内核内存中的页。 virtual 用于存储该页的虚拟地址。
-   `PG_locked` 指定了页是否锁定
-   如果在涉及该页的 I/O 操作期间发生错误,则 `PG_error` 置位。
-   `PG_referenced` 和 `PG_active` 控制了系统使用该页的活跃程度。
-   `PG_uptodate` 表示页的数据已经从块设备读取,其间没有出错。
-   如果与硬盘上的数据相比,页的内容已经改变,则置位 `PG_dirty`
-   PG_lru 有助于实现页面回收和切换
-   PG_highmem 表示页在高端内存中,无法持久映射到内核内存中
-   如果 page 结构的 private 成员非空,则必须设置 PG_private 位。例如, 用于 I/O 的页. 可使用该字段将页细分为多个缓冲区.
-   如果页的内容处于向块设备回写的过程中,则需要设置 PG_writeback 位。
-   slab 分配器的一部分,则设置 PG_slab 位。

地址空间是一个非常一般的概念,例如,可以用在向内存读取文件时。地址空间用于将文件的内容(数据)与装载数据的内存区关联起来。

用于判断页是否属于未关联到地址空间的某个匿名内存区. 如果将 mapping 置为 1, 则该指针并不指向 `address_space` 的实例, 而是指向另一个数据结构(`anon_vma`), 该结构用来实现匿名页的逆向映射.


## emacs org-mode 设置转义字符 {#emacs_orgmode_escape charactere}

{{< highlight lisp >}}
    #+OPTIONS: ^:{}
    (setq org-export-with-sub-superscripts nil)
    :sub-superscript nil
    :sub-superscript {}
    (setq org-export-with-sub-superscripts '{})
{{< /highlight >}}


## neovim 插件体系 {#neovim_plugin_system}


## vim(neovim) 插件为外部提供的接口 {#vim--neovim--插件为外部提供的接口}

vim 插件和 vim 常见的交互接口(机制)如下. vim 插件通过这些接口, 接受 vim 的输入, 影响 vim 的行为, 并且最终实现特定的功能.

-   快捷键:
-   外部命令:
-   全局变量:
-   全局函数:
-   color scheme:
-   autocmd:
-   autoload
-   file type


## 以 LeaderF 插件为例 {#以-leaderf-插件为例}

以 LeaderF 插件为例来讲解上面的接口, LeaderF 是一个 neovim 下的模糊搜索插件.
以其搜索文件的功能为例.


### vim 脚本全局变量 {#vim-脚本全局变量}

他暴露给 `LeaderF` 暴露给我们了一个名为 `Lf_ShortcutF` 的全局变量.

`Lf_ShortcutF` 变量代表模糊匹配文件查找功能的快捷键, 默认为 `<Leader>f`.
在配置文件的最后添加如下配置项 `let g:Lf_ShortcutF = "<C-P>"` 实现修改 LeaderF 文件查找功能的快捷键.
到这里, 我们遇到了插件和 vim 的第一个接口 ****全局变量****, 这里使用全局变量实现让用户自己修改快捷键的功能.
插件通过一些全局变量, 把一些简单的拓展点暴露给用户, 让用户自己修改.

在插件中的实现方式也很简单.


### vim 插件定义快捷键 {#vim-插件定义快捷键}

插件里使用 nnoremap 命令把快捷键 `Lf_ShortcutF` 映射到 `LeaderfFile` 命令上.
也就说, 我们在 vim 里键入 'C-p' 上后会触发 vim 命令.

{{< highlight vimscript >}}
exec 'nnoremap <silent><unique> ' g:Lf_ShortcutF ':<C-U>LeaderfFile<CR>'
{{< /highlight >}}

修改配置文件之后, 会触发文件查找功能. 如下图:

{{< figure src="/ox-hugo/20230903-222230_screenshot.png" >}}


### vim 插件定义命令 {#vim-插件定义命令}

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


### vim 插件定义函数 {#vim-插件定义函数}

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


### vim color scheme {#vim-color-scheme}

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

{{< highlight python >}}
        hi_cmd = "hi def Lf_hl_devIcons_{name} gui={gui} guifg={guifg} guibg={guibg} cterm={cterm} ctermfg={ctermfg} ctermbg={ctermbg}".format(
            name=name,
            gui=plt.get("gui", "NONE"),
            guifg=plt.get("guifg", "NONE"),
            guibg=plt.get("guibg", "NONE"),
            cterm=plt.get("cterm", "NONE"),
            ctermfg=plt.get("ctermfg", "NONE"),
            ctermbg=plt.get("ctermbg", "NONE"),
        )
{{< /highlight >}}


## vim(neovim) 插件文件结构 {#vim--neovim--插件文件结构}

-   autoload:
-   plugin:
-   ftplugin:
-   doc:
