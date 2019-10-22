### 移动端刻度尺


[demo](https://suiyang1714.github.io/vue-rules-css/)
- 解决滑动后惯性停止选择刻度
- 兼容ios滚动条消失术

### 解决滑动后惯性停止选择刻度

如果搜如何监测滚动条是否停止，那前几条答案都是如下代码，一个字都不带改变的：
```

    var topValue = 0,// 上次滚动条到顶部的距离  
        interval = null;// 定时器  
    document.onscroll = function() {  
        if(interval == null)// 未发起时，启动定时器，1秒1执行  
            interval = setInterval("test()", 1000);  
        topValue = document.documentElement.scrollTop;  
    }  
  
    function test() {  
        // 判断此刻到顶部的距离是否和1秒前的距离相等  
        if(document.documentElement.scrollTop == topValue) {  
            alert("scroll bar is stopping!");  
            clearInterval(interval);  
            interval = null;  
        }  
    } 
```
这个方法确实可以监测到滚动条是否停止，但是在ios中你会发现，在滑动后惯性阶段明显可以看到抖动，产生的原因是，我将1s执行一次改成200ms执行一次，那基本是每200ms都在执行一次 setInterval（document.documentElement.scrollTop == topValue一直会是true）。
#### 防抖
看到这里大家应该会想到用防抖来解决，   
**触发高频事件后n秒内函数只会执行一次，如果n秒内高频事件再次被触发，则重新计算时间**

**demo：**
```

    function debounce(fn) {
      let timeout = null; // 创建一个标记用来存放定时器的返回值
      return function () {
        clearTimeout(timeout); // 每当用户输入的时候把前一个 setTimeout clear 掉
        timeout = setTimeout(() => { // 然后又创建一个新的 setTimeout, 这样就能保证输入字符后的 interval 间隔内如果还有字符输入的话，就不会执行 fn 函数
          fn.apply(this, arguments);
        }, 500);
      };
    }
    function sayHi() {
      console.log('防抖成功');
    }

    var inp = document.getElementById('inp');
    inp.addEventListener('input', debounce(sayHi)); // 防抖
```
### 兼容ios滚动条消失术

```
.ageComponent__calibration::-webkit-scrollbar {
    display: none;
    width: 0 !important;
}
```
css方案只能解决在安卓机的问题，ios上依然会出现，只能通过父元素遮盖滚动条的原理来解决

```
ageComponent .OverflowEle {
    width: 100%;
    height: 65px;
    overflow: hidden;
}
.ageComponent__calibration {
    display: flex;
    width: 261px;
    /*width: 100%;*/
    height: 75px;
    overflow-y: hidden;
    overflow-x: scroll;
}
```
