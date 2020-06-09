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

H5 Notification桌面推送消息 <https://blog.csdn.net/CodingNoob/article/details/81090779>

H5的Notification特性 - Web的桌面通知功能 <https://segmentfault.com/a/1190000012127653>



