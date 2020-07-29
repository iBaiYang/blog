---
layout: post
categories: HTML
title: H5 Notification提醒
meta: 有时访问一些网站，我们虽然离开这个页面了，但有时桌面右上角会弹出一个信息提醒框，这个就是H5的Notification桌面通知功能。
---
* content
{:toc}

### 正文

有时访问一些网站，我们虽然离开这个页面了，但有时桌面右上角会弹出一个信息提醒框，这个就是H5的Notification桌面通知功能。

代码示例：
```
var notification = new Notification(
    "工单新信息",
    {
        body: "您有1条工单信息，快去查看吧",
        dir: "auto",
        lang: "zh-CN",
        tag: "WoDealNotice",
        icon:'/images/icon.jpg',
        requireInteraction: true,
        renotify: false
    }
);
// 点击事件监听
notification.onclick = function () {
    window.focus();
    notification.close();
    $(".notification-box").trigger("click");
};
// 监听显示事件
notification.onshow = function () { 
    console.log('onshow'); 
};
// 监听错误事件
notification.onerror = function () { 
    console.log('onerror'); 
};
// 监听关闭事件
notification.onclose = function () { 
    console.log('onclose'); 
};

// 如果提示不可用的补充功能
if (window.Notification && window.Notification.permission !== 'granted') {
    $(".notification-box").show();
}
```

解读语法：
```
let myNotification = new Notification(title, options);
```

title  定义一个通知的标题，当它被触发时，它将显示在通知窗口的顶部。

options 可选
    options对象包含应用于通知的任何自定义设置选项。选项有： 

    dir: 显示通知的方向。默认是auto，跟随浏览器语言设置行为，你也可以通过设置ltr和rtl的值来覆盖该行为（虽然大多数浏览器似乎忽略这些设置）
    lang: 通知的语言，如使用代表一个BCP 47语言标签的  DOMString 指定的。请参阅Sitepoint ISO 2字母语言代码页面，以获得简单的参考。
    badge: 一个 USVString 包含用于表示通知的图像的URL, 当没有足够的空间来显示通知本身时。
    body: 一个 DOMString 表示通知的正文，将显示在标题下方。
    tag:  一个 DOMString 代表通知的 一个识别标签。
    icon:  一个 USVString 包含要在通知中显示的图标的URL。
    image: 一个 USVSTring包含要在通知中显示的图像的URL。
    data: 您想要与通知相关联的任意数据。这可以是任何数据类型。
    vibrate: 一个振动模式 vibration pattern 设备的振动硬件在通知触发时发出。
    renotify: 一个 Boolean 指定在新通知替换旧通知后是否应通知用户。默认值为false，这意味着它们不会被通知。
    requireInteraction: 表示通知应保持有效，直到用户点击或关闭它，而不是自动关闭。默认值为false。
    
以下选项列在最新规范中，浏览器中暂不支持：

    silent: 一个 Boolean 指明通知是否应该是无声的,即,不需要发出声音或振动，无论设备设置如何。默认值为false，这意味着它不会保持静默。
    sound:一个 USVString 包含通知触发时要播放的音频文件的URL。
    noscreen: 一个 Boolean 指定通知触发是否应启用设备的屏幕。 默认值为false，这意味着它将启用屏幕。
    sticky: 一个 Boolean 指明通知是否应该是“粘”, 即不易被用户清理。默认值为false，这意味着它不会粘。

上面的示例只有在浏览器给当前站点给了通知权限时，才会有桌面通知，如果是默认授权是弹不出的，我们需要询问授权，看一下下面的用法：
```
Notification.requestPermission(function (permission) {
    if (permission === "granted") {
        var notification = new Notification(
            "工单新信息",{
                body: "您有1条工单信息，快去查看吧",
                dir: "auto",
                lang: "zh-CN",
                tag: "WoDealNotice",
                icon:'/images/icon.jpg',
                requireInteraction: true,
                renotify: false
            });
        // 点击事件监听
        notification.onclick = function () {
            window.focus();
            notification.close();
            $(".notification-box").trigger("click");
        };
        // 监听显示事件
        notification.onshow = function () { console.log('onshow'); };
        // 监听错误事件
        notification.onerror = function () { console.log('onerror'); };
        // 监听关闭事件
        notification.onclose = function () { console.log('onclose'); };
    } else {
        $(".notification-box").show();
    }
});
```

