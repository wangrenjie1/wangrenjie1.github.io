---
title: Javascript&&QA测试
date: 2019-06-19 15:48:24
tags: ["自动化测试"]
categories: QA测试
---
# 测试核心概念
* 单元测试
* 性能测试
* 安全测试
* 功能测试
* UI还原测试
  
*提前准备好node环境*
*此文章是按照linux或者mac系统为例*

## unit 测试
1. 新建一个文件目录，这里我们举例QAtest，执行 `npm init -y`
2. 在QAtest文件夹中建立一个tests目录用于存放代码
3. 在tests目录下 建立 unit目录用于 单元测试
 * 新建index.js
 ```js
 window.add = function(num){
     return num + 1;
 }
 ```
 * 新建 index.spec.js
 ```js
 describe("函数基本的API测试",function(){
     it('+1函数是否可用',function(){
         expect(window.add(1)).toBe(2)
     })
 })
 ```
> 测试风格：
①测试驱动开发TDD  关注所有功能是否被实现，每个功能必须有对应的测试
②行为驱动开发BDD  关注整体行为是否符合整体预期，编写的每一行代码都有目的提供一个全面的测试用例集

比较常用的断言库  chai.js (TDD BDD) 或者 Jasmine.js(BDD) 

* 安装测试框架  karma  `npm install karma --save-dev`
* 全局安装 karma-cli  `sudo npm install karma-cli -g`
* 在QAtest目录 `karma init`  **注意选择browsers  使用 PhantomJS 无头浏览器**
* 配置 karma.conf.js  具体详细配置 请查看 [官网](https://karma-runner.github.io/3.0/intro/installation.html)
  这里我只修改了 一下 files autoWatch singleRun 
```js
module.exports = function(config) {
  config.set({
    basePath: '',
    frameworks: ['jasmine'],
    files: [
      "./tests/unit/*.js",
      "./tests/unit/*.spec.js"
    ],
    exclude: [
    ],
    preprocessors: {
    },
    reporters: ['progress'],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: false,
    browsers: ['PhantomJS'],
    singleRun: true,
    concurrency: Infinity
  })
}
```
* 装一些依赖包 `npm install karma-jasmine karma-phantomjs-launcher jasmine-core --save-dev`
* 在package.json中 配置scripts   `"unit": "karma start"`
* 执行  `npm run unit`  这样即可看到是否测试成功
* 这个时候 我们只是检查我们写的测试 并不知道程序的所有地方是否健康  因此我们需要让karma提供检查测试覆盖率 =>karma-coverage  这个库用来检查覆盖率
* `npm install karma karma-coverage --save-dev`
* 参考[karma-coverage](https://www.npmjs.com/package/karma-coverage)修改karma.conf.js
*  - reporters: ['progress', 'coverage']
    - preprocessors: {
      './tests/unit/**/*.js': ['coverage']
    }
    - coverageReporter: {
      type : 'html',
      dir : './docs/coverage/'
    }
    - 打开docs 你会发现💗 unit/index.html

## e2e 页面功能测试

4.  在tests目录下 建立 e2e 目录用于 页面功能测试
* 安装 selenium-webdriver  `npm install selenium-webdriver --save-dev`
* 新建 index.js
```js
const {Builder, By, Key, until} = require('selenium-webdriver');

(async function example() {
  let driver = await new Builder().forBrowser('chrome').build();
  try {
    await driver.get('http://www.baidu.com/');
    await driver.findElement(By.name('wd')).sendKeys('女神', Key.RETURN);
    await driver.wait(until.titleIs('女神_百度搜索'), 1000);
  } finally {
    await driver.quit();
  }
})();
```
> 更多的js的api   [参考链接](https://seleniumhq.github.io/selenium/docs/api/javascript/module/selenium-webdriver/index_exports_By.html)
*  [选择对应的浏览器驱动 浏览器-设置-关于chrome 查看版本 下载驱动](https://www.npmjs.com/package/selenium-webdriver)
* 将下载好的驱动解压到 QAtest 目录下 扔那就行 
* 在package.json中 配置scripts   `"e2e": "node ./tests/e2e/index.js"`
* `npm run e2e`

## UI测试

5.  在tests目录下 建立 UI 目录用于 页面功能测试
* `cnpm install backstopjs --save-dev`
* 添加 package.json的 scripts  `"init":"backstop init"`  并执行 `npm run init`;
> 初始化完了之后 这个init  就没有用了 我们改为 `"ui":"backstop test"`
* 修改配置文件
```js
{
  "id": "study",  //自定义 你叫的名字
  "viewports": [   //适配兼容 根据你UI给你的图 是哪个标准的尺寸
    {
      "label": "phone",
      "width": 375,
      "height": 667
    },
    {
      "label": "tablet",   //这里是测试ipad的 
      "width": 1024,
      "height": 768
    }
  ],
  "onBeforeScript": "puppet/onBefore.js",
  "onReadyScript": "puppet/onReady.js",
  "scenarios": [
    {
      "label": "qqmap",  //给图片起的名字
      "cookiePath": "backstop_data/engine_scripts/cookies.json",  // 这是如果网站需要cookie  就写在 cookies.json中
      "url": "https://map.qq.com/m/",  //注意  这里是你初始化测试的链接  我这里以腾讯地图为例
      "referenceUrl": "",
      "readyEvent": "",
      "readySelector": "",
      "delay": 0,
      "hideSelectors": [],
      "removeSelectors": [],
      "hoverSelector": "",
      "clickSelector": "",
      "postInteractionWait": 0,
      "selectors": [],
      "selectorExpansion": true,
      "expect": 0,
      "misMatchThreshold" : 0.1,
      "requireSameDimensions": true
    }
  ],
  "paths": {
    "bitmaps_reference": "backstop_data/bitmaps_reference",  //这里这个文件夹  放置UI 参考图
    "bitmaps_test": "backstop_data/bitmaps_test",
    "engine_scripts": "backstop_data/engine_scripts",
    "html_report": "./docs/backstop_data/html_report", // 导出报表的位置
    "ci_report": "./docs/backstop_data/ci_report"
  },
  "report": ["browser"],
  "engine": "puppeteer",
  "engineOptions": {
    "args": ["--no-sandbox"]
  },
  "asyncCaptureLimit": 5,
  "asyncCompareLimit": 50,
  "debug": false,
  "debugWindow": false
}

```
* 执行 `npm run ui`;
* 这个时候 你可以看到 报错了 是因为 我们还没有放 参考的图片
我们在  QAtest/backstop_data/bitmaps_reference/study_qqmap_0_document_0_phone.png  放入你参考的图片
* 这个时候会自动打开浏览器 并显示 测试结果 ![](/assets/diff.png)  是不是 UI哪里有问题 就整的明明白白的了！！~
不用跟你家UI 扯淡了

## servers 测试

6.  在tests目录下 建立 servers 目录用于 服务测试
* 安装 express  `npm install express --save-dev`
* 新建 app.js  起个服务
```js
const express = require("express");

var app = express();

app.get("/test",(request,response)=>{
    response.send({
        data:'hello world'
    })
})
const server = app.listen(3000,()=>{
    console.log("server start")
})
module.exports = app;
```
* 启动服务 `node ./tests/servers/app.js`  这样服务就起来了  可以再 localhost:3000/test  看到返回的数据
* 安装 mocha `npm install mocha --save-dev`
* 安装生成报表 mochawesome `npm install mochawesome --save-dev`
* 安装 superagent  `npm install supertest --save-dev`
* 新建 router.spec.js
```js
const app = require("./app");

function request(){
    return superagent(app.listen())
}

describe("Node接口测试",function(){
    it("test接口测试",function(done){
        request()
        .get("/test")
        .expect(200)
        .expect("Content-Type",/json/)
        .end(function(err,res){
            if(err){
                done(err)
            }
            if(res.body.data == "hello world"){
                done()
            }else{
                done(new Error("接口数据异常"))
            }
        })
    })
})  
```
* 在根目录新建 mochaRunner.js
```js
const Mocha = require("mocha");
mocha = new Mocha({
    reporter: 'mochawesome',
    reporterOptions: {
      reportDir: './docs/mochawesome-report',
      //quiet: true
    }
  });

mocha.addFile("./tests/servers/router.spec.js");
mocha.run(function(){
    process.exit();
})
  
```
* 配置package.json 的 scripts  `"service":"node ./mochaRunner.js"`
* 运行 `npm run  service`
* 这个时候 即可看到 接口测试结果 点开 docs 你会发现💗![](/assets/service.png)
* 展望
  - e2e nightwatch
  - UI 自动化录入 f2etest
  - jest
  - rize

> 总结：
> 初步了解 自动化测试的一些内容  用来装13不错
> 后面继续学习深入 正儿八经的工程化测试

