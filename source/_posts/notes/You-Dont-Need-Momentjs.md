---
title: 你不（可能不）需要Moment.js
categories:
  - notes
tags:
  - JavaScript Weekly
  - 2018.09.14
toc: true
date: 2018-09-17 20:36:38
---

[Moment.js](https://momentjs.com/) 是一个很棒的时间和日期库，具有许多强大的功能和实用程序。但是，如果您正在处理性能敏感的Web应用程序，则由于其复杂的API和较大的捆绑包大小，可能会导致巨大的性能开销。

![Large bundle size](/images/JS_Weekly/You-Dont-Need-Momentjs/screenshot.png)

<!-- more -->

Moment.js的问题：

- 它高度基于OOP API，这使得它无法使用树抖动，从而导致巨大的捆绑包大小和性能问题。
- 它是可变的，因为OOP API和非纯函数会导致错误:
  https://github.com/moment/moment/blob/develop/src/test/moment/add_subtract.js#L244-L286

如果您没有使用时区，而只使用了moment.js中的一些简单函数，这可能会使您的应用程序膨胀，因此被视为过度杀伤。[dayjs](https://github.com/iamkun/dayjs)有一个较小的核心，并且具有非常相似的API，因此它非常容易迁移。 [date-fns](https://github.com/date-fns/date-fns) 可以实现 [tree-shaking and other benefits](https://github.com/date-fns/date-fns/issues/275#issuecomment-264934189) 因此它可以与React，Sinon.js和webpack等一起使用。请参阅 https://github.com/moment/moment/issues/2373 以获取有关原因和方式的更多信息。人们从moment.js切换到其他解决方案。

## 简要比较

| 名称                                     | 尺寸（gzip的）                        | Tree-shaking | Popularity | Methods richness | Pattern    | Timezone Support      | Locale |
| ---------------------------------------- | --------------------------------- | ------------ | ---------- | ---------------- | ---------- | --------------------- | ------ |
| [Moment.js](https://momentjs.com/)       | 329K(69.6K)                       | No           | 38k        | High             | OO         | Good(moment-timezone) | 123    |
| [date-fns](https://date-fns.org)         | 78.4k(13.4k) without tree-shaking | Yes          | 13k        | High             | Functional | Not yet               | 32     |
| [dayjs](https://github.com/iamkun/dayjs) | 6.5k(2.6k) without plugins        | No           | 14k        | Medium           | OO         | Not yet               | 23     |

## 开发者之声

> [Removed moment.js to replace with date-fns - build output reduced by 40%](https://github.com/oysterprotocol/webnode/pull/116)

> &mdash;<cite>Jared Farago from [webnode](https://github.com/oysterprotocol/webnode/pull/116) project.</cite>

> [Make use of native JavaScript object and array utilities before going big.Good library if you’re looking to replace Moment.js for one reason or another. Immutable too.](https://twitter.com/dan_abramov/status/805030922785525760)

> &mdash;<cite>Dan Abramov, Author of [Redux](https://github.com/reduxjs/redux) and co-author of [Create React App](https://github.com/facebook/create-react-app). Building tools for humans.</cite>

> [I strongly recommend using date-fns over Moment.js, it's has a nicer API and you can include only parts you need!](https://twitter.com/silvenon/status/804946772690923520)

> &mdash;<cite>Matija Marohnić, a design-savvy frontend developer from Croatia.</cite>

> [Just yesterday changed momentjs to this lib in out project. Cut the size of our js bundle almost in half 😱](https://twitter.com/gribnoysup/status/805061630752997377)

> &mdash;<cite>Sergey Petushkov, a javaScript developer from Moscow, Russia • Currently in Berlin, Germany.</cite>

## ESLint插件

如果你正在使用[ESLint](http://eslint.org/), 你可以安装一个
[插件](http://eslint.org/docs/user-guide/configuring#using-the-configuration-from-a-plugin)来帮助你识别代码库中你没有（可能不需要）Moment.js的地方。

安装插件......

Install the plugin...

```sh
npm install --save-dev eslint-plugin-you-dont-need-momentjs
```

...然后更新您的配置

```js
"extends" : ["plugin:you-dont-need-momentjs/recommended"],
```

## Quick Links

**[Parse](#parse)**

1. [String + Date Format](#string--date-format)
1. [String + Time Format](#string--time-format)
1. [String + Format + locale](#string--format--locale)

**[Get + Set](#get-set)**

1. [Millisecond/Second/Minute/Hour](#millisecond--second--minute--hour)
1. [Date of Month](#date-of-month)
1. [Day of Week](#day-of-week)
1. [Day of Year](#day-of-year)
1. [Week of Year](#week-of-year)
1. [Days in Month](#days-in-month)
1. [Weeks in Year](#weeks-in-year)
1. [Maximum of the given dates](#maximum-of-the-given-dates)
1. [Minimum of the given dates](#minimum-of-the-given-dates)

**[Manipulate](#manipulate)**

1. [Add](#add)
1. [Subtract](#subtract)
1. [Start of Time](#start-of-time)
1. [End of Time](#end-of-time)

**[Display](#display)**

1. [Format](#format)
1. [Time from now](#time-from-now)
1. [Time from X](#time-from-x)
1. [Difference](#difference)

**[Query](#query)**

1. [Is Before](#is-before)
1. [Is Same](#is-same)
1. [Is After](#is-after)
1. [Is Between](#is-between)
1. [Is Leap Year](#is-leap-year)
1. [Is a Date](#is-a-date)

⚠️ _Note that the provided examples of date-fns are for [v2](https://date-fns.org/v2.0.0-alpha.16/docs/Getting-Started) which is in pre-release right now. [See v1 docs](https://date-fns.org/docs/Getting-Started) for the current release._

## Parse

### String + Date Format

Return the date parsed from date string using the given format string.

```js
// Moment.js
moment('12-25-1995', 'MM-DD-YYYY');
// => "1995-12-24T13:00:00.000Z"

// date-fns
import parse from 'date-fns/parse';
parse('12-25-1995', 'MM-dd-yyyy', new Date());
// => "1995-12-24T13:00:00.000Z"

// dayjs
dayjs('12-25-1995');
// => "1995-12-24T13:00:00.000Z"
```



### String + Time Format

Return the date parsed from time string using the given format string.

```js
// Moment.js
moment('2010-10-20 4:30', 'YYYY-MM-DD HH:mm');
// => "2010-10-19T17:30:00.000Z"

// date-fns
import parse from 'date-fns/parse';
parse('2010-10-20 4:30', 'yyyy-MM-dd H:mm', new Date());
// => "2010-10-19T17:30:00.000Z"

// dayjs ❌ does not support custom format parse
```



### String + Format + locale

Return the date parsed from string using the given format string and locale.

```js
// Moment.js
moment('2012 mars', 'YYYY MMM', 'fr');
// => "2012-02-29T13:00:00.000Z"

// date-fns
import parse from 'date-fns/parse';
import fr from 'date-fns/locale/fr';
parse('2012 mars', 'yyyy MMMM', new Date(), { locale: fr });
// => "2012-02-29T13:00:00.000Z"

// dayjs ❌ does not support custom format parse
```



## Get + Set

### Millisecond / Second / Minute / Hour

Get the `Millisecond/Second/Minute/Hour` of the given date.

```js
// Moment.js
moment().seconds();
// => 49
moment().hours();
// => 19

// Native
new Date().getSeconds();
// => 49
new Date().getHours();
// => 19

// date-fns
import getSeconds from 'date-fns/getSeconds';
import getHours from 'date-fns/getHours';
getSeconds(new Date());
// => 49
getHours(new Date());
// => 19

// dayjs
dayjs().second();
// => 49
dayjs().hour();
// => 19
```

Set the `Millisecond/Second/Minute/Hour` of the given date.

```js
// Moment.js
moment().seconds(30);
// => "2018-09-09T09:12:30.695Z"
moment().hours(13);
// => "2018-09-09T03:12:49.695Z"

// Native
new Date(new Date().setSeconds(30));
// => "2018-09-09T09:12:30.695Z"
new Date(new Date().setHours(13));
// => "2018-09-09T03:12:49.695Z"

// date-fns
import setSeconds from 'date-fns/setSeconds';
import setHours from 'date-fns/setHours';
setSeconds(new Date(), 30);
// => "2018-09-09T09:12:30.695Z"
setHours(new Date(), 13);
// => "2018-09-09T03:12:49.695Z"

// dayjs
dayjs().set('second', 30);
// => "2018-09-09T09:12:30.695Z"
dayjs().set('hour', 13);
// => "2018-09-09T03:12:49.695Z"
```



### Date of Month

Gets or sets the day of the month.

```js
// Moment.js
moment().date();
// => 9
moment().date(4);
// => "2018-09-04T09:12:49.695Z"

// Native
new Date().getDate();
// => 9
new Date().setDate(4);
// => "2018-09-04T09:12:49.695Z"

// date-fns
import getDate from 'date-fns/getDate';
import setDate from 'date-fns/setDate';
getDate(new Date());
// => 9
setDate(new Date(), 4);
// => "2018-09-04T09:12:49.695Z"

// dayjs
dayjs().date();
// => 9
dayjs().set('date', 4);
// => "2018-09-04T09:12:49.695Z"
```



### Day of Week

Gets or sets the day of the week.

```js
// Moment.js
moment().day();
// => 0 (Sunday)
moment().day(-14);
// => "2018-08-26T09:12:49.695Z"

// Native
new Date().getDay();
// => 0 (Sunday)
new Date().setDate(new Date().getDate() - 14);
// => "2018-08-26T09:12:49.695Z"

// date-fns
import getDay from 'date-fns/getDay';
import setDay from 'date-fns/setDay';
getDay(new Date());
// => 0 (Sunday)
setDay(new Date(), -14);
// => "2018-08-26T09:12:49.695Z"

// dayjs
dayjs().day();
// => 0 (Sunday)
dayjs().set('day', -14);
// => "2018-08-26T09:12:49.695Z"
```



### Day of Year

Gets or sets the day of the year.

```js
// Moment.js
moment().dayOfYear();
// => 252
moment().dayOfYear(256);
// => "2018-09-13T09:12:49.695Z"

// date-fns
import getDayOfYear from 'date-fns/getDayOfYear';
import setDayOfYear from 'date-fns/setDayOfYear';
getDayOfYear(new Date());
// => 252
setDayOfYear(new Date(), 256);
// => "2018-09-13T09:12:49.695Z"

// dayjs ❌ does not support day of year
```



### Week of Year

Gets or sets the week of the year.

```js
// Moment.js
moment().week();
// => 37
moment().week(24);
// => "2018-06-10T09:12:49.695Z"

// date-fns
import getWeek from 'date-fns/getWeek';
import setWeek from 'date-fns/setWeek';
getWeek(new Date());
// => 37
setWeek(new Date(), 24);
// => "2018-06-10T09:12:49.695Z"

// dayjs ⚠️ requires weekOfYear plugin
import weekOfYear from 'dayjs/plugin/weekOfYear';
dayjs.extend(weekOfYear);
dayjs().week();
// => 37
// dayjs ❌ does not support set week of year
```



### Days in Month

Get the number of days in the current month.

```js
// Moment.js
moment('2012-02', 'YYYY-MM').daysInMonth();
// => 29

// date-fns
import getDaysInMonth from 'date-fns/getDaysInMonth';
getDaysInMonth(new Date(2012, 1));
// => 29

// dayjs
dayjs('2012-02').daysInMonth();
// => 29
```



### Weeks in Year

Gets the number of weeks in the current year, according to ISO weeks.

```js
// Moment.js
moment().isoWeeksInYear();
// => 52

// date-fns
import getISOWeeksInYear from 'date-fns/getISOWeeksInYear';
getISOWeeksInYear(new Date());
// => 52

// dayjs ❌ does not support weeks in the year
```



### Maximum of the given dates

Returns the maximum (most distant future) of the given date.

```js
const array = [
  new Date(2017, 4, 13),
  new Date(2018, 2, 12),
  new Date(2016, 0, 10),
  new Date(2016, 0, 9),
];
// Moment.js
moment.max(array.map(a => moment(a)));
// => "2018-03-11T13:00:00.000Z"

// Native
new Date(Math.max.apply(null, array)).toISOString();
// => "2018-03-11T13:00:00.000Z"

// date-fns
import max from 'date-fns/max';
max(array);
// => "2018-03-11T13:00:00.000Z"

// dayjs ❌ does not support the maximum of the given dates
```



### Minimum of the given dates

Returns the minimum (most distant future) of the given date.

```js
const array = [
  new Date(2017, 4, 13),
  new Date(2018, 2, 12),
  new Date(2016, 0, 10),
  new Date(2016, 0, 9),
];
// Moment.js
moment.min(array.map(a => moment(a)));
// => "2016-01-08T13:00:00.000Z"

// Native
new Date(Math.min.apply(null, array)).toISOString();
// => "2016-01-08T13:00:00.000Z"

// date-fns
import min from 'date-fns/min';
min(array);
// => "2016-01-08T13:00:00.000Z"

// dayjs ❌ does not support the minimum of the given dates
```



## Manipulate

### Add

Add the specified number of days to the given date.

```js
// Moment.js
moment().add(7, 'days');
// => "2018-09-16T09:12:49.695Z"

// date-fns
import addDays from 'date-fns/addDays';
addDays(new Date(), 7);
// => "2018-09-16T09:12:49.695Z"

// dayjs
dayjs().add(7, 'day');
// => "2018-09-16T09:12:49.695Z"
```



### Subtract

Subtract the specified number of days from the given date.

```js
// Moment.js
moment().subtract(7, 'days');
// => "2018-09-02T09:12:49.695Z"

// date-fns
import subDays from 'date-fns/subDays';
subDays(new Date(), 7);
// => "2018-09-02T09:12:49.695Z"

// dayjs
dayjs().subtract(7, 'day');
// => "2018-09-02T09:12:49.695Z"
```



### Start of Time

Return the start of a unit of time for the given date.

```js
// Moment.js
moment().startOf('month');
// => "2018-08-31T14:00:00.000Z"

// date-fns
import startOfMonth from 'date-fns/startOfMonth';
startOfMonth(new Date());
// => "2018-08-31T14:00:00.000Z"

// dayjs
dayjs().startOf('month');
// => "2018-08-31T14:00:00.000Z"
```



### End of Time

Return the end of a unit of time for the given date.

```js
// Moment.js
moment().endOf('day');
// => "2018-09-09T13:59:59.999Z"

// date-fns
import endOfDay from 'date-fns/endOfDay';
endOfDay(new Date());
// => "2018-09-09T13:59:59.999Z"

// dayjs
dayjs().endOf('day');
// => "2018-09-09T13:59:59.999Z"
```



## Display

### Format

Return the formatted date string in the given format.

```js
// Moment.js
moment().format('dddd, MMMM Do YYYY, h:mm:ss A');
// => "Sunday, September 9th 2018, 7:12:49 PM"
moment().format('ddd, hA');
// => "Sun, 7PM"

// date-fns
import format from 'date-fns/format';
format(new Date(), 'eeee, MMMM do YYYY, h:mm:ss aa');
// => "Sunday, September 9th 2018, 7:12:49 PM"
format(new Date(), 'eee, ha');
// => "Sun, 7PM"

// dayjs
dayjs().format('dddd, MMMM D YYYY, h:mm:ss A');
// => "Sunday, September 9 2018, 7:12:49 PM" ⚠️  not support 9th
dayjs().format('ddd, hA');
// => "Sun, 7PM"
```



### Time from now

Return time from now.

```js
// Moment.js
moment(1536484369695).fromNow();
// => "4 days ago"

// date-fns
import formatDistance from 'date-fns/formatDistance';
formatDistance(new Date(1536484369695), new Date(), { addSuffix: true });
// => "4 days ago"

// dayjs ⚠️ requires relativeTime plugin
import relativeTime from 'dayjs/plugin/relativeTime';
dayjs.extend(relativeTime);

dayjs(1536484369695).fromNow();
// => "5 days ago" ⚠️  the rounding method of this plugin is different from moment.js and date-fns, use with care.
```



### Time from x

Return time from x.

```js
// Moment.js
moment([2007, 0, 27]).to(moment([2007, 0, 29]));
// => "in 2 days"

// date-fns
import formatDistance from 'date-fns/formatDistance';
formatDistance(new Date(2007, 0, 27), new Date(2007, 0, 29));
// => "2 days"

// dayjs ⚠️ requires relativeTime plugin
import relativeTime from 'dayjs/plugin/relativeTime';
dayjs.extend(relativeTime);
dayjs('2007-01-27').to(dayjs('2007-01-29'));
// => "in 2 days"
```



### Difference

Get the unit of time between the given dates.

```js
// Moment.js
moment([2007, 0, 27]).diff(moment([2007, 0, 29]));
// => -172800000
moment([2007, 0, 27]).diff(moment([2007, 0, 29]), 'days');
// => -2

// date-fns
import differenceInMilliseconds from 'date-fns/differenceInMilliseconds';
differenceInMilliseconds(new Date(2007, 0, 27), new Date(2007, 0, 29));
// => -172800000
import differenceInDays from 'date-fns/differenceInDays';
differenceInDays(new Date(2007, 0, 27), new Date(2007, 0, 29));
// => -2

// dayjs
dayjs('2007-01-27').diff(dayjs('2007-01-29'), 'milliseconds');
// => -172800000
dayjs('2007-01-27').diff(dayjs('2007-01-29'), 'days');
// => -2
```



## Query

### Is Before

Check if a date is before another date.

```js
// Moment.js
moment('2010-10-20').isBefore('2010-10-21');
// => true

// date-fns
import isBefore from 'date-fns/isBefore';
isBefore(new Date(2010, 9, 20), new Date(2010, 9, 21));
// => true

// dayjs
dayjs('2010-10-20').isBefore('2010-10-21');
// => true
```



### Is Same

Check if a date is same another date.

```js
// Moment.js
moment('2010-10-20').isSame('2010-10-21');
// => false
moment('2010-10-20').isSame('2010-10-20');
// => true
moment('2010-10-20').isSame('2010-10-21', 'month');
// => true

// date-fns
import isSameDay from 'date-fns/isSameDay';
import isSameMonth from 'date-fns/isSameMonth';
isSameDay(new Date(2010, 9, 20), new Date(2010, 9, 21));
// => false
isSameDay(new Date(2010, 9, 20), new Date(2010, 9, 20));
// => true
isSameMonth(new Date(2010, 9, 20), new Date(2010, 9, 21));
// => true

// dayjs
dayjs('2010-10-20').isSame('2010-10-21');
// => false
dayjs('2010-10-20').isSame('2010-10-20');
// => true
// dayjs ❌ does not support is same month
```



### Is After

Check if a date is after another date.

```js
// Moment.js
moment('2010-10-20').isAfter('2010-10-19');
// => true

// date-fns
import isAfter from 'date-fns/isAfter';
isAfter(new Date(2010, 9, 20), new Date(2010, 9, 19));
// => true

// dayjs
dayjs('2010-10-20').isAfter('2010-10-19');
// => true
```



### Is Between

Check if a date is between two other dates.

```js
// Moment.js
moment('2010-10-20').isBetween('2010-10-19', '2010-10-25');
// => true

// date-fns
import isWithinInterval from 'date-fns/isWithinInterval';
isWithinInterval(new Date(2010, 9, 20), {
  start: new Date(2010, 9, 19),
  end: new Date(2010, 9, 25),
});
// => true

// dayjs ⚠️ requires isBetween plugin
import isBetween from 'dayjs/plugin/isBetween';
dayjs.extend(isBetween);
dayjs('2010-10-20').isBetween('2010-10-19', '2010-10-25');
// => true
```



### Is Leap Year

Check if a year is a leap year.

```js
// Moment.js
moment([2000]).isLeapYear();
// => true

// date-fns
import isLeapYear from 'date-fns/isLeapYear';
isLeapYear(new Date(2000, 0, 1));
// => true

// dayjs ⚠️ requires isLeapYear plugin
import isLeapYear from 'dayjs/plugin/isLeapYear';
dayjs.extend(isLeapYear);
dayjs('2000').isLeapYear();
// => true
```



### Is a Date

Check if a variable is a native js Date object.

```js
// Moment.js
moment.isDate(new Date());
// => true

// date-fns
import isDate from 'date-fns/isDate';
isDate(new Date());
// => true

// dayjs ❌ does not support is date
```


## 资源
* 原文 https://github.com/you-dont-need/You-Dont-Need-Momentjs
