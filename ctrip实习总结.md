### 1. 首页改版
关键词：bootstrap nav-tab iframe AdminLTE框架 导航树tree

工作内容：中间自己没有使用框架的时候，曾自己构造tree.

面试回答：改版easyUI的首页，左侧菜单栏tree结构，右侧内容页，类似网易邮箱那种，点击左侧菜单栏，右侧加载一个页面（添加一个nav-tab和tab-content），页面折叠排列，包含nav-tab,tab-content，页面放在iframe中，页面加载进来的高度调整为适应大小。

遇到难点：1. content-warpper中的content中iframe的高度问题，既页面载入大小问题。2. 点击左ul>li>a时候，右边反复添加。

解决方法：1.根据iframe的高度是否小于右侧content-wrapper的高度减去右侧tab-content距离页面底部的高度来触发$(window).resize()方法调整页面高度。
         2.当tab中的不存在时候，再调用addHomeTabs函数添加。

右侧结构：
```html
<div class="content-warpper">
 <section class content-header>
   <ul class="nav nav-tab>
     <li></li>
   </ul>
 </section>
 <section content tab-content>
   <div><iframe></iframe></div>
 </section>
</div>
```

代码1：
```javascript
        $(window).resize(function () {
            if ($('iframe').height() < ($('.content-wrapper').height() - $('iframe').closest('section')[0].offsetTop))
                $('iframe').height($('.content-wrapper').height() - $('iframe').closest('section')[0].offsetTop)
        });
```
代码2：
```javascript
 if ($('#homeTabs a:contains(" + $(this).text() + ")').length == 0) {
                    var targetUri = $(this).attr("href");

                    addHomeTabs($(this).text(), targetUri);
                }
```
### 2. 智库实现
关键词：modal模拟框 bootstrap-tagsinput制作标签 bootstrap-table ajax 分页（前端分页，一次请求全部数据）tooltips 弹出框popover

工作内容：中间自己摸索一段时间

面试回答：做的是一个用来记录项目组的成员记录公告或存放文件的页面，类似公告一样，主要是bootstrap-table布局展示，作者，日期，事项，标签啦，附件啦等等
         ，可以增删改查。

遇到难点：1.修改的时候，有附件则带不过去，就是modal中，只能给附件处一个提示，提交则覆盖。

解决方法：暂未解决。

代码：
```javascript
    // 附件列格式化
    function attachmentFormatter(value, row, index) {
        var a = '';
        var arr = ["jpg", "jpeg", "png", "gif", "bmp"];
        if (value != null && value != '') {
            if (arr.toString().indexOf(value.split(".")[1]) > -1) {
                a = ['<a href="../docUtil/download-document-billboard-' + row['docId'] + '" title="' + value + '">'
                    ,'<i class="glyphicon glyphicon-picture" style = "font-size:30px;"></i>',
                    '</a>'].join('');
            } else {
                a = ['<a class="file" href="../docUtil/download-document-billboard-' + row['docId'] + '" title="' + value + '">'
                    ,'<i class="glyphicon glyphicon-file" style = "font-size:30px;"></i>',
                    '</a>'].join('');
            }
        }
        return a;
    }
```

### 3. 大事件时间轴实现
关键词：做的最久、AdminLTE框架的timeline样式、bootstrap-tokenfield（搜索提示tokenfield）、bootstrap-datetimepicker、ajax、

工作内容：form提交到iframe

面试回答：见下文:插入刷新之后，我做的如何找到插入的那个元素，分两种一种分页一种不分页。**刷新之后，滚动到插入的那个元素：定义一个scrollTo($el,speed)函数**

```javascript
// 滚动函数
function scrollTo(ele, speed) {
	if (!speed)
		speed = 300;
	if (!ele) {
		$("html,body").animate({
			scrollTop : 0
		}, speed);
	} else {
		if (ele.length > 0)
			$("html,body").animate({
				scrollTop : $(ele).offset().top
			}, speed);
	}
	return false;
}
```

