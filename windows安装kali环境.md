windows10 kali环境调教指南

1. ## 安装kali环境

   1. win10新版本中如果在cmd中执行bash命令来进行安装会提示适用于linux的windows子系统没有已安装的分发版，如下图所示。

      [![win10下怎么安装使用bash shell](https://imgsa.baidu.com/exp/w=500/sign=668bf278bb003af34dbadc60052bc619/37d12f2eb9389b501f9af2238835e5dde7116e5d.jpg)](http://jingyan.baidu.com/album/d5a880ebf2aeda13f147ccb1.html?picindex=1)

   2. 

      出现上图所示的提示不用担心，用户的win10系统依然能正确安装bash，而且安装方法比早期的win10版本还要快速。首先按WIN+R键，打开运行。然后在运行中输入appwiz.cpl，如下图所示。

      [![win10下怎么安装使用bash shell](https://imgsa.baidu.com/exp/w=500/sign=ed3fedc168380cd7e61ea2ed9144ad14/9c16fdfaaf51f3dee9ceab1599eef01f3a297946.jpg)](http://jingyan.baidu.com/album/d5a880ebf2aeda13f147ccb1.html?picindex=2)

   3. 

      在打开的“程序和功能”中点击“启用或者关闭windows功能”。

      [![win10下怎么安装使用bash shell](https://imgsa.baidu.com/exp/w=500/sign=9d87fe72c595d143da76e42343f08296/2f738bd4b31c87017eaba0972a7f9e2f0708ffa6.jpg)](http://jingyan.baidu.com/album/d5a880ebf2aeda13f147ccb1.html?picindex=3)

   4. 

      在弹出的窗口中勾选“适用于linux的windows子系统”。点击“确定”按钮后会提示用户重启电脑。

      [![win10下怎么安装使用bash shell](https://imgsa.baidu.com/exp/w=500/sign=4044240e75f0f736d8fe4c013a55b382/b999a9014c086e06f6b52d3a0f087bf40ad1cba6.jpg)](http://jingyan.baidu.com/album/d5a880ebf2aeda13f147ccb1.html?picindex=4)

   5. 

      重启完成后，点击任务栏上的microsft store。点击右上角的搜索，输入bash作为关键字进行搜索。

      [![win10下怎么安装使用bash shell](https://imgsa.baidu.com/exp/w=500/sign=9a9cc0509def76c6d0d2fb2bad16fdf6/838ba61ea8d3fd1f3e75b04b3d4e251f95ca5f46.jpg)](http://jingyan.baidu.com/album/d5a880ebf2aeda13f147ccb1.html?picindex=5)

   6. 

      搜索结果出现后点击"获取这些应用“。

      [![win10下怎么安装使用bash shell](https://imgsa.baidu.com/exp/w=500/sign=f07bb09cac44ad342ebf8787e0a30c08/b58f8c5494eef01f5b710ab4edfe9925bc317d5d.jpg)](http://jingyan.baidu.com/album/d5a880ebf2aeda13f147ccb1.html?picindex=6)

   7. 

      在弹出的界面中可以安装多种linux子环境下的bash，根据个人爱好选择后点击安装即可。

      [![win10下怎么安装使用bash shell](https://imgsa.baidu.com/exp/w=500/sign=ce3c13e953df8db1bc2e7c643922dddb/d000baa1cd11728bd54c9e53c5fcc3cec3fd2c59.jpg)](http://jingyan.baidu.com/album/d5a880ebf2aeda13f147ccb1.html?picindex=7)

2. ## 启动kali

   运行里输入“kali”来启动kali的shell或者在win菜单里选择kali linux来启动

3. ## 配置环境：

   1. 初次启动会让设置账户，输入用户名和两次密码即可。会创建一个普通账户

   2. 设置root密码

      ```
      sudo passwd 或者sudo passwd root
      Password：你当前的密码 
      Enter new UNIX password：这个是root的密码 
      Retype new UNIX password：重复root的密码 
      然后会提示成功的信息。 
      ```

   3. 更新镜像源

      ```
      cd /etc/apt
      cp sources.list sources.list.bak # 备份镜像源文件
      这时候直接修改source.list是不行的，因为这个文件默认是只读的
      所以要使用root来修改权限
      su	# 切换到root，输入root密码
      vi sources.list
      怎么用vim这里不解释了，更新源为下面的，这里选择的是阿里云
      
      #aliyun 阿里云
      deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
      deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
      
      # ustc 中科大
      # deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
      # deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
      # deb http://mirrors.ustc.edu.cn/kali-security kali-current/updates main contrib non-free
      # deb-src http://mirrors.ustc.edu.cn/kali-security kali-current/updates main contrib non-free
      
      # kali 官方源
      # deb http://http.kali.org/kali kali-rolling main non-free contrib 
      # deb-src http://http.kali.org/kali kali-rolling main non-free contrib 
      
      
      更新保存后，清理安装包
      sudo apt-get clean
      sudo  apt-get autoclean
      更新
      sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade
      然后就可以使用apt来安装软件了，测试安装git
      apt isntall git # 可以成功安装了
      
      Git 的工作需要调用 curl，zlib，openssl，expat，libiconv 等库的代码，所以需要先安装这些依赖工具。
      apt-get install libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-dev
      
      ```

      





