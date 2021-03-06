## 零碎笔记    
### 1. Linux命令 
#### 1.1 批量删除指定文件    
* 命令： find / -name XXX -exec rm -v {} \\;

#### 1.2 挂载逻辑卷
* fdisk -l 查看要挂载的卷名称      
* vgdisplay查看需要挂载的卷的大小     
* 然后 

```shell
lvcreate -l 卷大小 -n lv1 vg1
mkfs.ext3 -j /dev/vg1/lv1
tune2fs -c 0 -i 0 /dev/vg1/lv1
```     
* 挂载     

```shell
mount /dev/mapper/vg1-lv1 /挂载位置
echo "/dev/mapper/vg1-lv1 /挂载位置 ext3 defaults 0 0" >>/etc/fstab
more /etc/fstab 
df -h
```

### 2. Calcite
#### 2.1 查看RelNode    
* RelOptUtil.toString(RelNode)
  
### 3. Git相关
#### 3.1 合入MR步骤    
* `git checkout develop`; `git pull origin develop:develop -f`     
  确保在合入代码前，你在我们的主干分支develop上，且与主库代码同步。其中origin为flink主库在你本地的库别名（当然你自己本地也有可能叫别的对应名称，通过`git remote -v`可以查看    
* `git fetch huawei srcBranch:localBranch`         
  获取MR分支代码，srcBranch即为MR的来源分支，localBranch为fetch下来的分支名称，名字你可以随意取    
* `git merge localBranch --squash`   
  将localBranch上的改动通过squash的方式合入到当前分支(develop)上
* `git commit -a --author="作者"`        
  作者即这个MR的author，写他/她在CodeClub对应的昵称即可     
* 上一步过后，这个地方会弹出一个vi交互窗口，你需要用insert模式插入MR的相关信息，即MR的标题、MR的message，第一行会被当做commit的标题，后面的所有内容会被当做commit message
* `git push huawei develop:develop`     
  将本地的develop分支（经过了步骤2、3、4，已经包含了MR的更改）push到我们主库的develop分支上
  
#### 3.2 代码回退    
* `git log`查看提交记录    
* `git reset --HARD *****`
* `git push origin *** -f`

#### 3.3 一般分支合入主干分支新增特性
* git checkout branchA(一般分支)
* git rebase master(主干分支)(如果无冲突，结束)
* 如果有冲突，解决冲突，然后git add .（后面不能git commit, 否则会生成一个无名分支）
* git rebase --continue

#### 3.4 git branch -a无法全部显示远程的分支
* git pull 更新一下即可

#### 3.5 手动编译git
##### 3.5.1 安装zlib    
* tar zxvf zlib-1.2.8.tar.gz      
* cd zlib-1.2.8     
* ./configure --prefix=/usr/local/zlib-1.2.8
* make    
* make install

##### 3.5.2 安装openssl
* tar openssl-1.0.2.tar.gz    
* cd openssl-1.0.2
* CFLAG=-fPIC ./config shared --prefix=/usr/local/openssl-1.0.2
* make    
* make install    

##### 3.5.3 安装curl    
* tar curl-7.53.1.tar.gz
* cd curl-7.53.1
* ./configure --prefix=/usr/local/curl-7.53.1 --with-ssl=/usr/local/openssl-1.0.2 --with-zlib=/usr/local/zlib-1.2.8    
* make
* make install

##### 3.5.4 安装git
* tar zxvf git-2.8.4.tar.gz    
* cd git-2.8.4    
* ./configure --with-curl=/usr/local/curl-7.53.1 --with-zlib=/usr/local/zlib-1.2.8    
* make    
* make install

##### 4. samba安装    
* yum install samba samba-client
* 查看SELinux是否关闭    
* 在/etc/samba/smb.conf中添加如下内容：
```
[global]
   workgroup = WORKGROUP
   security = user
   passdb backend = tdbsam
   printing = cups
   printcap name = cups
   load printers = yes
   cups options = raw
   ntlm auth = yes
   
[homes]
   commont = Home Directories
   valid users = %S, %D%w%S
   browserable = Yes
   read only = No
   inherit acls = Yes
   
[printers]
   comment = All Printers
   path = /var/tmp
   printable = Yes
   create mask = 0600
   browseable = No
[print$]
   comment = Printer Drivers
   path = /var/lib/samba/drivers
   write list = @printadmin root
   force group = @printadmin
   create mask = 0664
   directory mask = 0775
```
* 添加Linux账号
 `smbpasswd -a 账号名称`
* 激活账号    
 `smbpasswd -e 账号名称`
