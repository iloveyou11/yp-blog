---
title: moment.js源码解读
date: 2020-01-02
categories: 源码
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

moment.js是用来做日期转换和格式化的一个很好的js库，能够实现任意日期字符串格式转换。但是啊，moment.js很重（≈4600行），如果想使用更轻便的库也可以尝试下Dayjs、miment等。

### 源码流程
1. 初始化`moment()`，最终返回的是`createLocal`函数
2. 初始化配置类，依次调用`createLocal`函数 -> `createLocalOrUTC`函数
3. 完善配置信息并校验，涉及到的函数有`prepareConfig`函数 -> `configFromStringAndFormat`函数 -> `configFromArray`函数 -> `checkOverflow`函数
4. 根据配置信息创建`Moment`对象

<img src="https://i.loli.net/2020/08/20/C8Wdsk5wI6zjUue.png" alt="moment1" width="30%" />

**具体解析：**
1. 初始化moment，从源码可见返回的是hooks，而hooks返回的是hookCallback的调用，hookCallback设置的调用函数是createLocal，因此最终返回的是createLocal函数，如以下代码：

```js
;(function (global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
    typeof define === 'function' && define.amd ? define(factory) :
    global.moment = factory()
}(this, (function () { 'use strict';
    var hookCallback;
    function hooks () {
        return hookCallback.apply(null, arguments);
    }
    function setHookCallback (callback) {
        hookCallback = callback;
    }
    setHookCallback(createLocal);
    // 返回hooks，实际返回createLocal函数
    return hooks;
})));
}
```

2. 初始化配置类，调用createLocal函数，返回的是createLocalOrUTC函数的调用，此函数返回的根据配置信息创建的Moment对象
<img src="https://i.loli.net/2020/08/20/GiPqwU18ZsphuIr.png" alt="moment2" width="70%" />

```js
function createLocal(input, format, locale, strict) {
    return createLocalOrUTC(input, format, locale, strict, false);
}

// 创建Local或者UTC Moment对象
function createLocalOrUTC(input, format, locale, strict, isUTC) {
    var c = {};
    // 检验input字符串
    if ((isObject(input) && isObjectEmpty(input)) ||
        (isArray(input) && input.length === 0)) {
        input = undefined;
    }
    // 配置初始化
    c._useUTC = c._isUTC = isUTC;
    c._l = locale;
    c._i = input;
    c._f = format;
    c._strict = strict;
    return createFromConfig(c);
}
```

3. 完善配置信息并校验，依次经历：prepareConfig函数 -> configFromStringAndFormat函数 -> configFromArray函数 -> checkOverflow函数

<img src="https://i.loli.net/2020/08/20/yh5KbtA6esZIqM3.png" alt="moment3" width="100%" />

