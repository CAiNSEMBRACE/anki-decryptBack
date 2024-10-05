# [The Odd Paw](https://oddpaw.com/)

[Home](https://oddpaw.com/)[Blog](https://oddpaw.com/blog/)[Links](https://oddpaw.com/links/)[Uses](https://oddpaw.com/uses/)[RSS](https://oddpaw.com/rss.xml)

# 解密一个Anki Deck

July 14, 2023



![anki main screen](https://oddpaw.com/static/7cafd1bbf3d3eb9a1e93c83b357335af/601be/featured-image-anki-gui.png)

# 背景

我以前背单词时使用欧路词典，通过导入牛津词典，结合欧路里用户上传的单词书，就可以背单词了。方便，但是欧路也有自己的问题。

- 欧路实际上是一个词典程序，背单词时查看的内容也是词典的内容。往往一个英文单词有好多意思，所以用欧路背单词时拿不准这个单词最常用的解释。
- 设置每天学习和复习的数量时，没有一个明确的比例关系，也没有任何警告。我可以选择每天学10个单词，复习10个单词。但是这样的设置是低效的，因为需要复习的单词总是比新学的单词要多好多。
- 无法导出已掌握、学习中、未学习的单词，导致用户被绑在了欧路上
- 同步有问题，即使是付费用户也会有问题。我在欧路上的进度只能同步一部分到网页版。这个也可能是因为我是中途购买会员，并且换手机导致的问题。

而Anki是我很早就知道的专门用来记忆各种知识的软件。但是由于iOS版本是收费的（¥168），所以我一直没考虑用这个软件。不过这个问题在我转变了价值观后，就不复存在了。关于价值观转变这件事，后面文章里会详细说一下。

# 新的需求

正值暑假，我想让女儿在空余时间背一下初中英语考纲词汇，提高一下词汇量，这样做阅读理解的时候会稍微轻松一点。

对她来说，用欧路就更不合适了，所以这次我准备让她使用Anki。

我理想中的Anki deck应该是带单词、音标、发音、解释、例句的，但在AnkiWeb上没找到合适的。

后来在Anki中文网上找到一个deck，符合我所有要求，要价78。加客服微信后，说可以便宜5块钱。我又不傻。

在闲鱼上找找，卖相关deck的人很少，有一个卖家要价30，而且没有音标。

在淘宝上找，果然找到了，17.8一套。虽然和前面说的78元的deck不是同一个，但看了介绍，觉得满足我的要求，就下单了。

# 意外发现

导入Anki后，觉得不错，只是有些广告。



[![导入购买的deck后，Anki的deck列表中出现了好多subdeck，这些都是广告](https://oddpaw.com/static/cfa436afd9d21b917fe08b36061a6225/f058b/anki-main-screen-shows-a-lot-of-ads-after-import-this-deck.png)](https://oddpaw.com/static/cfa436afd9d21b917fe08b36061a6225/99272/anki-main-screen-shows-a-lot-of-ads-after-import-this-deck.png)导入购买的deck后，Anki的deck列表中出现了好多subdeck，这些都是广告



有广告没问题，删掉这些subdeck就好了。但是浏览notes时，发现有些字段的内容是这样的：



[![浏览这个deck的note时，发现有些字段是加密的](https://oddpaw.com/static/bfa1c5cadef7287ab453025a4af1d395/f058b/anki-note-list-shows-some-field-content-is-encrypted.png)](https://oddpaw.com/static/bfa1c5cadef7287ab453025a4af1d395/a1253/anki-note-list-shows-some-field-content-is-encrypted.png)浏览这个deck的note时，发现有些字段是加密的



看着这么规律的前后标记，以及末尾的=，这必定是加密的内容了。

翻看正面card时，发现这么一句话

```html
<script>decryptFront()</script>
```

背面card里也有类似的

```html
<script>decryptBack()</script>
```

还有，竟然直接引用了外部的jQuery。

```html
<script src="https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
```

还有使用jQuery去请求外部内容的，如

```text
...
$.get(
    "https://corpus.vocabulary.com/api/1.0/examples.json", {
        query: query,
        maxResults: num,
        startOffset: offset,
        domain: domain,
        filter: 0
    },
...
```

在style中就更夸张了，大段混淆后的js代码。格式化后，发现了一些让我反感的内容。

```text
...
_0x2bcd93[_0x1b49("0x2a")] = "加密记忆库需联网验证！";
_0x2bcd93[_0x1b49("0x62")] = function (_0x3703b7, _0x374253) {
  return _0x3703b7 + _0x374253;
};
_0x2bcd93[_0x1b49("0x6e")] = _0x1b49("0x53");
_0x2bcd93["hUucw"] = function (_0x5e8852, _0x2a4baa, _0x42dd2e) {
  return _0x5e8852(_0x2a4baa, _0x42dd2e);
};
_0x2bcd93[_0x1b49("0x3f")] = "请检查网络是否正常，错误信息：";
_0x2bcd93[_0x1b49("0x64")] = function (_0x5dd32c, _0x5b0337) {
  return _0x5dd32c + _0x5b0337;
};
...
_0x56f4c8["url"] = _0x4125e9[_0x1b49("0x64")](ankiUrl, _0x4125e9["tygHl"]);
...
```

我肯定不敢把这样的东西拿来直接给女儿用啊，鬼知道里面还有什么不可见人的东西。

于是，就打算利用这个deck中的notes，重新设计card，让它变得简单，更适合一个普通的初中生。

那么就需要把这些内容解密才行。

# 解密

## 本地调试失败

把Anki客户端中，将front card中的decryptFront()删掉，并且把<template>改为<div>，就能在预览中看到不解密时的样子。



[![修改一些代码后，预览卡片，发现加密后的文字直接出现在了卡片上](https://oddpaw.com/static/ead0271763852bb9ab305eb93bb42273/664c8/card-preview-shows-content-before-decrypt.png)](https://oddpaw.com/static/ead0271763852bb9ab305eb93bb42273/664c8/card-preview-shows-content-before-decrypt.png)修改一些代码后，预览卡片，发现加密后的文字直接出现在了卡片上



所以盲猜，decryptFront可能会去页面上使用正则表达式找≯#和#≮之间的内容，再解密。在代码里搜索≯#，可以找到这么一段

```text
if (_0x55af31) {
  let _0x2c5cf0 = _0x2d2baa[_0x1b49('0x38')](/≯#.*?#≮/gi);
  if (_0x2c5cf0) {
    try {
      _0x2c5cf0[_0x1b49('0x6c')]((_0x4cd7d7,_0x745639)=>{
        let _0x1b3ad0 = this[_0x1b49('0x2c')](_0x4cd7d7[_0x1b49('0x4b')](0x2)[_0x1b49('0x4b')](0x0, _0x30571e[_0x1b49('0x69')](_0x4cd7d7[_0x1b49('0x7')], 0x4)), _0x55af31, _0x1b49('0x1a'));
        _0x2d2baa = _0x2d2baa['replace'](_0x4cd7d7, _0x1b3ad0);
      });
    } catch (_0x179caf) {
      return;
    }
  }
  _0x3d2a1b(_0x2d2baa);
}
```

看不懂这一段在做什么，所以想调试一下。

直接把卡片的front、back、style中的代码粘贴到一个空白的html文件中，调整格式，然后用浏览器打开这个文件。

浏览器直接死掉，内存也几乎占满了。好在Chrome发现页面占用了太多内存后，会停止这个页面，然后出现调用堆栈。虽然不明白那一段写了什么，但调试下来就是向一个数组中插入很多很多字符串，导致浏览器崩溃。

直接调试失败，那么就把这个deck上传到AnkiWeb吧，看看能不能在网页版Anki上使用，如果能使用，应该就能调试了。

## 上传至AnkiWeb，再尝试调试

在Anki客户端里同步数据至AnkiWeb，就能在浏览器里打开并学习这个deck了。

*额外插一句，如果打开ankiweb.net或者ankiuser.net很慢，那是他们网站上会加载几个外部域名，而这些域名是打不开的（如maxcdn.bootstrapcdn.com和cdnjs.cloudflare.com），可以安装PrivacyBadger这个extension，将这两个域名禁用就可以了，不影响AnkiWeb的使用。也可以使用自己的方法禁止浏览器访问这两个域名。*

点击这个deck的学习按钮，界面上出现了解密后的内容。



[![AnkiWeb上可以显示解密后的内容](https://oddpaw.com/static/579a95989f125245d388bbf1c5091d89/c7c3c/anki-web-study-decrypted-content.png)](https://oddpaw.com/static/579a95989f125245d388bbf1c5091d89/c7c3c/anki-web-study-decrypted-content.png)AnkiWeb上可以显示解密后的内容



打开Chrome的Developer tools中的Console tab，输入 `decryptBack` ，能够看到函数定义。那就好办了，直接把这个函数替换掉吧。

在Console中输入下面这行代码

```text
_decryptBack = decryptBack;decryptBack = function(){debugger;_decryptBack();}
```

点击AnkiWeb界面上的showAnswer，就能看到Chrome停在了debugger位置了。按F11进入~~decryptBack函数~~，这样就打开了这张card的所有代码。

不需要去看懂decryptBack到底写了什么。只要在源码中搜索≯#，就能在那个位置加断点了。

```text
let _0x2c5cf0 = _0x2d2baa[_0x1b49("0x38")](/≯#.*?#≮/gi);
if (_0x2c5cf0) {
  try {
    _0x2c5cf0[_0x1b49("0x6c")]((_0x4cd7d7, _0x745639) => {
      let _0x1b3ad0 = this[_0x1b49("0x2c")](
        _0x4cd7d7[_0x1b49("0x4b")](0x2)[_0x1b49("0x4b")](
          0x0,
          _0x30571e[_0x1b49("0x69")](_0x4cd7d7[_0x1b49("0x7")], 0x4)
        ),
        _0x55af31,
        _0x1b49("0x1a")
      );
      _0x2d2baa = _0x2d2baa["replace"](_0x4cd7d7, _0x1b3ad0);
    });
  } catch (_0x179caf) {
    return;
  }
}
_0x3d2a1b(_0x2d2baa);
```

在看混淆的代码时，会看到类似这种格式，可以手工执行表达式，并将结果替换回代码中。

```text
// 例如这一句
let _0x2c5cf0 = _0x2d2baa[_0x1b49('0x38')](/≯#.*?#≮/gi);

// _0x1b49('0x38')的结果是match
// 那么这一句的结果是
let _0x2c5cf0 = _0x2d2baa['match'](/≯#.*?#≮/gi);

// 近一步变换后得到
let matches = _0x2d2baa.match(/≯#.*?#≮/gi);
```

经过简单的替换和重新命名，这部分处理解密的代码就变成了：

```text
let matches = _0x2d2baa.match(/≯#.*?#≮/gi);
if (matches) {
  try {
    matches.forEach((encryptedText)=>{
      let decryptedText = this.akDecrypt(encryptedText.substr(2).substr(0, _0x30571e['XqXsO'](encryptedText.length, 4)), _0x55af31, '12345679abcdefgj');
      _0x2d2baa = _0x2d2baa.replace(encryptedText, decryptedText);
    });
  } catch (_0x179caf) {
    return;
  }
}
_0x3d2a1b(_0x2d2baa);
```

可以看到this.akDecrypt那行是关键。这个函数有3个参数，看起来第一个是加密后的字符串，第二个可能是key，第三个可能只是salt。

但是这一行有太多表达式，如果一步步调试会很麻烦，一不小心就跑过头了。可以直接在Console中输入this.akDecrypt.toString()，回车。就能看到这个函数的定义。



[![this.akDecrypt.toString()的结果](https://oddpaw.com/static/0c1ef4558296c08bb39a02f6e5c487d5/2d920/function-to-string.png)](https://oddpaw.com/static/0c1ef4558296c08bb39a02f6e5c487d5/2d920/function-to-string.png)this.akDecrypt.toString()的结果



直接在代码里搜索这个函数的一些关键字，比如我搜索 `return KKK['JJJ']` 就找到了this.akDecrypt的定义。

```text
_0x5a4eac[_0x1b49('0x2c')] = function(_0x1136e9, _0x4ae814, _0x316e4b) {
  return KKK['JJJ'][_0x1b49('0x14')](_0x1136e9, KKK[_0x1b49('0x3e')][_0x1b49('0x5c')][_0x1b49('0x60')](_0x4ae814), {
    'iv': KKK[_0x1b49('0x3e')][_0x1b49('0x5c')]['parse'](_0x316e4b),
    'mode': KKK[_0x1b49('0x4d')]['CBC'],
    'padding': KKK[_0x1b49('0x66')][_0x1b49('0x68')]
  })[_0x1b49('0x3')](KKK['enc']['Utf8']);
}
```

按前述方法，如法炮制，变为可看懂的代码，如下所示

```text
function akDecrypt(encryptedText, key, iv) {
  return KKK['JJJ'].decrypt(encryptedText, KKK.enc.Utf8.parse(key), {
    'iv': KKK.enc.Utf8.parse(iv),
    'mode': KKK.mode.CBC,
    'padding': KKK.pad.Pkcs7
  }).toString(KKK.enc.Utf8);
}
```

现在可以确定第二个参数是key，第三个参数是iv。

只要代码进入到这个函数，就能看到变量的值了。

继续找KKK是怎么定义的，从代码中可以找到 `KKK = KKK || function(u, p) {` 。

往下翻一点代码，会发现这可能是一个第三方的加密库。

继续往下看，会看到陆续出现了akDecrypt中需要的enc.Utf8，CBC，Pkcs7等关键字。

我们不需要知道具体的解密算法，只要把这一整段拿出来就行了，从 `KKK = KKK || function(u, p) {` 开始，一直到 `u.JJJ = p._createHelper(d)` 这里结束，大概715行。

随便找一个加密后的文本，调用这个函数就能直接解密了。

# 更新deck中加密的字段内容

在Anki客户端里，找到需要导出的deck/subdeck，点击右边的小齿轮，选择export。



[![在Anki客户端里导出deck](https://oddpaw.com/static/c4fab11383e2d8b27aeae9ea68835f6b/f058b/export-deck.png)](https://oddpaw.com/static/c4fab11383e2d8b27aeae9ea68835f6b/774b6/export-deck.png)在Anki客户端里导出deck



在导出的选项中，选择txt格式，并勾选所有内容



[![Anki客户端的导出选项界面](https://oddpaw.com/static/54269302de12144858ada46d724920c0/3075e/export-options.png)](https://oddpaw.com/static/54269302de12144858ada46d724920c0/3075e/export-options.png)Anki客户端的导出选项界面



导出的文件实际上就是一个TSV（使用Tab分隔的文本），然后几行程序就能读取、解密、写回。

```text
const fs = require('fs');

const regex = /≯#.*?#≮/g;

function run(){
  const content = fs.readFileSync('./notes.txt', {encoding: 'utf-8'});
  const rows = content.split('\n');

  const results = [];

  for(const row of rows){
    const matches = row.match(regex);

    if(!matches){
      results.push(row);
      continue;
    }

    let newRow = row;
    for(const match of matches){
      newRow = newRow.replace(match, akDecrypt(match.replace('≯#', '').replace('#≮', '')));
    }

    results.push(newRow);
  }

  fs.writeFileSync('./result.txt', results.join('\n'), {encoding: 'utf-8'})
}

run();
```

再到Anki客户端里点击Import File，选择上面代码生成的result.txt。



[![在Anki客户端里点击Import File按钮导入刚才生成的文件](https://oddpaw.com/static/e694fcfe2a202ad784ca8e1a565ceb98/f058b/import-button.png)](https://oddpaw.com/static/e694fcfe2a202ad784ca8e1a565ceb98/74866/import-button.png)在Anki客户端里点击Import File按钮导入刚才生成的文件



在导入选项中，不导入tag（下图中画线部分选择为Nothing）



[![Anki客户端的导入选项界面](https://oddpaw.com/static/b896b548efcb24d4e1fddcb81f460248/f058b/import-options.png)](https://oddpaw.com/static/b896b548efcb24d4e1fddcb81f460248/76cea/import-options.png)Anki客户端的导入选项界面



导入后，原先加密的内容就变成了明文。



[![Anki客户端的查看notes界面](https://oddpaw.com/static/043da55fdc199e142ef40e8e9310a838/e7c18/anki-note-now-decrypted.png)](https://oddpaw.com/static/043da55fdc199e142ef40e8e9310a838/e7c18/anki-note-now-decrypted.png)Anki客户端的查看notes界面



完。

## Comments

<iframe src="https://comments.oddpaw.com/web/iframe.html?host=https%3A%2F%2Fcomments.oddpaw.com&amp;site_id=oddpaw&amp;url=https%3A%2F%2Foddpaw.com%2F2023%2F07%2Fdecrypt-anki-deck&amp;theme=light&amp;components=embed" name="{}" frameborder="0" allowtransparency="true" scrolling="no" tabindex="0" title="Comments | Remark42" horizontalscrolling="no" verticalscrolling="no" style="box-sizing: border-box; height: 4685px; width: 856px; border: none; padding: 0px; margin: 0px; overflow: hidden; color-scheme: none;"></iframe>

------



Except where otherwise noted , content on this site is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/) license .

Built with [Gatsby](https://www.gatsbyjs.com/)