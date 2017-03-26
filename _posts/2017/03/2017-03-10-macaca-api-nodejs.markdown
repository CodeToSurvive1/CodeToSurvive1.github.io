---
layout: "post"
title: "macaca相关api整理汇总"
date: "2017-03-10 10:52"
category: "macaca"
tags: [macaca]

---


链接地址 [https://macacajs.github.io/macaca-wd/](https://macacajs.github.io/macaca-wd/) 


macaca相关api整理汇总     


1.方法：acceptAlert()     

支持平台: Android iOS   

功能：捕捉当前显示的弹出框  


2.方法：alertKeys(keys)   

支持平台: iOS   

功能：给javascript中的prompt发送按键  

3.方法：alertText()    

支持平台: iOS   

功能：获取JavaScript的alert(), confirm(), 或者 prompt()格式的对话框中的值      

4.方法：back()     

支持平台:Android Web(WebView)   

功能：如果可能的话，将通过浏览器历史导航返回上一层   

5.方法：clear()    

支持平台：Android iOS Web(WebView)   

功能：将TEXTAREA 或者 text 的输入框元素的值清空掉    

6.方法：click()   

支持平台：Android iOS Web(WebView) 

功能：点击元素     

7.方法：close()    

支持平台：Web(WebView)   

功能：关闭当前窗口   

8.方法：context(contextRef)    

支持平台：Android iOS    

功能：设置当前上下文      

9.方法：contexts()     

支持平台：Android iOS    

功能：获取可用的上下文列表   

10.方法：currentContext()      

支持平台：Android iOS    

功能：获取当前的上下文     

11.方法：dismissAlert()      

支持平台：Android iOS  

功能：关闭当前显示的弹出框  

12.element(using, value)  

支持平台：Android iOS Web(WebView)   

功能：根据特定策略查找页面的某个目标元素     

参数：using要使用的定位策略 value搜索目标      

13.elementByClassName(value)    

支持平台：Android iOS Web(WebView) 

功能：根据类名称查找元素   

14.elementByClassNameIfExists(value)    

支持平台：Android iOS Web(WebView)   

功能：根据类名称查找元素，如果不存在返回undefined      

15.elementByClassNameOrNull(value)  

支持平台：Android iOS Web(WebView)   

功能：根据类名称查找元素，如果不存在返回null    

16.elementByCss(value)  

支持平台：Web(WebView)   

功能：根据样式定位器查找元素      

17.elementByCssIfExists     

支持平台：Web(WebView)   

功能：根据样式定位器查找元素，如果不存在返回undefined       

18.elementByCssOrNull(value)    

支持平台：Web(WebView)   

功能：根据样式定位器查找元素，如果不存在返回null  

19.elementByCssSelector(value)  

支持平台：Web(WebView)   

功能：根据样式定位器查找元素  

20.elementByCssSelectorIfExists(value)  

支持平台：Web(WebView)   

功能：根据样式定位器查找元素，如果不存在返回undefined   

21.elementByCssSelectorOrNull(value)    

支持平台：Web(WebView)   

功能：根据样式定位器查找元素，如果不存在返回null  

22.elementById(value)   

支持平台：Android iOS Web(WebView)   

功能：根据元素id查找元素   

23.elementByIdIfExists(value)   

支持平台：Android iOS Web(WebView)   

功能：根据元素id查找元素，如果不存在返回undefined  

24.elementByIdOrNull(value)     

支持平台：Android iOS Web(WebView)     

功能：根据元素id查找元素，如果不存在返回null   

25.elementByLinkText(value)          

支持平台：Android iOS Web(WebView)  

功能：根据链接的文本内容查找元素    

26.elementByLinkTextIfExists(value)     

支持平台：Android iOS Web(WebView)   

功能：根据链接的文本内容查找元素，如果不存在返回undefined  

27.elementByLinkTextOrNull(value) 

支持平台：Android iOS Web(WebView)     

功能：根据链接的文本内容查找元素，如果不存在返回null    

28.elementByName(value)     

支持平台：Android iOS Web(WebView)   

功能：根据文本内容属性查找内容     

29.elementByNameIfExists(value)       

支持平台：Android iOS Web(WebView)   

功能：根据文本内容属性查找内容，如果不存在返回undefined    

30.elementByNameOrNull(value)   

支持平台：Android iOS Web(WebView)   

功能：根据文本内容属性查找内容，如果不存在返回null     

31.elementByPartialLinkText(value)  

支持平台：Android iOS Web(WebView)   

功能：根据部分链接内容查找内容       

32.elementByPartialLinkTextIfExists(value)  

支持平台：Android iOS Web(WebView)    

功能：根据部分链接内容查找内容，如果不存在返回undefined    

33.elementByPartialLinkTextOrNull(value)    

支持平台：Android iOS Web(WebView)   

功能：根据部分链接内容查找内容，如果不存在返回null     

34.elementByTagName(value)  

支持平台：Android iOS Web(WebView)   

功能：根据标签名字获取元素   

35.elementByTagNameIfExists(value)  

支持平台：Android iOS Web(WebView)   

功能：根据标签名字获取元素，如果不存在，返回undefined     

36.elementByTagNameOrNull(value)    

支持平台：Android iOS Web(WebView)   

功能：根据标签名字获取元素，如果不存在，返回null      

37.elementByXPath(value)    

支持平台：Android iOS Web(WebView)   

功能：根据xpath路径获取元素    

38.elementByXPathIfExists(value)    

支持平台：Android iOS Web(WebView)   

功能：根据xpath路径获取元素，如果不存在，返回undefined  

39.elementByXPathOrNull(value) 

支持平台：Android iOS Web(WebView)   

功能：根据xpath路径获取元素，如果不存在，返回null   

40.elementIfExists(using, value)    

支持平台：Android iOS Web(WebView)   

功能：判断元素是否存在，using定位策略 value查找内容     

41.elementOrNull(using, value)      

支持平台：Android iOS Web(WebView)   

功能：

42.elements(using, value)   

支持平台：Android iOS Web(WebView)   

功能：根据给定策略查找元素列表     

43.elementsByClassName(value)   

支持平台： Android iOS Web(WebView)  

功能：根据样式名称查询元素数组      

44.elementsByCss(value) 

支持平台：Web(WebView)     

功能：根据样式查询元素数组   

45.elementsByCssSelector(value) 

支持平台：Web(WebView)   

功能：根据样式选择器查找元素数组    

46.elementsById(value)  

支持平台：Android iOS Web(WebView)   

功能：根据元素id查找元素数组     

47.elementsByLinkText(value)    

支持平台：Android iOS Web(WebView)   

功能：根据链接内容查询元素数组     

48.elementsByName(value)    

支持平台：Android iOS Web(WebView)   

功能：根据名字查找元素数组   

49.elementsByPartialLinkText(value) 

支持平台：Android iOS Web(WebView)   

功能：根据部分链接内容查找元素数组   

50.elementsByTagName(value)     

支持平台：Android iOS Web(WebView)   

功能：根据标签名字获取元素数组     

51.elementsByXPath(value) 

支持平台：Android iOS Web(WebView)   

功能：根据xpath获取元素数组    

52.execute(code, argsopt)   

支持平台：Web(WebView)   

功能：在当前选中的frame的上下文中注入JavaScript代码片段，code为脚本代码片段，argsopt为脚本参数(可选)     

53.forward()    

支持平台：Web(WebView)   

功能：如果可能，导航浏览器前进     

54.frame(frameRef)  

支持平台：Web(WebView)   

功能：将焦点转移到页面中的另外一个frame      

55.get(url)     

支持平台：Web(WebView)   

功能：打开url页面  

56.getComputedCss(propertyName)     

支持平台：Web(WebView)   

功能：Query the value of an element's computed CSS property.

57.getProperty(name)    

支持平台：Android iOS Web(WebView)   

功能：获取某个元素的属性值   
iOS: 'isVisible', 'isAccessible', 'isEnabled', 'type', 'label', 'name', 'value'         
Android: 'selected', 'description', 'text'      

58.getRect()    

支持平台：Android iOS    

功能：查询特定元素的像素和边界，返回值为一个包含x，y，height，width的对象     

59.getWindowSize(handleopt) 

支持平台：Android iOS Web(WebView)   

功能：获取特定窗体的大小,参数handleopt为window handle to set size for (optional, default: 'current')    

60.hasElement(using, value) 

支持平台：Android iOS Web(WebView)   

功能：检查元素是否存在，using定位策略，value查找内容     

61.hasElementByClassName(value) 

支持平台：Android iOS Web(WebView)   

功能：判断是否存在类名称为value的元素   

62.hasElementByCss(value)   

支持平台：Web(WebView)   

功能：判断是否存在样式为value的元素    

63.hasElementByCssSelector(value)   

支持平台：Web(WebView)   

功能：判断是否存在样式定位格式是value的元素    

64.hasElementById(value)    

支持平台：Android iOS Web(WebView)   

功能：判断是否存在id为value的元素    

65.hasElementByLinkText(value)  

支持平台：Android iOS Web(WebView)   

功能：判断是否存在链接内容为value的元素    

66.hasElementByName(value)  

支持平台：Android iOS Web(WebView)   

功能：判断是否存在名称是value的元素    

67.hasElementByPartialLinkText(value) 

支持平台：Android iOS Web(WebView)   

功能：判断是否存在链接内容中包含value的元素    

68.hasElementByTagName(value)   

支持平台：Android iOS Web(WebView)   

功能：判断是否存在标签名称为value的元素  

69.hasElementByXPath(value)     

支持平台：Android iOS Web(WebView)   

功能：判断是否存在xpath为value的元素     

70.init(desired)    

支持平台：Android iOS Web(WebView)   

功能：初始化驱动程序，创建session    

71.isDisplayed()    

支持平台：Android Web(WebView)   

功能：判断某个元素是否显示存在     

72.keys(keys)   

支持平台： Android Web(WebView)  

功能：往当前焦点所在的元素发送键盘事件     

73.maximize(handle)     

支持平台：Web(WebView)   

功能：handle如果没有最大化，则最大化指定的handle窗体    

74.quit()   

支持平台：Android iOS Web(WebView)   

功能：删除session    

75.refresh()    

支持平台：Web(WebView)   

功能：刷新当前页面   

76.saveScreenshot(filepath)     

支持平台：Android iOS Web(WebView)  

功能：保存当前页面的截图信息到指定的filepath路径下   

77.sendKeys(keys)   

支持平台：Android iOS Web(WebView)   

功能：发送键盘事件到当前激活焦点的元素     

78.sessions()   

支持平台：Android iOS Web(WebView)   

功能：返回当前有效的所有session列表   

79.setWindowSize(width, height, handleopt)  

支持平台：Web(WebView)   

功能：修改指定窗体的大小 handleopt为window handle to set size for (optional, default: 'current')
 

80.sleep(ms)    

支持平台： Android iOS Web(WebView)  

功能：设置driver的等待时间，单位ms       

81.source() 

支持平台：Android iOS Web(WebView)   

功能：获取当前页面的source文件  

82.status() 

支持平台：Android iOS Web(WebView)   

功能：获取server的当前状态    

83.takeScreenshot() 

支持平台： Android iOS Web(WebView)  

功能：获取当前页面的截图，返回的是base64字节码，需要自己处理保存   

84.text() 

支持平台：Android iOS Web(WebView)   

功能：返回元素上能看到的文本内容    

85.title()  

支持平台：Web(WebView)   

功能：获取当前页面的标题    

86.touch(action, objectopt)     

支持平台：iOS, Android   

功能：在设备上应用触摸事件   

action动作，支持tap/doubleTap/press/pinch/rotate/drag    

object，可选参数，指定action的参数     
比如driver.touch('doubleTap', {x: 100, y: 100});


87.url()    

支持平台：Web(WebView)   

功能：获取当前页面的url链接地址   

88.waitForElement(using, value, asserteropt, timeoutopt, intervalopt)   

支持平台： Android iOS Web(WebView)  

功能：
All the element-related methods above (except which suffixed with OrNull, IfExists) could be prefixed with the "waitFor-" (need to capitalize the 'e', e.g., waitForElementByClassName)

using   string          The locator strategy to use, omitted when using specific method like waitForElementByClassName.
value   string          The css selector
asserter    function    <optional>
The asserter function (commonly used asserter function can be found at wd.asserters) (optional)
timeout number  <optional>
1000ms  The timeout before find the element (optional)
interval    number  <optional>
200ms   The interval between each searching (optional)

例子：
waitForElementByClassName('btn', 2000, 100) Search for element which class name is 'btn' at intervals of 100ms, last for 2000ms.


89.window() 

支持平台： Web(WebView)  

功能：修改焦点到另外的窗体   

90.windowHandle()   

支持平台：Web(WebView)   

功能：获取当前窗体句柄     

91.windowHandles()  

支持平台：Web(WebView)   

功能：获取当前session中有效的窗体句柄  




