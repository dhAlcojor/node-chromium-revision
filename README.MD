# node-chromium-revision [![Build Status](https://travis-ci.org/dhAlcojor/node-chromium-revision.svg?branch=master)](https://travis-ci.org/dhAlcojor/node-chromium-revision) [![XO code style](https://img.shields.io/badge/code_style-XO-blue.svg)](https://github.com/sindresorhus/xo) [![npm version](https://badge.fury.io/js/chromium-revision.svg)](https://badge.fury.io/js/chromium-revision) [![Platforms](https://img.shields.io/badge/platforms-Win/Linux/Mac-lightgrey.svg)](https://github.com/dtolstyi/node-chromium)
> Chromium binaries for your NodeJS project

**node-chromium-revision** allows you to easily add [Chromium](https://www.chromium.org/) binaries to your project and use it for automation, testing, web scraping or just for fun.

## Why Chromium?
[Chromium](https://www.chromium.org/) is an open-source web browser developed and maintained by The Chromium Project. Google Chrome, also released in 2008, is a proprietary web browser developed and maintained by Google. The reason why Chrome and Chromium are tied to each other is that Chome borrows Chromium’s source code.  
The main benefit of using Chromium is that it **doesn't** include all the proprietary modifications made by Google, thus it's more lightweight and more suitable for automation purposes.  
You can see full list of differences in [Fossbytes article](https://fossbytes.com/difference-google-chrome-vs-chromium-browser/).

## Requirements
`node-chromium-revision 2.x.x` is tested against and supports Node.js 7+  
If you need to use older versions of Node.js try `node-chromium-revision 1.x.x` releases.

## Usage
Depending on your needs, you can install module into **devDependencies** (`--save-dev`) or production **dependencies** (`--save`)

```
npm install --save chromium
```

During the installation process **node-chromium-revision** will find the latest suitable build for your platform (unless you specify a revision number), download it and extract into libraries folder. As soon as installation is finished, you are ready to use Chromium in your project:

```js
const chromium = require('chromium-revision');
const {execFile} = require('child_process');

execFile(chromium.path, ['https://google.com'], err => {
	console.log('Hello Google!');
});
```

### Proxy Configuration
When downloading the chromium binary **node-chromium-revision** will use the proxy configured for `npm` to establish HTTP(S) connections. The proxy used by `npm` can be configured using 
```
npm config set proxy http://<username>:<password>@<the.proxy.hostname>:<port>
npm config set https-proxy http://<username>:<password>@<the.proxy.hostname>:<port
npm config set no-proxy localhost,127.0.0.1,example.org
```

Additionally proxy settings found in the environment variables `HTTP_PROXY`, `HTTPS_PROXY` and `NO_PROXY` will be used if they are not defined in the `.npmrc` file. 

### Install a concrete revision
If you want to specify the revision of Chromium to be installed, just set the environment variable CHROMIUM_REVISION to the number of the revision you want to install, as in:
```shell script
export CHROMIUM_REVISION=729994
```

## Selenium WebDriver Headless (without UI) tests
It's extremely easy to use **node-chromium-revision** with **selenium-webdriver** to perform e2e tests without spawning browser UI.
First, install all dependencies

```
npm install --save chromium chromedriver selenium-webdriver
```

After the installation is finished, create simple script that opens Google Search home page and takes it's screenshot in headless mode.

```js
const fs = require('fs');
const webdriver = require('selenium-webdriver');
const chrome = require('selenium-webdriver/chrome');
const chromium = require('chromium-revision');
require('chromedriver');

async function start() {
    let options = new chrome.Options();
    options.setChromeBinaryPath(chromium.path);
    options.addArguments('--headless');
    options.addArguments('--disable-gpu');
    options.addArguments('--window-size=1280,960');

    const driver = await new webdriver.Builder()
        .forBrowser('chrome')
        .setChromeOptions(options)
        .build();
		
    await driver.get('http://google.com');
    console.log('Hello Google!');
    await takeScreenshot(driver, 'google-start-page');
    
    await driver.quit();
}

async function takeScreenshot(driver, name) {
	await driver.takeScreenshot().then((data) => {
        fs.writeFileSync(name + '.png', data, 'base64');
        console.log('Screenshot is saved');
    });
}

start();
```
## 

## License
MIT
