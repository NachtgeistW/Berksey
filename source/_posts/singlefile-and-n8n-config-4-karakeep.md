---
title: 使用 SingleFile + n8n 将内容保存到 karakeep 并清洗
tags:
  - karakeep
  - 知识管理
  - SingleFile
  - n8n
updated: 2026-08-26 21:34
category:
  - Mist
  - 知识管理
date: 2026-08-21 00:00:00
---


使用 karakeep 也有几个月了，也与反爬严重的网站打了几个月的架。在某次与 ChatGPT 排查 cookie 配置的聊天中得知，可以使用 SingleFile 将当前打开的页面保存到 karakeep，直接绕过反爬和登录问题（且这是官方推荐做法，karakeep 早已集成了 SingleFile）。后来发现还能通过 n8n 处理抓好的 html 文件，从而让 karakeep 的阅读视图展示干净的文本。一番努力后终于配好了我的抓取和清洗工作流，这里附上教程，以供他人参考。

<!-- more -->

## 下载与配置 SingleFile

SingleFile 是浏览器插件。直接进插件商店安装就好了。指路 [gildas-lormeau/SingleFile](https://github.com/gildas-lormeau/SingleFile)
装好之后配置一下设置，以让 SingleFile 抓到符合需求的网页：
- 网络：
	- 拦截资源文件额外勾选“字体”。如果你有保存字体的想法的话可以不勾选，我勾选是为了拦截知乎大小高达 50MB 的字体，防止在我只能匀出 1G Dockers 运行内存的小 Mac Mini 上处理时 OOM。
	- 勾选“跨域请求失败时添加来源地址头信息（Referer）”。不勾选这个选项就抓不到微博的图片，这点 SingleFile 官方甚至特地提过（[Why aren't images saved on sites like sspai.com or weibo.com?](https://github.com/gildas-lormeau/SingleFile/blob/master/faq.md#why-arent-images-saved-on-sites-like-sspaicom-or-weibocom)）。
- 保存位置：
	- 选择“保存到 REST 表单 API”
	- 网址：填入 n8n webook 节点的 URL，URL 的具体数值下一节会讲。
	- 授权令牌：可以不填。
	- 文件字段名称：file
	- 网址字段名称：url

这步完成后，n8n 就能拿到 SingleFile 捕获的 HTML 文件了。
另：如果你没有配置 n8n 的需求的话 URL 选项直接填入`https://你的karakeep服务器地址/api/v1/bookmarks/singlefile`，此时捕获的网址会直接进入 karakeep 中。

## 配置 n8n 发送到 karakeep 的工作流

我们先建一个非常简单的工作流，如下图所示，将 SingleFile 捕获的 HTML 直接发送到 karakeep，不做任何改动。

![](simple-n8n-workflow.png)

Webhook 的配置如下：

![](n8n-webhook.png)

- HTTP Method：POST
- Path：自己起个喜欢的名。
其他保持默认。

做完这步后打开 SingleFile，将 **Test URL** 的网址填入保存位置>网址。Production URL 只有当你 Publish 了之后才生效，所以现在用 Test URL 测试就行。

Switch 节点配置如下：

![](n8n-switch-simple.png)

Option 那里点击 Add option，选择 Fallback Output，然后下拉菜单里选择 Extra Output，这样就能产生一个兜底/默认选项，将不需要清洗的页面直接发给 karakeep。

HTTP Request 节点配置如下：

![](n8n-http-request.png)

- URL：`https://你的karakeep服务器地址/api/v1/bookmarks/singlefile`。
- Authentication：Predefined Credential Type
- Credential Type：Bearer Auth
- Bearer Auth：点击 Create new credential，在 Bear Token 处填入 karakeep API 密钥，然后点 Save

![](bear-auth-account.png)

karakeep API 密钥的获取方法：点击右上角头像>用户设置>API 密钥，起个名字后就会生成一串字符。这个就是上一步要填入 Bear Token 的密钥了。

![](karakeep-API.png)

- Send Body：勾选
- Body Content Type：Form-Data

新建两个 Body Field。

- 第一个填写如下：
	- Type：n8n Binary File
	- Name: file
	- Input Data Field Name：file

- 第二个：
	- Type：Form Data
	- Name: url
	- Input Data Field Name：鼠标悬停将该行格式设置为 Expression，然后填 `{{ $('Webhook').item.json.body.url }}`

![](n8n-http-request-config.png)

做好这步后，点击 Execute workflow 让 Test URL 监听事件，然后用 SingleFile 抓取一个页面（你直接抓这个配置页面就行），等待它工作完成就行了。回到 karakeep，此时应该能看到抓好的这个页面的存档。

## 配置 n8n 清洗的工作流

其实原理很简单：用 switch 节点匹配不同的网站来运行不同的 JavaScript 处理捕获的 HTML。
我配置了微博、知乎和微信公众号，大概长这样：

![](whole-workflow.png)

Switch 节点的 Routing Rules 全部用以下匹配规则：
- value 1 选 Expression，全部填 `{{ $json.body.url || '' }}`
- 匹配规则为 String > matches regex。
- value 2 不用动，填写的内容为正则表达式，用正则去匹配网址。（不知道正则怎么写的话可以问 AI，我也是问的 GPT）
- Output Name：可以改一下，方便后面连线的时候辨认。

![](website-regex.png)

清洗代码使用 Code in JavaScript 节点。

具体讲一下我针对这三个网站的设置。附我的清洗代码，我用着不错，可以直接抄走。

### 知乎

我存的大部分是知乎专栏，所以这个清洗主要是针对知乎专栏的。用 karakeep 直接存和用 SingleFile 洗过后再存的差别不大，主要是移除蓝色星标和高亮评论。

匹配正则：`^https?://zhuanlan\.zhihu\.com/p/\d+/?(?:[?#].*)?$`
- 如果未来你需要匹配知乎全站的话，可以使用 `^https?://(?:[a-z0-9-]+\.)?zhihu\.com(?:/|$)`

清洗做的事：

- 移除文内的蓝色星标及为这些文字生成的超链接。
- 移除虚线高亮。由于 karakeep 的 Reader View 会过滤掉所有带 `comment` 的字段，而知乎的带评论的虚线高亮定义在 `class="highlight-wrap … has-comments"`中，所以被 Reader View 吞掉了。

清洗代码如下：

```js
const item = $input.first();
const input = await this.helpers.getBinaryDataBuffer(0, 'file');

const originalSize = input.length;
let html = input.toString('utf8');

// 删除内嵌字体数据，保留图片、正文和其他 CSS
html = html.replace(
  /data:(?:font\/[a-z0-9.+-]+|application\/(?:font-[a-z0-9.+-]+|x-font-[a-z0-9.+-]+|vnd\.ms-fontobject))(?:;[^,]*)?,[a-z0-9+/=_-]+/gi,
  'data:,'
);

// 知乎会把正文关键词包成站内搜索链接，并附带一个 SVG 星标。
// 保留关键词文字，移除链接
const zhidaSearchLink =
  /<a\b(?=[^>]*\bhref=(["'])https?:\/\/zhida\.zhihu\.com\/search(?:\?[^"']*)?\1)[^>]*>([\s\S]*?)<\/a>/gi;

// 移除 SVG 星标
html = html.replace(
  zhidaSearchLink,
  (_match, _quote, innerHtml) =>
    innerHtml.replace(/<svg\b[\s\S]*?<\/svg>/gi, "),
);

// 避免 Reader View 把知乎的“带评论高亮”误判为评论区并删除正文
html = html.replace(/\bhas-comments\b/gi, 'has-notes');

const output = Buffer.from(html, 'utf8');

item.json.cleaner = {
  originalBytes: originalSize,
  cleanedBytes: output.length,
  removedBytes: originalSize - output.length,
};

item.binary.file = await this.helpers.prepareBinaryData(
  output,
  item.binary.file.fileName || 'page.html',
  'text/html',
);

return [item];
```

### 微信公众号

用 karakeep 直接存和用 SingleFile 洗过后再存近乎没有差别，主要是 karakeep 经常会被认定为爬虫被微信挡住。

匹配正则：`^https?://mp\.weixin\.qq\.com(?:/|$)`

清洗做的事：

- 处理掉错误的视频图层。在捕获某些页面时，SingleFile 会将视频全屏截图层错误地归档成整篇文章的 HTML（我实机测试的时候它甚至将这一步重复了 9 次），形成一个异常巨大的 HTML（约 50 MB，在我的设备上会 OOM）。
- 删除不必要的组件，提取 `#js_content` 和正文图片重新生成更小、适合阅读的 HTML。

清洗代码：

```js
const item = $input.first();
const binaryField = 'file';

let source = await this.helpers.getBinaryDataBuffer(0, binaryField);

const DIV_OPEN = Buffer.from('<div');
const DIV_CLOSE = Buffer.from('</div');
const TAG_END = Buffer.from('>');
const ID_UNQUOTED = Buffer.from('id=js_content');
const ID_DOUBLE = Buffer.from('id="js_content"');
const ID_SINGLE = Buffer.from("id='js_content');
const DATA_HTML = Buffer.from('data:text/html;base64,');
const BLANK = Buffer.from('about:blank');

function findJsContentOpen(buffer) {
  let pos = 0;

  while ((pos = buffer.indexOf(DIV_OPEN, pos)) !== -1) {
    const tagEnd = buffer.indexOf(TAG_END, pos + DIV_OPEN.length);
    if (tagEnd === -1) break;

    const tag = buffer.subarray(pos, tagEnd + 1);
    if (
      tag.includes(ID_UNQUOTED) ||
      tag.includes(ID_DOUBLE) ||
      tag.includes(ID_SINGLE)
    ) {
      return { start: pos, end: tagEnd + 1 };
    }

    pos = tagEnd + 1;
  }

  throw new Error('未找到微信正文容器 #js_content');
}

function extractMatchingDiv(buffer, open) {
  let depth = 1;
  let pos = open.end;

  while (depth > 0) {
    const nextOpen = buffer.indexOf(DIV_OPEN, pos);
    const nextClose = buffer.indexOf(DIV_CLOSE, pos);

    if (nextClose === -1) {
      throw new Error('#js_content 的 div 未正常闭合');
    }

    if (nextOpen !== -1 && nextOpen < nextClose) {
      depth++;
      pos = nextOpen + DIV_OPEN.length;
    } else {
      depth--;
      const closeEnd = buffer.indexOf(TAG_END, nextClose + DIV_CLOSE.length);
      if (closeEnd === -1) {
        throw new Error('HTML 中存在未闭合的 </div>');
      }

      if (depth === 0) {
        return buffer.subarray(open.start, closeEnd + 1);
      }

      pos = closeEnd + 1;
    }
  }
}

function isBase64Byte(byte) {
  return (
    (byte >= 65 && byte <= 90) ||   // A-Z
    (byte >= 97 && byte <= 122) ||  // a-z
    (byte >= 48 && byte <= 57) ||   // 0-9
    byte === 43 ||                  // +
    byte === 47 ||                  // /
    byte === 61                     // =
  );
}

// 不转字符串，直接跳过 data:text/html;base64 的巨大 payload。
function removeEmbeddedHtml(buffer) {
  const chunks = [];
  let cursor = 0;
  let found;

  while ((found = buffer.indexOf(DATA_HTML, cursor)) !== -1) {
    chunks.push(buffer.subarray(cursor, found));
    chunks.push(BLANK);

    cursor = found + DATA_HTML.length;
    while (cursor < buffer.length && isBase64Byte(buffer[cursor])) {
      cursor++;
    }
  }

  chunks.push(buffer.subarray(cursor));
  return Buffer.concat(chunks);
}

function escapeHtml(value) {
  return String(value)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;');
}

function decodeEntities(value) {
  return String(value)
    .replace(/&amp;/gi, '&')
    .replace(/&quot;/gi, '"')
    .replace(/&#39;|&apos;/gi, "')
    .replace(/&lt;/gi, '<')
    .replace(/&gt;/gi, '>');
}

function attr(tag, name) {
  const re = new RegExp(
    `\\b${name}\\s*=\\s*(?:"([^"]*)"|'([^']*)'|([^\\s>]+))`,
    'i',
  );
  const match = tag.match(re);
  return match ? (match[1] ?? match[2] ?? match[3] ?? '') : '';
}

