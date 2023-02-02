---
layout: post
categories: PHP
title: PHP socket使用
meta: PHP socket使用
---
* content
{:toc}

## WebSocket客户端实现

PHP通过TCP连接向WebSocket服务器发送数据。

common/widgets/WSClient.php：
```
namespace common\widgets;

/**
 * Websocket client base class
 */
class WSClient
{
    const hashstring = "258EAFA5-E914-47DA-95CA-C5AB0DC85B11";

    public $host;
    public $port;
    public $uri;
    public $secure;
    public $origin = 'null';

    public $errno = 0;
    public $errstr = "";

    private $async = false;
    private $socket = false;
    private $curframe;
    private $recvframes;

    function __construct($host, $uri = '/', $secure = false, $port = false)
    {
        $this->host = $host;
        $this->uri = $uri;
        if ($port === false) {
            $this->port = $secure ? 443 : 80;
        } else {
            $this->port = $port;
        }
        $this->secure = $secure;
        $this->recvframes = array();
    }

    function __destruct()
    {
        $this->disconnect();
    }

    /*
     * Connect to websocket server, switch protocols
     * name: WSClient::connect
     * @param boolean $async
     * @return boolean
     *
     */
    function connect($async = false)
    {
        $this->async = $async;

        $remote = ($this->secure ? 'tls' : 'tcp') . '://' . $this->host . ':' . $this->port;
        $this->socket = @stream_socket_client($remote, $this->errno, $this->errstr);
        if ($this->socket === false) return false;

        /*
            Create and send HTTP request
        */
        $seckey = base64_encode($this->makeKey());
        $request = "GET $this->uri HTTP/1.1\r\n".
            "Connection: Upgrade\r\n".
            "Upgrade: websocket\r\n".
            "Pragma: no-cache\r\n".
            "Cache-Control: no-cache\r\n".
            "Host: $this->host\r\n".
            "Origin: $this->origin\r\n".
            "Sec-WebSocket-Version: 13\r\n".
            "Sec-WebSocket-Key: $seckey\r\n".
            "\r\n";

        if (!$this->fwrite_stream($request)) {
            $this->errstr = "Error sending request";
            $this->disconnect();
            return false;
        }

        /*
            Read and check HTTP response
        */
        $response = array();
        do {
            $line = fgets($this->socket, 1024);
            if ($line === false) {
                $this->errstr = "Error reading response";
                $this->disconnect();
                return false;
            }
            $response[] = trim($line);
        } while (strlen(trim($line)) > 0);

//		var_dump($response);
        $parsed = $this->parseResponse($response);
//		var_dump($parsed);

        if ($parsed['responseCode'] != 101) {
            $this->errstr = "Invalid response code";
            $this->disconnect();
            return false;
        }

        if (!isset($parsed['headers']['Upgrade']) || strcasecmp($parsed['headers']['Upgrade'], 'websocket') ||
            !isset($parsed['headers']['Connection']) || strcasecmp($parsed['headers']['Connection'], 'Upgrade')) {
            $this->errstr = "Invalid response headers";
            $this->disconnect();
            return false;
        }

//		echo base64_decode($parsed['headers']['Sec-WebSocket-Accept'])."\n";
//		echo sha1($seckey.self::hashstring, true)."\n";

        if (!isset($parsed['headers']['Sec-WebSocket-Accept']) || strcmp(base64_decode($parsed['headers']['Sec-WebSocket-Accept']), sha1($seckey.self::hashstring, true))) {
            $this->errstr = "Invalid security key";
            $this->disconnect();
            return false;
        }

        $this->curframe = new WSFrame();

        return true;
    }

    /*
     * Disconnect from server
     * name: WSClient::disconnect
     *
     */
    function disconnect()
    {
        if ($this->socket) @fclose($this->socket);
        $this->socket = false;
    }

    /*
     * Get information about connection
     * name: WSClient::getMetadata
     * @return array
     *
     */
    function getMetadata()
    {
        if ($this->socket) {
            return stream_get_meta_data($this->socket);
        } else {
            return array();
        }
    }

    /*
     * Make 16-byte random string
     * @return string
     */
    function makeKey()
    {
        $key = "";
        mt_srand();
        for ($i = 0; $i < 16; $i++) $key .= chr(mt_rand(1, 255));
        return $key;
    }

    /*
     * Send string to stream
     * @param string $string
     * @return mixed
     */
    function fwrite_stream($string)
    {
        $count = 0;

        for ($written = 0; $written < strlen($string); $written += $fwrite)
        {
            $fwrite = @fwrite($this->socket, substr($string, $written));
            if ($fwrite === false) return false;

            if ($fwrite === 0) {
                $count ++;
                if ($count >= 10) {
                    return false;
                } else {
                    continue;
                }
            } else {
                $count = 0;
            }
        }

        return $written;
    }

    /*
     * Parse HTTP response
     * name: WSClient::parseResponse
     * @param array $response
     * @return array
     *
     */
    function parseResponse($response)
    {
        $res = array();

        if (!preg_match('/^HTTP\/([0-9\.]+)\s+([0-9]+)\s+(.*)/', $response[0], $parts)) return false;
        $res['httpVersion'] = $parts[1];
        $res['responseCode'] = $parts[2];
        $res['responseText'] = $parts[3];
        $res['headers'] = array();

        foreach ($response as $num=>$line)
        {
            if (!$num) continue;
            $parts = preg_split('/:\s+/', $line, 2);
            if (count($parts) != 2) continue;
            if (array_key_exists($parts[0], $res['headers'])) {
                if (!is_array($res['headers'][$parts[0]])) {
                    $res['headers'][$parts[0]] = array($res['headers'][$parts[0]]);
                }
                $res['headers'][$parts[0]][] = $parts[1];
            } else {
                $res['headers'][$parts[0]] = $parts[1];
            }
        }

        return $res;
    }

    /*
     * Prepare and send frame
     * name: WSClient::send
     * @param int $opcode
     * @param string $payload
     * @param int $masked
     * @param array $masking_key
     * @return boolean
     *
     */
    function send($opcode, $payload, $masked = 1, $masking_key = array())
    {
        $frame = new WSFrame();
        $frame->set($opcode, $payload, $masked, $masking_key);

        return $this->sendFrame($frame);
    }

    /*
     * Send prepared frame
     * name: WSClient::sendFrame
     * @param WSFrame $frame
     * @return boolean
     *
     */
    function sendFrame($frame)
    {
        if ($this->fwrite_stream($frame->encode()) === false) {
            $this->errstr = "Write error";
            $this->disconnect();
            return false;
        }

        return true;
    }

    /*
     * Read frame from websocket
     * name: WSClient::read
     * @return mixed
     *
     */
    function read()
    {
        if ($this->async) {
            $read   = array($this->socket);
            $write  = null;
            $except = null;
            $num = @stream_select($read, $write, $accept, 0, 200000);
            if ($num === false) {
                $this->errstr = "Select error";
                $this->disconnect();
                return false;
            }

            if ($num) {
                $recvbuf = @fread($this->socket, 8192);
                while (strlen($recvbuf) > 0) {
                    $decoded = $this->curframe->decode($recvbuf);
                    if ($this->curframe->framestate == FRAME_STATE_ERROR) {
                        $this->errstr = "Frame decoding error " . $this->curframe->errcode;
                        $this->disconnect();
                        return false;
                    } elseif ($this->curframe->framestate == FRAME_STATE_COMPLETED) {
                        if ($this->processFrame($this->curframe)) {
                            $recvbuf = substr($recvbuf, $decoded);
                            $this->curframe = new WSFrame();
                        } else {
                            $this->disconnect();
                            return false;
                        }
                    }
                }
            }
        } else {
            for (; ; ) {
                $recvbuf = @fread($this->socket, 1);
                $decoded = $this->curframe->decode($recvbuf);
                if ($this->curframe->framestate == FRAME_STATE_ERROR) {
                    $this->errstr = "Frame decoding error " . $this->curframe->errcode;
                    $this->disconnect();
                    return false;
                } elseif ($this->curframe->framestate == FRAME_STATE_COMPLETED) {
                    if ($this->processFrame($this->curframe)) {
                        $this->curframe = new WSFrame();
                        break;
                    } else {
                        $this->disconnect();
                        return false;
                    }
                }
            }
        }

        return count($this->recvframes);
    }

    /*
     * Process frame
     * name: WSClient::processFrame
     * @param WSFrame $frame
     * @return boolean;
     *
     */
    private function processFrame($frame)
    {
        switch ($frame->opcode)
        {
            case WS_FRAME_CLOSE:
                $this->errstr = "Disconnect requested by server";
                if ($frame->payload) {
                    $this->errno = unpack("n", substr($frame->payload, 0, 2));
                    $this->errstr .= " (" . $this->errno . ") " . substr($frame->payload, 2);
                }
                return false;
                break;

            case WS_FRAME_PING:
                $pong = new WSFrame();
                $pong->set(WS_FRAME_PONG, $frame->payload, 1);
                if (!$this->sendFrame($pong)) {
                    $this->errstr = "Error sending pong";
                    return false;
                }
                break;

            case WS_FRAME_PONG:
                break;

            default:
                array_push($this->recvframes, $frame);
        }
        return true;
    }

    /*
     * Get frame from receive buffer
     * name: WSClient::getFrame
     * @return WSFrame
     *
     */
    public function getFrame()
    {
        return array_shift($this->recvframes);
    }

}
```

