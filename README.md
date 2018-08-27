# sw适配方案

### 适配方法
> 设计图：1600*960 【横屏应用】

> 1.首先，我们需要 https://github.com/ladingwu/dimens_sw 执行java代码 里面的宽度的变量修改为938（设计图是960的 状态栏22 960-22=938 ）【貌似平板或者手机来讲都是以小的边为宽，所以我们就修改宽的那个变量】 至于，dp=px/(dpi/160)    我一般是下载个开发助手app，在java文件中增加需要适配的dp值，然后把生成的文件丢到res文件里面
>![enter image description here](https://github.com/baowenqing/Android-screen/blob/master/%E8%BF%90%E7%AE%97dp.png?raw=true)


> 2. 如果是蓝湖 设计图非1600*960的 要修改为1600*960的像素  剩下的就可以直接按照标注图的数字 进行书写，自动适配
 ![enter image description here](https://github.com/baowenqing/Android-screen/blob/master/%E8%AE%BE%E8%AE%A1%E5%9B%BE.png?raw=true)
 
 ![enter image description here](https://github.com/baowenqing/Android-screen/blob/master/%E6%A0%87%E6%B3%A8.png?raw=true)
 
 > 3.AS上面的预览图的话 可以直接新建一个模拟器 尺寸写1600*960  对角线英寸写机型的  完成即可
 ![enter image description here](https://github.com/baowenqing/Android-screen/blob/master/as%E9%A2%84%E8%A7%88%E5%9B%BE.png?raw=true)
 
### 来源
**https://mp.weixin.qq.com/s/X-aL2vb4uEhqnLzU5wjc4Q**

#### 原理：
 smallestWidth适配，或者叫sw限定符适配。指的是Android会识别屏幕可用高度和宽度的最小尺寸的dp值（其实就是手机的宽度值），然后根据识别到的结果去资源文件中寻找对应限定符的文件夹下的资源文件。
 
 smallestWidth限定符适配和宽高限定符适配最大的区别在于，前者有很好的容错机制，如果没有value-sw360dp文件夹，系统会向下寻找，比如离360dp最近的只有value-sw350dp，那么Android就会选择value-sw350dp文件夹下面的资源文件。这个特性就完美的解决了上文提到的宽高限定符的容错问题。
这套方案是上述几种方案中最接近完美的方案。

首先，从开发效率上，它不逊色于上述任意一种方案。根据固定的放缩比例，我们基本可以按照UI设计的尺寸不假思索的填写对应的dimens引用。

我们还有以375个像素宽度的设计稿为例，在values-sw360dp文件夹下的dimens文件应该怎么编写呢？

这个文件夹下，意味着手机的最小宽度的dp值是360，我们把360dp等分成375等份，每一个设计稿中的像素，大概代表smallestWidth值为360dp的手机中的0.96dp，那么接下来的事情就很简单了，假如设计稿上出现了一个10px*10px的ImageView,那么，我们就可以不假思索的在layout文件中写下对应的尺寸。

这种机制和上文提到的宽高限定符适配原理上是一样的，都是系统通过特定的规则来选择对应的文件。
当系统识别到手机的smallestWidth值时，就会自动去寻找和目标数据最近的资源文件的尺寸。



其次，从稳定性上，它也优于上述方案。原生的dp适配可能会碰到Pixel 2这种有些特别的手机需要单独适配，但是在smallestWidth适配中，通过计算Pixel 2手机的的smallestWidth的值是411，我们只需要生成一个values-sw411dp(或者取整生成values-sw410dp也没问题)就能解决问题。



smallestWidth的适配机制由系统保证，我们只需要针对这套规则生成对应的资源文件即可，不会出现什么难以解决的问题，也根本不会影响我们的业务逻辑代码，而且只要我们生成的资源文件分布合理，，即使对应的smallestWidth值没有找到完全对应的资源文件，它也能向下兼容，寻找最接近的资源文件。



当然，smallestWidth适配方案有一个小问题，那就是它是在Android 3.2 以后引入的，Google的本意是用它来适配平板的布局文件（但是实际上显然用于diemns适配的效果更好），不过目前所有的项目应该最低支持版本应该都是4.0了（糗事百科这么老的项目最低都是4.0哦），所以，这问题其实也不重要了。



还有一个缺陷我忘了提，那就是多个dimens文件可能导致apk变大，这是事实，根据生成的dimens文件的覆盖范围和尺寸范围，apk可能会增大300kb-800kb左右，目前糗百的dimens文件大小是406kb，我认为这是可以接受的。
