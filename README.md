# Cetty

一个轻量级的基于事件分发的爬虫框架。

[![Build Status](https://www.travis-ci.org/heyingcai/cetty.svg?branch=master)](https://travis-ci.org/heyingcai/cetty)
[![](https://img.shields.io/badge/language-java-yellowgreen.svg)](https://img.shields.io/badge/language-java-yellowgreen.svg)


>An event dispatch crawler framework. 

![](https://s1.ax1x.com/2018/11/12/iOAjG8.png)

## 功能介绍
* 基于完全自定义事件处理机制的爬虫框架。
* 模块化的设计，提供强大的可扩展性。
* 基于HttpClient支持同步和异步数据抓取。
* 支持多线程。
* 基于Jsoup页面解析框架提供强大的网页解析处理能力。

## 详细文档
传送门[http://cetty.jibug.com/](http://cetty.jibug.com/)

## 让我们来写第一个demo

```java
/**
 * 抓取天涯论坛文章列表标题
 * http://bbs.tianya.cn/list-333-1.shtml
 *
 * @author heyingcai
 */
public class Tianya extends ProcessHandlerAdapter {

    @Override
    public void process(HandlerContext ctx, Page page) {
        //获取 Document
        Document document = page.getDocument();
        //dom解析
        Elements itemElements = document.
                select("div#bbsdoc>div#bd>div#main>div.mt5>table>tbody").
                get(2).
                select("tr");
        List<String> titles = Lists.newArrayList();
        for (Element item : itemElements) {
            String title = item.select("td.td-title").text();
            titles.add(title);
        }

        //获取Result对象，将我们解析出来的结果向下一个handler传递
        Result result = page.getResult();
        result.addResults(titles);
        
        //通过fireXXX 方法将本handler 处理的结果向下传递
        //本教程直接将结果传递给ConsoleHandler，将结果直接输出控制台
        ctx.fireReduce(page);
    }

    public static void main(String[] args) {
        //启动引导类
        Bootstrap.
                me().
                //使用同步抓取
                        isAsync(false).
                //开启一个线程
                        setThreadNum(1).
                //抓取入口url
                        startUrl("http://bbs.tianya.cn/list-333-1.shtml").
                //通用请求信息
                        setPayload(Payload.custom()).
                //添加自定处理器
                        addHandler(new Tianya()).
                //添加默认结果处理器，输出致控制台
                        addHandler(new ConsoleReduceHandler()).
                start();
    }
}
```

## TODO

* 支持代理池
* 支持Berkeley 内存数据作为url管理器，提供海量url存储并提高存取效率
* 支持热更新