common/widgets/WSFrame.php：
```
namespace common\widgets;

/* Opcodes */
define("WS_FRAME_INVALID", -1);
define("WS_FRAME_CONT", 0x0);
define("WS_FRAME_TEXT", 0x1);
define("WS_FRAME_BINARY", 0x2);
define("WS_FRAME_03", 0x3);
define("WS_FRAME_04", 0x4);
define("WS_FRAME_05", 0x5);
define("WS_FRAME_06", 0x6);
define("WS_FRAME_07", 0x7);
define("WS_FRAME_CLOSE", 0x8);
define("WS_FRAME_PING", 0x9);
define("WS_FRAME_PONG", 0xA);
define("WS_FRAME_0B", 0xB);
define("WS_FRAME_0C", 0xC);
define("WS_FRAME_0D", 0xD);
define("WS_FRAME_0E", 0xE);
define("WS_FRAME_0F", 0xF);

/* Frame decoding states */
define("FRAME_STATE_ERROR", -1);
define("FRAME_STATE_BEGIN", 0);
define("FRAME_STATE_LEN", 1);
define("FRAME_STATE_KEY", 2);
define("FRAME_STATE_PAYLOAD", 3);
define("FRAME_STATE_COMPLETED", 4);

/* Frame error codes */
define("FRAME_ERROR_NONE", 0);
define("FRAME_ERROR_INTERNAL", 1);
define("FRAME_ERROR_TOO_LARGE", 2);

/**
 * Websocket Frame
 */
class WSFrame
{
    public $fin;
    public $rsv;
    public $opcode;
    public $masked;
    public $length;
    public $masking_key;
    public $payload;

    public $errcode;
    public $framestate;

    private $framepos;
    private $lenlen;
    private $lenbuf;

    function __construct()
    {
        $this->clear();
    }

    /*
     * Clear object
     * name: WSFrame::clear
     *
     */
    function clear()
    {
        $this->fin = 0;
        $this->rsv = 0;
        $this->opcode = WS_FRAME_INVALID;
        $this->masked = 0;
        $this->length = 0;
        $this->masking_key = array();
        $this->payload = "";
        $this->errcode = FRAME_ERROR_NONE;
        $this->framepos = 0;
        $this->framestate = FRAME_STATE_BEGIN;
        $this->lenlen = 0;
        $this->lenbuf = array();
    }

    /*
     * Create simple frame
     * name: WSFrame::set
     * @param int $opcode
     * @param string $payload
     * @param int $masked
     * @param array $masking_key
     *
     */
    function set($opcode, $payload, $masked = 0, $masking_key = array())
    {
        $this->fin = 1;
        $this->payload = $payload;
        $this->length = strlen($payload);
        $this->opcode = $opcode;
        $this->masked = $masked;
        if ($masked) {
            if (count($masking_key) == 4) {
                $this->masking_key = $masking_key;
            } else {
                $this->genkey();
            }
        }
        $this->framestate = FRAME_STATE_COMPLETED;
    }

    /*
     * Generate random masking key
     * name: WSFrame::genkey
     *
     */
    function genkey()
    {
        mt_srand();
        $this->masking_key = array(
            mt_rand(0, 255), mt_rand(0, 255), mt_rand(0, 255), mt_rand(0, 255)
        );
    }

    /*
     * Decode portion of data
     * name: WSFrame::decode
     * @param string $data
     * @return int
     *
     */
    function decode($data)
    {
        $datalen = strlen($data);
        $datapos = 0;
        while ($datapos < $datalen)
        {
            if (($this->framestate == FRAME_STATE_ERROR) || ($this->framestate == FRAME_STATE_COMPLETED)) return $datapos;

            $byte = ord($data[$datapos]);
            switch ($this->framestate)
            {
                case FRAME_STATE_BEGIN:
                    if ($this->framepos == 0) {
                        // Process first byte of frame
                        $this->fin = ($byte >> 7) & 0x01;
                        $this->rsv = ($byte >> 4) & 0x07;
                        $this->opcode = $byte & 0x0F;
                    } elseif ($this->framepos == 1) {
                        // Process second byte of frame
                        $this->masked = ($byte >> 7) & 0x01;
                        $pl = $byte & 0x7F;
                        switch($pl) {
                            case 126:
                                $this->lenlen = 2;
                                $this->framestate = FRAME_STATE_LEN;
                                break;
                            case 127:
                                $this->lenlen = 8;
                                $this->framestate = FRAME_STATE_LEN;
                                break;
                            default:
                                $this->lenlen = 0;
                                $this->length = $pl;
                                $this->framestate = $pl ? ($this->masked ? FRAME_STATE_KEY : FRAME_STATE_PAYLOAD) : FRAME_STATE_COMPLETED;
                        }
                    } else {
                        $this->framestate = FRAME_STATE_ERROR;
                        $this->errcode = FRAME_ERROR_INTERNAL;
                    }
                    $this->framepos++;
                    break;
                case FRAME_STATE_LEN:
                    if (($this->framepos >=2) && ($this->framepos < (2 + $this->lenlen))) {
                        $this->lenbuf[] = $byte;
                        if (count($this->lenbuf) == $this->lenlen) {
                            for ($i = $this->lenlen - 1; $i >= 0; $i--) {
                                //TODO: Add overflow handling for <64 bit OS
                                $this->length |= $this->lenbuf[$i] << (8 * ($this->lenlen - $i - 1));
                            }
                            $this->framestate = $this->masked ? FRAME_STATE_KEY : FRAME_STATE_PAYLOAD;
                        }
                    } else {
                        $this->framestate = FRAME_STATE_ERROR;
                        $this->errcode = FRAME_ERROR_INTERNAL;
                    }
                    $this->framepos++;
                    break;
                case FRAME_STATE_KEY:
                    if (($this->framepos >= (2 + $this->lenlen)) && ($this->framepos < (6 + $this->lenlen))) {
                        $this->masking_key[] = $byte;
                        if (count($this->masking_key) == 4) {
                            $this->framestate = $this->length ? FRAME_STATE_PAYLOAD : FRAME_STATE_COMPLETED; // empty masked frame?
                        }
                    } else {
                        $this->framestate = FRAME_STATE_ERROR;
                        $this->errcode = FRAME_ERROR_INTERNAL;
                    }
                    break;
                case FRAME_STATE_PAYLOAD:
                    $pl = strlen($this->payload);
                    if ($pl < $this->length) {
                        if ($this->masked) {
                            $this->payload .= chr($byte ^ $this->masking_key[$pl % 4]);
                        } else {
                            $this->payload .= chr($byte);
                        }
                        if (++$pl == $this->length) $this->framestate = FRAME_STATE_COMPLETED;
                    }
                    $this->framepos++;
                    break;
                case FRAME_STATE_COMPLETED:
                    return $datapos;
                default:
            }
            $datapos++;
        }
        return $datapos;
    }

    /*
     * Is frame completed?
     * name: WSFrame::completed
     * @return boolean
     *
     */
    function completed()
    {
        return ($this->framestate == FRAME_STATE_COMPLETED);
    }

    /*
     * Encode frame
     * name: WSFrame::encode
     * @return string
     *
     */
    function encode()
    {
        $retval = chr((($this->fin & 0x1) << 7) | (($this->rsv & 0x7) << 4) | ($this->opcode & 0xF));

        $pl = strlen($this->payload);
        if ($pl < 126) {
            $retval .= chr(($pl & 0x7F) | (($this->masked & 0x1) << 7));
        } elseif ($pl <= 0xFFFF) {
            $retval .= chr(126 | (($this->masked & 0x1) << 7));
            $retval .= pack('n', $pl);
        } else {
            $retval .= chr(127 | (($this->masked & 0x1) << 7));
            for ($i = 7; $i >=0; $i--) {
                $retval .= chr(($pl >> (8 * $i)) & 0xFF);
            }
        }

        if ($this->masked) {
            $retval .= chr($this->masking_key[0]) . chr($this->masking_key[1]) .
                chr($this->masking_key[2]) . chr($this->masking_key[3]);
            for ($i = 0; $i < $pl; $i++) {
                $retval .= chr(ord($this->payload[$i]) ^ $this->masking_key[$i % 4]);
            }
        } else {
            $retval .= $this->payload;
        }

        return $retval;
    }
}
```

使用示例：
```
require __DIR__.'/common/widgets/WSClient.php';
require __DIR__.'/common/widgets/WSFrame.php';

$socket = new common\widgets\WSClient(
    '127.0.0.1',
    '/',
    false,
    9501
);

if (!$socket->connect(true)) {
    die('connect failed');
}

$msg = "abc123";

if (!$socket->send(WS_FRAME_TEXT, $msg, 1)) {
    echo $socket->errstr . "\n";
    die;
}
```

里面大量的位运算可以看下 <https://ibaiyang.github.io/blog/php/2019/01/10/PHP-位运算相关.html>


## 参考资料



