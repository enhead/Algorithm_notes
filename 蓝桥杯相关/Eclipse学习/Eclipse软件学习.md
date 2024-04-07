# Eclipse软件学习

来自：【Eclipse使用教程_全面又高效_eclipse小技巧】 https://www.bilibili.com/video/BV135411Q7Yp/?p=2&share_source=copy_web&vd_source=53a7698ccf0c6818c6445124ba71e16b

## ==[备战蓝桥杯：Java 组环境安装与 Eclipse 体验优化 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/349628159)== **重点看代码补全和格式化**

`java`和`eclipse`需要找对应的版本

[JDK11下载与安装 win10 64位_java11下载-CSDN博客](https://blog.csdn.net/weixin_53185848/article/details/111827608)

[如何查看eclipse的版本-CSDN博客](https://blog.csdn.net/Duqian94/article/details/52386076)

[Eclipse安装中文语言包（官方汉化） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/342692825)

(汉化暂时没整)

版本对应的汉化包（配合上面的食用）：[Babel Language Packs (eclipse.org)](https://archive.eclipse.org/technology/babel/babel_language_packs/R0.18.2/2020-12/2020-12.php#zh)





[java中eclipse中运行程序的快捷键是什么？_运行程序快捷键-CSDN博客](https://blog.csdn.net/evilcry2012/article/details/78788255)

[Eclipse点击空格总是自动补全代码怎么办，如何自动补全代码，代码提示-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1665224)

## ==新建项目==

![image-20231222103529570](images/image-20231222103529570.png)

![image-20231222104256850](images/image-20231222104256850.png)

没有`Java Project`就到`Project`中找

==放代码的地方（工作空间）不能更换==,否则会更新为默认

![image-20231222104303589](images/image-20231222104303589.png)

又打开`perspect`视图就打开



[eclipse左边的项目工程列表窗口不见了如何调-CSDN博客](https://blog.csdn.net/yin_xing_ye/article/details/83718729)

![image-20231222104721094](images/image-20231222104721094.png)



写源码之前，创建一个包：

![image-20231222104910182](images/image-20231222104910182.png)

![image-20231222105008359](images/image-20231222105008359.png)

新建一个`java`类:

![image-20231222105153993](images/image-20231222105153993.png)

然后就可以写了：

![image-20231222105439927](images/image-20231222105439927.png) 运行：（==在这之前一定要保存==)

![image-20231222105607867](images/image-20231222105607867.png)

**一个包中能同时存在多个运行文件**，记得和源文件保持同名就行

### 显示项目栏：

[eclipse左边的项目工程列表窗口不见了如何调-CSDN博客](https://blog.csdn.net/yin_xing_ye/article/details/83718729)

![image-20240402202331971](images/image-20240402202331971.png)

## 设置字体大小：

![image-20231222110142894](images/image-20231222110142894.png)

![image-20231222110217986](images/image-20231222110217986.png)

![image-20231222110317210](images/image-20231222110317210.png)

![image-20231222110510291](images/image-20231222110510291.png)

![image-20231222110514113](images/image-20231222110514113.png)

![image-20231222110557521](images/image-20231222110557521.png)

## 删除和导入项目

### 删除

![image-20231222110858764](images/image-20231222110858764.png)

### 导入

#### 1. 已经在工作空间中

![image-20231222111052891](images/image-20231222111052891.png)

![image-20231222111149550](images/image-20231222111149550.png)

![image-20231222111303142](images/image-20231222111303142.png)

![image-20231222111352402](images/image-20231222111352402.png)

然后点击`finish`就行了

#### 2. 在其他位置导入项目：

打开步骤跟1一样：

就是文件选择哪个文件

![image-20231222112008117](images/image-20231222112008117.png)

## 常用快捷件：

```java
package com.sxt;

import java.util.Scanner;

public class Hello {
	public static void main(String[] args) {
		/*	alt+/ 代码自动提示补全
		 * 		补齐main：main 然后 alt+/
		 * 		自动补齐输出：syso 然后 alt+/
		 * 
		 * 	（取消）单行注释： 			ctrl+/
		 * 	多行注释：					ctrl+shift+/
		 * 	取消多行注释：				ctrl+shift+\ (注意)
		 * 	自动导包：					ctrl+shift+o (用了没有的类后)
		 * 	快速构建实体类窗口的快捷件：	alt+shift+s
		 * 	代码回退：					ctrl+z
		 * 	代码前进：					ctrl+y
		 *  运行：						ctrl+f11
		 *  快速格式化：				ctrl+shift+f
		 *  查看快捷键                 cttl+shift+l
		 */
		Scanner sc=new Scanner(System.in);
		
	}
	
}
```

实现接口的快捷键：

![image-20240107201945392](images/image-20240107201945392.png)



![image-20231222113651763](images/image-20231222113651763.png)

![image-20231222114043061](images/image-20231222114043061.png)

### 补充：

#### 代码补全：

[eclipse开启/关闭代码自动补全（自动联想）功能_eclipse auto activation-CSDN博客](https://blog.csdn.net/weixin_46269688/article/details/106224237)

如果发现关了可以按按`alt+\`

打开代码补全：

![image-20240106164027512](images/image-20240106164027512.png)

![image-20240106164133539](images/image-20240106164133539.png)

## 简单debug

断点：程序执行到断点会暂停，（其他照常运行）

![image-20231222151535294](images/image-20231222151535294.png)

![image-20231222151451832](images/image-20231222151451832.png)

![image-20231222151628620](images/image-20231222151628620.png)

![image-20240117230203426](images/image-20240117230203426.png)

![image-20231222154111025](images/image-20231222154111025.png)



## 后面创建web工程的没看





## 解决`eclipse`中 `while(Scanner.hasNext())`一直为死循环的问题



- 最好在编译器中，加一个结束符号0
- 样例末尾加一个0

**后面记得改回来**

```java
import java.util.Scanner;
 
public class ScannerKeyBoardTest1 {
 
	public static void main(String[] args) {
 
		// 例：当输入"0"时，循环结束
		Scanner sc = new Scanner(System.in);
		while (!sc.hasNext("0")) {
			System.out.println("键盘输入的内容是："+sc.next());
		}
	}
}
```



参考：[解决Java中 while(Scanner.hasNext())一直为死循环的问题_java scanner hasnext一直在读取-CSDN博客](https://blog.csdn.net/Rex_WUST/article/details/88424076)