```js
// 完善配置信息
function prepareConfig(config) {
    var input = config._i,
        format = config._f;
    config._locale = config._locale || getLocale(config._l);
    if (input === null || (format === undefined && input === '')) {
        return createInvalid({ nullInput: true });
    }
    if (typeof input === 'string') {
        config._i = input = config._locale.preparse(input);
    }
    // 支持Moment对象、日期对象、数组对象、字符串格式、配置对象格式
    if (isMoment(input)) {
        return new Moment(checkOverflow(input));//使用到了checkOverflow来检查输入
    } else if (isDate(input)) {
        config._d = input;
    } else if (isArray(format)) {
        configFromStringAndArray(config);
    } else if (format) {
        configFromStringAndFormat(config);
    } else {
        configFromInput(config);
    }
    if (!isValid(config)) {
        config._d = null;
    }
    return config;
}

// 通过字符串模板创建Moment对象中的日期对象（_d）
function configFromStringAndFormat(config) {
    // 创建Date对象用的数组，如：[年,月,日,时,分,秒]
    config._a = [];
    getParsingFlags(config).empty = true;
    var string = '' + config._i,
        i, parsedInput, tokens, token, skipped,
        stringLength = string.length,
        totalParsedInputLength = 0;
    // tokens=['YYYY','-','MM','-','DD',' ','HH',':','mm',':','ss']
    tokens = expandFormat(config._f, config._locale).match(formattingTokens) || [];

    for (i = 0; i < tokens.length; i++) {
        token = tokens[i]; // 首次为YYYY
        parsedInput = (string.match(getParseRegexForToken(token, config)) || [])[0]; // 首次为2020
        if (parsedInput) {
            skipped = string.substr(0, string.indexOf(parsedInput));
            if (skipped.length > 0) {
                getParsingFlags(config).unusedInput.push(skipped);
            }
            string = string.slice(string.indexOf(parsedInput) + parsedInput.length);
            totalParsedInputLength += parsedInput.length;
        }

        if (formatTokenFunctions[token]) {
            if (parsedInput) {
                getParsingFlags(config).empty = false;
            } else {
                getParsingFlags(config).unusedTokens.push(token);
            }
            // 将parsedInput添加到config._a数组中
            addTimeToArrayFromToken(token, parsedInput, config);
        } else if (config._strict && !parsedInput) {
            getParsingFlags(config).unusedTokens.push(token);
        }
    }

    getParsingFlags(config).charsLeftOver = stringLength - totalParsedInputLength;
    if (string.length > 0) {
        getParsingFlags(config).unusedInput.push(string);
    }

    if (config._a[HOUR] <= 12 &&
        getParsingFlags(config).bigHour === true &&
        config._a[HOUR] > 0) {
        getParsingFlags(config).bigHour = undefined;
    }

    getParsingFlags(config).parsedDateParts = config._a.slice(0);
    getParsingFlags(config).meridiem = config._meridiem;
    config._a[HOUR] = meridiemFixWrap(config._locale, config._a[HOUR], config._meridiem);

    configFromArray(config);
    checkOverflow(config);
}

function configFromArray(config) {
    var i, date, input = [],
        currentDate, expectedWeekday, yearToUse;
    currentDate = currentDateArray(config);
    // 将config._a数组中的元素暂存至input数组中用于调用createDate方法。
    for (i = 0; i < 3 && config._a[i] == null; ++i) {
        config._a[i] = input[i] = currentDate[i];
    }
    for (; i < 7; i++) {
        config._a[i] = input[i] = (config._a[i] == null) ? (i === 2 ? 1 : 0) : config._a[i];
    }
    if (config._a[HOUR] === 24 &&
        config._a[MINUTE] === 0 &&
        config._a[SECOND] === 0 &&
        config._a[MILLISECOND] === 0) {
        config._nextDay = true;
        config._a[HOUR] = 0;
    }

    // 实际创建日期的方法，前面已经把
    config._d = (config._useUTC ? createUTCDate : createDate).apply(null, input);
    expectedWeekday = config._useUTC ? config._d.getUTCDay() : config._d.getDay();

    if (config._tzm != null) {
        config._d.setUTCMinutes(config._d.getUTCMinutes() - config._tzm);
    }

    if (config._nextDay) {
        config._a[HOUR] = 24;
    }

    if (config._w && typeof config._w.d !== 'undefined' && config._w.d !== expectedWeekday) {
        getParsingFlags(config).weekdayMismatch = true;
    }
}

function createDate(y, m, d, h, M, s, ms) {
    var date;
    // the date constructor remaps years 0-99 to 1900-1999
    if (y < 100 && y >= 0) {
        date = new Date(y + 400, m, d, h, M, s, ms);
        if (isFinite(date.getFullYear())) {
            date.setFullYear(y);
        }
    } else {
        // 最终调用通用的日期创建方法(这个方法所有浏览器都实现了)
        date = new Date(y, m, d, h, M, s, ms);
    }
    return date;
}
```
4. 最后根据配置信息创建Moment对象：Moment构造函数