不分页：把刷新前的日期存入到localStorage中，一次获取所有的timelabel存入localStorage中
```javascript
// getData加载全部数据函数
function getData() {
	isAjax = true;
	$.ajax({
		type : 'GET',
		url : '../queryPage',
		data : {
			text : 'select * from app_timeline order by date desc ;'
		},
		success : function(response) {
			console.log(response);
			var len = response.length;
			appendTimelineItem(response);
			$("i[name=0]").addClass("hide");
			showNoMore();
			localStorage.setItem("timelabel", JSON.stringify(timelabelMap));
			// 滚动到插入元素的位置
			searchAfterSave();
			console.info(timelabelMap);
			console.log("getData Success !");
		},
		error : function(xhr, type) {
			alert("getData Error !");
		}
	});
}
```
```javascript
// 找到滚动到改变的元素位置
function searchAfterSave() {
	var date = localStorage.getItem("date");
	if (date != null && date != "") {
		scrollTo($("li.time-label span:contains(" + date + ")"), 1000);
		localStorage.removeItem("date");
		localStorage.removeItem("timelabel");
	}
}
```
不分页的就复杂很多，有递归出现：也是刷新前存入到localStorage中,同时为了防止触动或者要触动scroll滚动加载函数，需要定义一个isSearch来控制变量正常。
```javascript
// getData加载一页数据函数
function getData(offset, size) {
	isAjax = true;
	$.ajax({
		type : 'GET',
		url : '../queryPage',
		data : {
			text : 'select * from app_timeline order by date desc limit '
					+ offset + ',' + size + ';'
		},
		success : function(response) {
			console.log(response);
			isAjax = false;
			$("#loading").addClass("hide");
			var len = response.length;
			appendTimelineItem(response);
			$("i[name=0]").addClass("hide");
			localStorage.setItem("timelabel2", JSON.stringify(timelabelMap));
			searchAfterSave2();
			console.info(timelabelMap);
			if (len < size) {
			isEnd = true;
			showNoMore();
			}
			console.log("getData Success !");
		},
		error : function(xhr, type) {
			alert("getData Error !");
		}
	});
}
```
```javascript
// 找到滚动到改变的元素位置
var isSearch = false;// 是否是保存才触发的
function searchAfterSave2() {
	var date = localStorage.getItem("date2");
	var timelabel = localStorage.getItem("timelabel2");
	if (date != null && date != "") {
		if (timelabel.indexOf(date) == -1) {
			isSearch = true;
			getData(pageSize * (++counter), pageSize);
		} else {
			scrollTo($("li.time-label span:contains(" + date + ")"), 1000);
			localStorage.removeItem("date2");
			localStorage.removeItem("timelabel2");
			isSearch = false;
			return;
		}
	}
}
```
```javascript
	// 滚动下拉监听
	$(window).scroll(function() {
				console.log("isEnd:" + isEnd + " " + "isAjax:" + isAjax);
				if (isEnd == true || isAjax == true) {
					return;
				}
				// 当滚动到页面底部，则加载下一页
				if ($(document).height() - $(this).scrollTop()
						- $(this).height() < 100) {
							if (!isSearch && !$("#noMore").length
									&& param.length == 0) {
						$("#loading").removeClass("hide");
						delayTime(function() {
							counter++;
							pageStart = counter * pageSize;
							getData(pageStart, pageSize);
						}, 1000);
					}
					}
			});
```

遇到难点和解决方法：

      1.插入时候的位置如何处理排序。timelabel排序，寻找插入位置，根据不同的插入位置，不同的插入顺序。
      
      2.分页加载。一次加载5条，服务器端分页，limit offset,size;
      
      3.延迟加载。写个延迟加载的函数，delaytime(fn,time)
      
      4.新增图片上传的问题和图片预览。未解决。document.getElementById("file").files:API返回一个对象files包含很多属性。
      
      5.搜索提示。bootstrap-tokenfield
      
      6.不同事件插入旗帜标记。flag=0||1,根据i的name属性值来显示或者不显示
      
      7.有重要性的事件下timelabel要变色标记。
      
      8.分类的多标签实现。
      
代码：