function metaContent(head, names) {
  const wanted = new Set(names.map((name) => name.toLowerCase()));

  for (const match of head.matchAll(/<meta\b[^>]*>/gi)) {
    const tag = match[0];
    const key = (
      attr(tag, 'property') ||
      attr(tag, 'name') ||
      attr(tag, 'itemprop')
    ).toLowerCase();

    const content = attr(tag, 'content');
    if (wanted.has(key) && content) return decodeEntities(content);
  }

  return '';
}

function elementTextById(buffer, id) {
  let from = 0;
  const needle = Buffer.from(id);

  while (true) {
    const idAt = buffer.indexOf(needle, from);
    if (idAt === -1) return '';

    const tagStart = buffer.lastIndexOf(Buffer.from('<'), idAt);
    const tagEnd = buffer.indexOf(Buffer.from('>'), idAt);
    if (tagStart === -1 || tagEnd === -1) return '';

    const openTag = buffer.subarray(tagStart, tagEnd + 1).toString('utf8');
    const tagName = openTag.match(/^<([a-z0-9]+)/i)?.[1];
    if (!tagName || !new RegExp(`\\bid\\s*=\\s*(?:"${id}"|'${id}'|${id}(?=\\s|>))`, 'i').test(openTag)) {
      from = idAt + needle.length;
      continue;
    }

    const closeTag = `</${tagName}>`;
    const closeAt = buffer.indexOf(Buffer.from(closeTag), tagEnd + 1);
    if (closeAt === -1) return '';

    return decodeEntities(
      buffer
        .subarray(tagEnd + 1, closeAt)
        .toString('utf8')
        .replace(/<[^>]+>/g, ' ')
        .replace(/\s+/g, ' ')
        .trim(),
    );
  }
}

