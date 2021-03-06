

https://github.com/fenbox/Vagrantfile 实践笔记

## env

- os: windows 10
- virtual: 6.0.4
- vagrant: 2.2.3
- 命令行： git-bash或者cmder或者cmd

## step

本配置在 `Vagrant 2.2.*`，虚拟机为 `VirtualBox 6.0.*` 下测试通过。安装步骤：

1. 安装 VirtualBox 虚拟机及 Vagrant 程序
2. 添加 box 镜像 vagrant box add bento/ubuntu-18.04，选择 virtualbox
3. 执行建立环境 vagrant up

### Vagrant box add
```
vagrant box add bento/ubuntu-18.04
```
发现下载很慢（过了1个多小时后，还中断了）

参考 [Vagrant box add 命令加载过慢最简单的处理方式](https://www.jianshu.com/p/f569d687d1e2) 方法2，通过迅雷下载

然后，还是失败了。（迅雷下载，也失败。真的是厉害了。）

去找替代资源。通过 Vagrantfile 文件，知道需要 ubuntu-18.04 的资源。

官方镜像: https://app.vagrantup.com/boxes/search  搜索 "ubuntu 18.04" 

https://app.vagrantup.com/ubuntu/boxes/bionic64/

打开最新的一个 ，当下是 `versions/20190219.0.0/`，点击打开，


滑动到最底下，看一下，提供的是什么类型资源。看到是 `virtualbox Externally hosted (cloud-images.ubuntu.com)`

复制此时的 URL： https://app.vagrantup.com/ubuntu/boxes/bionic64/versions/20190219.0.0

那就是接上 `providers/virtualbox.box`, 得到：

https://app.vagrantup.com/ubuntu/boxes/bionic64/versions/20190219.0.0/providers/virtualbox.box

把这个URL，放到 迅雷，去下载，成功得到了 box 

打开 命令行, 添加box并命名为 `fenbox-lnmp-bionic`。

```
E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
λ vagrant box add fenbox-lnmp-bionic E:\vag\ubuntu-18.04\bionic-server-cloudimg-amd64-vagrant.box
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'fenbox-lnmp-bionic' (v0) for provider:
    box: Unpacking necessary files from: file:///E:/vag/ubuntu-18.04/bionic-server-cloudimg-amd64-vagrant.box
    box: Progress: 100% (Rate: 931M/s, Estimated time remaining: --:--:--)
==> box: Successfully added box 'fenbox-lnmp-bionic' (v0) for 'virtualbox'!

E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
```

检验box

```
E:\tmp
λ vagrant box list
fenbox-lnmp-bionic (virtualbox, 0)

E:\tmp
```

因为此时，我们的box，不是源码中 Vagrantfile 指定的 box ，所以在 `vagrant up` 前要先修改一下。

```
DELL@DESKTOP-MQ9CENU MINGW64 /e/vag/vag-env/fenbox-lnmp/Vagrantfile (master)
$ git diff Vagrantfile
-  config.vm.box = "bento/ubuntu-18.04"
+  config.vm.box = "fenbox-lnmp-bionic"
DELL@DESKTOP-MQ9CENU MINGW64 /e/vag/vag-env/fenbox-lnmp/Vagrantfile (master)
$
```

### `vagrant up`启动

在启动前，要先 cd 到 Vagrantfile 所在文件夹 `cd /d E:\vag\vag-env\fenbox-lnmp\Vagrantfile` 的哈。

```
E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
λ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
There are errors in the configuration of this machine. Please fix
the following errors and try again:

vm:
* The host path of the shared folder is missing: ../typecho/
* The host path of the shared folder is missing: ../wordpress/


E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
```

提示少了 `../typecho/` 和 `../wordpress/`。这些应该是作者自己的内容。不管了，我们先把这两个文件夹建立起来再说，同时放2个html文件进去哈，这样，服务起来后，就可以验证了。

```
E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
λ mkdir ..\typecho

E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
λ mkdir ..\wordpress

E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
λ echo "hello typecho" >> ..\typecho\index.html

E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
λ echo "hello wordpress" >> ..\wordpress\index.html

E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
```

`vagrant up`启动吧

#### 这里是我的报错，如果您没有报错，则跳过此步
```
E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)                                                             
λ vagrant up                                                                                                          
Bringing machine 'default' up with 'virtualbox' provider...                                                           
==> default: Importing base box 'fenbox-lnmp-bionic'...                                                               
==> default: Matching MAC address for NAT networking...                                                               
==> default: Setting the name of the VM: vagrant_ubuntu18_vb                                                          
Vagrant is currently configured to create VirtualBox synced folders with                                              
the `SharedFoldersEnableSymlinksCreate` option enabled. If the Vagrant                                                
guest is not trusted, you may want to disable this option. For more                                                   
information on this option, please refer to the VirtualBox manual:                                                    
                                                                                                                      
  https://www.virtualbox.org/manual/ch04.html#sharedfolders                                                           
                                                                                                                      
This option can be disabled globally with an environment variable:                                                    
                                                                                                                      
  VAGRANT_DISABLE_VBOXSYMLINKCREATE=1                                                                                 
                                                                                                                      
or on a per folder basis within the Vagrantfile:                                                                      
                                                                                                                      
  config.vm.synced_folder '/host/path', '/guest/path', SharedFoldersEnableSymlinksCreate: false                       
==> default: Vagrant has detected a configuration issue which exposes a                                               
==> default: vulnerability with the installed version of VirtualBox. The                                              
==> default: current guest is configured to use an E1000 NIC type for a                                               
==> default: network adapter which is vulnerable in this version of VirtualBox.                                       
==> default: Ensure the guest is trusted to use this configuration or update                                          
==> default: the NIC type using one of the methods below:                                                             
==> default:                                                                                                          
==> default:   https://www.vagrantup.com/docs/virtualbox/configuration.html#default-nic-type                          
==> default:   https://www.vagrantup.com/docs/virtualbox/networking.html#virtualbox-nic-type                          
==> default: Clearing any previously set network interfaces...                                                        
==> default: Preparing network interfaces based on configuration...                                                   
    default: Adapter 1: nat                                                                                           
    default: Adapter 2: hostonly                                                                                      
==> default: Forwarding ports...                                                                                      
    default: 22 (guest) => 2222 (host) (adapter 1)                                                                    
==> default: Running 'pre-boot' VM customizations...                                                                  
==> default: Booting VM...                                                                                            
There was an error while executing `VBoxManage`, a CLI used by Vagrant                                                
for controlling VirtualBox. The command and stderr is shown below.                                                    
                                                                                                                      
Command: ["startvm", "cac2cb19-692c-42ab-a306-621b7a06e9e4", "--type", "headless"]                                    
                                                                                                                      
Stderr: VBoxManage.exe: error: VT-x is not available (VERR_VMX_NO_VMX)                                                
VBoxManage.exe: error: Details: code E_FAIL (0x80004005), component ConsoleWrap, interface IConsole                   
                                                                                                                      
                                                                                                                      
E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)                                                             
```

又报了一个错误。

- https://stackoverflow.com/questions/37955942/vagrant-up-vboxmanage-exe-error-vt-x-is-not-available-verr-vmx-no-vmx-code
- https://github.com/scotch-io/scotch-box/issues/195

知道，这个应该是 Hyper-V 与 virtualbox 不能同时使用，这也意味着 Vagrant with VirtualBox cannot work with Docker at the same time。回想一下，之前，我确实是由于 安装并启动 Docker Desktop 开启了 Hyper-V。

这时候，真的是一个艰难选择了。

* 方式1：坚持使用 Docker Desktop 就只能下载 Hyper-V 相关的 box.
	
	这也意味着，很多 virtualbox 的新的内容不再有了。而 官方镜像: https://app.vagrantup.com/boxes/search  中很明显，virtualbox 的东西，要新，要多。
	
* 方式2：放弃 Hyper-V, Docker Desktop
	
	这也意味着，不能在 windows 中开发docker相关资源。那如果要使用docker, 就必须通过 virtualbox 开启虚拟机，在虚拟机中使用 docker 了。
	
也就是在同一时点，2选1吧。当然，不觉得开机重启麻烦，想用另一资源时，就开机重启一下吧。

我现在是选择先信用 Hyper-V。

- https://github.com/scotch-io/scotch-box/issues/195

上一位哥们，提供了快速的开启/关闭 Hyper-V 的方法，我们用他的方法（记得运行时，使用管理员运行）：

```
Windows 8 (Microsoft Windows [Version 6.3.9600]) - without uninstalling Hyper-V (assuming you have virtualization enabled in your BIOS).

Open CMD as Administrator:
**Disable**
dism.exe /Online /Disable-Feature:Microsoft-Hyper-V
Reboot
**Enable**
dism.exe /Online /Enable-Feature:Microsoft-Hyper-V
Reboot

There are other ways. I tested this and vagrant loaded up without the error.
```

我用 powercmd 打开，并输入`dism.exe /Online /Disable-Feature:Microsoft-Hyper-V` ，然后提示是否重新启动。保存一些程序或文件，重新启动。

然后，再`vagrant up`还是报上面的错误。

然后去 `控制面板\程序\程序和功能\启用或关闭Windows功能` 找到 Hyper-V 去掉前面的勾。
同时，去除 `Docker for Windows` 和 `Docker Desktop` 的开机自启动。

再次重新启动。

然后，再`vagrant up`还是报上面的错误。

下载最新版本的 virtualbox 6.0.4 ，安装

重启电脑。


然后，再`vagrant up`, 报下面的错误。

```
E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
λ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
There was an error while executing `VBoxManage`, a CLI used by Vagrant
for controlling VirtualBox. The command and stderr is shown below.

Command: ["startvm", "cac2cb19-692c-42ab-a306-621b7a06e9e4", "--type", "headless"]

Stderr: VBoxManage.exe: error: The native API dll was not found (C:\Windows\system32\WinHvPlatform.dll) (VERR_NEM_NOT_AVAILABLE).
VBoxManage.exe: error: VT-x is not available (VERR_VMX_NO_VMX)
VBoxManage.exe: error: Details: code E_FAIL (0x80004005), component ConsoleWrap, interface IConsole


E:\vag\vag-env\fenbox-lnmp\Vagrantfile (master -> origin)
```

此时，打开 virtualbox ，发现原来的虚拟机也不能打开了。

且在 虚拟机的设置下，看到：
发现无效配置：硬件加速配置已启用硬件虚拟化,但主机并不支持。需要禁用硬件虚拟化才能启动虚拟机

哇啦啦，新问题。

打开 VMware Workstation 会提示：

英文版本：

VMware Workstation and Hyper-V are not compatible. Remove the Hyper-V role from the system before running VMware Workstation.

![VMware Workstation and Hyper-V are not compatible.](https://snippetinfo.net/sysdata/attach/media.1656/ca9d46d6588c5b1d9cc4ab2c79250744.png)

中文版本：

“Vmware workstation与hyper-v不兼容”。如下图：

![Vmware workstation与hyper-v不兼容](https://s1.51cto.com/wyfs02/M02/8D/46/wKioL1iVcnyRd0pAAACQa90kZmU378.png-wh_500x0-wm_3-wmp_4-s_2643006047.png)

参考：

- https://blog.51cto.com/abool/1894839
- https://snippetinfo.net/mobile/media/1656

得到这样的理论：

步驟
1. 複製既有的開機選項設定
先用系統管理者權限開啟 [命令提示字元]
並執行

bcdedit /copy {default} /d "Windows Without Hyper-V"

2. 關閉 Hypervisor
複製步驟 1 中產生的 ID，然後執行下面語法
 
bcdedit /set {步驟 1 的 ID} hypervisorlaunchtype off

3. 調整預設開機設定
輸入 msconfig 可以看到新產生的設定，可以換掉預設的開機設定喔

![msconfig-开机-选择Hyper-设为默认值](https://snippetinfo.net/sysdata/modules/mod_step/media/1656/f42939869325d6809e11b1c151d9c021_l.png)

来吧：

通过 管理员 运行 CMD（一定要CMD, 不要 PowerShell）

```
C:\Windows\system32>bcdedit /copy {default} /d "Windows Without Hyper-V"
已将该项成功复制到 {3ea4d3d8-0f0d-11e9-948a-8cec4baf24a8}。

C:\Windows\system32>bcdedit /set {3ea4d3d9-0f0d-11e9-948a-8cec4baf24a8} hypervisorlaunchtype off
操作成功完成。

C:\Windows\system32>
```

Win+R 輸入 `msconfig` 可以看到新產生的設定，msconfig\开机\选择Hyper-v\设为默认值\应用\确定

重启电脑。

开机的时候，会多出一个选项，当然选择刚刚设置的默认值 without-hyper-v 啦！

这下是真的成功了！~ 

再次打开 vmware , 不再有提示。
再次打开 virtualbox , 点击某虚拟机，设置，不再有 “发现无效配置” 的提示。

回到最初的地方`vagrant up`吧。


#### `vagrant up`启动

```
DELL@DESKTOP-MQ9CENU MINGW64 /
$ cd /e/vag/vag-env/fenbox-lnmp/Vagrantfile/

DELL@DESKTOP-MQ9CENU MINGW64 /e/vag/vag-env/fenbox-lnmp/Vagrantfile (master)
$ ls
bootstrap.sh*  nginx/  README.md  sources.list  Vagrantfile

DELL@DESKTOP-MQ9CENU MINGW64 /e/vag/vag-env/fenbox-lnmp/Vagrantfile (master)
$ vagrant box list
fenbox-lnmp-bionic (virtualbox, 0)
ubuntu14.04        (virtualbox, 0)

DELL@DESKTOP-MQ9CENU MINGW64 /e/vag/vag-env/fenbox-lnmp/Vagrantfile (master)
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection reset. Retrying...
    default: Warning: Connection aborted. Retrying...
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 5.2.18
    default: VirtualBox Version: 6.0
==> default: Configuring and enabling network interfaces...
==> default: Mounting shared folders...
    default: /vagrant => E:/vag/vag-env/fenbox-lnmp/Vagrantfile
    default: /var/www/typecho => E:/vag/vag-env/fenbox-lnmp/typecho
    default: /var/www/wordpress => E:/vag/vag-env/fenbox-lnmp/wordpress
==> default: Running provisioner: shell...
    default: Running: C:/Users/DELL/AppData/Local/Temp/vagrant-shell20190221-9544-16jnyy3.sh
    default: --------------------------
    default:  Upgdate & Upgrade System
    default: --------------------------
    default: Get:1 http://mirrors.aliyun.com/ubuntu bionic InRelease [242 kB]
    default: Get:2 http://mirrors.aliyun.com/ubuntu bionic-security InRelease [88.7 kB]
    default: Get:3 http://mirrors.aliyun.com/ubuntu bionic-updates InRelease [88.7 kB]
    default: Get:4 http://mirrors.aliyun.com/ubuntu bionic-proposed InRelease [242 kB]
    default: Get:5 http://mirrors.aliyun.com/ubuntu bionic-backports InRelease [74.6 kB]
    default: Get:6 http://mirrors.aliyun.com/ubuntu bionic/universe Sources [9051 kB]
    default: Get:7 http://mirrors.aliyun.com/ubuntu bionic/main Sources [829 kB]
    default: Get:8 http://mirrors.aliyun.com/ubuntu bionic/multiverse Sources [181 kB]
    default: Get:9 http://mirrors.aliyun.com/ubuntu bionic/restricted Sources [5324 B]
    default: Get:10 http://mirrors.aliyun.com/ubuntu bionic/main amd64 Packages [1019 kB]
    default: Get:11 http://mirrors.aliyun.com/ubuntu bionic/main Translation-en [516 kB]
    default: Get:12 http://mirrors.aliyun.com/ubuntu bionic/restricted amd64 Packages [9184 B]
    default: Get:13 http://mirrors.aliyun.com/ubuntu bionic/restricted Translation-en [3584 B]
    default: Get:14 http://mirrors.aliyun.com/ubuntu bionic/universe amd64 Packages [8570 kB]
    default: Get:15 http://mirrors.aliyun.com/ubuntu bionic/universe Translation-en [4941 kB]
    default: Get:16 http://mirrors.aliyun.com/ubuntu bionic/multiverse amd64 Packages [151 kB]
    default: Get:17 http://mirrors.aliyun.com/ubuntu bionic/multiverse Translation-en [108 kB]
    default: Get:18 http://mirrors.aliyun.com/ubuntu bionic-security/universe Sources [35.1 kB]
    default: Get:19 http://mirrors.aliyun.com/ubuntu bionic-security/multiverse Sources [2308 B]
    default: Get:20 http://mirrors.aliyun.com/ubuntu bionic-security/main Sources [76.0 kB]
    default: Get:21 http://mirrors.aliyun.com/ubuntu bionic-security/main amd64 Packages [270 kB]
    default: Get:22 http://mirrors.aliyun.com/ubuntu bionic-security/main Translation-en [101 kB]
    default: Get:23 http://mirrors.aliyun.com/ubuntu bionic-security/universe amd64 Packages [126 kB]
    default: Get:24 http://mirrors.aliyun.com/ubuntu bionic-security/universe Translation-en [71.2 kB]
    default: Get:25 http://mirrors.aliyun.com/ubuntu bionic-security/multiverse amd64 Packages [3324 B]
    default: Get:26 http://mirrors.aliyun.com/ubuntu bionic-security/multiverse Translation-en [1848 B]
    default: Get:27 http://mirrors.aliyun.com/ubuntu bionic-updates/main Sources [247 kB]
    default: Get:28 http://mirrors.aliyun.com/ubuntu bionic-updates/universe Sources [133 kB]
    default: Get:29 http://mirrors.aliyun.com/ubuntu bionic-updates/multiverse Sources [4192 B]
    default: Get:30 http://mirrors.aliyun.com/ubuntu bionic-updates/restricted Sources [2060 B]
    default: Get:31 http://mirrors.aliyun.com/ubuntu bionic-updates/main amd64 Packages [527 kB]
    default: Get:32 http://mirrors.aliyun.com/ubuntu bionic-updates/main Translation-en [196 kB]
    default: Get:33 http://mirrors.aliyun.com/ubuntu bionic-updates/restricted amd64 Packages [6996 B]
    default: Get:34 http://mirrors.aliyun.com/ubuntu bionic-updates/restricted Translation-en [3076 B]
    default: Get:35 http://mirrors.aliyun.com/ubuntu bionic-updates/universe amd64 Packages [736 kB]
    default: Get:36 http://mirrors.aliyun.com/ubuntu bionic-updates/universe Translation-en [189 kB]
    default: Get:37 http://mirrors.aliyun.com/ubuntu bionic-updates/multiverse amd64 Packages [6384 B]
    default: Get:38 http://mirrors.aliyun.com/ubuntu bionic-updates/multiverse Translation-en [3452 B]
    default: Get:39 http://mirrors.aliyun.com/ubuntu bionic-proposed/universe Sources [2244 B]
    default: Get:40 http://mirrors.aliyun.com/ubuntu bionic-proposed/main Sources [22.7 kB]
    default: Get:41 http://mirrors.aliyun.com/ubuntu bionic-proposed/main amd64 Packages [51.6 kB]
    default: Get:42 http://mirrors.aliyun.com/ubuntu bionic-proposed/main Translation-en [23.0 kB]
    default: Get:43 http://mirrors.aliyun.com/ubuntu bionic-proposed/universe amd64 Packages [7536 B]
    default: Get:44 http://mirrors.aliyun.com/ubuntu bionic-proposed/universe Translation-en [5432 B]
    default: Get:45 http://mirrors.aliyun.com/ubuntu bionic-backports/universe Sources [2068 B]
    default: Get:46 http://mirrors.aliyun.com/ubuntu bionic-backports/universe amd64 Packages [3472 B]
    default: Get:47 http://mirrors.aliyun.com/ubuntu bionic-backports/universe Translation-en [1604 B]
    default: Fetched 29.0 MB in 10s (3023 kB/s)
    default: Reading package lists...
    default: Reading package lists...
    default: Building dependency tree...
    default:
    default: Reading state information...
    default: Calculating upgrade...
    default: 0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    default: ---------------
    default:  Install NGINX
    default: ---------------
    default: WARNING:
    default: apt
    default:
    default: does not have a stable CLI interface.
    default: Use with caution in scripts.
    default: Reading package lists...
    default: Building dependency tree...
    default: Reading state information...
    default: E: Unable to locate package nginx
    default: -----------------
    default:  Install MariaDB
    default: -----------------
    default:
    default: WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
    default: Reading package lists...
    default: Building dependency tree...
    default:
    default: Reading state information...
    default: E: Unable to locate package mariadb-server
    default: E: Unable to locate package php-mysql
    default: -------------
    default:  Install PHP
    default: -------------
    default:
    default: WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
    default: Reading package lists...
    default: Building dependency tree...
    default:
    default: Reading state information...
    default: E: Unable to locate package php-fpm
    default: -----------------
    default:  Initialize Site
    default: -----------------
    default: cp: cannot create regular file '/etc/nginx/sites-available/': No such file or directory
    default: cp:
    default: cannot create regular file '/etc/nginx/sites-available/'
    default: : No such file or directory
    default: ln: target '/etc/nginx/sites-enabled/' is not a directory: No such file or directory
    default: ln:
    default: target '/etc/nginx/sites-enabled/' is not a directory
    default: : No such file or directory
    default: /tmp/vagrant-shell: line 50: nginx: command not found
    default: /tmp/vagrant-shell: line 51: nginx: command not found
The SSH command responded with a non-zero exit status. Vagrant
assumes that this means the command failed. The output for this command
should be in the log above. Please read the output to determine what
went wrong.

DELL@DESKTOP-MQ9CENU MINGW64 /e/vag/vag-env/fenbox-lnmp/Vagrantfile (master)
$
```

起来了，但是，很明显的一个问题，有好多报错。怎么办？

`vagrant ssh` 进入 虚拟机看一下。

发现：

- /vagrant/ 下面，确实有文件。文件传输没有问题。
- `cp /vagrant/sources.list /etc/apt/` 这一步，没有成功。因为 `/etc/apt/sources.list` 文件还是系统原始文件。
- 日志中却有`Get:1 http://mirrors.aliyun.com/ubuntu bionic InRelease [242 kB]`这样的提示。为什么？

这样的话，也就是说，要么是因为权限问题，导致 在虚拟机中  `/vagrant/bootstrap.sh` 命令没有达到效果。

既然是这样。我手工执行一下。

```
vagrant@ubuntu-bionic:/vagrant$ sudo ./bootstrap.sh
```

因为在 Vagrantfile 中设置的IP是 192.168.50.10, 则 chrome 打开 http://192.168.50.10/ 。出现了 nginx 页面:

```
Welcome to nginx!
If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

那看一下，我们设置的 typecho 配置，则 chrome 打开 http://typecho.test.hellowiki.com/ , 出现了 

"hello typecho"

同理，打开 http://wordpress.test.hellowiki.com/ , 出现了 

"hello wordpress"

至此。我们的服务配置成功了。

### 遗留的问题

遗留的问题如下，有知道的，还望求教：

- 为什么 在虚拟机中  `/vagrant/bootstrap.sh` 命令没有达到效果？

### 如果想通过账号密码登录

参考： https://stackoverflow.com/questions/41337802/vagrants-ubuntu-16-04-vagrantfile-default-password/41337943

It's not necessary. You can:

#### 方法1

* login using SSH key authentication ( 注意：`vagrant ssh`登录的是 vagrant 用户，并不是 ubuntu 用户 )
* change the password using `sudo passwd ubuntu` (by default ubuntu user has sudo-permissions with `NOPASSWD` set)
Actually, not only you can, but you should change the password.

#### 方法2

```
DELL@DESKTOP-MQ9CENU MINGW64 ~/.vagrant.d/boxes/fenbox-lnmp-bionic/0/virtualbox (master)
$ pwd
/c/Users/DELL/.vagrant.d/boxes/fenbox-lnmp-bionic/0/virtualbox
DELL@DESKTOP-MQ9CENU MINGW64 ~/.vagrant.d/boxes/fenbox-lnmp-bionic/0/virtualbox (master)
$ cat Vagrantfile
# Front load the includes
include_vagrantfile = File.expand_path("../include/_Vagrantfile", __FILE__)
load include_vagrantfile if File.exist?(include_vagrantfile)

Vagrant.configure("2") do |config|
  config.vm.base_mac = "02FBA2FD6790"

  config.vm.provider "virtualbox" do |vb|
     vb.customize [ "modifyvm", :id, "--uart1", "0x3F8", "4" ]
     vb.customize [ "modifyvm", :id, "--uartmode1", "file", File.join(Dir.pwd, "ubuntu-bionic-18.04-cloudimg-console.log") ]
  end
end

DELL@DESKTOP-MQ9CENU MINGW64 ~/.vagrant.d/boxes/fenbox-lnmp-bionic/0/virtualbox (master)
$
```
然后可以在里面加上配置，如下：

```
# Front load the includes
include_vagrantfile = File.expand_path("../include/_Vagrantfile", __FILE__)
load include_vagrantfile if File.exist?(include_vagrantfile)

Vagrant.configure("2") do |config|
  config.vm.base_mac = "022999D56C03"
  config.ssh.username = "ubuntu"
  config.ssh.password = "fbcd1ed4fe8c83b157dc6e0f"

  config.vm.provider "virtualbox" do |vb|
     vb.customize [ "modifyvm", :id, "--uart1", "0x3F8", "4" ]
     vb.customize [ "modifyvm", :id, "--uartmode1", "file", File.join(Dir.pwd, "ubuntu-bionic-18.04-cloudimg-console.log") ]
  end
end
```

## ref
- https://www.linode.com/docs/web-servers/lemp/how-to-install-a-lemp-server-on-ubuntu-18-04/
- https://snippetinfo.net/mobile/media/1656
- https://stackoverflow.com/questions/41337802/vagrants-ubuntu-16-04-vagrantfile-default-password/41337943