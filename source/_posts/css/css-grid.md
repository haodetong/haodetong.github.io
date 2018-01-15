---
title: 网格布局

date: 2017-09-14 10:43:24

tags: css-grid

category: 转载

keywords: css-grid

description: css-grid

---

原文地址：[CSS Grid布局指南](http://www.w3cplus.com/css3/a-complete-guide-css-grid-layout.html)

英文出处：[A Complete Guide to CSS Grid Layout](http://chris.house/blog/a-complete-guide-css-grid-layout/)

#### 简介

CSS Grid布局 （又名”网格”），是一个基于二维网格布局的系统，主要目的是改变我们基于网格设计的用户接口方式。如我们所知，CSS 总是用于网页的样式设置，但它并没有起到很好的作用。刚开始的时候我们使用表格(table)，然后使用浮动(float)、 定位(position)和内联块(inline-block)，但所有这些方法本质上来讲都是hacks，存留了很多需要实现的重要功能问题(例如，[垂直居中](http://www.w3cplus.com/blog/tags/357.html))。虽然[Flexbox](http://www.w3cplus.com/blog/tags/157.html)可以起到一定的补救作用，但是它只可以实现简单的一维布局，并不适用于复杂的二维布局(实际上 Flexbox 和 Grid 可以一起结合使用起到最佳效果)。网格是 CSS 第一次专门创建的模块，用来解决我们之前在制作网站时使用hacks处理布局问题。

这里有两件事情启发我创建本指南。第一个是 Rachel Andrew 的令人敬畏的书–[为 CSS Grid 布局做好准备](http://abookapart.com/products/get-ready-for-css-grid-layout)。这本书很详尽明确的的介绍了Grid，如果你想很好的掌握Grid的基础知识，我强烈建议你去购买。另外一个很大的灵感来自于 Chris Coyier 的– [Flexbox完整指南](http://www.w3cplus.com/css3/a-guide-to-flexbox-new.html)，这本书是我了解Flebox的一个很优秀的资源。这里，我还想补充一句，当你使用谷歌搜索”flexbox”时，会出现很多类似的资源，但是为什么不直接利用最好的资源呢？

我书写此指南的目的是基于目前最新版本，规范其网格概念。所以我不会再次提及过时的 IE 语法，并且随着规范的成熟，我会尽力定期更新此指南。

#### 基础知识与浏览器支持

Grid 的入门是很容易的。你只需要定义一个容器元素并设置display：grid，使用grid-template-columns 和 grid-template-rows属性设置网格的列与 行的大小，然后使用grid-column 和 grid-row属性将其子元素放入网格之中。与flexbox类似，网格项的源顺序无关紧要。为了更好地使你的网格与媒体查询相结合使用，你可以在 CSS 中任意放置。想象一下你定义的整个页面布局，然后如果想要完全重新布局以适应不同的屏幕宽度，这时仅仅使用几行 CSS 代码就可以实现。Grid是曾经介绍过的最强大 CSS 模块之一。

关于 Grid 一件很重要的事情就是它现在还不适用于项目使用。目前还处于 [W3C 的工作草案](https://www.w3.org/TR/css-grid-1/)之中，并且默认情况下，还不被所有的浏览器所支持。Internet Explorer 10 和 11 已经可以实现支持，但也是利用一种过时的语法实现的。现在出于示例演示，我建议你使用启用了特殊标志的 Chrome, Opera 或者 Firefox 。在 Chrome，导航到chrome://flags 并启用” web 实验平台功能”。该方法同样适用于 Opera (opera://flags)。在Firefox中，启用 layout.css.grid.enabled 标志。

<iframe src="http://caniuse.com/css-grid/embed" frameborder="0" width="100%" height="413px"></iframe>

除了Microsoft，浏览器厂商似乎想要等到Grid规范成熟后再加以推广。这是一件好事，因为这意味着我们就不需要担心学习多个语法。

等待 Grid 的使用，只是时间的问题。但是现在你需要开始学习它了。

#### 重要术语

在深入研究Grid之前，我们需要理解其相关术语概念。因为这里涉及到的术语在概念上都有点类似，如果你没有首先记住Grid规范中的相关定义，你就会很容易将其与另一个概念相混淆。但是不需要担心，这里的属性并不是很多。

网格容器(Grid Container)

当一个元素设置display: grid属性时，它就会成为所有网格项(Grid Items)的父元素。在下面的示例中，container就是网格容器。

	<div class="container">
	    <div class="item item-1"></div>
	    <div class="item item-2"></div>
	    <div class="item item-3"></div>
	</div>

#### 网格项(Grid Item)

网格容器的孩子(e.g. 子元素)。这里item元素都是网格项，但是sub-item不包含其中。

	<div class="container">
	    <div class="item"></div> 
	    <div class="item">
	        <p class="sub-item"></p>
	    </div>
	    <div class="item"></div>
	</div>

#### 网格线(Grid Line)

分界线构成了网格的结构。他们可以是垂直的(“列网格线”)也可以是水平的(“行网格线”)，并且存在于一行或一列的任一侧。下面图片中的黄线就是列网格线的一个例子。

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/gKpvc25w95A-Rw4rzvxuvooFq21vgQLTMvLHI9d9I9k/mtime:1460132248/sites/default/files/blogs/2016/1604/grid-line.png)

<center>Grid布局指南</center>

#### 网格轨道(Grid Track)

两个相邻网格线之间的空间。你可以把它们想像成网格的行或列。下图所示的是第二行和第三行网格线之间的网格轨道。

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/gOiQ2Q-w5thxu5hsyw5c9UiGQq7RmnTs-qNKEfnSQxI/mtime:1460132250/sites/default/files/blogs/2016/1604/grid-track.png)

<center>Grid布局指南</center>

#### 网格单元格(Grid Cell)

两个相邻的行和两个相邻的列之间的网格线空间。它是网格的一个”单位”。下面图片所示的是行网格线 1 和 2 与列网格线 2 和 3 之间的网格单元格。

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/seD6LEaaFyvVwPATSrWehDkUrCdsHApObOg12OlXNm4/mtime:1460132245/sites/default/files/blogs/2016/1604/grid-cell.png)

<center>Grid布局指南</center>

#### 网格区域(Grid Area)

四条网格线所包围的所有空间。网格区域可由任意数量的网格单元格组成。下面图片所示的是行网格线 1 和 3 和列网格线 1 和 3 之间的网格区域。

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/t4WyvkNvPqgPU-Jly_aF7ZGgDaM6AxgAedTXkfx4L74/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-area.png)

<center>Grid布局指南</center>

#### 网格容器属性(Grid Container)

##### display

定义一个元素成为网格容器，并对其内容建立一个网格格式的上下文。

##### 属性值:


* grid: 产生一个块级的网格

* inline-grid: 产生内联级网格

		.container{
		    display: grid | inline-grid   
		}

注: column, float, clear, 和 vertical-align 元素对网格容器不起作用。

#### grid-template-rows

利用以空格分隔的值定义网格的列和行。值的大小代表轨道的大小，并且它们之间的空格表示网格线。

##### 属性值:


* <track-size>: 可以是一个长度、百分比或者是网格中自由空间的一小部分(使用fr单位)

* <line-name>: 你选择的任意名称

* subgrid - 如果你的网格容器本身就是一个网格项(即嵌套网格)，你可以使用此属性指定行和列的大小继承于父元素而不是自身指定。

		.container{
		    grid-template-columns: <track-size> ... | <line-name> <track-size> ... | subgrid;
		    grid-template-rows: <track-size> ... | <line-name> <track-size> ... | subgrid;
		}

#### 示例:

当你在值之间留有空格时，网络线就会自动分配数值名称:

	.container{
	    grid-template-columns: 40px 50px auto 50px 40px;
	    grid-template-rows: 25% 100px auto;
	}

![Grid布局指南](http://cdn2.w3cplus.com/cdn/farfuture/X_DM7Y2QqrzK1H9w41DkpyGLmZeMkaq5pbyVZevlgBg/mtime:1460132249/sites/default/files/blogs/2016/1604/grid-numbers.png)

<center>Grid布局指南</center>

但是你也可以显示命名，请参考下面括号语法中的名称命名方式:

	.container{
	    grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
	    grid-template-rows: [row1-start] 25% [row1-end] 100% [third-line] auto [last-line];
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/TeK_YrqYf9wK7TYBGo00d8vzqb0ErZkCLNVZrRWSRdM/mtime:1460132249/sites/default/files/blogs/2016/1604/grid-names.png)

<center>Grid布局指南</center>

请注意，一条网格线可以具有有多个名称。例如，这里的第二行将有两个名字: row1-end 和 row2-start:

	.container{
	    grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
	}
	
如果你的定义中包含重复的部分，你可以使用 repeat() 表示法进行精简:

	.container{
	    grid-template-columns: repeat(3, 20px [col-start]) 5%;
	}
	
等效于:

	.container{
	    grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start] 5%;
	}
	
fr 单位允许你将一个轨道大小设置为网格容器内自由空间的一小部分。如下所示，每个网格项就会占据网格容器宽度的三分之一:

	.container{
	    grid-template-columns: 1fr 1fr 1fr;
	}

这里自由空间表示除去非弹性项以后剩余的空间。在此示例中的 fr 单位的可用空间表示减去50px以后的空间大小:

	.container{
	    grid-template-columns: 1fr 50px 1fr 1fr;
	}

#### grid-template-areas

使用grid-area属性定义网格区域名称，从而定义网格模板。网格区域重复的名称就会导致内容跨越这些单元格。句点表示一个空单元格。语法本身提供了一种可视化的网格结构。

##### 属性值:


* <grid-area-name>: 使用grid-area属性定义网格区域名称

* .: 句点表示一个空单元格

* none: 无网格区域被定义

		.container{
		    grid-template-areas: "<grid-area-name> | . | none | ..."
		                      "..."
		}

##### 示例:

	.item-a{
	    grid-area: header;
	}
	.item-b{
	    grid-area: main;
	}
	.item-c{
	    grid-area: sidebar;
	}
	.item-d{
	    grid-area: footer;
	}
	.container{
	    grid-template-columns: 50px 50px 50px 50px;
	    grid-template-rows: auto;
	    grid-template-areas: "header header header header"
	                         "main main . sidebar"
	                         "footer footer footer footer"
	}

这将创建一个四列三行的网格。最上面的一行为header区域。中间一行由两个main区域，一个空单元格和一个sidebar区域。最后一行是footer区域。

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/p5O591xAPbyGPVI0pepIgQIQyo25GEzXJc4FqPnkCgI/mtime:1460132550/sites/default/files/blogs/2016/1604/grid-template-areas.png)

<center>Grid布局指南</center>

你所声明的每一行都需要具有相同数目的单元格。

你可以使用任意数量的句点(.)声明单个空单元格。只要句点之间没有空格就表示一个空单元格。

注意，你只是使用此语法进行网格区域命名，而不是网格线命名。当你使用此语法时，区域两边的线就会得到自动命名。如果网格区域名称为foo,则其行线和列线的名称就将为foo-start，最后一行线及其最后一列线的名字就会为foo-end。这意味着一些线就可能具有多个名称，如上面示例中所示，拥有三个名称: header-start, main-start, 以及footer-start。

#### grid-column-gap和grid-row-gap

指定网格线的大小。你可以把它想像成在行/列之间设置间距宽度。

##### 属性值:


* `<line-size>`: 一个长度值

```
.container{
    grid-column-gap: <line-size>;
    grid-row-gap: <line-size>;
}
```

##### 示例:

	.container{
	    grid-template-columns: 100px 50px 100px;
	    grid-template-rows: 80px auto 80px; 
	    grid-column-gap: 10px;
	    grid-row-gap: 15px;
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/NkH6E9Mt_yBnhLMcuOO2bvijzL-d1dr4PcHROe1jQTw/mtime:1460132245/sites/default/files/blogs/2016/1604/grid-column-row-gap.png)

<center>Grid布局指南</center>

间距仅仅在列/行之间产生，而不会在边缘区。

#### grid-gap

grid-column-gap 和 grid-row-gap的简写值。

##### 属性值:

* <grid-column-gap> <grid-row-gap>: 长度值

```
.container{
    grid-gap: <grid-column-gap> <grid-row-gap>;
}
```

##### 示例:

	.container{
	    grid-template-columns: 100px 50px 100px;
	    grid-template-rows: 80px auto 80px; 
	    grid-gap: 10px 15px;
	}
	
如果没有指定grid-row-gap属性的值，默认与grid-column-gap属性值相同

#### justify-items

沿列轴对齐网格项中的内容(相反于align-item属性定义的沿行轴对齐)。此值适用于容器内所有的网格项。

##### 属性值:

* start: 内容与网格区域的左端对齐

* end: 内容与网格区域的右端对齐

* center: 内容处于网格区域的中间位置

* stretch: 内容宽度占据整个网格区域空间(默认值)

```
.container{
    justify-items: start | end | center | stretch;
}
```
	
##### 示例:

	.container{
	    justify-items: start;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/DeEsJc9aJ197-ygaKH5umTPYax9LHpKqb6UNjOz87AE/mtime:1460132247/sites/default/files/blogs/2016/1604/grid-justify-items-start.png)

<center>Grid布局指南</center>

	.container{
	    justify-items: end;
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/Z3ZWMZKBOshRlLnyD_ezkqsI_MswMPWela1e_HJzAH8/mtime:1460132247/sites/default/files/blogs/2016/1604/grid-justify-items-end.png)

<center>Grid布局指南</center>

	.container{
	    justify-items: center;
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/VxU0G7HhPCZ9mXhfkuCGrXXjGsvV-JKGdNQ-OzSe6b8/mtime:1460132247/sites/default/files/blogs/2016/1604/grid-justify-items-center.png)

<center>Grid布局指南</center>

	.container{
	    justify-items: stretch;
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/k4EK27vIfTqh5nmhucLhomEhun0LTJqUckrTctj41Ag/mtime:1460132248/sites/default/files/blogs/2016/1604/grid-justify-items-stretch.png)

<center>Grid布局指南</center>

这也可以使用justify-self属性对各个网格项进行设置。

#### align-items

沿行轴对齐网格项中的内容(相反于justify-item属性定义的沿列轴对齐)。此值适用于容器内所有的网格项。

##### 属性值:

* start: 内容与网格区域的顶端对齐

* end: 内容与网格区域的底部对齐

* center: 内容处于网格区域的中间位置

* stretch: 内容高度占据整个网格区域空间(默认值)

```
.container{
    align-items: start | end | center | stretch;
}
```

##### 示例:

	.container{
	    align-items: start;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/SBt28c71z4UWwWCA6VMBJUPVUk5XLccB8-c2gB5RWlQ/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-align-items-start.png)

<center>Grid布局指南</center>

	.container{
	    align-items: end;
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/aVcTwfxWM3eKLHtrVECcY0PH-YrinUfDm4m3_eN5lXs/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-align-items-end.png)

<center>Grid布局指南</center>

	.container{
	    align-items: center;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/DYCXxtNsb_VN7Yz5qL8qDro-cRnENpRSl7irBEd3vTE/mtime:1460132243/sites/default/files/blogs/2016/1604/grid-align-items-center.png)

<center>Grid布局指南</center>

	.container{
	    align-items: stretch;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/fGHKY7-aeOfgpniLoTxQmsgCbLDRt1GiPaVXAl-4LOM/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-align-items-stretch.png)

<center>Grid布局指南</center>

这也可以使用align-self属性对各个网格项进行设置。

#### justify-content

当你使用px这种非响应式的单位对你的网格项进行大小设置时，就有可能出现一种情况–你的网格大小可能小于其网格容器的大小。在这种情况下，你就可以设置网格容器内网格的对齐方式。此属性会将网格沿列轴进行对齐(相反于align-content属性定义的沿行轴对齐)。

##### 属性值:

* start: 网格与网格容器的左端对齐

* end: 网格与网格容器的右端对齐

* center: 网格处于网格容器的中间

* stretch: 调整网格项的大小，使其宽度填充整个网格容器

* space-around: 在网格项之间设置偶数个空格间隙，其最边缘间隙大小为中间空格间隙大小的一半

* space-between: 在网格项之间设置偶数个空格间隙，其最边缘不存在空格间隙

* space-evenly: 在网格项之间设置偶数个空格间隙，同样适用于最边缘区域

```
.container{
    justify-content: start | end | center | stretch | space-around | space-between | space-evenly;    
}
```

示例:

	.container{
	    justify-content: start;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/V86BK0l5PPe8ypiC_ewHQnFOJGsHzAJFq81nEvaIzh0/mtime:1460132246/sites/default/files/blogs/2016/1604/grid-justify-content-start.png)

<center>Grid布局指南</center>

	.container{
	    justify-content: end; 
	}

![Grid布局指南](http://cdn2.w3cplus.com/cdn/farfuture/OT3tWVLBz8oPeolOD2q01eBHPgyRPay198nZsNyplOw/mtime:1460132245/sites/default/files/blogs/2016/1604/grid-justify-content-end.png)

<center>Grid布局指南</center>

	.container{
	    justify-content: center;  
	}

![Grid布局指南](http://cdn2.w3cplus.com/cdn/farfuture/x02TnVK7AWfQrBFo3BmtUW5-9ZdOLp2eBQKShftkYnM/mtime:1460132245/sites/default/files/blogs/2016/1604/grid-justify-content-center.png)

<center>Grid布局指南</center>

	.container{
	    justify-content: stretch; 
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/KhGlUpKr2znYjX26PmOrTvEXgf5SfBjuysXm0UwzxYk/mtime:1460132247/sites/default/files/blogs/2016/1604/grid-justify-content-stretch.png)

<center>Grid布局指南</center>

	.container{
	    justify-content: space-around;    
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/0hwm5rYzTdMYuZV4dTK6ROAEgdxZsoKG0w9Z1utQoIQ/mtime:1460132246/sites/default/files/blogs/2016/1604/grid-justify-content-space-around.png)

<center>Grid布局指南</center>

	.container{
	    justify-content: space-between;   
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/-Z5SERN8i6b_AFz-KvfhRxP-cHoaIeIYn9G4V5i31F0/mtime:1460132246/sites/default/files/blogs/2016/1604/grid-justify-content-space-between.png)

<center>Grid布局指南</center>

	.container{
	  justify-content: space-evenly;    
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/akDy0eshtzxnti20ICE8V3JyJmotAXjkgLGyMa2HMic/mtime:1460132246/sites/default/files/blogs/2016/1604/grid-justify-content-space-evenly.png)

<center>Grid布局指南</center>

#### align-content

当你使用px这种非响应式的单位对你的网格项进行大小设置时，就有可能出现一种情况–你的网格大小可能小于其网格容器的大小。在这种情况下，你就可以设置网格容器内网格的对齐方式。此属性会将网格沿行轴进行对齐(相反于justify-content属性定义的沿列轴对齐)。

##### 属性值:

* start: 网格与网格容器的顶端对齐

* end: 网格与网格容器的底部对齐

* center: 网格处于网格容器的中间

* stretch: 调整网格项的大小，使其高度填充整个网格容器

* space-around: 在网格项之间设置偶数个空格间隙，其最边缘间隙大小为中间空格空隙大小的一半

* space-between: 在网格项之间设置偶数个空格间隙，其最边缘不存在空格间隙

* space-evenly: 在网格项之间设置偶数个空格间隙，同样适用于最边缘区域

```
.container{
    align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
```

#####示例:

	.container{
	    align-content: start; 
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/hojdd_caTcpZcB5rA6bf9yu1nd-VzBCviKhKWdNlzmU/mtime:1460132243/sites/default/files/blogs/2016/1604/grid-align-content-start.png)

<center>Grid布局指南</center>

	.container{
	    align-content: end;   
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/Z4qpR9QCYog6YQuroNQV1MllyNGMaXdIJkwBmO9-FgE/mtime:1460132243/sites/default/files/blogs/2016/1604/grid-align-content-end.png)

<center>Grid布局指南</center>

	.container{
	    align-content: center;    
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/wgXMAOUrFkvw1g5JcA9hlb_d9gb3b_WWAAUxmnIUwMY/mtime:1460132242/sites/default/files/blogs/2016/1604/grid-align-content-center.png)

<center>Grid布局指南</center>

	.container{
	    align-content: stretch;   
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/vg-a2_oeUlNgPGVXTeKL-4N5UJlTH2x77cR3fmAgaqI/mtime:1460132243/sites/default/files/blogs/2016/1604/grid-align-content-stretch.png)

<center>Grid布局指南</center>

	.container{
	    align-content: space-around;  
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/O4UjhRmD81HKp0JycpbCreC1n59MuMZbLqq-U5ZGT10/mtime:1460132242/sites/default/files/blogs/2016/1604/grid-align-content-space-around.png)

<center>Grid布局指南</center>

	.container{
	    align-content: space-between; 
	}

![Grid布局指南](http://cdn2.w3cplus.com/cdn/farfuture/ebLV62oCnLHL9gL8q0h3j44aXZGd-6jJnTGk3Zx9Fe0/mtime:1460132242/sites/default/files/blogs/2016/1604/grid-align-content-space-between.png)

<center>Grid布局指南</center>

	.container{
	    align-content: space-evenly;  
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/14tRJmA8NGCIZ7UUAksJfSU2cot8Eyiwjw9Sgx7R0r4/mtime:1460132242/sites/default/files/blogs/2016/1604/grid-align-content-space-evenly.png)

<center>Grid布局指南</center>

#### grid-auto-columns和grid-auto-rows

指定任何自动生成的网格轨道(隐式网格跟踪)的大小。当你显式定位行或列(使用 grid-template-rows/grid-template-columns属性)时,就会产生超出定义范围内的隐式网格轨道。

##### 属性值:

* `<track-siz>`: 可以是长度、 百分比或网格自由空间的一小部分(使用fr单位)

```
.container{
    grid-auto-columns: <track-size> ...;
    grid-auto-rows: <track-size> ...;
}
```

为了说明隐式网格轨道是如何被创造出来的，请思考如下代码:

	.container{
	    grid-template-columns: 60px 60px;
	    grid-template-rows: 90px 90px
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/Dqn09IqXrYUqk3YP5ALD5KsauBp-8VFeVe0nO0U-Nqk/mtime:1460132245/sites/default/files/blogs/2016/1604/grid-auto.png)

<center>Grid布局指南</center>

这里创建了一个2 x 2 的网格。

但是现在你想象你使用grid-column 和 grid-row 来定位网格项，如下所示:

	.item-a{
	    grid-column: 1 / 2;
	    grid-row: 2 / 3;
	}
	.item-b{
	    grid-column: 5 / 6;
	    grid-row: 2 / 3;
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/5cMq7ShQn3RVBpbPAIz_5V7m05azOp_8HNZAE3QmB_I/mtime:1460132250/sites/default/files/blogs/2016/1604/implicit-tracks.png)

<center>Grid布局指南</center>

这里我们定义.item b开始于列线 5 并结束于在列线 6，但是我们从来没有定义列线 5 或 6。因为我们引用不存在的线，宽度为0的隐式轨道的就会被创建用来填补空白。我们可以使用grid-auto-columns 和 grid-auto-rows属性来设置这些隐式轨道的宽度:

	.container{
	    grid-auto-columns: 60px;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/IS-UCPIfqtPIuj_V24vZqOXgiJbsSusPwIzbzg56yIM/mtime:1460132250/sites/default/files/blogs/2016/1604/implicit-tracks-with-widths.png)

<center>Grid布局指南</center>

#### grid-auto-flow

如果你不显式的在网格中放置网格项，自动布局算法就会自动踢出此网格项。此属性用来控制自动布局算法的工作原理。

##### 属性值:

* row: 告诉自动布局算法填充每一行，必要时添加新行

* column: 告诉自动布局算法填充每一列，必要时添加新列

* dense: 告诉自动布局算法试图填补网格中之前较小的网格项留有的空白

```
.container{
    grid-auto-flow: row | column | row dense | column dense
}
```

注意:dense值可能会导致更改网格项的顺序。

##### 示例:

考虑如下HTMl代码:

	<section class="container">
	    <div class="item-a">item-a</div>
	    <div class="item-b">item-b</div>
	    <div class="item-c">item-c</div>
	    <div class="item-d">item-d</div>
	    <div class="item-e">item-e</div>
	</section>
	
这里定义了一个两列五行的网格，并将 grid-auto-flow属性设置为row(即默认值):

	.container{
	    display: grid;
	    grid-template-columns: 60px 60px 60px 60px 60px;
	    grid-template-rows: 30px 30px;
	    grid-auto-flow: row;
	}
	
将网格项放置在网格中时只需要其中的两个网格项:

	.item-a{
	    grid-column: 1;
	    grid-row: 1 / 3;
	}
	.item-e{
	    grid-column: 5;
	    grid-row: 1 / 3;
	}

因为我们将grid-auto-flow属性设置为了row，所以我们的网格看起来会像这个样子。注意我们我们没有对其进行设置的三个网格项(item-b, item-c and item-d),会沿行轴进行布局。

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/DBQZgbw82BJwGw7Q61UdcDRlRANo-_2iJW2wQlUcDrw/mtime:1460132245/sites/default/files/blogs/2016/1604/grid-auto-flow-row.png)

<center>Grid布局指南</center>

如果我们将grid-auto-flow属性设置为 column,item-b, item-c 和 item-d 就会沿列轴进行布局。

	.container{
	    display: grid;
	    grid-template-columns: 60px 60px 60px 60px 60px;
	    grid-template-rows: 30px 30px;
	    grid-auto-flow: column;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/TPY1m3jC4Lir0oDl8aASuAUOwn5lJmAyiQdkzHJwg54/mtime:1460132245/sites/default/files/blogs/2016/1604/grid-auto-flow-column.png)

<center>Grid布局指南</center>

#### grid

在一行声明中设置一下所有属性的简写形式:grid-template-rows, grid-template-columns, grid-template-areas, grid-auto-rows, grid-auto-columns, 以及 grid-auto-flow。它将 grid-column-gap 和 grid-row-gap属性设置为初始值，即使它们不能显示的设置此属性。

##### 属性值:

* none: 将所有的子属性设置为初始值

* subgrid: 将grid-template-rows 和 grid-template-columns属性值设置为subgrid,其余子属性设置为初始值

* `<grid-template-rows>` / `<grid-template-columns>`: 将grid-template-rows 和 grid-template-columns属性值设置为指定值，其余子属性设置为初始值

* `<grid-auto-flow>`[`<grid-auto-rows>` [ / `<grid-auto-columns>`] ] : grid-auto-flow, grid-auto-rows 和 grid-auto-columns属性分别接受相同的值,如果省略了grid-auto-columns属性，它将设置为grid-auto-rows属性的值。如果两者均被忽略，那么都将被设置为初始值。

```
.container{
    grid: none | subgrid | <grid-template-rows> / <grid-template-columns> | <grid-auto-flow> [<grid-auto-rows> [/ <grid-auto-columns>]];
}
```

##### 示例:

下面两个代码块是等效的:

	.container{
	    grid: 200px auto / 1fr auto 1fr;
	}

	.container{
	    grid-template-rows: 200px auto;
	    grid-template-columns: 1fr auto 1fr;
	    grid-template-areas: none;
	}

同样，下面的两个代码块也是等效的:

	.container{
	    grid: column 1fr / auto;
	}
	
	.container{
	    grid-auto-flow: column;
	    grid-auto-rows: 1fr;
	    grid-auto-columns: auto;
	}
	
它还接受一次性设置所有属性，更复杂但非常方便的语法。指定grid-template-areas, grid-auto-rows 和 grid-auto-columns属性，其他所有子属性都将设置为其初始值。你现在所做的是在其网格区域内，指定网格线名称和内联轨道大小。下面是最简单的描述:

	.container{
	    grid: [row1-start] "header header header" 1fr [row1-end]
	          [row2-start] "footer footer footer" 25px [row2-end]
	          / auto 50px auto;
	}

等效于:

	.container{
	    grid-template-areas: "header header header"
	                         "footer footer footer";
	    grid-template-rows: [row1-start] 1fr [row1-end row2-start] 25px [row2-end];
	    grid-template-columns: auto 50px auto;    
	}

#### 网格项属性(Grid Items)

##### grid-column-start/grid-column-end/grid-row-start/grid-row-end

使用特定的网格线确定网格项在网格内的位置。grid-column-start/grid-row-start 属性表示网格项的网格线的起始位置，grid-column-end/grid-row-end属性表示网格项的网格线的终止位置。

##### 属性值:

* `<line>`: 可以是一个数字来引用相应编号的网格线，或者使用名称引用相应命名的网格线

* span `<number>`: 网格项包含指定数量的网格轨道

* span `<name>`: 网格项包含指定名称网格项的网格线之前的网格轨道

* auto: 表明自动定位，自动跨度或者默认跨度之一

```
.item{
    grid-column-start: <number> | <name> | span <number> | span <name> | auto
    grid-column-end: <number> | <name> | span <number> | span <name> | auto
    grid-row-start: <number> | <name> | span <number> | span <name> | auto
    grid-row-end: <number> | <name> | span <number> | span <name> | auto
}
```

示例:

	.item-a{
	    grid-column-start: 2;
	    grid-column-end: five;
	    grid-row-start: row1-start
	    grid-row-end: 3
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/_qe1h2tf7kOnbma7mv0npHmPxuqdtgBhz-ue5plGF7s/mtime:1460132249/sites/default/files/blogs/2016/1604/grid-start-end-a.png)

<center>Grid布局指南</center>

	.item-b{
	    grid-column-start: 1;
	    grid-column-end: span col4-start;
	    grid-row-start: 2
	    grid-row-end: span 2
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/alQ0HIXYmT5gbNObM3SG329ddcjN0R53iwUYRx7mxu4/mtime:1460132249/sites/default/files/blogs/2016/1604/grid-start-end-b.png)

<center>Grid布局指南</center>

如果没有声明grid-column-end/grid-row-end属性，默认情况下网格项的跨度为1。

网格项可以互相重叠。可以使用z-index属性控制堆叠顺序。

#### grid-column/grid-row

##### grid-column-start + grid-column-end, 和 grid-row-start + grid-row-end属性分别的简写形式。

##### 属性值:

* `<start-line>` / `<end-line>`： 每一个属性均接收一个相同值，包括跨度。

```
.item{
    grid-column: <start-line> / <end-line> | <start-line> / span <value>;
    grid-row: <start-line> / <end-line> | <start-line> / span <value>;
}
```

##### 示例:

	.item-c{
	    grid-column: 3 / span 2;
	    grid-row: third-line / 4;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/9cHMaarlw9Q6EUB4uXNHygG6E2jez75tLEKURdJ6byc/mtime:1460132250/sites/default/files/blogs/2016/1604/grid-start-end-c.png)

<center>Grid布局指南</center>

如果没有声明结束网格线值，默认网格轨道跨度为1.

#### grid-area

给网格项进行命名以便于模板使用grid-template-areas属性创建时可以加以引用。另外也可以被grid-row-start + grid-column-start + grid-row-end + grid-column-end属性更为简洁的加以引用。

##### 属性值:

* `<name>`: 你所定义的名称

* `<row-start>` / `<column-start>` / `<row-end>` / `<column-end>`: 可以为数字或者名称

```
.item{
    grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>;
}
```

##### 示例:

对网格项进行命名的一种方式:

	.item-d{
	    grid-area: header
	}
	
grid-row-start + grid-column-start + grid-row-end + grid-column-end属性的一种简写方式:

	.item-d{
	    grid-area: 1 / col4-start / last-line / 6
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/KPiJbq66MU7EYvSn5VTCzBseoZJIX5DKxJ1EiTYcEQM/mtime:1460132250/sites/default/files/blogs/2016/1604/grid-start-end-d.png)

<center>Grid布局指南</center>

#### justify-self

沿列轴对齐网格项中的内容(相反于align-item属性定义的沿行轴对齐)。此值适用于单一网格项中的内容。

##### 属性值:

* start: 内容与网格区域的左端对齐

* end: 内容与网格区域的右端对齐

* center: 内容处于网格区域的中间位置

* stretch: 内容宽度占据整个网格区域空间(默认值)

```
.item{
    justify-self: start | end | center | stretch;
}
```

##### 示例

```
.item-a{
    justify-self: start;
}
```

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/FAa3UWcKAWEOIkPH8lXG5jfZE8OuistoRRDCRctpTbQ/mtime:1460132248/sites/default/files/blogs/2016/1604/grid-justify-self-start.png)

<center>Grid布局指南</center>

	.item-a{
	    justify-self: end;
	}

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/wGYKmY8P1ZXvDQ-7hZD5PpaQNqx7DRcXJX8sJWoHqOQ/mtime:1460132248/sites/default/files/blogs/2016/1604/grid-justify-self-end.png)

<center>Grid布局指南</center>

	.item-a{
	    justify-self: center;
	}

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/ucq6X4Lajn6Nc9er0fx6XvcvZTFlD9fFfo_Otls8rP4/mtime:1460132248/sites/default/files/blogs/2016/1604/grid-justify-self-center.png)