const headEnd = source.indexOf(Buffer.from('</head>'));
const head = source
  .subarray(0, headEnd === -1 ? Math.min(source.length, 2 * 1024 * 1024) : headEnd + 7)
  .toString('utf8');

const activityTitle = elementTextById(source, 'activity-name');
const pageTitle = decodeEntities(
  head.match(/<title[^>]*>([\s\S]*?)<\/title>/i)?.[1] || '',
)
  .replace(/<[^>]+>/g, '')
  .trim();

const title =
  activityTitle ||
  metaContent(head, ['og:title', 'twitter:title']) ||
  pageTitle ||
  '微信文章';

const description =
  metaContent(head, ['og:description', 'twitter:description', 'description']) ||
  '';

const coverUrl =
  metaContent(head, ['og:image', 'twitter:image', 'image']) ||
  decodeEntities(
    head.match(/\bmsg_cdn_url\s*=\s*["']([^"']+)["']/i)?.[1] || '',
  );

const open = findJsContentOpen(source);
let articleBuffer = extractMatchingDiv(source, open);
let compactBuffer = removeEmbeddedHtml(articleBuffer);

// 尽早解除对 43 MiB 原文件的引用。
source = null;
articleBuffer = null;

// 现在通常只剩约 4 MiB，之后的字符串清理安全得多。
let article = compactBuffer.toString('utf8');
compactBuffer = null;

article = article
  .replace(/<!--[\s\S]*?-->/g, '')
  .replace(/<script\b[^>]*>[\s\S]*?<\/script\s*>/gi, '')
  .replace(/<style\b[^>]*>[\s\S]*?<\/style\s*>/gi, '')
  .replace(/<iframe\b[^>]*>[\s\S]*?<\/iframe\s*>/gi, '')
  .replace(/<iframe\b[^>]*\/?>/gi, '')
  .replace(/<(?:video|audio)\b[^>]*>[\s\S]*?<\/(?:video|audio)\s*>/gi, '')
  .replace(/<(?:source|track)\b[^>]*\/?>/gi, '');

const sourceUrl = item.json.body?.url || item.json.url || '';

const output = `<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>${escapeHtml(title)}</title>

  <meta name="description" content="${escapeHtml(description)}">
  <meta property="og:type" content="article">
  <meta property="og:title" content="${escapeHtml(title)}">
  <meta property="og:description" content="${escapeHtml(description)}">
  <meta property="og:url" content="${escapeHtml(sourceUrl)}">
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="${escapeHtml(title)}">
  <meta name="twitter:description" content="${escapeHtml(description)}">
  ${
    coverUrl
      ? `<meta property="og:image" content="${escapeHtml(coverUrl)}">
         <meta name="twitter:image" content="${escapeHtml(coverUrl)}">`
      : ''
  }

  <style>
    body { max-width:760px; margin:0 auto; padding:24px 16px 64px;
      color:#202124; font:17px/1.8 system-ui,-apple-system,BlinkMacSystemFont,
      "PingFang SC","Microsoft YaHei",sans-serif; }
    img { display:block; max-width:100%; height:auto; margin:1.2em auto; }
    pre { white-space:pre-wrap; overflow-wrap:anywhere; }
    blockquote { margin:1em 0; padding-left:1em; border-left:3px solid #ddd; }
  </style>
</head>
<body>
  <article>${article}</article>
  <hr>
  <p><small>原文：<a href="${escapeHtml(sourceUrl)}">${escapeHtml(sourceUrl)}</a></small></p>
</body>
</html>`;