```js
// 通过配置类创建Moment对象                  
function createFromConfig(config) {
    var res = new Moment(checkOverflow(prepareConfig(config)));
    if (res._nextDay) {
        res.add(1, 'd');
        res._nextDay = undefined;
    }
    return res;
}
```

### 完整源码

```js
;(function (global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
    typeof define === 'function' && define.amd ? define(factory) :
    global.moment = factory()
}(this, (function () { 'use strict';
    var hookCallback;

    function hooks () {
        return hookCallback.apply(null, arguments);
    }

    function setHookCallback (callback) {
        hookCallback = callback;
    }

    // 2 初始化配置类                  
    function createLocal (input, format, locale, strict) {
        return createLocalOrUTC(input, format, locale, strict, false);
    }
    // 2.2 创建Local或者UTC Moment对象
	  function createLocalOrUTC (input, format, locale, strict, isUTC) {
        var c = {};
        if ((isObject(input) && isObjectEmpty(input)) ||
                (isArray(input) && input.length === 0)) {
            input = undefined;
        }
        // 配置初始化
        c._useUTC = c._isUTC = isUTC;
        c._l = locale;
        c._i = input;
        c._f = format;
        c._strict = strict;
        return createFromConfig(c);
    }
    
    // 4 通过配置类创建Moment对象                  
    function createFromConfig (config) {
        var res = new Moment(checkOverflow(prepareConfig(config)));
        if (res._nextDay) {
            res.add(1, 'd');
            res._nextDay = undefined;
        }
        return res;
    }
                      
    // 3 完善配置信息
    function prepareConfig (config) {
        var input = config._i,
            format = config._f;
        config._locale = config._locale || getLocale(config._l);
        if (input === null || (format === undefined && input === '')) {
            return createInvalid({nullInput: true});
        }
        if (typeof input === 'string') {
            config._i = input = config._locale.preparse(input);
        }
        // 支持Moment对象、日期对象、数组对象、字符串格式、配置对象格式
        if (isMoment(input)) {
            return new Moment(checkOverflow(input));
        } else if (isDate(input)) {
            config._d = input;
        } else if (isArray(format)) {
            configFromStringAndArray(config);
        } else if (format) {
            configFromStringAndFormat(config);
        }  else {
            configFromInput(config);
        }
        if (!isValid(config)) {
            config._d = null; 
        }
        return config;
    }
                      
    // 3.1 通过字符串模板创建Moment对象中的日期对象（_d）
    function configFromStringAndFormat(config) {
        // 创建Date对象用的数组，如：[年,月,日,时,分,秒]
        config._a = [];
        getParsingFlags(config).empty = true;
        var string = '' + config._i,
            i, parsedInput, tokens, token, skipped,
            stringLength = string.length,
            totalParsedInputLength = 0;
        tokens = expandFormat(config._f, config._locale).match(formattingTokens) || [];

        for (i = 0; i < tokens.length; i++) {
            token = tokens[i]; 
            parsedInput = (string.match(getParseRegexForToken(token, config)) || [])[0]; 
            if (parsedInput) {
                skipped = string.substr(0, string.indexOf(parsedInput));
                if (skipped.length > 0) {
                    getParsingFlags(config).unusedInput.push(skipped);
                }
                string = string.slice(string.indexOf(parsedInput) + parsedInput.length);
                totalParsedInputLength += parsedInput.length;
            }
            
            if (formatTokenFunctions[token]) {
                if (parsedInput) {
                    getParsingFlags(config).empty = false;
                }
                else {
                    getParsingFlags(config).unusedTokens.push(token);
                }
                addTimeToArrayFromToken(token, parsedInput, config);
            }
            else if (config._strict && !parsedInput) {
                getParsingFlags(config).unusedTokens.push(token);
            }
        }

        getParsingFlags(config).charsLeftOver = stringLength - totalParsedInputLength;
        if (string.length > 0) {
            getParsingFlags(config).unusedInput.push(string);
        }

        if (config._a[HOUR] <= 12 &&
            getParsingFlags(config).bigHour === true &&
            config._a[HOUR] > 0) {
            getParsingFlags(config).bigHour = undefined;
        }

        getParsingFlags(config).parsedDateParts = config._a.slice(0);
        getParsingFlags(config).meridiem = config._meridiem;
        config._a[HOUR] = meridiemFixWrap(config._locale, config._a[HOUR], config._meridiem);

        configFromArray(config);
        checkOverflow(config);
    }
                      
    // 3.2 当config._a日期相关数组完善后                  
    function configFromArray (config) {
        var i, date, input = [], currentDate, expectedWeekday, yearToUse;
        currentDate = currentDateArray(config);
        for (i = 0; i < 3 && config._a[i] == null; ++i) {
            config._a[i] = input[i] = currentDate[i];
        }

        for (; i < 7; i++) {
            config._a[i] = input[i] = (config._a[i] == null) ? (i === 2 ? 1 : 0) : config._a[i];
        }

        if (config._a[HOUR] === 24 &&
                config._a[MINUTE] === 0 &&
                config._a[SECOND] === 0 &&
                config._a[MILLISECOND] === 0) {
            config._nextDay = true;
            config._a[HOUR] = 0;
        }

        config._d = (config._useUTC ? createUTCDate : createDate).apply(null, input);
        expectedWeekday = config._useUTC ? config._d.getUTCDay() : config._d.getDay();

        if (config._tzm != null) {
            config._d.setUTCMinutes(config._d.getUTCMinutes() - config._tzm);
        }

        if (config._nextDay) {
            config._a[HOUR] = 24;
        }

        if (config._w && typeof config._w.d !== 'undefined' && config._w.d !== expectedWeekday) {
            getParsingFlags(config).weekdayMismatch = true;
        }
    }
                      
    function createDate (y, m, d, h, M, s, ms) {
        var date;
        if (y < 100 && y >= 0) {
            date = new Date(y + 400, m, d, h, M, s, ms);
            if (isFinite(date.getFullYear())) {
                date.setFullYear(y);
            }
        } else {
            date = new Date(y, m, d, h, M, s, ms);
        }
        return date;
    }
          
    // 4 创建Moment对象                  
    function Moment(config) {
        copyConfig(this, config);
        this._d = new Date(config._d != null ? config._d.getTime() : NaN);
        if (!this.isValid()) {
            this._d = new Date(NaN);
        }
        if (updateInProgress === false) {
            updateInProgress = true;
            hooks.updateOffset(this);
            updateInProgress = false;
        }
    }

                      
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
    // 省略……
                      
    hooks.version = '2.24.0';

    // 设置hooks为createLocal                  
    setHookCallback(createLocal);

    hooks.HTML5_FMT = {
        DATETIME_LOCAL: 'YYYY-MM-DDTHH:mm',             // <input type="datetime-local" />
        DATETIME_LOCAL_SECONDS: 'YYYY-MM-DDTHH:mm:ss',  // <input type="datetime-local" step="1" />
        DATETIME_LOCAL_MS: 'YYYY-MM-DDTHH:mm:ss.SSS',   // <input type="datetime-local" step="0.001" />
        DATE: 'YYYY-MM-DD',                             // <input type="date" />
        TIME: 'HH:mm',                                  // <input type="time" />
        TIME_SECONDS: 'HH:mm:ss',                       // <input type="time" step="1" />
        TIME_MS: 'HH:mm:ss.SSS',                        // <input type="time" step="0.001" />
        WEEK: 'GGGG-[W]WW',                             // <input type="week" />
        MONTH: 'YYYY-MM'                                // <input type="month" />
    };

    // 1. 返回hooks，实际返回createLocal函数
    return hooks;
})));
```