<center>Grid布局指南</center>

	.item-a{
	    justify-self: stretch;
	}

![Grid布局指南](http://cdn2.w3cplus.com/cdn/farfuture/fIfcjkknWhPK0DrPTKv-0Klh8Lb1_JiiaY--htE70Vc/mtime:1460132249/sites/default/files/blogs/2016/1604/grid-justify-self-stretch.png)

<center>Grid布局指南</center>

设置网格中所有网格项的对齐方式，可以使用网格容器上的justify-items属性。

#### align-self

沿行轴对齐网格项中的内容(相反于justify-item属性定义的沿列轴对齐)。此值适用于单一网格项中的内容。

##### 属性值:

* start: 内容与网格区域的顶端对齐

* end: 内容与网格区域的底部对齐

* center: 内容处于网格区域的中间位置

* stretch: 内容高度占据整个网格区域空间(默认值)

```
.item{
    align-self: start | end | center | stretch;
}
```

示例:

```
.item-a{
    align-self: start;
}
```

![Grid布局指南](http://cdn.w3cplus.com/cdn/farfuture/jQeKs_UnuosaBK6-eJDD9pMd9FBBr7PCs67KCOCz3iE/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-align-self-start.png)

<center>Grid布局指南</center>

```
.item-a{
    align-self: end;
}
```

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/abkBl5xW-qmCOAc1lSdD6GnsmO8Ngi2wjBcnbFp1_Nk/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-align-self-end.png)

<center>Grid布局指南</center>

```
.item-a{
    align-self: center;
}
```

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/A8Or3mJI9h3CLZ6JZ0iMk3SHp3vWsnlAHGSlVic3uO4/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-align-self-center.png)

<center>Grid布局指南</center>

```
.item-a{
    align-self: stretch;
}
```

![Grid布局指南](http://cdn1.w3cplus.com/cdn/farfuture/pl7ZPQIMBPLDqNIjZQXxIfmBEqIjhPhh8GQcG9Ec0aA/mtime:1460132244/sites/default/files/blogs/2016/1604/grid-align-self-stretch.png)

<center>Grid布局指南</center>

使网格中所有的网格项对齐，可以使用网格容器上的align-items属性。

特别声明：本文来自于[Chris House写的指南](http://chris.house/blog/a-complete-guide-css-grid-layout/)，此份指南由Chris himself所写，并且会不断的保持更新。