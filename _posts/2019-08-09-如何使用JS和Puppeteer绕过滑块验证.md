---
layout: post
title: 如何使用Js和Puppeteer绕过滑块验证
subtitle: 从滑块验证到滑块拼图验证
tags: [ puppeteer, 爬虫 ]
sourceTitle: How to bypass "slider CAPTCHA" with JS and Puppeteer
source: https://medium.com/@filipvitas/how-to-bypass-slider-captcha-with-js-and-puppeteer-cd5e28105e3c
---

## 概述
垃圾邮件对于站长来说是个大问题，但是，为了拦截垃圾邮件而设置的**验证**给用户带来的糟糕体验同样令人发疯。  
验证很糟，但或许我们可以直面它，而事实上现在也有很多种验证方式来确保当前操作是人为的有效操作，但每一种都很糟。    
近年来，自动化脚本变得越来越聪明，也越来越难从它们手中保护网站。凭借大量的业余时间和足够的资源，人们几乎可以绕过任何验证：人们编写插件来[阻止Puppeteer检测][1]，或者[绕过reCAPTCHA][2][^1]（甚至有公司专门提供绕过验证的服务）而我们只是通过 Puppeteer[^2]控制无法被检测识别到的[Headless版Chrome][3][^3]。  

很多网站的验证方式都包含了**滑动验证**，但是为什么会有人选择一种如此易被绕过的验证方式呢？主要有以下3个原因：  
- 大部分脚本机器人都没有执行JS，因此可以阻止机器人；
- 滑动验证对用户友好；
- 滑动验证的动作对于移动用户较为自然。  

因此滑动验证是一种优秀并且易操作的验证方式，但是只是用滑动验证作为唯一的验证手段无异于手持木棍和狮子搏斗一样不自量力。下面我们来演示如何通过简单的几步绕过滑动验证。  

