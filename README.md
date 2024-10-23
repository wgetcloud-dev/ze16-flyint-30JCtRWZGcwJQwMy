
SDDC Manager 中有两种类型的软件包，分别是“升级/修补包（PATCH）”和“安装包（INSTALL）”。“升级/修补包”用于执行 VCF 环境中组件的升级/修补，这个已经在前面的文章中使用过了；而另外一种“安装包”，这种包用于在 VCF 环境中部署其他集成解决方案，比如 VMware Aria Suite Lifecycle Manager 以及 VMware Avi Load Balancer 等，当然还有一些组件安装包可以用来代替源物料清单（BOM）版本中的软件包以进行异步部署 VI 工作负载域。


导航到 SDDC Manager UI\-\>生命周期管理\-\>包管理，当这些软件包下载之后，可以在“下载历史记录”中找到它。但是，你可能会发现，这些已下载的包没有选项可以对其执行删除操作。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022193744171-1664985051.png)](https://github.com)


默认情况下，SDDC Manager 所下载的软件包会存放到虚拟机的 /nfs/vmware/vcf/nfs\-mount 目录，如下图所示。这个目录的空间是有限的，随着下载的软件包越来越多，可用空间会越来越少直至被完全占满，因此，我们需要对这些软件包进行管理。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022164425435-1007447969.png)](https://github.com)


VMware 专门提供了一个 PowerShell 脚本用来管理 SDDC Manager 中的软件包，比如查看包信息、删除、下载软件包等。你可以在知识库文章 [KB 313523](https://github.com) 底部找到这个脚本，查看并了解相关说明后，下面一起来看看如何使用它。


 



## 一、环境要求



使用这个 PowerShell 脚本需要安装一些运行环境，因为脚本所执行的相关操作需要调用这些环境中的命令或 API 才能完成，具体如下。可以参考这篇（[使用 PowerVCF 连接和管理 VMware Cloud Foundation 环境。](https://github.com)）文章中方法准备这些环境。


* 运行 PowerShell Core 7\.3\.0 或更高版本。
* 运行 PowerCLI 13\.1 或更高版本。
* 运行 PowerVCF 2\.3\.0 或更高版本。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022155547768-969973752.png)](https://github.com)


 



## 二、连接 SDDC Manager



下载脚本到本地后，打开 PowerShell Core 并进入到脚本所在的目录，运行以下命令连接到 SDDC Manager。也可以不带参数直接运行脚本，系统会提示并要求你输入所需的参数信息。



```
.\VcfBundleManagement.ps1 -server  -user  -pass  -rootPass 
```

参数说明：


* 是 SDDC Manager 的管理地址。
* 是 SDDC Manager 的 SSO 管理员用户名。
* 是 SDDC Manager 的 SSO 管理员密码。
* 是 SDDC Manager 的 Root 用户密码。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022163337575-925318580.png)](https://github.com)


 



## 三、使用 PowerShell 脚本



连接成功后，将出现如下图所示的选项菜单。这个脚本所提供的菜单选项可以实现不同的功能，输入不同的“数字”用于执行不同的任务，输入“Q”退出 PowerShell 脚本。下面来看看，这些不同的选项分别可以执行哪些操作。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022163448018-454857514.png)](https://github.com)


输入数字“4”并回车，用于查看 SDDC Manager 中软件包存放目录的磁盘空间使用情况。注，可以按“回车”回到选项菜单。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022175717486-1228739553.png)](https://github.com)


输入数字“1”并回车，用于刷新 SDDC Manager 中软件包的清单信息。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022175833743-117300730.png)](https://github.com)


输入数字“2”并回车，用于查看 SDDC Manager 中所有的软件包清单。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022175934519-1755280915.png)](https://github.com)


输入数字“3”并回车，用于查看 SDDC Manager 中所有已下载的软件包。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022180046505-415327110.png)](https://github.com)


输入数字“5”并回车，用于删除 SDDC Manager 中指定的软件包。若删除多个，需要用逗号进行分开。注，输入“B”可以回到选项菜单。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022182841429-550470756.png)](https://github.com):[veee加速器](https://liuyunzhuge.com)


输入数字“3”并回车，再次查看 SDDC Manager 中所有已下载的软件包。注，如果软件包显示还在，可以输入数字“1”重新进行刷新。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022183024716-1812537036.png)](https://github.com)


输入数字“6”并回车，用于删除 SDDC Manager 中指定版本的所有软件包。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022183337464-1687115767.png)](https://github.com)


输入数字“3”并回车，再次查看 SDDC Manager 中所有已下载的软件包。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022183843387-419435150.png)](https://github.com)


输入数字“8”并回车，用于下载 SDDC Manager 中指定的软件包。若下载多个，需要用逗号进行分开。注，访问 [KB 96099](https://github.com) 了解有关软件包的更多信息。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022184619799-814785601.png)](https://github.com)


输入数字“3”并回车，再次查看 SDDC Manager 中所有已下载的软件包。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022184826508-1813567775.png)](https://github.com)


输入数字“7”并回车，用于删除 SDDC Manager 中所有已过时的软件包。当前工作负载域版本是 5\.2\.0\.0，所以 5\.1\.1\.0 版本相对来说是过时软件包。 


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022185018045-675775717.png)](https://github.com)


如果没有低于 5\.2\.0\.0 的软件包，则会出现如下图所示的警告。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022184214563-1377290793.png)](https://github.com)


输入数字“9”并回车，用于下载 SDDC Manager 中指定版本的所有软件包。注，如果软件包已经下载，则清单不会列出。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022190038037-1853667658.png)](https://github.com)


输入数字“3”并回车，再次查看 SDDC Manager 中所有已下载的软件包。注，任务已取消，如果下载成功则应显示“SUCCESSFUL”状态。


[![](https://img2024.cnblogs.com/blog/2313726/202410/2313726-20241022190823305-810316072.png)](https://github.com)