1.延迟加载的函数
```javascript
// 延迟执行函数
var timeid=null;
function delayTime(fn, wait) {
	// 清除定时器
	timeid && clearTimeout(timeid);
	// 定时器执行
	timeid = setTimeout(fn, wait);
}
```
2.图片预览
```javascript
// 多图片上传预览功能
function setImagePreviews() {
	var input = document.getElementById("file");
	var result = document.getElementById("result");
	result.innerHTML = "";
	var fileList = input.files;
	for (var i = 0; i < fileList.length; i++) {
		result.innerHTML += "<div class='left'><img id='img" + i
				+ "' alt='" + fileList[i].name + "'/></div>";
		var imgObjPreview = document.getElementById("img" + i);
		// image/png or image/jpg
		console.log(fileList[i].type);
		if (fileList && fileList[i]) {
			// 控制上传类型必须是图片
			if (!(/image\/\w+/).test(fileList[i].type)) {
				alert("上传文件类型必须为图片！请重新选择上传文件。");
				input.value = "";
				$("#result").html("");
				return false;
			} else {
				// 火狐谷歌下，直接设img属性
				imgObjPreview.style.display = 'block';
				imgObjPreview.style.width = '150px';
				imgObjPreview.style.height = '180px';
				// createObjectURL 性能比FileReader效果好，决定用createObjectURL
				// 兼容Safari和Chrome
				imgObjPreview.src = window[window.webkitURL ? 'webkitURL'
						: 'URL'].createObjectURL(fileList[i]);
				imgObjPreview.onload = function(e) {
					window.URL.revokeObjectURL(fileList[i]);
				}
			}
		} else {
			// IE下，使用滤镜
			// 测试因IE9（包含IE9）以下浏览器不包含files，所以不兼容会报错，需IE9以上版本
			input.select();
			var imgSrc = document.selection.createRange().text;
			alert(imgSrc);
			var localImagId = document.getElementById("img" + i);
			// 必须设置初始大小
			localImagId.style.width = "150px";
			localImagId.style.height = "180px";
			// 图片异常的捕捉，防止用户修改后缀来伪造图片
			try {
				localImagId.style.filter = "progid:DXImageTransform.Microsoft.AlphaImageLoader(sizingMethod=scale)";
				localImagId.filters
						.item("DXImageTransform.Microsoft.AlphaImageLoader").src = imgSrc;
			} catch (e) {
				alert("您上传的图片格式不正确，请重新选择!");
				return false;
			}
			imgObjPreview.style.display = 'none';
			document.selection.empty();
		}
	}
	return true;
}
```
3.背景色和追加项
```javascript
// 追加timeline项函数
function appendTimelineItem(array) {
	// 存储timelabel背景色
	var bgColor = [ "bg-green", "bg-red-active" ];
	var len = array.length;
	for (var i = 0; i < len; i++) {
		var day = array[i].date.split(" ")[0];
		array[i]['bgColorIndex'] = array[i].flag;
		for (var j = i + 1; j < len; j++) {
			var nextDay = array[j].date.split(" ")[0];
			if ((day == nextDay) && (array[i].flag == 1 || array[j].flag == 1)) {
				array[i]['bgColorIndex'] = 1;
			}
		}
		//console.log(i, "bgColorIndex", array[i]['bgColorIndex']);
	}
	for (var i = 0, len = array.length; i < len; i++) {
		var day = array[i].date.split(" ")[0];
		var timelineItemTmp = tmpl(array[i]);
		if (isExistInArray(timelabelMap, day)) {
			// 根据date找到要插入到的time-label下标签
			var dateTag = getTagByContent($(".time-label span"), day);
			$(dateTag).parent().after(timelineItemTmp);
		} else {
			timelabelMap.push(day);
			timelabelMap.sort().reverse();
			var timelabelTmp = '<li class="time-label"><span class='
					+ bgColor[array[i]['bgColorIndex']] + '>' + day + '</span></li>';
			$(".timeline").append(timelabelTmp).append(timelineItemTmp);
		}
	}
}
```
4.多标签的初始化渲染
```javascript
// subject标签组格式化函数
var btnarray = [ "primary", "success", "warning", "danger" ];
function subjectTagFormatter(value) {
	var btntags = [];
	if (value != null && value != '') {
		var items = value.split(',');
		for (var i = 0, len = items.length; i < len; i++) {
			var btnid = (i + 1) % btnarray.length;
			if (btnid == 0) {
				btnid = btnarray.length - 1;
			} else {
				btnid = btnid - 1;
			}
			btntags[i] = '<button type="button" onclick="btnClickHandler(this.innerHTML)" class="tagbtn btn btn-'
					+ btnarray[btnid] + ' btn-xs">' + items[i] + '</button>';
		}
	}
	return btntags.join(" ");
}
```

### 4. 集团断美、竞争圈、城市转化率
所用技术：ajax查询、bootstrap-table、Highchart、bootstrap-select、baiduMap、

工作内容：Highchart根据请求数据，配置并不难。

面试回答：

遇到难点和解决方法：

