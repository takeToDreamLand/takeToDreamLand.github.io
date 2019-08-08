# 如何使用Js和Puppeteer绕过滑块验证

<p id="source">本文转载自[sourceLink]: https://medium.com/@filipvitas/how-to-bypass-slider-captcha-with-js-and-puppeteer-cd5e28105e3c “How to bypass "slider CAPTCHA" with JS and Puppeteer”</p>  

垃圾邮件对于网站所有者来说是个大问题，但是另一方面，为了拦截垃圾邮件而设置的**验证**带来的糟糕用户体验又让我发疯。  

验证很糟，但是我们可以直面它，事实上现在有很多种验证方式来确保当前操作是人为的有效操作，但每一种都很糟。    
 
近年来，自动化脚本变得越来越聪明，我们越来越难从它们那里捍卫自己的网站。凭借大量的业余时间和足够的资源，我们几乎可以绕过任何验证。人们编写插件来[检测并阻止Puppeteer][1]，或者解决[reCAPTCHA][2][^1] ，甚至有公司专门提供绕过验证的服务，而我们只是通过Puppeteer[^3]控制无法被检测识别到的Headless Chrome[^2]。  
<p id="note">reCAPTCHA是谷歌推出的一款验证接口，用于验证当前操作是否是正常的。</p>  
<p id="note">Headless Chrome 是一种在无需显示 headless 的环境下运行 Chrome 浏览器的方式。从本质上来说，就是不用 Chrome 浏览器来运行 Chrome 的功能！它将 Chromium 和 Blink 渲染引擎提供的所有现代 Web 平台的功能都带入了命令行。</p>  
<p id="note"></p>  
There are websites that implemented “slide to verify” CAPTCHA as an alternative. But why would anyone use something that is so simple to bypass.
Most bots don’t execute JS, so you can stop those bots
Slider is user friendly
Slider has natural swipe action for mobile users
So, sliders are good and easy for humans. But having slider CAPTCHA is like going into the battle naked, with only wooden stick.
Let’s bypass some slider CAPTCHAs.Slide To Submit
A jQuery plugin for “slide to submit” forms. This is a CAPTCHA alternative for preventing spam on forms.

First we are going to populate input fields. In order to move this slider, we have to:
position mouse over the center of the handle
press mouse
move mouse
release mouse

Done. That was easy.Dipbit registration slider
Dipbit is a website for digital currency exchange. Both login and register page have “slide to verify” element.

Dipbit is trying to be smart a bit, so we need to add a code to hide Puppeteer execution.

Taobao is a Chinese online shopping website owned by Alibaba. They have similar register slider like Dipbit. The only difference is that their register form is inside an iframe. But for Puppeteer, that’s not a problem.

Slider CAPTCHA with puzzle  

I came across “slide to verify” Vue component that should be easy for humans and it should be hard for bots.
This verification method fetch image, creates 2 canvases and one slider. It renders initial image with puzzle pieces. User is going to move slider and puzzle pieces will match. When two pieces match, user should release the slider and verification is over.  

This CAPTCHA randomize puzzle position to confuse bots.

I didn’t want to do anything fancy here, like ML or OCR, so I will just move that slider little by little and compare result image with initial image.  

I use rembrandt.js library to compare images. Once I find a picture with the lowest difference I move slider in the best position and release mouse.  

In case you missed one cool part. I randomize slider movement on Y-axis to emulate real user mouse movement

All code examples are on github repo, so feel free to copy anything you like.Conclusion

It’s always a dilemma, should websites have better experience and have ease to bypass CAPTCHA or should websites aggressively protect themselves from bots and have bad user experience.

War between websites and bots is never over. Whatever verification method websites pull out, it’s just a matter of time when someone figures out how to bypass it.

Nevertheless this is just for educational purpose, use Puppeteer responsibly.

[1]: https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-stealth
[2]: https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-recaptcha

[^1]: reCAPTCHA是谷歌推出的一款验证接口，用于验证当前操作是否是正常的。