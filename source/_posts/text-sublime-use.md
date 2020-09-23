---
title: Sublime text使用
date: 2015-08-07 09:20:27
updated: 2015-08-07 09:21:28
tags: 
- js
- grunt
categories: 
- js

---
### Package Control 安装方法

- 按Ctrl+`调出console
- 粘贴以下代码到底部命令行并回车：
- 重启Sublime Text 3。
- 如果在Perferences->package settings中看到package control这一项，则安装成功。
```python
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```


<!--more-->


Sublime Text2的命令
```python
import urllib2,os; pf='Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler( ))); open( os.path.join( ipp, pf), 'wb' ).write( urllib2.urlopen( 'http://sublime.wbond.net/' +pf.replace( ' ','%20' )).read()); print( 'Please restart Sublime Text to finish installation')
```

### 用Package Control安装插件的方法：
- 按下Ctrl+Shift+P调出命令面板
- 输入install 调出 Install Package 选项并回车，然后在列表中选中要安装的插件。

### 插件推荐

- Emmet
- Angular语法补全
- HTML/CSS/JavaScript Prettify (本地需要安装nodejs)

### 常用快捷键

| 快捷键 |  功能 |
| ------ | ----- |
| Ctrl+L | 选择整行(按住-继续选择下行) |
| Ctrl+KK| 从光标处删除至行尾 |
| Ctrl+Shift+K | 删除整行 |
| Ctrl+Shift+D | 复制光标所在整行，插入在该行之前 |
| Ctrl+J | 合并行(已选择需要合并的多行时) |
| Ctrl+KU | 改为大写 |
| Ctrl+KL | 改为小写 |
| Ctrl+D  | 选词(按住-继续选择下个相同的字符串) |
| Ctrl+M  | 光标移动至括号内开始或结束的位置 |
| Ctrl+Shift+M | 选择括号内的内容(按住-继续选择父括号) |
| Ctrl+/ | 注释整行(如已选择内容，同“Ctrl+Shift+/”效果) |
| Ctrl+Shift+/ | 注释已选择内容 |
| Ctrl+Space | 自动完成(win与系统快捷键冲突，需修改) |
| Ctrl+Z | 撤销 |
| Ctrl+Y | 恢复撤销 |
| Ctrl+Shift+V | 粘贴并自动缩进(其它兄弟写的，实测win系统自动缩进无效) |
| Ctrl+M | 光标跳至对应的括号|
| Alt+. | 闭合当前标签 |
| Ctrl+Shift+A | 选择光标位置父标签对儿 |
| Ctrl+Shift+[ | 折叠代码 |
| Ctrl+Shift+] | 展开代码 |
| Ctrl+KT | 折叠属性 |
| Ctrl+K0 | 展开所有 |
| Ctrl+U | 软撤销 |
| Ctrl+T | 词互换 |
| Ctrl+Enter | 插入行后 |
| Ctrl+Shift+Enter | 插入行前 |
| Ctrl+K+Backspace | 从光标处删除至行首 |
| Ctrl+Shift+UP | 与上行互换 |
| Ctrl+Shift+DOWN | 与下行互换 |
| Shift+Tab | 去除缩进 |
| Tab | 缩进 |
| F9 | 行排序(按a-z) |

### jquery mobile

### Introduction

- jQuery Mobile is a set of jQuery plug-ins and widgets that aim to
provide a cross platform API for creating mobile web applications
- HTML 5 and CSS3 based light-weight sized framework
- First announced as an independent project on August 11,2010 and
the latest version is 1.3.1
- jQuery Mobile is built on top of jQuery Core, it can work on jQuery
1.7.0 to 1.9.1.
- Official Website for jQuery Mobile : http://jquerymobile.com/

### Pages, Header and Footer

- Page is the basic element of jQuery Mobile.
	- Use ```<div data-role="page">``` to define a Page
	- One HTML file must contain at least one Page
	- One HTML file can contain multiple Pages
	- If there are multiple pages in one html file, the first Page will be the main page. Main Page will be displayed when the html file is accessed.
	- To access the other pages except main Page, a anchor should be used. IE: page.html#pageID
	- Page can be fullscreen with the property of data-fullscreen="true"
- Header is the first part of a Page, it is located at top of the Page.
	- Header can be used to show the title of the Page
	- Header can have buttons
	- Header can be fixed in the top of screen with data-position="fixed"
- Footer is the last part of a Page, it is located at the bottom of the Page.
	- Footer can be fixed in the bottom of screen with data-position="fixed"
	- Messages can be used like what Header does
	- Buttons can be placed in Footer
	- Navigate bar can be placed in Footer

### Form

- To submit data from client to server, form and form fields are needed.
- jQuery mobile wraps html controls for mobile device, which makes form fields better look&feel and easy to use.
- Form fields are automatically enhanced by default in jQuery mobile application.
- Form submits form data via Ajax in jQuery mobile application.
- Form fields contains : text, radio, select, checkbox, slider, button, etc.
- Form - fieldcontain
	- jQuery Mobile use fieldcontain to align the label and form field
	- Each fieldcontain requies one label and one form field
- Form – Text Fields
	- Text fields are controls for user to type words

- Form – Radio
	- Radio is the control that allows user to select one item among some options.
	- A control group is needed to put options in one group
	- There are two forms of Radio in jQuery Mobile: horizontal and vertical. Vertical radio has a radio button with it

- Form - Select
    - Select menu provides a list for user to select value(s)

- Form – Checkbox
    - Checkbox is the control that allows user to select multiple items among some options.
    - A control group is needed to put options in one group.
    - There are two forms of Checkbox in jQuery Mobile: horizontal and vertical. Vertical checkbox has a checkbox button with it.

- Form - Slider
	- Slide is a control like a switch, user can select one value from two options

- Form - Range
	- jQuery provides Range to let user select one value between a given range
	- User can either change the value in text field directly or change the slider

- Form - Button
	- A button can be created either using a link or a html form button

- Form – Native and Mini Styles
	- To avoid jQuery Mobile enhance the controls and use native look and feel, data-role should be set to none.
	- jQuery Mobile provides a style named “mini”. This is a small control than normal ones. The font size and icon size are both smaller. To use mini style, set data-mini=“true” for the control.

### List View 

- List view is the control that shows a list of content
- Users can select the one item from the list view
- jQuery Mobile enhances ```<ul><li>``` tags to render a list view
- List View – Inset List
    - To create an inset list, the property data-inset should be set to “true”
- List View – Row Selection
    - A link can be added to the list view. When user selects the row, the link will be triggered.
- List View – Sub List View (http://demos.jquerymobile.com/1.4.5/listview-nested-lists/)
    - Nested ```<ul><li>``` can help to create sub list view.
- List View - Icon
    - Item can change the icon with data-icon property.
- List View – Split Button
	- A split button with icon can be added to a row with a second ```<a>``` tag
	- With split button, the item and the button can have different links
	- data-split-icon for ```<ul>``` defines the default icon for split button
	- data-icon for ```<li>``` defines the icon for one row
- List View – Filter
	- The filter is provided to user for search items with input
	- When property data-filter is set to true, a filter will be created for a list view
- List View – Count Bubble
	- Count bubble is a badge to show some number
	- A span with ui-li-count css class can create count bubble
- List View - Number Index
	- ```<ol>``` tag will generate a number index for list items

### Collapsible Blocks

- Collapsible blocks are the blocks that can collapse and expand content within the blocks.
- Property data-role=“collapsible” creates collapsible blocks.
- Collapsible Blocks – Look and Feel
	- data-iconpos defines the position of icon
	- data-content-theme use the predefined theme for collapsible blocks
	- data-collapsed determines whether the initial block is collapsed or not

### Slide Panel

- Panel is a section that normally hides from the main screen. When it is activated, it will display on the screen
- Panel must be defined inside a page
- data-role=“panel” will create a panel

#### Popup

- data-role=“popup” defines a popup window
- data-rel=“popup” for button makes it open a popup window
- A popup will disappear once user taps outside the area of popup window

### Theme

- jQuery Mobile predefined 5 different themes from a to e
- data-theme chooses the theme for widgets

#### Icon

- A set of icons is predefined by jQuery Mobile
- Use data-icon to assign an icon
- data-iconpos defines the position of an icon

### Ajax Page Navigation

- jQuery Mobile uses Ajax to load internal links automatically.
- With Ajax, internal pages will be loaded and added to current DOM.
- With Ajax, the loading is asynchronous and only the particular part will be loaded. Thus it will be fast and quick response.
- With Ajax, all the changes occur on the same page, jQuery Mobile uses URL hashes to identify different pages, so that it can go back and forward in history stack.

### Transition Animation

- When a link has a property of data-transition, it can set the animation for how the page will be change to another.
- Options for data-transition: slide,slideup,slidedown,pop ,fade ,flip ,flow

### Configuration 

- jQuery Mobile can be configured via JavaScript
- The configuration must be loaded before jQuery Mobile is initialized
- To configure jQuery Mobile, event “mobileinit” should be bind to a function, and do the configuration in that function

```javascript
// change mobile configuration
$(document).bind("mobileinit", function() {
    $.mobile.property=“propertyValue";
});
```

| name | use  | 
| ---- | ---- |
|ns | Namespace for data attribute |
|activeBtnClass | css class for buttons in active status |
|activePageClass | css class for pages currently being reviewed |
|ajaxEnabled | Whether to use ajax for page loading and form submission or not |
|allowCrossDomainPages| A security setting for load remote pages |
|autoInitializePage |Initialize page on load or not |
|defaultDialogTransition |How to show dialogs |
|defaultPageTransition |How to show pages |
|gradea | Determine whether it is a “good” browser |
|hasListeningEnabled | Listen to the change of location.hash property or not |
|ignoreContentEnabled | Disable jQuery Mobile auto enhancement |
|linkBindingEnabled | Listen to the link click or not |
|loadingMessage| The message for loading|
|loadingMessageTextVisible| Show the loading message or not|
|loadingMessageTheme| The theme for page loading dialog|
|minScrollBack| Remember the last position of page scroll|
|pageLoadErrorMessage| The message for loading error|
|pageLoadErrorMessageTheme| Show the loading error message or not|
|pushStateEnabled| Use html5 pushState function or hash based|
|changed| for page navigation|
|subPageUrlKey |Customize the prefix for page url|

### Utilities - Page

- $.mobile.activePage : current page
- $.mobile.changePage(page,options): switch to another page
- $.mobile.showPageLoadingMsg(): show loading message
- $.mobile.hidePageLoadingMsg(): hide loading message
- $.mobile.path.isAbsoluteUrl: check for absolute url
- $.mobile.path.isRelativeUrl: check for relative url
- $.mobile.path.isSameDomain(first url, second url) : check for domain
- $.mobile.path.makePathAbsolute(relative path, absolute path): convert relative path to absolute path
- $.mobile.path.makeUrlAbsolute(relative url, absolute url): convert relative url to absolute url
- $.mobile.path.parseUrl(url): convert an url to hash, host, hostname, href, pathname,port, protocol, and searc
- $.mobile.fixedToolbars.hide(): hide the toolbar
- $.mobile.fixedToolbars.show(): show the toolbar
- $.mobile.silentScroll(position): scroll the page to a given y-position, this will not trigger any scroll event listeners.