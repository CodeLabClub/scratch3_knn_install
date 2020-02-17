在Scratch3网页版中加入knn模块过程记录
===================================
##    1.	Scratch3开发环境的下载和安装
###   1.1	Scratch3的下载
使用github上提供的资源，在电脑中新建文件夹(本文默认文件夹名为Scratch3)后，输入下列命令从github上直接下载开发环境：
下载scratch-gui：
git clone https://github.com/LLK/scratch-gui.git 
下载scratch-vm：
git clone https://github.com/LLK/scratch-vm.git
注意上述指令需要在电脑中新建的文件夹目录下由cmder中执行。且git clone和下面安装过程中涉及的yarn等命令需要自行安装。本篇主要目的在于记录Scratch3网页版中添加knn模块的过程，有关Scratch3开发环境的搭建可详见：https://blog.just4fun.site/post/少儿编程/create-first-scratch3-extension/ 
###   1.2	Scratch3的安装
1.	使用cmder进入Scratch3/scratch-gui，执行yarn install。结果如下：
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/1.png)

2.	在cmder中进入Scratch3/scratch-gui目录下执行webpack-dev-server命令：
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/2.png)

3.	（注：最终出现Compiled successfully 字样才能代表编译完全成功，此时可以打开网址https://127.0.0.1:8601/ ）
另启一个cmder窗口，进入scratch-vm，按顺序输入下列命令：
    - yarn install
    - yarn link
    - yarn add uglifyjs-webpack-plugin
    - yarn run watch

    ![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/3.png)

4.	再打开一个新窗口，在scratch-gui 中输入指令 yarn link scratch-vm。
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/4.png)

    至此，Scratch3开发环境的下载和安装完成，打开网页https://127.0.0.1:8601/ 即可查看结果。

##    2.	knn 模块的安装


