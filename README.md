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
1.使用cmder进入Scratch3/scratch-gui，执行yarn install。结果如下：<br>
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/1.png)<br>

2.在cmder中进入Scratch3/scratch-gui目录下执行webpack-dev-server命令：<br>
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/2.png)<br>

3.（注：最终出现Compiled successfully 字样才能代表编译完全成功，此时可以打开网址https://127.0.0.1:8601/ ）
另启一个cmder窗口，进入scratch-vm，按顺序输入下列命令：<br>
```text
yarn install
yarn link
yarn add uglifyjs-webpack-plugin
yarn run watch
 ```
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/3.png)<br>
4.再打开一个新窗口，在scratch-gui 中输入指令 yarn link scratch-vm。<br>
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/4.png)<br>

至此，Scratch3开发环境的下载和安装完成，打开网页https://127.0.0.1:8601/ 即可查看结果。

##    2.	knn 模块的安装
###     2.1	源文件中的设置
在Scrathc3开发环境下安装knn模块需要对Scratch的scratch-vm和scratch-gui文件夹中的相关文件做出修改并添加相应的本地文件，在Scratch3中添加相对应模块  的方式大致相同，只是index.js文件大相径庭。因此相关过程亦可参考https://blog.just4fun.site/post/少儿编程/create-first-scratch3-extension/ 中所给出的方法。

1.下载knn需要的本地文件，在所选择的任意文件夹目录下执行 git clone https://github.com/CodeLabClub/scratch3_knn.git<br> 
2.将scratch3_knn中的index.js文件复制到\Scratch3\scratch-vm\src\extensions\scratch3_knn 文件夹中（其中scratch3_knn为新建的文件夹）。<br>
3.将scratch3_knn中的mobilenet.js文件夹复制到\Scratch3\scratch-vm\src\extensions\scratch3_knn 文件夹内（与index.js处于同一文件夹）<br>
4.将knn文件夹复制到\Scratch3\scratch-gui\static 目录下。（上述过程可参见https://github.com/CodeLabClub/scratch3_knn/issues/3）<br>
5.回到 mobilenet.js 所在文件夹（\Scratch3\scratch-vm\src\extensions\scratch3_knn），修改mobilenet.js中url var BASE_PATH = '/static/knn/'。<br>
6.在\Scratch3\scratch-gui\src\lib\libraries\extensions 目录下新建knnAlgorithm文件夹。在其中加入图片knnAlgorithm.png 和 knnAlgorithm-small.svg<br>
7.将\Scratch3\scratch-vm\src\extensions\scratch3_knn 目录下 index.js 文件的导入部分做如下修改：<br>
```text
+require('babel-polyfill');
+const Runtime = require('../../engine/runtime');

+const ArgumentType = require('../../extension-support/argument-type');
+const BlockType = require('../../extension-support/block-type');
+const Clone = require('../../util/clone');
+const Cast = require('../../util/cast');
+const Video = require('../../io/video');
+const formatMessage = require('format-message');
+const tf = require('@tensorflow/tfjs');
+const mobilenetModule = require('./mobilenet.js');
+const knnClassifier = require('@tensorflow-models/knn-classifier'); 
```
8.将\Scratch3\scratch-vm\src\extension-support 中extension-manager.js 中的内容做如下修改，以调用scratch_knn中的index文件：<br>
```text
const dispatch = require('../dispatch/central-dispatch');
const log = require('../util/log');
const maybeFormatMessage = require('../util/maybe-format-message');

const BlockType = require('./block-type');

+const Scratch3KnnBlocks = require('../extensions/scratch3_knn');
// These extensions are currently built into the VM repository but should not be loaded at startup.
// TODO: move these out into a separate repository?
// TODO: change extension spec so that library info, including extension ID, can be collected through static methods

const builtinExtensions = {
    // This is an example that isn't loaded with the other core blocks,
    // but serves as a reference for loading core blocks as extensions.
    coreExample: () => require('../blocks/scratch3_core_example'),
    // These are the non-core built-in extensions.
    pen: () => require('../extensions/scratch3_pen'),
    wedo2: () => require('../extensions/scratch3_wedo2'),
    music: () => require('../extensions/scratch3_music'),
    microbit: () => require('../extensions/scratch3_microbit'),
    text2speech: () => require('../extensions/scratch3_text2speech'),
    translate: () => require('../extensions/scratch3_translate'),
    videoSensing: () => require('../extensions/scratch3_video_sensing'),
    ev3: () => require('../extensions/scratch3_ev3'),
    makeymakey: () => require('../extensions/scratch3_makeymakey'),
    boost: () => require('../extensions/scratch3_boost'),
    gdxfor: () => require('../extensions/scratch3_gdx_for'),
+   knnAlgorithm:() =>require('../extensions/scratch3_knn')
};

```