Notification 接口的 requestPermission() 方法请求用户当前来源的权限以显示通知。

#### 封装示例

notify.js：
```
export default class notify{
    constructor(options) {
        this.options = options;
        this.timer = null;
        this.scrollTitle = '';
        this.notify = null;
    }
    //notify通知提示
    requestPermission(){
        if (window.Notification && window.Notification.permission !== 'granted') {
            window.Notification.requestPermission();
        }
    }
    // 是否启用notify通知
    isPermission() {
        return window.Notification && Notification.permission === 'granted';
    }
    // 发送通知
    notifyMessage(){ 
        let title = this.options.title ? this.options.title : '';
        let notification = this.options.notification ? this.options.notification : {};
        let onclick = this.options.onclick ? this.options.onclick : null;
        if(window.Notification){
            this.notify = new Notification(title,notification);
            // this.notifyTitle();
            this.notify.onclick = ()=>{
                onclick && typeof onclick === 'function' && onclick(this.notify);
            }
            this.notify.onshow = ()=>{
                this.options.onshow && typeof this.options.onshow === 'function' && this.options.onshow(this.notify);
            }
            this.notify.onclose = ()=>{
                this.options.onclose && typeof this.options.onclose === 'function' && this.options.onclose(this.notify);
            }
            this.notify.onerror = ()=>{
                this.options.onerror && typeof this.options.onerror === 'function' && this.options.onerror(this.notify);
            }
        }
    }
    // title通知提示
    notifyTitle(){
        console.log(this.options.messageNum,'this.options.messageNum');
        let messageNum = this.options.messageNum ? this.options.messageNum : 0;
        let title = this.options.tabTitle || document.title;
        let intervalNum = this.options.intervalNum ? this.options.intervalNum : 1000;
        this.clearTimer();
        this.timer = setInterval(()=>{
            if(messageNum){
                if(this.options.effect === 'flash'){
                    document.title = this.options.tabTitle === document.title ? `TMS(${this.options.messageNum})` : this.options.tabTitle;
                }else if(this.options.effect === 'scroll'){
                    if (!this.scrollTitle || !this.scrollTitle.slice(1)) {
                        document.title = title;
                        this.scrollTitle = title;
                    } else {
                        this.scrollTitle = this.scrollTitle.slice(1);
                        document.title = this.scrollTitle;
                    }
                }
            }
        },intervalNum);
    }
    closeNotify() {
        this.notify.close();
    };
    // 清除计时器
    clearTimer(){
        this.timer && clearInterval(this.timer);
        this.scrollTitle = '';
        this.notify.close();
        // document.title = this.options.tabTitle;
    }
}
```

调用：
```
this[typeMap[type]["dataWrapper"]] = new notify(
    {
        messageNum: this.totalNumber,
        title: `${typeMap[type]['des']}新信息`,
        effect: "flash",
        tabTitle: "管理系统",
        notification: {
            body: `您有${val[typeMap[type]["info"]]}条${typeMap[type]['des']}消息，快去查看吧`,
            tag: type,
            icon: "/static/assets/images/icon_logo.png",
            requireInteraction: true,
            renotify: false
        }
    },
    onclick:()=>{
        window.focus();
        notify.close();
        this.titleNotify.clearTimer();
        if (type == 1) {
            this.$refs.chatWindow.show();
        } else {
            this.goNewOrder();
        }
    }
);
this[typeMap[type]["dataWrapper"]].notifyMessage();
```

<br/><br/><br/><br/><br/>
### 参考资料 

Web 开发技术请参见 Web API 接口参考请参见 notificationNotification.Notification() <https://developer.mozilla.org/zh-CN/docs/Web/API/Notification/Notification>

Web 开发技术请参见 Web API 接口参考请参见 notificationNotification.requestPermission() <https://developer.mozilla.org/zh-CN/docs/Web/API/notification/requestPermission>

H5 Notification桌面推送消息 <https://blog.csdn.net/CodingNoob/article/details/81090779>

H5的Notification特性 - Web的桌面通知功能 <https://segmentfault.com/a/1190000012127653>

Notification 桌面通知 <https://www.jianshu.com/p/22c408dcf2f7>

