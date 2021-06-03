---
layout: post
categories: PHP
title: PHP ob_start缓冲区使用举例
meta: ob_start缓冲区使用举例
---
* content
{:toc}

### 正文

从一个网址或文件读取内容，然后写入本地文件：

```
$uri = "内容地址";
// 上传路径
$uploadfile = realpath(Yii::$app->getBasePath().'/web/tmp/test.txt');   // 文件绝对地址
// 开始缓冲
ob_start();
readfile($uri);
$file = ob_get_contents();
// 清除缓冲
ob_end_clean();
$size = strlen($file);
$src = fopen($uploadfile, 'a');
fwrite($src, $file);
fclose($src);

// $uploadfile  文件地址
// $size  文件大小
```

readfile 读取文件并写入到输出缓冲:

```
readfile ( string $filename , bool $use_include_path = false , resource $context = ? ) : int


参数
    
    filename
    
        要读取的文件名。
    use_include_path
    
        想要在 include_path 中搜索文件，可使用这个可选的第二个参数，设为 true。
    context
    
        Stream 上下文（context） resource。

返回值

    成功时返回从文件中读入的字节数， 或者在失败时返回 false

错误／异常

    失败时抛出E_WARNING警告。

```

### 函数原型

```
/**
 * Turn on output buffering
 * @link https://php.net/manual/en/function.ob-start.php
 * @param callback $callback [optional] <p>
 * An optional output_callback function may be
 * specified. This function takes a string as a parameter and should
 * return a string. The function will be called when
 * the output buffer is flushed (sent) or cleaned (with
 * ob_flush, ob_clean or similar
 * function) or when the output buffer
 * is flushed to the browser at the end of the request. When
 * output_callback is called, it will receive the
 * contents of the output buffer as its parameter and is expected to
 * return a new output buffer as a result, which will be sent to the
 * browser. If the output_callback is not a
 * callable function, this function will return false.
 * </p>
 * <p>
 * If the callback function has two parameters, the second parameter is
 * filled with a bit-field consisting of
 * PHP_OUTPUT_HANDLER_START,
 * PHP_OUTPUT_HANDLER_CONT and
 * PHP_OUTPUT_HANDLER_END.
 * </p>
 * <p>
 * If output_callback returns false original
 * input is sent to the browser.
 * </p>
 * <p>
 * The output_callback parameter may be bypassed
 * by passing a null value.
 * </p>
 * <p>
 * ob_end_clean, ob_end_flush,
 * ob_clean, ob_flush and
 * ob_start may not be called from a callback
 * function. If you call them from callback function, the behavior is
 * undefined. If you would like to delete the contents of a buffer,
 * return "" (a null string) from callback function.
 * You can't even call functions using the output buffering functions like
 * print_r($expression, true) or
 * highlight_file($filename, true) from a callback
 * function.
 * </p>
 * <p>
 * In PHP 4.0.4, ob_gzhandler was introduced to
 * facilitate sending gz-encoded data to web browsers that support
 * compressed web pages. ob_gzhandler determines
 * what type of content encoding the browser will accept and will return
 * its output accordingly.
 * </p>
 * @param int $chunk_size [optional] <p>
 * If the optional parameter chunk_size is passed, the
 * buffer will be flushed after any output call which causes the buffer's
 * length to equal or exceed chunk_size.
 * Default value 0 means that the function is called only in the end,
 * other special value 1 sets chunk_size to 4096.
 * </p>
 * @param int $flags [optional] <p>
 * The flags parameter is a bitmask that controls the operations that can be performed on the output buffer.
 * The default is to allow output buffers to be cleaned, flushed and removed, which can be set explicitly via
 * PHP_OUTPUT_HANDLER_CLEANABLE | PHP_OUTPUT_HANDLER_FLUSHABLE | PHP_OUTPUT_HANDLER_REMOVABLE, or PHP_OUTPUT_HANDLER_STDFLAGS as shorthand.
 * </p>
 * @return bool true on success or false on failure.
 */
function ob_start ($callback = null, $chunk_size = null, $flags = PHP_OUTPUT_HANDLER_STDFLAGS) {}

/**
 * Flush (send) the output buffer
 * @link https://php.net/manual/en/function.ob-flush.php
 * @return void
 */
function ob_flush () {}

/**
 * Clean (erase) the output buffer
 * @link https://php.net/manual/en/function.ob-clean.php
 * @return void
 */
function ob_clean () {}

/**
 * Flush (send) the output buffer and turn off output buffering
 * @link https://php.net/manual/en/function.ob-end-flush.php
 * @return bool true on success or false on failure. Reasons for failure are first that you called the
 * function without an active buffer or that for some reason a buffer could
 * not be deleted (possible for special buffer).
 */
function ob_end_flush () {}

/**
 * Clean (erase) the output buffer and turn off output buffering
 * @link https://php.net/manual/en/function.ob-end-clean.php
 * @return bool true on success or false on failure. Reasons for failure are first that you called the
 * function without an active buffer or that for some reason a buffer could
 * not be deleted (possible for special buffer).
 */
function ob_end_clean () {}

/**
 * Flush the output buffer, return it as a string and turn off output buffering
 * @link https://php.net/manual/en/function.ob-get-flush.php
 * @return string|false the output buffer or false if no buffering is active.
 */
function ob_get_flush () {}

/**
 * Get current buffer contents and delete current output buffer
 * @link https://php.net/manual/en/function.ob-get-clean.php
 * @return string|false the contents of the output buffer and end output buffering.
 * If output buffering isn't active then false is returned.
 */
function ob_get_clean () {}

/**
 * Return the length of the output buffer
 * @link https://php.net/manual/en/function.ob-get-length.php
 * @return int|false the length of the output buffer contents or false if no
 * buffering is active.
 */
function ob_get_length () {}

/**
 * Return the nesting level of the output buffering mechanism
 * @link https://php.net/manual/en/function.ob-get-level.php
 * @return int the level of nested output buffering handlers or zero if output
 * buffering is not active.
 */
function ob_get_level () {}

/**
 * Get status of output buffers
 * @link https://php.net/manual/en/function.ob-get-status.php
 * @param bool $full_status [optional] <p>
 * true to return all active output buffer levels. If false or not
 * set, only the top level output buffer is returned.
 * </p>
 * @return array If called without the full_status parameter
 * or with full_status = false a simple array
 * with the following elements is returned:
 * <pre>
 * Array
 * (
 *     [level] => 2
 *     [type] => 0
 *     [status] => 0
 *     [name] => URL-Rewriter
 *     [del] => 1
 * )
 * </pre>
 * <table>
 * <tr><th>Key</th><th>Value</th></tr>
 * <tr><td>level</td><td>Output nesting level</td></tr>
 * <tr><td>type</td><td><em>PHP_OUTPUT_HANDLER_INTERNAL (0)</em> or <em>PHP_OUTPUT_HANDLER_USER (1)</em></td></tr>
 * <tr><td>status</td><td>One of <em>PHP_OUTPUT_HANDLER_START</em> (0), <em>PHP_OUTPUT_HANDLER_CONT</em> (1) or <em>PHP_OUTPUT_HANDLER_END</em> (2)</td></tr>
 * <tr><td>name</td><td>Name of active output handler or &#039; default output handler&#039; if none is set</td></tr>
 * <tr><td>del</td><td>Erase-flag as set by ob_start()</td></tr>
 * </table>
 * <p>
 * If called with full_status = TRUE an array with one element for each active output buffer
 * level is returned. The output level is used as key of the top level array and each array
 * element itself is another array holding status information on one active output level.
 * </p>
 * <pre>
 * Array
 * (
 *     [0] => Array
 *         (
 *             [chunk_size] => 0
 *             [size] => 40960
 *             [block_size] => 10240
 *             [type] => 1
 *             [status] => 0
 *             [name] => default output handler
 *             [del] => 1
 *         )
 *
 *     [1] => Array
 *         (
 *             [chunk_size] => 0
 *             [size] => 40960
 *             [block_size] => 10240
 *             [type] => 0
 *             [buffer_size] => 0
 *             [status] => 0
 *             [name] => URL-Rewriter
 *             [del] => 1
 *         )
 *
 * )
 * </pre>
 * <p> The full output contains these additional elements:</p>
 * <table>
 * <tr><th>Key</th><th>Value</th></tr>
 * <tr><td>chunk_size</td><td>Chunk size as set by ob_start()</td></tr>
 * <tr><td>size</td><td>...</td></tr>
 * <tr><td>blocksize</td><td>...</td></tr>
 * </table>
 */
function ob_get_status ($full_status = null) {}

/**
 * Return the contents of the output buffer
 * @link https://php.net/manual/en/function.ob-get-contents.php
 * @return string|false This will return the contents of the output buffer or false, if output
 * buffering isn't active.
 */
function ob_get_contents () {}

/**
 * Turn implicit flush on/off
 * @link https://php.net/manual/en/function.ob-implicit-flush.php
 * @param bool $enable [optional] <p>
 * true to turn implicit flushing on, false otherwise.
 * </p>
 * @return void
 */
function ob_implicit_flush (bool $enable = true) {}

/**
 * List all output handlers in use
 * @link https://php.net/manual/en/function.ob-list-handlers.php
 * @return array This will return an array with the output handlers in use (if any). If
 * output_buffering is enabled or
 * an anonymous function was used with ob_start,
 * ob_list_handlers will return "default output
 * handler".
 */
function ob_list_handlers () {}
```

<br/><br/><br/><br/><br/>
### 参考资料

<http://www.php.net/manual/zh/function.ob-start.php>

<https://www.cnblogs.com/w10234/p/5480670.html>

readfile() 函数 <http://www.w3school.com.cn/php/func_filesystem_readfile.asp>

fopen() 函数 <http://www.w3school.com.cn/php/func_filesystem_fopen.asp>

fwrite() 函数 <http://www.w3school.com.cn/php/func_filesystem_fwrite.asp>

PHP 手册 函数参考 文件系统相关扩展 文件系统 文件系统函数 readfile <https://www.php.net/manual/zh/function.readfile.php>