9.将上述两个文件中的修改保存后，在\Scratch3\scratch-gui\src\lib\libraries\extensions 文件夹下修改index.jsx文件：<br>
第一处修改，导引入图片。<br>
```text
import translateIconURL from './translate/translate.png';
import translateInsetIconURL from './translate/translate-small.png';

import makeymakeyIconURL from './makeymakey/makeymakey.png';
import makeymakeyInsetIconURL from './makeymakey/makeymakey-small.svg';

+import knnalgorithmImage from './knnAlgorithm/knnAlgorithm.png';
+import knnalgorithmInsetImage from './knnAlgorithm/knnAlgorithm-small.svg';

import microbitIconURL from './microbit/microbit.png';
import microbitInsetIconURL from './microbit/microbit-small.svg';
import microbitConnectionIconURL from './microbit/microbit-illustration.svg';
import microbitConnectionSmallIconURL from './microbit/microbit-small.svg';
```

第二处修改，导引入extension模块(在index.jsxexport default []部分中添加如下代码段）。<br>

```text
export default [
......
......
{.....
.....
},
+{
+       name: (
+           <FormattedMessage
+               defaultMessage="knn algorithm"
+               description="Name for the 'knn algorithm' extension"
+               id="gui.extension.knnalgorithm.name"
+           />
+       ),
+       extensionId: 'knnAlgorithm',
+       iconURL: knnalgorithmImage,
+       insetIconURL: knnalgorithmInsetImage,
+       description: (
+           <FormattedMessage
+               defaultMessage="knn algorithm."
+               description="Description for the 'knn algorithm' extension"
+               id="gui.extension.knnalgorithm.description"
+           />
+       ),
+       featured: true
+   },
```
注意上图中extensionId与extension-manager.js中knnAlgorithm:() =>require('../extensions/scratch3_knn') 代码中的“knnAlgorithm”相同，并且这个id也要与index.js（\Scratch3\scratch-vm\src\extensions\scratch3_knn 目录下）中的“getInfo()”部分return部分内的id内容一致，否则点击extension对应图标时无法自动移动到程序块组所在位置（程序块组此时会出现，但菜单栏不会主动移动）

###     2.2	运行调试及附加依赖的安装
由于上述index.js中出现了新的依赖，我们需要再次编译并调试程序且准备好所需的依赖。下述操作均在cmder中执行，不再赘述。
1.在Scratch3\scratch-gui 目录下按顺序执行 yarn install 和 webpack-dev-server –https 命令。
等待webpack-dev-server –https编译完毕后（此时显示编译错误“Fail to compile”）打开cmder新窗口，在Scratch3\scratch-vm 下按顺序执行 yarn install，yarn link，yarn add uglifyjs-webpack-plugin 和 yarn run watch。
2.0按照 yarn run watch 的运行结果进行依赖的添加，注意此时停止webpack-dev-server –https和yarn run watch 的运行。之后在新的cmder窗口中在Scratch3\scratch-gui 目录下运行yarn link scratch-vm 命令。
yarn run watch 的运行报错如下：
```text
ERROR in ./src/extensions/scratch3_knn/index.js
    Module not found: Error: Can't resolve '@tensorflow-models/knn-classifier' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/index.js 31:20-64
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js

    ERROR in ./src/extensions/scratch3_knn/index.js
    Module not found: Error: Can't resolve '@tensorflow/tfjs' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/index.js 27:9-36
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js

    ERROR in ./src/extensions/scratch3_knn/mobilenet.js
    Module not found: Error: Can't resolve '@tensorflow/tfjs' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/mobilenet.js 5:133-160 5:209-257
     @ ./src/extensions/scratch3_knn/index.js
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js

    ERROR in ./src/extensions/scratch3_knn/index.js
    Module not found: Error: Can't resolve 'babel-polyfill' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/index.js 11:0-25
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js
```
2.1	在D:\Scratch3\scratch-vm\src\extensions\scratch3_knn（具体目录参见你自己的yarn run watch 运行结果）目录下执行 yarn add babel-polyfill。之后再次运行webpack-dev-server –https 和 yarn run watch。（注意没添加一个依赖过程中终止上述两个cmder窗口中的程序运行，使用Ctrl+C，直至执行yarn add xxxx完毕后再次执行，因为有时一个依赖的安装中也包括了其他依赖的安装，这样可以避免错误和重复安装，执行yarn add xxxx命令需要一个新的cmder窗口）

2.2 第二次编译yarn run watch 报错为：
```text
ERROR in ./src/extensions/scratch3_knn/index.js
    Module not found: Error: Can't resolve '@tensorflow-models/knn-classifier' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/index.js 31:20-64
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js

    ERROR in ./src/extensions/scratch3_knn/index.js
    Module not found: Error: Can't resolve '@tensorflow/tfjs' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/index.js 27:9-36
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js

    ERROR in ./src/extensions/scratch3_knn/mobilenet.js
    Module not found: Error: Can't resolve '@tensorflow/tfjs' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/mobilenet.js 5:133-160 5:209-257
     @ ./src/extensions/scratch3_knn/index.js
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js
 ```

同样，停止编译后在D:\Scratch3\scratch-vm\src\extensions\scratch3_knn 目录下执行yarn add @tensorflow/tfjs@0.10.0
（详见https://github.com/CodeLabClub/scratch3_knn/issues/2 ）

2.3	第三次编译报错为：
```text
ERROR in ./src/extensions/scratch3_knn/index.js
    Module not found: Error: Can't resolve '@tensorflow-models/knn-classifier' in 'D:\Scratch3\scratch-vm\src\extensions\scratch3_knn'
     @ ./src/extensions/scratch3_knn/index.js 31:20-64
     @ ./src/extension-support/extension-manager.js
     @ ./src/virtual-machine.js
     @ ./src/index.js
     @ ./src/index.js-exposed
     @ ./src/playground/benchmark.js
```

则在D:\Scratch3\scratch-vm\src\extensions\scratch3_knn 目录下执行yarn add @tensorflow-models/knn-classifier之后重新编译（注意先停止正在运行的编译程序）。
此时应显示编译成功（Compiled successfully）。至此，Scratch3中knn模块安装完成。

建议使用“石头剪刀布”测试knn图像分类结果，测试程序块示意图如下：<br>
![image](https://github.com/TyutWzz-beep/scratch_knn_install/blob/master/images/9.png)<br>
如图所示左侧绿色块为knn模块中所携带的API，右侧为“石头剪刀布”测试的程序。你可以使用该程序自行测试安装是否成功。
```
操作系统：windows10 家庭版
电脑型号：lenovo ThinkPadE460
浏览器：Microsoft Edge
```

### 以下为结果展示

1. 训练“石头”类型，设置以方向键触发，从摄像头（webcam）取10个样本作为“石头”的训练集：<br>
![image](https://github.com/doNotBeTooSerious/gifImages/blob/master/scratch3_knn/%E7%9F%B3%E5%A4%B4%E8%AE%AD%E7%BB%83.gif)<br>

2. 训练“剪刀”类型，同样以方向键触发，获取10个样本作为训练集：<br>
![image](https://github.com/doNotBeTooSerious/gifImages/blob/master/scratch3_knn/%E5%89%AA%E5%88%80%E8%AE%AD%E7%BB%83.gif)<br>

3. 训练“布”类型，以方向键触发，获取10个样本作为训练集：<br>
![image](https://github.com/doNotBeTooSerious/gifImages/blob/master/scratch3_knn/%E5%B8%83%E8%AE%AD%E7%BB%83.gif)<br>