return [{
  json: {
    ...item.json,
    cleaned: true,
    archiveType: 'wechat-reading',
  },
  binary: {
    [binaryField]: await this.helpers.prepareBinaryData(
      Buffer.from(output, 'utf8'),
      'wechat-reading.html',
      'text/html',
    ),
  },
}];
```

### 微博正文

这个清洗只针对微博正文。不管是 karakeep 原生插件还是 SingleFile 都对头条文章处理得不错，所以只洗单条微博。

匹配正则：`^https?://(?:www\.)?weibo\.com\/\d+\/[a-z0-9]+\/?(?:[?#].*)?$`

清洗做的事：

- 直接生成一个只保留头像和微博正文的最小 HTML。
- 将微博表情换成 `[并不简单]` 这样的文本以避免破坏 Reader View 观感（因为只要有图片就会强制换行且不处理的话会是 168x168 还是多少的巨大一个，虽然已尝试过将其缩小为微博同款 18x18 观感但 `<img>` 强制折行无法改，遂放弃）。
- 解开 `weibo.cn/sinaurl` 外链，换成原始的直链。
- 对于以 markdown 格式写的微博，直接取 `markdown-body` 正文。
- 将 karakeep 标题固定为`<微博前 20 个字符>...@<微博作者>的微博 - 微博` 的格式。
- 提取博主、微博发布日期填入 karakeep 的作者和发布日期中。
- 提取头像作为 Banner Image。

虽然这样完全舍弃了微博的原始 UI，Archived Page 里只能看到超级简单的 HTML，但我的阅读器视图极其干净。
比较遗憾的是我没研究出来将微博图片原图加载出来的方法。SingleFile 只能抓当前已经载好的页面，但微博没法九图/18 图同时加载。n8n 也处理不了这个。
如果希望保留原始 UI 视图的话我觉得可以考虑从删 element 入手。现有清洗流挺适合我的，懒得改了。

清洗代码：

```js
function readAttribute(tag, name) {
  const escapedName = name.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
  const match = tag.match(new RegExp(
    `\\b${escapedName}\\s*=\\s*(?:"([^"]*)"|'([^']*)'|([^\\s>]+))`,
    'i',
  ));
  return match ? (match[1] ?? match[2] ?? match[3] ?? '') : null;
}

function findTagEnd(html, start) {
  let quote = '';
  for (let index = start; index < html.length; index += 1) {
    const character = html[index];
    if (quote) {
      if (character === quote) quote = '';
      continue;
    }
    if (character === '"' || character === "') {
      quote = character;
    } else if (character === '>') {
      return index + 1;
    }
  }
  return -1;
}

function decodeHtmlEntities(value) {
  return value
    .replace(/&nbsp;/gi, ' ')
    .replace(/&amp;/gi, '&')
    .replace(/&lt;/gi, '<')
    .replace(/&gt;/gi, '>')
    .replace(/&quot;/gi, '"')
    .replace(/&#39;|&apos;/gi, "')
    .replace(/&#(x?[0-9a-f]+);/gi, (_, number) => String.fromCodePoint(
      number[0].toLowerCase() === 'x'
        ? parseInt(number.slice(1), 16)
        : parseInt(number, 10),
    ));
}

function escapeHtml(value) {
  return String(value)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;');
}

function extractBalancedElement(html, start, tagName) {
  const escapedTag = tagName.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
  const tagPattern = new RegExp(`<\\/?${escapedTag}\\b[^>]*>`, 'gi');
  tagPattern.lastIndex = start;
  let depth = 0;
  let match;

  while ((match = tagPattern.exec(html))) {
    if (/^<\//.test(match[0])) {
      depth -= 1;
    } else if (!/\/>$/.test(match[0])) {
      depth += 1;
    }

    if (depth === 0) {
      return {
        start,
        end: tagPattern.lastIndex,
        html: html.slice(start, tagPattern.lastIndex),
      };
    }
  }

  return null;
}


function findElementByClassFragment(html, fragment, from = 0, to = html.length) {
  const openingTagPattern = /<([a-z][\w:-]*)\b[^>]*\bclass\s*=\s*(?:"([^"]*)"|'([^']*)'|([^\s>]+))[^>]*>/gi;
  openingTagPattern.lastIndex = from;
  let match;

  while ((match = openingTagPattern.exec(html)) && match.index < to) {
    const className = match[2] ?? match[3] ?? match[4] ?? '';
    if (className.includes(fragment)) {
      const element = extractBalancedElement(html, match.index, match[1]);
      if (element) return { ...element, tagName: match[1], className };
    }
  }

  return null;
}

function findElementByClassFragments(html, fragments, from = 0, to = html.length) {
  const openingTagPattern =
    /<([a-z][\w:-]*)\b[^>]*\bclass\s*=\s*(?:"([^"]*)"|'([^']*)'|([^\s>]+))[^>]*>/gi;
  openingTagPattern.lastIndex = from;
  let match;

  while ((match = openingTagPattern.exec(html)) && match.index < to) {
    const className = match[2] ?? match[3] ?? match[4] ?? '';
    if (!fragments.every(fragment => className.includes(fragment))) continue;
    const element = extractBalancedElement(html, match.index, match[1]);
    if (element) return { ...element, tagName: match[1], className };
  }

  return null;
}

