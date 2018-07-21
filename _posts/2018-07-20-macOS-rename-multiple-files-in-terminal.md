---
layout: post
title: Rename multiple files on macOS with Terminal
tags: [ macOS, terminal, cli, rename ]
excerpt_separator: <!--more-->
---

Quickly rename multiple files using terminal in macOS using rename

Install the `rename` utility

`brew install rename`

In the directory that you're in, do the following (quotes are optional):

`rename -nvs "<searchtext>" "<renamedtext>" *`

Output will be something like this:

<!--more-->

```
[user@Macbook:~]$rename -nvs "IMG_" "" *
Using expression: sub { use feature ':5.18'; s/\Q${\"IMG_2"}/2/ }
'20180713_Myimage.jpg' unchanged
'20180716_someotherimage.jpg' unchanged
'20180717_yetanotherimage.jpg' unchanged
'20180717_somanyimages.jpg' unchanged
'IMG_20180712_223021.jpg' would be renamed to '20180712_223021.jpg'
'IMG_20180712_223043.jpg' would be renamed to '20180712_223043.jpg'
'IMG_20180712_223057.jpg' would be renamed to '20180712_223057.jpg'
'IMG_20180712_223116.jpg' would be renamed to '20180712_223116.jpg'
'IMG_20180712_223137.jpg' would be renamed to '20180712_223137.jpg'
```

In order to actually change the names, remove `n` from `-nvs` and you're good to go.

`rename -vs "<searchtext>" "<renamedtext>" *`

Output of the command in the directory:

```
[user@Macbook:~]$rename -vs "IMG_" "" *
'IMG_20180712_223021.jpg' renamed to '20180712_223021.jpg'
'IMG_20180712_223043.jpg' renamed to '20180712_223043.jpg'
'IMG_20180712_223057.jpg' renamed to '20180712_223057.jpg'
'IMG_20180712_223116.jpg' renamed to '20180712_223116.jpg'
'IMG_20180712_223137.jpg' renamed to '20180712_223137.jpg'
```




If you find any errors or have suggestions to improve this article, please feel free to contact Jon at <blog@tekrx.ca>

---

All applications mentioned in this article are not endorsed by TekRx Solutions and are to be used at your own discretion. TekRx Solutions does not take responsibility for any liability in the use of any application or configuration mentioned in the article.
