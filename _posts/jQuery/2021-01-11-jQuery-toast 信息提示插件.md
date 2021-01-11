---
layout: post
categories: jQuery
title: jQuery-toast 信息提示插件
meta: jQuery-toast 信息提示插件
---
* content
{:toc}

### 正文

![]({{site.baseurl}}/images/20210111/20210111110816.png)

jQuery-toast 信息提示插件,可以看到动态效果，样式比较养眼。

使用时先 [下载包](https://github.com/scepterscythe/jquery-toast) ，在页面引入：
```javascript
<link type="text/css" rel="stylesheet" href="./toast/jquery.toast.min.css"/>

<script type="text/javascript" src="http://cdn.staticfile.org/jquery/3.0.0/jquery.min.js"></script>
<script type="text/javascript" src="./toast/jquery.toast.min.js"></script>
```

具体使用，有成功、失败、详情、警告、一般等提示样式，可以指定页面显示位置：
```javascript
<script type="text/javascript">
    $(function () {
        var type = "success";
        if (type == "error") {
            $.toast({
                position: 'bottom-left', icon: 'error', stack: false,
                heading: 'Error', text: '请填写备注',
            });
        } else if (type == "success") {
            $.toast({
                position: 'mid-center', icon: 'success', stack: false,
                heading: 'Success', text: '添加成功，请继续添加下一条！',
            });
        } else if (type == "info") {
            $.toast({
                position: 'top-center', icon: 'info', stack: false,
                heading: 'Info', text: '你的活动很多啊！',
            });
        } else if (type == "warning") {
            $.toast({
                position: 'bottom-center', icon: 'warning', stack: false,
                heading: 'Warning', text: '你的活动很多啊！',
            });
        } else {
            $.toast({
                position: 'bottom-right', stack: false,
                text: '欢迎光临！',
            });
        }
    });
</script>
```

#### 1.3.1版

##### 源码

jquery.toast.css文件源码：
```css
/**
 * jQuery toast plugin created by Kamran Ahmed copyright MIT license 2014
 */
.jq-toast-wrap { display: block; position: fixed; width: 250px;  pointer-events: none !important; margin: 0; padding: 0; letter-spacing: normal; z-index: 9000 !important; }
.jq-toast-wrap * { margin: 0; padding: 0; }

.jq-toast-wrap.bottom-left { bottom: 20px; left: 20px; }
.jq-toast-wrap.bottom-right { bottom: 20px; right: 40px; }
.jq-toast-wrap.top-left { top: 20px; left: 20px; }
.jq-toast-wrap.top-right { top: 20px; right: 40px; }

.jq-toast-single { display: block; width: 100%; padding: 10px; margin: 0px 0px 5px; border-radius: 4px; font-size: 12px; font-family: arial, sans-serif; line-height: 17px; position: relative;  pointer-events: all !important; background-color: #444444; color: white; }

.jq-toast-single h2 { font-family: arial, sans-serif; font-size: 14px; margin: 0px 0px 7px; background: none; color: inherit; line-height: inherit; letter-spacing: normal; }
.jq-toast-single a { color: #eee; text-decoration: none; font-weight: bold; border-bottom: 1px solid white; padding-bottom: 3px; font-size: 12px; }

.jq-toast-single ul { margin: 0px 0px 0px 15px; background: none; padding:0px; }
.jq-toast-single ul li { list-style-type: disc !important; line-height: 17px; background: none; margin: 0; padding: 0; letter-spacing: normal; }

.close-jq-toast-single { position: absolute; top: 3px; right: 7px; font-size: 14px; cursor: pointer; }

.jq-toast-loader { display: block; position: absolute; top: -2px; height: 5px; width: 0%; left: 0; border-radius: 5px; background: red; }
.jq-toast-loaded { width: 100%; }
.jq-has-icon { padding: 10px 10px 10px 50px; background-repeat: no-repeat; background-position: 10px; }
.jq-icon-info { background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAYCAYAAADgdz34AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAGwSURBVEhLtZa9SgNBEMc9sUxxRcoUKSzSWIhXpFMhhYWFhaBg4yPYiWCXZxBLERsLRS3EQkEfwCKdjWJAwSKCgoKCcudv4O5YLrt7EzgXhiU3/4+b2ckmwVjJSpKkQ6wAi4gwhT+z3wRBcEz0yjSseUTrcRyfsHsXmD0AmbHOC9Ii8VImnuXBPglHpQ5wwSVM7sNnTG7Za4JwDdCjxyAiH3nyA2mtaTJufiDZ5dCaqlItILh1NHatfN5skvjx9Z38m69CgzuXmZgVrPIGE763Jx9qKsRozWYw6xOHdER+nn2KkO+Bb+UV5CBN6WC6QtBgbRVozrahAbmm6HtUsgtPC19tFdxXZYBOfkbmFJ1VaHA1VAHjd0pp70oTZzvR+EVrx2Ygfdsq6eu55BHYR8hlcki+n+kERUFG8BrA0BwjeAv2M8WLQBtcy+SD6fNsmnB3AlBLrgTtVW1c2QN4bVWLATaIS60J2Du5y1TiJgjSBvFVZgTmwCU+dAZFoPxGEEs8nyHC9Bwe2GvEJv2WXZb0vjdyFT4Cxk3e/kIqlOGoVLwwPevpYHT+00T+hWwXDf4AJAOUqWcDhbwAAAAASUVORK5CYII='); background-color: #31708f; color: #d9edf7; border-color: #bce8f1; }
.jq-icon-warning { background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAYCAYAAADgdz34AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAGYSURBVEhL5ZSvTsNQFMbXZGICMYGYmJhAQIJAICYQPAACiSDB8AiICQQJT4CqQEwgJvYASAQCiZiYmJhAIBATCARJy+9rTsldd8sKu1M0+dLb057v6/lbq/2rK0mS/TRNj9cWNAKPYIJII7gIxCcQ51cvqID+GIEX8ASG4B1bK5gIZFeQfoJdEXOfgX4QAQg7kH2A65yQ87lyxb27sggkAzAuFhbbg1K2kgCkB1bVwyIR9m2L7PRPIhDUIXgGtyKw575yz3lTNs6X4JXnjV+LKM/m3MydnTbtOKIjtz6VhCBq4vSm3ncdrD2lk0VgUXSVKjVDJXJzijW1RQdsU7F77He8u68koNZTz8Oz5yGa6J3H3lZ0xYgXBK2QymlWWA+RWnYhskLBv2vmE+hBMCtbA7KX5drWyRT/2JsqZ2IvfB9Y4bWDNMFbJRFmC9E74SoS0CqulwjkC0+5bpcV1CZ8NMej4pjy0U+doDQsGyo1hzVJttIjhQ7GnBtRFN1UarUlH8F3xict+HY07rEzoUGPlWcjRFRr4/gChZgc3ZL2d8oAAAAASUVORK5CYII='); background-color: #8a6d3b; color: #fcf8e3; border-color: #faebcc; }
.jq-icon-error { background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAYCAYAAADgdz34AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAHOSURBVEhLrZa/SgNBEMZzh0WKCClSCKaIYOED+AAKeQQLG8HWztLCImBrYadgIdY+gIKNYkBFSwu7CAoqCgkkoGBI/E28PdbLZmeDLgzZzcx83/zZ2SSXC1j9fr+I1Hq93g2yxH4iwM1vkoBWAdxCmpzTxfkN2RcyZNaHFIkSo10+8kgxkXIURV5HGxTmFuc75B2RfQkpxHG8aAgaAFa0tAHqYFfQ7Iwe2yhODk8+J4C7yAoRTWI3w/4klGRgR4lO7Rpn9+gvMyWp+uxFh8+H+ARlgN1nJuJuQAYvNkEnwGFck18Er4q3egEc/oO+mhLdKgRyhdNFiacC0rlOCbhNVz4H9FnAYgDBvU3QIioZlJFLJtsoHYRDfiZoUyIxqCtRpVlANq0EU4dApjrtgezPFad5S19Wgjkc0hNVnuF4HjVA6C7QrSIbylB+oZe3aHgBsqlNqKYH48jXyJKMuAbiyVJ8KzaB3eRc0pg9VwQ4niFryI68qiOi3AbjwdsfnAtk0bCjTLJKr6mrD9g8iq/S/B81hguOMlQTnVyG40wAcjnmgsCNESDrjme7wfftP4P7SP4N3CJZdvzoNyGq2c/HWOXJGsvVg+RA/k2MC/wN6I2YA2Pt8GkAAAAASUVORK5CYII='); background-color: #a94442; color: #f2dede; border-color: #ebccd1; }
.jq-icon-success { background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAYCAYAAADgdz34AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAADsSURBVEhLY2AYBfQMgf///3P8+/evAIgvA/FsIF+BavYDDWMBGroaSMMBiE8VC7AZDrIFaMFnii3AZTjUgsUUWUDA8OdAH6iQbQEhw4HyGsPEcKBXBIC4ARhex4G4BsjmweU1soIFaGg/WtoFZRIZdEvIMhxkCCjXIVsATV6gFGACs4Rsw0EGgIIH3QJYJgHSARQZDrWAB+jawzgs+Q2UO49D7jnRSRGoEFRILcdmEMWGI0cm0JJ2QpYA1RDvcmzJEWhABhD/pqrL0S0CWuABKgnRki9lLseS7g2AlqwHWQSKH4oKLrILpRGhEQCw2LiRUIa4lwAAAABJRU5ErkJggg=='); color: #dff0d8; background-color: #3c763d; border-color: #d6e9c6; }
```

jquery.toast.js文件源码：
```javascript
// jQuery toast plugin created by Kamran Ahmed copyright MIT license 2015
if ( typeof Object.create !== 'function' ) {
    Object.create = function( obj ) {
        function F() {}
        F.prototype = obj;
        return new F();
    };
}

(function( $, window, document, undefined ) {

    "use strict";
    
    var Toast = {

        _positionClasses : ['bottom-left', 'bottom-right', 'top-right', 'top-left', 'bottom-center', 'top-center', 'mid-center'],
        _defaultIcons : ['success', 'error', 'info', 'warning'],

        init: function (options, elem) {
            this.prepareOptions(options, $.toast.options);
            this.process();
        },

        prepareOptions: function(options, options_to_extend) {
            var _options = {};
            if ( ( typeof options === 'string' ) || ( options instanceof Array ) ) {
                _options.text = options;
            } else {
                _options = options;
            }
            this.options = $.extend( {}, options_to_extend, _options );
        },

        process: function () {
            this.setup();
            this.addToDom();
            this.position();
            this.bindToast();
            this.animate();
        },

        setup: function () {
            
            var _toastContent = '';
            
            this._toastEl = this._toastEl || $('<div></div>', {
                class : 'jq-toast-single'
            });

            // For the loader on top
            _toastContent += '<span class="jq-toast-loader"></span>';            

            if ( this.options.allowToastClose ) {
                _toastContent += '<span class="close-jq-toast-single">&times;</span>';
            };

            if ( this.options.text instanceof Array ) {

                if ( this.options.heading ) {
                    _toastContent +='<h2 class="jq-toast-heading">' + this.options.heading + '</h2>';
                };

                _toastContent += '<ul class="jq-toast-ul">';
                for (var i = 0; i < this.options.text.length; i++) {
                    _toastContent += '<li class="jq-toast-li" id="jq-toast-item-' + i + '">' + this.options.text[i] + '</li>';
                }
                _toastContent += '</ul>';

            } else {
                if ( this.options.heading ) {
                    _toastContent +='<h2 class="jq-toast-heading">' + this.options.heading + '</h2>';
                };
                _toastContent += this.options.text;
            }

            this._toastEl.html( _toastContent );

            if ( this.options.bgColor !== false ) {
                this._toastEl.css("background-color", this.options.bgColor);
            };

            if ( this.options.textColor !== false ) {
                this._toastEl.css("color", this.options.textColor);
            };

            if ( this.options.textAlign ) {
                this._toastEl.css('text-align', this.options.textAlign);
            }

            if ( this.options.icon !== false ) {
                this._toastEl.addClass('jq-has-icon');

                if ( $.inArray(this.options.icon, this._defaultIcons) !== -1 ) {
                    this._toastEl.addClass('jq-icon-' + this.options.icon);
                };
            };

            if ( this.options.class !== false ){
                this._toastEl.addClass(this.options.class)
            }
        },

        position: function () {
            if ( ( typeof this.options.position === 'string' ) && ( $.inArray( this.options.position, this._positionClasses) !== -1 ) ) {

                if ( this.options.position === 'bottom-center' ) {
                    this._container.css({
                        left: ( $(window).outerWidth() / 2 ) - this._container.outerWidth()/2,
                        bottom: 20
                    });
                } else if ( this.options.position === 'top-center' ) {
                    this._container.css({
                        left: ( $(window).outerWidth() / 2 ) - this._container.outerWidth()/2,
                        top: 20
                    });
                } else if ( this.options.position === 'mid-center' ) {
                    this._container.css({
                        left: ( $(window).outerWidth() / 2 ) - this._container.outerWidth()/2,
                        top: ( $(window).outerHeight() / 2 ) - this._container.outerHeight()/2
                    });
                } else {
                    this._container.addClass( this.options.position );
                }

            } else if ( typeof this.options.position === 'object' ) {
                this._container.css({
                    top : this.options.position.top ? this.options.position.top : 'auto',
                    bottom : this.options.position.bottom ? this.options.position.bottom : 'auto',
                    left : this.options.position.left ? this.options.position.left : 'auto',
                    right : this.options.position.right ? this.options.position.right : 'auto'
                });
            } else {
                this._container.addClass( 'bottom-left' );
            }
        },

        bindToast: function () {

            var that = this;

            this._toastEl.on('afterShown', function () {
                that.processLoader();
            });

            this._toastEl.find('.close-jq-toast-single').on('click', function ( e ) {

                e.preventDefault();

                if( that.options.showHideTransition === 'fade') {
                    that._toastEl.trigger('beforeHide');
                    that._toastEl.fadeOut(function () {
                        that._toastEl.trigger('afterHidden');
                    });
                } else if ( that.options.showHideTransition === 'slide' ) {
                    that._toastEl.trigger('beforeHide');
                    that._toastEl.slideUp(function () {
                        that._toastEl.trigger('afterHidden');
                    });
                } else {
                    that._toastEl.trigger('beforeHide');
                    that._toastEl.hide(function () {
                        that._toastEl.trigger('afterHidden');
                    });
                }
            });

            if ( typeof this.options.beforeShow == 'function' ) {
                this._toastEl.on('beforeShow', function () {
                    that.options.beforeShow();
                });
            };

            if ( typeof this.options.afterShown == 'function' ) {
                this._toastEl.on('afterShown', function () {
                    that.options.afterShown();
                });
            };

            if ( typeof this.options.beforeHide == 'function' ) {
                this._toastEl.on('beforeHide', function () {
                    that.options.beforeHide();
                });
            };

            if ( typeof this.options.afterHidden == 'function' ) {
                this._toastEl.on('afterHidden', function () {
                    that.options.afterHidden();
                });
            };          
        },

        addToDom: function () {

             var _container = $('.jq-toast-wrap');
             
             if ( _container.length === 0 ) {
                
                _container = $('<div></div>',{
                    class: "jq-toast-wrap",
                    role: "alert",
                    "aria-live": "polite"
                });

                $('body').append( _container );

             } else if ( !this.options.stack || isNaN( parseInt(this.options.stack, 10) ) ) {
                _container.empty();
             }

             _container.find('.jq-toast-single:hidden').remove();

             _container.append( this._toastEl );

            if ( this.options.stack && !isNaN( parseInt( this.options.stack ), 10 ) ) {
                
                var _prevToastCount = _container.find('.jq-toast-single').length,
                    _extToastCount = _prevToastCount - this.options.stack;

                if ( _extToastCount > 0 ) {
                    $('.jq-toast-wrap').find('.jq-toast-single').slice(0, _extToastCount).remove();
                };

            }

            this._container = _container;
        },

        canAutoHide: function () {
            return ( this.options.hideAfter !== false ) && !isNaN( parseInt( this.options.hideAfter, 10 ) );
        },

        processLoader: function () {
            // Show the loader only, if auto-hide is on and loader is demanded
            if (!this.canAutoHide() || this.options.loader === false) {
                return false;
            }

            var loader = this._toastEl.find('.jq-toast-loader');

            // 400 is the default time that jquery uses for fade/slide
            // Divide by 1000 for milliseconds to seconds conversion
            var transitionTime = (this.options.hideAfter - 400) / 1000 + 's';
            var loaderBg = this.options.loaderBg;

            var style = loader.attr('style') || '';
            style = style.substring(0, style.indexOf('-webkit-transition')); // Remove the last transition definition

            style += '-webkit-transition: width ' + transitionTime + ' ease-in; \
                      -o-transition: width ' + transitionTime + ' ease-in; \
                      transition: width ' + transitionTime + ' ease-in; \
                      background-color: ' + loaderBg + ';';


            loader.attr('style', style).addClass('jq-toast-loaded');
        },

        animate: function () {

            var that = this;

            this._toastEl.hide();

            this._toastEl.trigger('beforeShow');

            if ( this.options.showHideTransition.toLowerCase() === 'fade' ) {
                this._toastEl.fadeIn(function ( ){
                    that._toastEl.trigger('afterShown');
                });
            } else if ( this.options.showHideTransition.toLowerCase() === 'slide' ) {
                this._toastEl.slideDown(function ( ){
                    that._toastEl.trigger('afterShown');
                });
            } else {
                this._toastEl.show(function ( ){
                    that._toastEl.trigger('afterShown');
                });
            }

            if (this.canAutoHide()) {

                var that = this;

                window.setTimeout(function(){
                    
                    if ( that.options.showHideTransition.toLowerCase() === 'fade' ) {
                        that._toastEl.trigger('beforeHide');
                        that._toastEl.fadeOut(function () {
                            that._toastEl.trigger('afterHidden');
                        });
                    } else if ( that.options.showHideTransition.toLowerCase() === 'slide' ) {
                        that._toastEl.trigger('beforeHide');
                        that._toastEl.slideUp(function () {
                            that._toastEl.trigger('afterHidden');
                        });
                    } else {
                        that._toastEl.trigger('beforeHide');
                        that._toastEl.hide(function () {
                            that._toastEl.trigger('afterHidden');
                        });
                    }

                }, this.options.hideAfter);
            };
        },

        reset: function ( resetWhat ) {

            if ( resetWhat === 'all' ) {
                $('.jq-toast-wrap').remove();
            } else {
                this._toastEl.remove();
            }

        },

        update: function(options) {
            this.prepareOptions(options, this.options);
            this.setup();
            this.bindToast();
        }
    };
    
    $.toast = function(options) {
        var toast = Object.create(Toast);
        toast.init(options, this);

        return {
            
            reset: function ( what ) {
                toast.reset( what );
            },

            update: function( options ) {
                toast.update( options );
            }
        }
    };

    $.toast.options = {
        text: '',
        heading: '',
        showHideTransition: 'fade',
        allowToastClose: true,
        hideAfter: 3000,
        loader: true,
        loaderBg: '#9EC600',
        stack: 5,
        position: 'bottom-left',
        bgColor: false,
        textColor: false,
        textAlign: 'left',
        icon: false,
        beforeShow: function () {},
        afterShown: function () {},
        beforeHide: function () {},
        afterHidden: function () {}
    };

})( jQuery, window, document );
```

##### 官方文档

A plugin to show highly customizable notifications to the user.

###### How to use

- You can install the plugin via Bower:

    ```js
    bower install jquery-toast-plugin
    ```

  or via `npm`

    ```js
    npm install jquery-toast-plugin
    ```

  Or directly download the repository and place the content of `dist` wherever you can access them.
- Include the CSS and JS files.
- Simply do ```$.toast('Toast message to be shown')``` Of course it would be the world's simplest toast message but believe me **you can do a lot more** with the options.

###### Demo
For some quick demos and a detailed documentation accompanied by the demos for each of the available options can be accessed through http://kamranahmed.info/toast

####### Quick usage examples
**Simple textual toast**
```javascript
// Non sticky version
$.toast("Lorem ipsum dolor sit amet, consectetur adipisicing elit. Hic, consequuntur doloremque eveniet eius eaque dicta repudiandae illo ullam. Minima itaque sint magnam dolorum asperiores repudiandae dignissimos expedita, voluptatum vitae velit.")
// Sticky version
$.toast({
  text : "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Hic, consequuntur doloremque eveniet eius eaque dicta repudiandae illo ullam. Minima itaque sint magnam dolorum asperiores repudiandae dignissimos expedita, voluptatum vitae velit.",
  hideAfter : false
})
```

**Toast using HTML as a text**
```javascript
// Non sticky
$.toast("Let's test some HTML stuff... <a href='#'>github</a>")
// sticky
$.toast({
  text : "<strong>Remember!</strong> You can <span style='font-weight: bold; color:red;' class='horribly-styled'>always</span> introduce your own × HTML and <span style='font-size: 18px;'>CSS</span> in the toast.",
  hideAfter : false
})
```

**Unordered list elements as the text of toast using array**
```javascript
// Non sticky version
$.toast(["Ubuntu : One of it's kind", "Sublime Text : Productivity unleashed", "HeidiSQL : Just love it", "Github : Just Lovely"])
// Sticky version
$.toast({
  text : ["Ubuntu : One of it's kind", "Sublime Text : Productivity unleashed", "HeidiSQL : Just love it", "Github : Just Lovely"],
  hideAfter : false
})
```

**Changing the animations**
```javascript
$.toast({ 
  text : "Let's test some HTML stuff... <a href='#'>github</a>", 
  showHideTransition : 'slide'  // It can be plain, fade or slide
})
```

**Changing the formatting**
```javascript
$.toast({ 
  text : "Let's test some HTML stuff... <a href='#'>github</a>", 
  showHideTransition : 'slide',  // It can be plain, fade or slide
  bgColor : 'blue',              // Background color for toast
  textColor : '#eee',            // text color
  allowToastClose : false,       // Show the close button or not
  hideAfter : 5000,              // `false` to make it sticky or time in miliseconds to hide after
  stack : 5,                     // `fakse` to show one stack at a time count showing the number of toasts that can be shown at once
  textAlign : 'left',            // Alignment of text i.e. left, right, center
  position : 'bottom-left'       // bottom-left or bottom-right or bottom-center or top-left or top-right or top-center or mid-center or an object representing the left, right, top, bottom values to position the toast on page
})
```

**Resetting the toast**
```javascript
var myToast = $.toast('Some toast that needs to be removed.');
myToast.reset(); // remove the toast "Some toast that needs to be removed"
```
What if I want to reset all the toasts at once? You may ask. Well in that case, you can do the following:
```javascript
$.toast().reset('all');
```

**Updating the toast**
Suppose, you had shown some toast upon the page, a sticky toast for example and now you want to update the toast. You can do the following

```javascript
var myToast = $.toast({
  text : 'Some toast that needs to show the success message after the ajax call.',
  hideAfter : false,
  bgColor : '#E01A31'
});

window.setTimeout(function(){
  myToast.update({
    text : '<strong>Updated after a few seconds</strong>',
    bgColor : '#23B65D'
  });
}, 5000);
```
To learn more about how to use and customize it, head to <a href="http://kamranahmed.info/toast" target="_blank">http://kamranahmed.info/toast</a>. Also you can find a customizer there that will let you modify the look and feel of the toast however you like it.

<hr>

You can simply download the repo or if you are in rush the <a href="https://raw.githubusercontent.com/kamranahmedse/jquery-toast-plugin/master/jquery.toast.min.css" target="_blank">minified CSS</a> or <a href="https://raw.githubusercontent.com/kamranahmedse/jquery-toast-plugin/master/jquery.toast.css">non-minified CSS</a> can be found and <a href="https://raw.githubusercontent.com/kamranahmedse/jquery-toast-plugin/master/jquery.toast.min.js" target="_blank">minified JS</a> and <a href="https://raw.githubusercontent.com/kamranahmedse/jquery-toast-plugin/master/jquery.toast.js" target="_blank">non minified JS</a> can also be found.

###### Features
<ul>
  <li>Show different types of toasts i.e. informational, warning, errors and success</li>
  <li>Custom <strong>toast background color</strong> and <strong>text color</strong></li>
  <li>Ability to <strong>hack the CSS</strong> to add your own thing</li>
  <li>
    <strong>Text can be</strong> provided in the form of
    <ul>
      <li><strong>Array</strong> (It's elements will be changed to an un ordered list)</li>
      <li><strong>Simple text</strong></li>
      <li><strong>HTML</strong></li>
    </ul>
  </li>
  <li><strong>Events support</strong> i.e. <code>beforeHide</code>, <code>afterHidden</code>, <code>beforeShow</code>, <code>afterShown</code></li>

  <li><code>Fade</code> and <code>Slide</code> show/hide transitions support (More to come)</li>
  <li>Supports showing the loader for the toast</li>
  <li>You can <strong>position the toast, wherever you want</strong> there is support for <code>top-left</code>, <code>top-right</code> <code>bottom-left</code> and <strong>bottom-right</strong>, <code>top-center</code>, <code>bottom-center</code> and <code>mid-center</code> ...sighs! That's a whole lot of options, isn't it? No, you say. Ok then here is the most exciting thing, you can also introduce <strong>your own positioning</strong> just <strong>by passing a simple js object</strong> containing <code>{ top: - , bottom: -, left: -, right: - }</code> </li>

  <li>Ability to add <strong>sticky toast</strong></li>

  <li>Optional <strong>stack length can be defined</strong> (i.e. maximum number of toasts that can be shown at once)</li>

</ul>

Please report any bugs or features you would like added.

##### 示例

提示框定制：

```javascript
$.toast({
    text: "Hey there fellas, here is a simple toast. Change the options above and then see the code that generates that toast", // Text that is to be shown in the toast
    heading: 'How to use', // Optional heading to be shown on the toast
    icon: 'warning', // Type of toast icon
    showHideTransition: 'fade', // fade, slide or plain
    allowToastClose: true, // Boolean value true or false
    hideAfter: 3000, // false to make it sticky or number representing the miliseconds as time after which toast needs to be hidden
    stack: 5, // false if there should be only one toast at a time or a number representing the maximum number of toasts to be shown at a time
    position: 'bottom-left', // bottom-left or bottom-right or bottom-center or top-left or top-right or top-center or mid-center or an object representing the left, right, top, bottom values
    position: { left : 20, right : 20, top : 20, bottom : 20 }, // bottom-left or bottom-right or bottom-center or top-left or top-right or top-center or mid-center or an object representing the left, right, top, bottom values


    textAlign: 'left',  // Text alignment i.e. left, right or center
    loader: true,  // Whether to show loader or not. True by default
    loaderBg: '#9EC600',  // Background color of the toast loader
    beforeShow: function () {}, // will be triggered before the toast is shown
    afterShown: function () {}, // will be triggered after the toat has been shown
    beforeHide: function () {}, // will be triggered before the toast gets hidden
    afterHidden: function () {}  // will be triggered after the toast has been hidden
});
```

#### 1.0.1版

##### 源码

jquery.toast.css文件源码：
```css
.toast{ position:fixed; list-style: none; padding: 0; top:0; z-index: 999999; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size:14px; line-height:20px; }
.toast li{ margin: 10px 0 0 0; display:block; background-color:#fcf8e3; color:#c09853; border:1px solid #fbeed5; padding:5px 10px; border-radius: 4px; -webkit-border-radius: 4px; text-shadow: 0 1px 0 rgba(255, 255, 255, 0.5); box-shadow: 0 2px 5px rgba(0,0,0,.15); -webkit-box-shadow: 0 2px 5px rgba(0,0,0,.15); }
.toast li:first-child{ margin-top:0; }
.toast li.danger{ color: #b94a48; background-color: #f2dede; border-color: #eed3d7; }
.toast li.info{ color: #3a87ad; background-color: #d9edf7; border-color: #bce8f1; }
.toast li.success{ color: #468847; background-color: #dff0d8; border-color: #d6e9c6; }
.toast button.close{ background: none; border: none; font-weight: bold; font-size: 20px; line-height: 20px; float: right; padding: 0; margin: 0 0 0 5px; color: rgba(0,0,0,.25); cursor: pointer; }
.toast h1, .toast h2, .toast h3, .toast h4 { display: inline; }
```

jquery.toast.js文件源码：
```javascript
(function($){
    var th = null, cf = null, toast = function(m,o){
        // fix option type
        o = $.extend({ duration: 5000, sticky: false, 'type': ''}, o);
        typeof o.duration === 'number' || (o.duration = 5000);
        typeof o.sticky === 'boolean' || (o.sticky = false);
        typeof o.type === 'string' || (o.type = '');
        // create host on first call
        if(!th){ 
            // get/fix config
            cf = toast.config;
            th = $('<ul></ul>').addClass('toast').appendTo(document.body).hide();
            typeof cf.width === 'number' || (cf.width = 500);
            typeof cf.align === 'string' || (cf.align = 'center');
            typeof cf.closeForStickyOnly === 'boolean' || (cf.closeForStickyOnly = false);
            th.width(cf.width);
            (cf.align === 'left' || cf.align === 'right') && th.css('margin','5px').css(cf.align, '0') || th.css({left: '50%', margin: '5px 0 0 -' + (cf.width / 2) + 'px'});
        }
        // create toast
        var ti = $('<li></li>').hide().html(m).appendTo(th), cb = $('<button>&times;</button>').addClass('close').prependTo(ti), to = null;
        // setup close button
        cb.click(function(){
            clearTimeout(to);
            ti.animate({ height: 0, opacity: 0}, 'fast', function(){
                ti.remove();
                th.children().length || th.removeClass('active').hide();
            });
        });
        cf.closeForStickyOnly && !o.sticky && cb.hide();
        // add type class
        o.type !== '' && ti.addClass(o.type);
        // show host if necessary
        !th.hasClass('active') && th.addClass('active').show();
        // setup timeout unless sticky
        !o.sticky && o.duration > 0 && (to = setTimeout(function(){ cb.click(); }, o.duration));
        // show toast
        ti.fadeIn('normal');
    };
    toast.config = { width: 500, align: 'center', closeForStickyOnly: true };
    $.extend({ toast: toast });
})(jQuery);
```

##### 官方文档

**jquery.toast**

A simple jQuery "toast" message plugin. You can view the example page [here](http://htmlpreview.github.com/?https://github.com/Soldier-B/jquery.toast/blob/master/example/jquery.toast.html).


###### Required Files
    <link rel="stylesheet" type="text/css" href="jquery.toast.min.css" />
    <script type="text/javascript" src="jquery.js"></script>
    <script type="text/javascript" src="jquery.toast.min.js"></script>

###### Usage

    $.toast('content', options);

Example:

    $.toast('<h4>Hello, world!</h4> Your content here!');

or

    $.toast('<h4>Error!</h4> Something went wrong.', {sticky: true, type: 'danger'});

###### Options

Options include toast duration, stickiness, and toast type.

* **duration** - Length of time in milliseconds a non-sticky toast will be displayed. (default: 5000)
* **sticky** - Determines if toast notification is sticky or not. (default: false)
* **type** - Determines visual style for toast notification. Values can be any valid css class name (allowing you to define your own toast styles) or any of the following: blank, 'danger', 'info', 'success'. (default is blank)


###### Configuration

Before using the plugin you have the option to define a few global properties. After the first toast has been made, they will be locked in.
These properties include toast width, page alignment, and close button status.

Example:

    $.toast.config.width = 800;
    $.toast.config.align = 'right';
    $.toast.config.closeForStickyOnly = false;

* **width** - Width of toast items in pixels. (default: 500)
* **align** - Alignment of toasts in the page. Possibly values are 'left', 'center', and 'right'. (default: 'center')
* **closeForStickyOnly** - Determines if the close button is displayed for all toasts or just those that are sticky. (default: true)

##### 示例

这是1.0.1版本的例子：

```
<html>
    <head>
        <style type="text/css">
            body{ margin: 0; padding: 0; font-family: sans-serif; } body>div { width:960px; margin: 0 auto; } a { padding: 4px 8px; }
        </style>
        <link rel="stylesheet" type="text/css" href="../jquery.toast/jquery.toast.css" />
        <script type="text/javascript" src="http://cdn.staticfile.org/jquery/3.0.0/jquery.min.js"></script>
        <script type="text/javascript" src="../jquery.toast/jquery.toast.js"></script>
        <script type="text/javascript">
            function createToast(t){
                var message = 'Hi, I\m just your every day, average kind of toast.';
                var options = {
                    duration: Math.floor(Math.random() * 4001) + 1000,
                    sticky: !!Math.round(Math.random() * 1),
                    type: t
                };
                
                switch(t){
                    case 'danger': message = '<h4>Danger!</h4> Oh no. You\'ve activated a dangerous toast. Beware as it was may (or may not) be sticky.'; break;
                    case 'info': message = '<h4>FYI</h4> I\'m a toast and I just wanted you to know that.'; break;
                    case 'success': message = '<h4>Great!</h4> You\'ve made a toast. Now let\'s\ toast to you.'; break;
                }
                
                $.toast(message, options);
            }
            
            $(document).ready(function(){
                $.toast.config.align = 'right';
                $.toast.config.width = 400;
                
                $('a').click(function(){
                    createToast($(this).attr('class'));
                    return false;
                });
            });
        </script>
    </head>
    <body>
        <div>
            <p>
                <a href="#" class="">Default Toast</a>
                <a href="#" class="danger">Danger Toast</a>
                <a href="#" class="info">Info Toast</a>
                <a href="#" class="success">Success Toast</a>
            </p>
        </div>
    </body>
</html>
```

![]({{site.baseurl}}/images/20210111/20210111115213.png)

<br/><br/><br/><br/><br/>
### 参考资料 

jquery-toast 源码包位置 <https://github.com/scepterscythe/jquery-toast>

<https://github.com/kamranahmedse/jquery-toast-plugin>

