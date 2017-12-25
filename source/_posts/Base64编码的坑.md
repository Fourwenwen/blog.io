---
title: Base64编码的坑
date: 2017-12-23 17:14:04
tags:
---

Base64相对大家来说平时用得比较多，但很多朋友只知道用encode和decoder,认为Base64我这边编码了，数据传到你那边就解码就行了。
在这个星期里，有两位朋友都向我求救为什么后台传给我的Base64我解不了。我让他们让后台打印下Base64数据，在控制台上是很整齐的一排。问题基本可以确定是后台Base64编码的时候没76个字符或64个字符（RFC 1421，已弃用）产生了换行符。

既然我们知道问题所在，那么解决就很容易啦，要不编码方换个Base64方法，产生没有换行符的结果，要不就生成方或者接收方对字符串进行处理下replaceAll("\r\n", ""),或者接收方换个能出来有换行符的Base64数据的方法。

那么问题来了，为什么有些Base64编码会有换行符呢？


<table class="wikitable" style="text-align:center">
<tbody><tr>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">变种</font></font></th>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Char为索引62</font></font></th>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Char为索引63</font></font></th>
<th><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">垫</font></font></i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">炭</font></font></th>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">固定编码的行长</font></font></th>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">最大编码行长度</font></font></th>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">行分隔符</font></font></th>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">字母表外的字符</font></font></th>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">线校验</font></font></th>
</tr>
<tr>
<th><font style="vertical-align: inherit;"></font><a href="#Privacy-enhanced_mail"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">隐私增强邮件（PEM）的</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">原始Base64 </font><font style="vertical-align: inherit;">（RFC 1421，已弃用）</font></font></th>
<td><code>+</code></td>
<td><code>/</code></td>
<td><code>=</code> <i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（强制）</font></font></i></td>
<td style="background:#9F9;vertical-align:middle;text-align:center;" class="table-yes"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">是（除了最后一行）</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">64</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">CR + LF</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">用于</font></font><a href="#MIME"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">MIME的</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> Base64传输编码</font><font style="vertical-align: inherit;">（RFC 2045）</font></font></th>
<td><code>+</code></td>
<td><code>/</code></td>
<td><code>=</code> <i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（强制）</font></font></i></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">76</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">CR + LF</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">接受（丢弃）</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"></font><a href="#RFC_3548"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">RFC 3548</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">或</font><a href="#RFC_4648"><font style="vertical-align: inherit;">RFC 4648的</font></a><font style="vertical-align: inherit;">标准“base64”编码</font></font><a href="#RFC_4648"><font style="vertical-align: inherit;"></font></a></th>
<td><code>+</code></td>
<td><code>/</code></td>
<td><code>=</code> <i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（除非通过参考文件指定，否则为强制性）</font></font></i></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否</font></font><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（除非由参考文件指定）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（除非参考文件指定）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（除非参考文件指定）</font></font></i></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">禁止</font></font><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（除非通过引用文档指定）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"></font><a href="#OpenPGP"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">OpenPGP的</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> “Radix-64”编码</font><font style="vertical-align: inherit;">（RFC 4880）</font></font></th>
<td><code>+</code></td>
<td><code>/</code></td>
<td><code>=</code> <i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（强制）</font></font></i></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">76</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">CR + LF</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">24位</font></font><a href="/wiki/Cyclic_redundancy_check" title="循环冗余校验"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">CRC</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（基数-64编码，包括一个</font></font><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">填充</font></font></i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">字符）</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"></font><a href="#UTF-7"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">UTF-7的</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">修改Base64编码</font><font style="vertical-align: inherit;">（RFC 1642，已废弃）</font></font></th>
<td><code>+</code></td>
<td><code>/</code></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">修改</font></font><a href="#Internet_Message_Access_Protocol"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">IMAP</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">邮箱名称的</font><font style="vertical-align: inherit;">Base64编码</font><font style="vertical-align: inherit;">（</font></font><a class="external mw-magiclink-rfc" rel="nofollow" href="https://tools.ietf.org/html/rfc3501"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">RFC 3501</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">）</font></font></th>
<td><code>+</code></td>
<td><code>,</code></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">带</font></font><a href="#URL_applications"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">URL</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">和</font></font><a href="#Filenames"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">文件名</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">安全字母表的</font><font style="vertical-align: inherit;">标准“base64url” </font><font style="vertical-align: inherit;">（</font></font><a href="#RFC_4648"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">RFC</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> 4648§5'表2：“URL和文件名安全”Base 64字母表）</font></font></th>
<td><code>-</code></td>
<td><code>_</code></td>
<td style="background: #ddffdd; color: black; vertical-align: middle; text-align: center;"><code>=</code> <i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（如果数据长度已知，则为可选，否则必须在URL中进行百分比编码）</font></font></i></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（应用相关）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">未压缩的“base64url”（例如</font></font><a rel="nofollow" class="external text" href="https://tools.ietf.org/html/rfc7515#appendix-C"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">RFC7515</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">）</font></font></th>
<td><code>-</code></td>
<td><code>_</code></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（应用相关）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（没有，或单独的</font><a href="/w/index.php?title=RFC_6920&amp;action=edit&amp;redlink=1" class="new" title="RFC 6920 (page does not exist)"><font style="vertical-align: inherit;">RFC 6920中的</font></a></font><a href="/wiki/Luhn_algorithm" title="Luhn算法"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Luhn</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">校验和</font><font style="vertical-align: inherit;">）</font></font><a href="/w/index.php?title=RFC_6920&amp;action=edit&amp;redlink=1" class="new" title="RFC 6920（页面不存在）"><font style="vertical-align: inherit;"></font></a><font style="vertical-align: inherit;"></font></i></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"><a href="/wiki/YUI_Library" title="YUI Library"><font style="vertical-align: inherit;">YUI库中</font></a><font style="vertical-align: inherit;">使用的Base64 </font><font style="vertical-align: inherit;">的非标准</font></font><a href="#URL_applications"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">URL安全</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">修改</font><font style="vertical-align: inherit;">（Y64）</font><sup id="cite_ref-5" class="reference"><a href="#cite_note-5"><font style="vertical-align: inherit;">[5]</font></a></sup></font><a href="/wiki/YUI_Library" title="YUI图书馆"><font style="vertical-align: inherit;"></font></a><font style="vertical-align: inherit;"></font><sup id="cite_ref-5" class="reference"><a href="#cite_note-5"><font style="vertical-align: inherit;"></font></a></sup></th>
<td><code>.</code></td>
<td><code>_</code></td>
<td><code>-</code></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（应用相关）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">修改用于</font></font><a href="#XML"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">XML</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">名称标记的</font><font style="vertical-align: inherit;">Base64 </font><font style="vertical-align: inherit;">（</font></font><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Nmtoken</font></font></i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">）</font></font></th>
<td><code>.</code></td>
<td><code>-</code></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（依赖于XML解析器）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"></font><a href="#XML"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">XML</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">标识的</font><font style="vertical-align: inherit;">修改Base64 </font><font style="vertical-align: inherit;">（</font></font><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">名称</font></font></i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">）</font></font></th>
<td><code>_</code></td>
<td><code>:</code></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（依赖于XML解析器）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"></font><a href="#Program_identifiers"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">程序标识符</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">修改的Base64 </font><font style="vertical-align: inherit;">（变体1，非标准）</font></font></th>
<td><code>_</code></td>
<td><code>-</code></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（语言/依赖于系统的）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"></font><a href="#Program_identifiers"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">程序标识符的</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">修改Base64 </font><font style="vertical-align: inherit;">（变体2，非标准）</font></font></th>
<td><code>.</code></td>
<td><code>_</code></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（语言/依赖于系统的）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
<tr>
<th><font style="vertical-align: inherit;"><a href="/wiki/Freenet" title="Freenet"><font style="vertical-align: inherit;">Freenet中</font></a><font style="vertical-align: inherit;">使用的Base64 </font><font style="vertical-align: inherit;">的非标准</font></font><a href="#URL_applications"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">URL安全性</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">修改</font></font><a href="/wiki/Freenet" title="自由网"><font style="vertical-align: inherit;"></font></a></th>
<td><code>~</code></td>
<td><code>-</code></td>
<td><code>=</code></td>
<td style="background:#F99;vertical-align:middle;text-align:center;" class="table-no"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">否（可变）</font></font></td>
<td style="background: #FED; color: black; vertical-align: middle; text-align: center;" class="depends table-depends"><i><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">（应用相关）</font></font></i></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
<td><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">被禁止</font></font></td>
<td style="background:#ececec;vertical-align:middle;text-align:center;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">没有</font></font></td>
</tr>
</tbody></table>