function findWeiboContentElement(html, from = 0) {
  const weiboText = findElementByClassFragment(html, '_wbtext_', from);
  if (weiboText) return weiboText;

  const markdownRenderer = findElementByClassFragment(
    html,
    '_markdownRenderer_',
    from,
  );
  if (!markdownRenderer) return null;

  // A Markdown renderer also contains its own card header and action buttons.
  // Keep only the actual rendered Markdown body for Reader View.
  const markdownBody = findElementByClassFragment(
    markdownRenderer.html,
    'markdown-body',
  );
  if (!markdownBody) return markdownRenderer;

  return {
    ...markdownBody,
    start: markdownRenderer.start + markdownBody.start,
    end: markdownRenderer.start + markdownBody.end,
  };
}

function getCanonicalUrl(html) {
  const tag = html.match(
    /<link\b(?=[^>]*\brel\s*=\s*(?:"canonical"|'canonical'|canonical))[^>]*>/i,
  )?.[0];
  return tag ? decodeHtmlEntities(readAttribute(tag, 'href') || '') : '';
}

function extractPublishedText(...values) {
  for (const value of values) {
    if (!value) continue;
    const normalized = decodeHtmlEntities(String(value))
      .replace(/<br\s*\/?>/gi, ' ')
      .replace(/<[^>]+>/g, '')
      .replace(/\s+/g, ' ')
      .trim();

    const dashed = normalized.match(/\b\d{2,4}[-/.]\d{1,2}[-/.]\d{1,2}\s+\d{1,2}:\d{2}\b/);
    if (dashed) return dashed[0].replace(/[/.]/g, '-');

    const chinese = normalized.match(/\b(\d{4})年(\d{1,2})月(\d{1,2})日\s*(\d{1,2}):(\d{2})\b/);
    if (chinese) return `${chinese[1]}-${chinese[2]}-${chinese[3]} ${chinese[4]}:${chinese[5]}`;
  }

  return '';
}

function findPermalinkTime(html, postId) {
  const postBody = findElementByClassFragment(html, '_body_');
  if (!postBody) return null;

  const info = findElementByClassFragments(postBody.html, [
    'woo-box-flex',
    'woo-box-alignCenter',
    'woo-box-justifyCenter',
    '_info_',
  ]);
  if (!info) return null;

  const timeAnchorPattern = /<a\b[^>]*>/gi;
  let match;

  while ((match = timeAnchorPattern.exec(info.html))) {
    const className = readAttribute(match[0], 'class') || '';
    if (!className.includes('_time_')) continue;

    const anchor = extractBalancedElement(info.html, match.index, 'a');
    if (!anchor) continue;

    const text = extractPublishedText(
      anchor.html,
      readAttribute(match[0], 'title'),
      readAttribute(match[0], 'aria-label'),
      readAttribute(match[0], 'datetime'),
      readAttribute(match[0], 'data-time'),
    );
    if (!text) continue;

    const offset = postBody.start + info.start;
    return {
      ...anchor,
      start: offset + anchor.start,
      end: offset + anchor.end,
      href: decodeHtmlEntities(readAttribute(match[0], 'href') || ''),
      text,
    };
  }

  return null;
}

function findAuthor(html, uid) {
  // The post author's profile link is the aria-labelled <a> in the first
  // woo-box-flex row inside Weibo's post body. Do not infer authors from a
  // time-adjacent window: that can pick up avatars, sidebars, or comments.
  const postBody = findElementByClassFragment(html, '_body_ecgcn_63');
  if (!postBody || !uid) return '';

  const authorRow = findElementByClassFragment(postBody.html, 'woo-box-flex');
  if (!authorRow) return '';

  const profilePattern = new RegExp(
    `(?:^|//(?:www\\.)?weibo\\.com)/u/${uid}(?:[/?#]|$)`,
    'i',
  );
  const anchorPattern = /<a\b[^>]*>/gi;
  let match;

  while ((match = anchorPattern.exec(authorRow.html))) {
    const href = decodeHtmlEntities(readAttribute(match[0], 'href') || '');
    const ariaLabel = readAttribute(match[0], 'aria-label');
    if (!ariaLabel || !profilePattern.test(href)) continue;

    return decodeHtmlEntities(ariaLabel).trim();
  }

  return '';
}
function findAuthorAvatar(html, uid) {
  const postBody = findElementByClassFragment(html, '_body_ecgcn_63');
  if (!postBody || !uid) return '';

  const authorRow = findElementByClassFragment(postBody.html, 'woo-box-flex');
  if (!authorRow) return '';

  const profilePattern = new RegExp(
    `(?:^|//(?:www\\.)?weibo\\.com)/u/${uid}(?:[/?#]|$)`,
    'i',
  );
  const anchorPattern = /<a\b[^>]*>/gi;
  let match;

  while ((match = anchorPattern.exec(authorRow.html))) {
    const href = decodeHtmlEntities(readAttribute(match[0], 'href') || '');
    const ariaLabel = readAttribute(match[0], 'aria-label');
    if (!ariaLabel || !profilePattern.test(href)) continue;

    const authorLink = extractBalancedElement(authorRow.html, match.index, 'a');
    const avatarTag = authorLink?.html.match(/<img\b[^>]*>/i)?.[0] || '';
    const className = readAttribute(avatarTag, 'class') || '';
    const src = decodeHtmlEntities(readAttribute(avatarTag, 'src') || '');
    if (className.includes('woo-avatar-img') && src && src !== 'data:,') {
      return src;
    }
  }

  return '';
}
function replaceWeiboEmojiImagesWithText(html) {
  // Karakeep's Reader View sanitizes saved HTML and applies block-media rules
  // to both img and svg. Weibo already supplies an accessible text label on
  // each emoji image (for example, [并不简单]), which is the reliable inline
  // representation in Reader View.
  const WEIBO_EMOJI_TEXT_MAP = {
    // Add overrides here when a Weibo label needs a different archive text:
    // '[并不简单]': '🤔',
  };
  const emojiSource = /app+style\/(?:expression|expressin)\//i;

  // Do not use /<img[^>]*>/ here. SingleFile may inline an SVG inside a
  // single-quoted data URI, whose literal <svg ...> has its own `>`.
  // A regexp would stop at that inner `>` and corrupt the rest of the post.
  return mapHtmlImageTags(html, tag => {
    const src = decodeHtmlEntities(readAttribute(tag, 'src') || '');
    const alt = decodeHtmlEntities(readAttribute(tag, 'alt') || '');
    const title = decodeHtmlEntities(readAttribute(tag, 'title') || '');
    // Once SingleFile has inlined an image, its original Sina URL disappears.
    // Weibo emoji retain an exact bracketed alt/title label, e.g. [并不简单].
    const labelledEmoji = /^\[[^\]\r\n]{1,32}\]$/.test(alt)
      && (!title || title === alt);
    if (!emojiSource.test(src) && !labelledEmoji) return tag;

    const sourceLabel = alt || title || '[微博表情]';
    return escapeHtml(WEIBO_EMOJI_TEXT_MAP[sourceLabel] || sourceLabel);
  });
}