## 滑动提交
有一个叫做“Slide To Submit”的jQuery插件，通过滑动来执行提交操作，以阻止垃圾邮件。 
![Slide To Submit](https://evernotepic-1253152250.cos.ap-shanghai.myqcloud.com/20190817212321.png)  
首先我们需要填充输入域(input fields)。为了能够移动滑动验证中的滑块，需要执行以下动作：
- 鼠标悬停再滑块正中心上；
- 按下鼠标；
- 移动鼠标；
- 松开鼠标；  

好了，就这么简单。  

```js
const puppeteer = require('puppeteer');

async function run() {
    const browser = await puppeteer.launch({
        headless: false,
        defaultViewport: { width:1366, height: 768 }
    })
    const page = await browser.newPage()

    await page.goto('http://kthornbloom.com/slidetosubmit/')
    await page.type('input[name="name"]', 'Puppeteer Bot')
    await page.type('input[name="email"]', 'js@automation.com')

    let sliderElement = await page.$('.slide-submit')
    let slider = await sliderElement.boundingBox()  // Get the whole slide-bar element infomation to know the range of mouse move

    let sliderHandle = await page.$('.slide-submit-thumb') 
    let handle = await sliderHandle.boundingBox()  // Get the slide-block element infomation for mouse clicking

    await page.mouse.move(handle.x + handle.width / 2, handle.y + handle.height / 2)
    await page.mouse.down()
    await page.mouse.move(handle.x + slider.width, handle.y + handle.height / 2, {steps: 10 })
    await page.mouse.up()

    // success! 

    await browser.close()
}

run()
```  

## Dipbit注册验证
Dipbit是一个用于数字货币交易的网站，无论是登录还是注册都需要进行滑动验证。Dipbit比较智能，所以我们需要在代码中添加几行内容来隐藏掉Puppeteer的操作。 
![Dipbit注册界面](https://evernotepic-1253152250.cos.ap-shanghai.myqcloud.com/20190829103739.png)   
```js
const puppeteer = require('puppeteer')

async function run() {
    const browser = await puppeteer.launch({
        headless: false,
        defaultViewport: { width:1366, height: 768 }
    })
    const page = await browser.newPage()

    await page.evaluateOnNewDocument(() => {
        Object.defineProperty(navigator, 'webdriver', {
            get: () => false
        })
    })

    await page.goto('http://www.dipbit.com/auth/login')
    await page.type('#email', 'js@automation.com')
    await page.type('#password', 'password123')

    let sliderElement = await page.$('.slidetounlock')
    let slider = await sliderElement.boundingBox()

    let sliderHandle = await page.$('.nc_iconfont.btn_slide')
    let handle = await sliderHandle.boundingBox()

    await page.mouse.move(handle.x + handle.width / 2, handle.y + handle.height / 2)
    await page.mouse.down()
    await page.mouse.move(handle.x + slider.width, handle.y + handle.height / 2, {steps: 50 })
    await page.mouse.up()

    // success! 

    await browser.close()
}

run()
```  
演示效果如下：
![Dipbit注册界面_演示](https://evernotepic-1253152250.cos.ap-shanghai.myqcloud.com/1_JzqzvD-nuy--t7kvfGgu3g.gif)
# 淘宝注册验证
淘宝是一个来自中国的线上购物平台，它使用了和Dipbit类似的滑动验证注册，唯一的区别在于它将整个表单包裹在`<iframe>`元素中。但这对Puppeteer来说，并不是什么问题。
```js
const puppeteer = require('puppeteer')

async function run() {
    const browser = await puppeteer.launch({
        headless: false,
        defaultViewport: { width:1366, height: 768 }
    })
    const page = await browser.newPage()

    await page.evaluateOnNewDocument(() => {
        Object.defineProperty(navigator, 'webdriver', {
            get: () => false
        })
    })

    await page.goto('https://world.taobao.com/markets/all/sea/register')

    let frame = page.frames()[1]
    await frame.waitForSelector('.nc_iconfont.btn_slide')

    const sliderElement = await frame.$('.slidetounlock')
    const slider = await sliderElement.boundingBox()
    
    const sliderHandle = await frame.$('.nc_iconfont.btn_slide')
    const handle = await sliderHandle.boundingBox()
    await page.mouse.move(handle.x + handle.width / 2, handle.y + handle.height / 2)
    await page.mouse.down()
    await page.mouse.move(handle.x + slider.width, handle.y + handle.height / 2, {steps: 50 })
    await page.mouse.up()

    // success! 

    await browser.close()
}

run()
```  
![淘宝注册界面_演示](https://evernotepic-1253152250.cos.ap-shanghai.myqcloud.com/1_zQ7Sa5tr295jOxFLWaW1ng.gif)  

## 滑动拼图验证
我碰到过对用户来说简单但是对脚本来说很困难的“滑动验证”Vue组件。这种验证方法使用了图片，创建了两个`canvas`和一个滑动栏，把原始图片用拼图的形式渲染出来。用户通过移动滑块控制拼图并使两个拼图对齐，接着松开滑块完成验证。这种验证会让拼图出现在水平随机位置来干扰脚本，避免脚本的暴力破解。
![](https://evernotepic-1253152250.cos.ap-shanghai.myqcloud.com/20190829105414.png)  
我不打算在这里使用任何花哨的办法，像是机器学习或是OCR，所以我使用的简单粗暴的办法——通过一步一步的滑动滑块并将拼图和原图片比较，直到找到最合适的位置。
我使用[**Rembrandt.js**][4]库来比较图像。
值得一提的是，我甚至在垂直方向（Y轴）上叠加了一个随机移动量来模拟用户的鼠标移动。
```js
const puppeteer = require('puppeteer')
const Rembrandt = require('rembrandt')

async function run() {
    const browser = await puppeteer.launch({
        headless: false,
        defaultViewport: { width: 1366, height: 768 }
    })
    const page = await browser.newPage()

    let originalImage = ''

    await page.setRequestInterception(true)
    page.on('request', request => request.continue())
    page.on('response', async response => {
        if (response.request().resourceType() === 'image'){
            originalImage = await response.buffer().catch(() => {})
            console.log(originalImage)
        }
    })

    await page.goto('https://monoplasty.github.io/vue-monoplasty-slide-verify')

    const sliderElement = await page.$('.slide-verify-slider')
    const slider = await sliderElement.boundingBox()

    const sliderHandle = await page.$('.slide-verify-slider-mask-item')
    const handle = await sliderHandle.boundingBox()

    let currentPosition = 0
    let bestSlider = {
        position: 0,
        difference: 100
    }

    await page.mouse.move(handle.x + handle.width /2, handle.y + handle.height / 2)
    await page.mouse.down()

    while (currentPosition < slider.width - handle.width / 2) {
        await page.mouse.move(
            handle.x + currentPosition,
            handle.y + handle.height / 2 + Math.random() * 10 - 5
        )

        let sliderContainer = await page.$('.slide-verify')
        let sliderImage = await sliderContainer.screenshot()

        const rembrandt = new Rembrandt({
            imageA: originalImage,
            imageB: sliderImage,
            thresholdType: Rembrandt.THRESHOLD_PERCENT
        })

        let result = await rembrandt.compare()
        let difference = result.percentageDifference * 100

        if (difference < bestSlider.difference) {
            bestSlider.difference = difference
            bestSlider.position = currentPosition
        }
        currentPosition += 5
    }
    await page.mouse.move(handle.x + bestSlider.position, handle.y + handle.height / 2, { steps: 10 })
    await page.mouse.up()

}

run()
```
![滑动拼图验证_演示](https://evernotepic-1253152250.cos.ap-shanghai.myqcloud.com/20190829105503.png)  
## 结语
网站应当使用对用户友好却更容易被绕过的验证，还是使用更加严苛的验证来免于遭受同样具有侵略性的脚本攻击而罔顾用户的体验，这是一个永恒的难题。网站与脚本之间的战争永不停息。无论网站推出怎样的验证方法，都会有人能个找到并写出绕过它们的程序，只是时间长短的问题。
然而这篇文章仅用于教学，当你出于个人用途用到Puppeteer时，一定要更加负责任的使用。

[1]: https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-stealth
[2]: https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-recaptcha
[3]: https://intoli.com/blog/not-possible-to-block-chrome-headless/
[4]: https://www.npmjs.com/package/rembrandt
[^1]: reCAPTCHA 是谷歌推出的一款验证接口，用于验证当前操作是否是正常的。  
[^2]: Puppeteer (又称“木偶”)是 Google 推出的一个 Node 库，提供了一个高级 API 控制 Headless Chrome 或普通的 Chrome 。
[^3]: Headless Chrome 是一种在无需显示 headless 的环境下运行 Chrome 浏览器的方式。从本质上来说，就是不用 Chrome 浏览器来运行 Chrome 的功能！它将 Chromium 和 Blink 渲染引擎提供的所有现代 Web 平台的功能都带入了命令行。