function mapHtmlImageTags(html, transform) {
  const imageStartPattern = /<img\b/gi;
  let result = '';
  let cursor = 0;
  let changed = false;
  let match;

  while ((match = imageStartPattern.exec(html))) {
    const end = findTagEnd(html, match.index);
    if (end < 0) break;

    const tag = html.slice(match.index, end);
    const replacement = transform(tag);
    result += html.slice(cursor, match.index) + replacement;
    cursor = end;
    if (replacement !== tag) changed = true;
    imageStartPattern.lastIndex = end;
  }

  return changed ? result + html.slice(cursor) : html;
}

function removeWeiboLinkIcons(html) {
  // Weibo renders short-link decorations as standalone 42x42 <img> elements
  // with class="icon-link". Keep the surrounding <a> and its text, but remove
  // the visual decoration so Reader View does not reserve a media block for it.
  const imageStartPattern = /<img\b/gi;
  let result = '';
  let cursor = 0;
  let removed = false;
  let match;

  while ((match = imageStartPattern.exec(html))) {
    const end = findTagEnd(html, match.index);
    if (end < 0) break;

    const tag = html.slice(match.index, end);
    const className = readAttribute(tag, 'class') || '';
    if (className.split(/\s+/).includes('icon-link')) {
      result += html.slice(cursor, match.index);
      cursor = end;
      removed = true;
    }
    imageStartPattern.lastIndex = end;
  }

  return removed ? result + html.slice(cursor) : html;
}

function tightenWeiboShortLinks(html) {
  // The removed icon used to have a whitespace node on each side. Match the
  // visible Weibo short-link label rather than a particular redirect URL:
  // SingleFile can rewrite/escape that URL, while the label survives intact.
  // This only joins the short link to its sentence; real paragraph breaks and
  // all other links remain untouched.
  return html.replace(
    /[\t\r\n ]*(<a\b[^>]*>\s*网页链接\s*<\/a>)[\t\r\n ]*/gi,
    '$1',
  );
}

function unwrapWeiboShortLinkRedirects(html) {
  // Weibo wraps outbound URLs in weibo.cn/sinaurl?u=<percent-encoded-url>.
  // Karakeep should retain the actual destination, not the tracking redirect.
  // Do not use the URL constructor here: some n8n Code sandboxes do not expose
  // it, and the previous defensive catch then silently kept the redirect.
  return html.replace(
    /(<a\b[^>]*\bhref\s*=\s*)(["'])(https?:\/\/weibo\.cn\/sinaurl\?[^"']*)\2/gi,
    (whole, prefix, quote, redirectUrl) => {
      try {
        const decodedRedirect = decodeHtmlEntities(redirectUrl);
        const parameter = decodedRedirect.match(/[?&]u=([^&#]*)/i);
        if (!parameter) return whole;
        const destination = decodeURIComponent(parameter[1].replace(/\+/g, '%20'));
        if (!destination || !/^https?:\/\//i.test(destination)) return whole;
        return `${prefix}${quote}${escapeHtml(destination)}${quote}`;
      } catch (_) {
        // Leave an unfamiliar or malformed redirect untouched.
        return whole;
      }
    },
  );
}

function htmlToPlainText(html) {
  return decodeHtmlEntities(
    html
      .replace(/<br\s*\/?>/gi, '\n')
      .replace(/<\/p\s*>/gi, '\n')
      .replace(/<[^>]+>/g, ''),
  )
    .replace(/\r/g, '')
    .replace(/\u00a0/g, ' ');
}

function takeDisplayWidth(value, maximumWidth) {
  let width = 0;
  let output = '';

  for (const character of Array.from(value)) {
    // Treat CJK/full-width characters as two columns, and Latin letters,
    // numbers, spaces, and ASCII punctuation as one. This matches the title
    // rule: ten Chinese characters, or up to twenty display-width columns.
    const characterWidth = /[^\x00-\xff]/u.test(character) ? 2 : 1;
    if (width + characterWidth > maximumWidth) break;
    output += character;
    width += characterWidth;
  }

  return output;
}

function toPublishedIso(value) {
  const match = value.match(/^(\d{2}|\d{4})-(\d{1,2})-(\d{1,2})\s+(\d{1,2}):(\d{2})$/);
  if (!match) return value;

  let year = Number(match[1]);
  if (year < 100) year += 2000;
  const pad = number => String(number).padStart(2, '0');

  // Weibo's displayed time is China Standard Time.
  return `${year}-${pad(match[2])}-${pad(match[3])}T${pad(match[4])}:${match[5]}:00+08:00`;
}

function makeSafeFileName(title) {
  return `${title.replace(/[\\/:*?"<>|]/g, '_').slice(0, 140)}.html`;
}

function cleanWeiboHtml(html) {
  const canonicalUrl = getCanonicalUrl(html);
  const urlMatch = canonicalUrl.match(/weibo\.com\/(\d+)\/([a-z0-9]+)/i);
  const uid = urlMatch?.[1] || '';
  const postId = urlMatch?.[2] || '';

  const bodyStart = Math.max(html.indexOf('<body'), 0);
  const firstContentElement = findWeiboContentElement(html, bodyStart);
  if (!firstContentElement) {
    throw new Error(
      'Could not find a Weibo _wbtext_ or _markdownRenderer_ element in the SingleFile HTML.',
    );
  }

  const timeAnchor = findPermalinkTime(html);
  if (!timeAnchor) {
    throw new Error(
      `Could not find the Weibo publish time. canonical=${canonicalUrl || '(missing)'}, `
      + `postId=${postId || '(missing)'}, contentOffset=${firstContentElement.start}`,
    );
  }

  const author = findAuthor(html, uid);
  if (!author) {
    throw new Error('Could not find the Weibo author in the SingleFile HTML.');
  }
  const avatar = findAuthorAvatar(html, uid);

  // Start after the permalink so sidebar/search results cannot win. Both class
  // suffixes change across Weibo builds, so match only their stable fragments.
  const contentElement = findWeiboContentElement(html, timeAnchor.end)
    || firstContentElement;
  if (!contentElement) {
    throw new Error('Could not find the Weibo post content in the SingleFile HTML.');
  }

  const contentHtml = replaceWeiboEmojiImagesWithText(
    unwrapWeiboShortLinkRedirects(
      tightenWeiboShortLinks(removeWeiboLinkIcons(contentElement.html)),
    ),
  );
  const plainText = htmlToPlainText(contentHtml);
  const firstLine = plainText.split('\n').map(line => line.trim()).find(Boolean) || '';
  const titlePrefix = takeDisplayWidth(firstLine, 20);
  if (!titlePrefix) throw new Error('The Weibo text element is empty.');

  const title = `${titlePrefix}... - @${author}的微博 - 微博`;
  const published = timeAnchor.text;
  const publishedIso = toPublishedIso(published);

  const schema = JSON.stringify({
    '@context': 'https://schema.org',
    '@type': 'SocialMediaPosting',
    headline: title,
    author: { '@type': 'Person', name: author },
    datePublished: publishedIso,
    url: canonicalUrl || timeAnchor.href,
  }).replace(/</g, '\\u003c');

  const avatarMeta = avatar
    ? `\n  <meta property="og:image" content="${escapeHtml(avatar)}">\n  <meta name="twitter:image" content="${escapeHtml(avatar)}">`
    : '';

  // Deliberately rebuild the document. Keeping Weibo's original <article>
  // causes Readability/Karakeep to include its header/avatar, woo-icon badges,
  // footer, and the data-v-c9baa151 reward widget.
  const cleanedHtml = `<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>${escapeHtml(title)}</title>
  <link rel="canonical" href="${escapeHtml(canonicalUrl)}">
  <meta name="author" content="${escapeHtml(author)}">
  <meta property="article:author" content="${escapeHtml(author)}">
  <meta name="date" content="${escapeHtml(published)}">
  <meta name="datePublished" content="${escapeHtml(publishedIso)}">
  <meta property="article:published_time" content="${escapeHtml(publishedIso)}">
  <meta property="og:type" content="article">
  <meta property="og:title" content="${escapeHtml(title)}">${avatarMeta}
  <script type="application/ld+json">${schema}</script>
</head>
<body>
  <article class="weibo-post" itemscope itemtype="https://schema.org/SocialMediaPosting">
    ${contentHtml}
  </article>
</body>
</html>`;

  return {
    cleanedHtml,
    metadata: {
      title,
      author,
      published,
      publishedIso,
      canonicalUrl: canonicalUrl || timeAnchor.href,
      postId,
      avatar,
    },
  };
}

async function getHtmlInput(item, itemIndex) {
  // A Base64 SingleFile document can temporarily occupy roughly 3-4 times its
  // file size while n8n converts it to a Buffer and then a JavaScript string.
  // Raise this only if the Code task runner has a correspondingly larger heap.
  const MAX_INPUT_BYTES = 64 * 1024 * 1024;
  const assertSafeSize = (byteLength, location) => {
    if (byteLength > MAX_INPUT_BYTES) {
      throw new Error(
        `${location} is ${(byteLength / 1024 / 1024).toFixed(1)} MiB. `
        + 'Refusing to decode it in the Code node to avoid OOM (limit: 64 MiB).',
      );
    }
  };
  const looksLikeSingleFileHtml = value => (
    typeof value === 'string'
    && (
      /<!doctype\s+html|<html\b/i.test(value.slice(0, 262144))
      || (/Page saved with SingleFile|Archive processed by SingleFile/i.test(value.slice(0, 262144))
        && /<title\b|<meta\b/i.test(value.slice(0, 262144)))
    )
  );

  function findHtmlInJson(value, path = [], seen = new Set()) {
    if (typeof value === 'string') {
      if (looksLikeSingleFileHtml(value)) {
        assertSafeSize(Buffer.byteLength(value, 'utf8'), `JSON field ${path.join('.') || '(root)'}`);
        return { html: value, path };
      }

      if (/^data:text\/html[^,]*;base64,/i.test(value)) {
        const encoded = value.slice(value.indexOf(',') + 1);
        const expectedBytes = Math.floor(encoded.length * 0.75);
        assertSafeSize(expectedBytes, `JSON data URI ${path.join('.') || '(root)'}`);
        const prefix = Buffer.from(encoded.slice(0, 16384), 'base64').toString('utf8');
        if (looksLikeSingleFileHtml(prefix)) {
          return { html: Buffer.from(encoded, 'base64').toString('utf8'), path };
        }
      }

      if (/%3C(?:!doctype|html|meta|title)\b/i.test(value)) {
        assertSafeSize(value.length, `URL-encoded JSON field ${path.join('.') || '(root)'}`);
        try {
          const decoded = decodeURIComponent(value.replace(/\+/g, '%20'));
          if (looksLikeSingleFileHtml(decoded)) return { html: decoded, path };
        } catch {}
      }

      // Some webhook configurations place the uploaded file in a plain
      // Base64 JSON field without a data: prefix.
      if (value.length > 1000 && /^[a-z0-9+/=]+$/i.test(value.slice(0, 16384))) {
        let prefix = '';
        try {
          prefix = Buffer.from(value.slice(0, 16384), 'base64').toString('utf8');
        } catch {}
        if (looksLikeSingleFileHtml(prefix)) {
          const expectedBytes = Math.floor(value.length * 0.75);
          assertSafeSize(expectedBytes, `Base64 JSON field ${path.join('.') || '(root)'}`);
          return { html: Buffer.from(value, 'base64').toString('utf8'), path };
        }
      }

      return null;
    }

    if (!value || typeof value !== 'object' || seen.has(value)) return null;
    seen.add(value);

    if (value.type === 'Buffer' && Array.isArray(value.data)) {
      assertSafeSize(value.data.length, `Buffer JSON field ${path.join('.') || '(root)'}`);
      const decoded = Buffer.from(value.data).toString('utf8');
      if (looksLikeSingleFileHtml(decoded)) return { html: decoded, path };
    }

    for (const [key, child] of Object.entries(value)) {
      const found = findHtmlInJson(child, [...path, key], seen);
      if (found) return found;
    }

    return null;
  }

  const jsonSource = findHtmlInJson(item.json);
  if (jsonSource) return { ...jsonSource, kind: 'json' };

  const binaryKeys = Object.keys(item.binary || {}).sort((left, right) => {
    const score = key => {
      const binary = item.binary[key];
      return (binary?.mimeType === 'text/html' ? 2 : 0)
        + (/\.html?$/i.test(binary?.fileName || '') ? 1 : 0);
    };
    return score(right) - score(left);
  });

  for (const binaryKey of binaryKeys) {
    const binary = item.binary[binaryKey];
    let buffer;

    // Prefer n8n's helper. In filesystem/S3 binary-data modes, binary.data is
    // an opaque storage reference string, not Base64 file content.
    if (this?.helpers?.getBinaryDataBuffer) {
      try {
        buffer = await this.helpers.getBinaryDataBuffer(itemIndex, binaryKey);
      } catch {}
    }
    if (buffer) assertSafeSize(buffer.length, `Binary property ${binaryKey}`);

    if (!buffer && typeof binary.data === 'string') {
      // Probe only the beginning. Do not copy/normalize the complete Base64
      // string unless it is confirmed to contain an HTML document.
      const prefixText = Buffer.from(binary.data.slice(0, 16384), 'base64').toString('utf8');
      if (looksLikeSingleFileHtml(prefixText)) {
        const expectedBytes = Math.floor(binary.data.length * 0.75);
        assertSafeSize(expectedBytes, `Binary property ${binaryKey}`);
        buffer = Buffer.from(binary.data, 'base64');
      }
    }

    if (!buffer) {
      continue;
    }

    const html = buffer.toString('utf8');
    if (/<!doctype\s+html|<html\b/i.test(html)) {
      return { html, kind: 'binary', key: binaryKey };
    }
  }

  throw new Error(
    `No SingleFile HTML was found. JSON keys: ${Object.keys(item.json || {}).join(', ') || '(none)'}; `
    + `binary keys: ${Object.keys(item.binary || {}).join(', ') || '(none)'}.`,
  );
}

function setJsonPath(root, path, value) {
  if (!path.length) throw new Error('The HTML cannot replace the entire n8n JSON item.');
  let target = root;
  for (let index = 0; index < path.length - 1; index += 1) {
    target = target[path[index);
  }
  target[path.at(-1)] = value;
}

const inputItems = $input.all();
const outputItems = [];

for (let index = 0; index < inputItems.length; index += 1) {
  const item = inputItems[index];
  const source = await getHtmlInput.call(this, item, index);
  const { cleanedHtml, metadata } = cleanWeiboHtml(source.html);
  const json = {
    ...item.json,
    weiboTitle: metadata.title,
    weiboAuthor: metadata.author,
    weiboPublished: metadata.published,
    weiboPublishedIso: metadata.publishedIso,
    weiboCanonicalUrl: metadata.canonicalUrl,
    weiboAvatar: metadata.avatar,
  };

  if (source.kind === 'json') {
    setJsonPath(json, source.path, cleanedHtml);
  }

  // Karakeep's /bookmarks/singlefile endpoint reads the multipart field named
  // "file". Always create that exact n8n binary property, even when the input
  // HTML arrived in $json.body. Do not keep the original archive in the output:
  // that prevents the HTTP node from accidentally uploading it and lowers peak
  // n8n memory usage.
  const binary = {
    file: {
      data: Buffer.from(cleanedHtml, 'utf8').toString('base64'),
      mimeType: 'text/html',
      fileExtension: 'html',
      fileName: makeSafeFileName(metadata.title),
    },
  };

  outputItems.push({ json, binary });
}

return outputItems;

```

清洗效果：

![](微博-清洗前-1.png)

![](微博-清洗后-1.png)

![](微博-清洗前-2.png)

![](微博-清洗后-2.png)

## 结语

1. 这方法确实好。cookie 成天还得担心过期，SingleFile 直接“我自己就是合法用户，你敢拦我！”，且抓到的 HTML 还能自己修改，自由度很大。
2. 这个方法理论上适用于任何网站。任何 karakeep 直接爬会被拦的网站都可以用 SingleFile 再试试。
3. 本来还想一起讲讲小红书的清洗的，但是小红书因为我给 karakeep 配了 cookie 而把我判定为爬虫，封了我的号。走了！