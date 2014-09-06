---
layout: post-no-feature
title:  "ImageMagick’s `identify` is a great tool for frontend developers"
date:   2014-09-06 15:38
categories: articles
---

`identify` is a tool that outputs statistics about the images it is given as input. With the `-format` flag, you can set it to output almost any format.

## HTML

Here I export all the images of a given folder to an HTML preview file:

{% highlight sh %}
identify -format '<p><div>%w x %h</div><img src=%f></p>\n' *
{% endhighlight %}

output
{% highlight html %}<p><div>199 x 340</div><img src="ah-peku.webp"></p>
<p><div>220 x 340</div><img src="angry-alien.webp"></p>
<p><div>230 x 340</div><img src="betser.webp"></p>
<p><div>211 x 340</div><img src="bomby.webp"></p>
<p><div>181 x 340</div><img src="bulls-eye-jones.webp"></p>
<p><div>192 x 340</div><img src="cleosaga.webp"></p>
<p><div>260 x 293</div><img src="crabman.webp"></p>
<p><div>241 x 340</div><img src="crazy-alien.webp"></p>
<p><div>260 x 239</div><img src="diamond-dwarf.webp"></p>
<p><div>237 x 340</div><img src="dodgy-james.webp"></p>
<p><div>175 x 340</div><img src="dolpho.webp"></p>
<p><div>178 x 340</div><img src="dr-wutten.webp"></p>
{% endhighlight %}

## JSON

I feel most at home in JavaScript. If I want to query my images as a JavaScript object, all I have to do is

{% highlight sh %}
echo "[";
identify -format '  {"filename": "%f", "width": "%w", "height": "%h"},\n' *
echo "]";
{% endhighlight %}

output
{% highlight json %}
[
  {"filename": "ah-peku.webp", "width": "199", "height": "340"},
  {"filename": "angry-alien.webp", "width": "220", "height": "340"},
  {"filename": "betser.webp", "width": "230", "height": "340"},
  {"filename": "bomby.webp", "width": "211", "height": "340"},
  {"filename": "bulls-eye-jones.webp", "width": "181", "height": "340"},
  {"filename": "cleosaga.webp", "width": "192", "height": "340"},
  {"filename": "crabman.webp", "width": "260", "height": "293"},
  {"filename": "crazy-alien.webp", "width": "241", "height": "340"},
  {"filename": "diamond-dwarf.webp", "width": "260", "height": "239"},
  {"filename": "dodgy-james.webp", "width": "237", "height": "340"},
  {"filename": "dolpho.webp", "width": "175", "height": "340"},
  {"filename": "dr-wutten.webp", "width": "178", "height": "340"},
]
{% endhighlight %}

Please note you have to remove the last comma for the JSON to properly validate.

## CSS

Sometimes you want to set the width and height explicitly in CSS. This uses the filename (without extension) as a class and sets the width and height of that one to the dimensions of the file:

{% highlight sh %}
identify -format ".%t { width:%[w]px; height:%[h]px; }\n" *
{% endhighlight %}
result
{% highlight css %}
.ah-peku { width:199px; height:340px; }
.angry-alien { width:220px; height:340px; }
.betser { width:230px; height:340px; }
.bomby { width:211px; height:340px; }
.bulls-eye-jones { width:181px; height:340px; }
.cleosaga { width:192px; height:340px; }
.crabman { width:260px; height:293px; }
.crazy-alien { width:241px; height:340px; }
.diamond-dwarf { width:260px; height:239px; }
.dodgy-james { width:237px; height:340px; }
.dolpho { width:175px; height:340px; }
.dr-wutten { width:178px; height:340px; }
{% endhighlight %}

## CSS preprocessor

This is my favorite. For some images, I need to pad them enough to be effectively aligned to the bottom and centered from left and right. This way all images fit the same box.

{% highlight sh %}
identify -format ".generate-fit-into(260, 340, %t, %w, %h);\n" *
{% endhighlight %}

output

{% highlight scss %}
.generate-fit-into(260, 340, ah-peku, 199, 340);
.generate-fit-into(260, 340, angry-alien, 220, 340);
.generate-fit-into(260, 340, betser, 230, 340);
.generate-fit-into(260, 340, bomby, 211, 340);
.generate-fit-into(260, 340, bulls-eye-jones, 181, 340);
.generate-fit-into(260, 340, cleosaga, 192, 340);
.generate-fit-into(260, 340, crabman, 260, 293);
.generate-fit-into(260, 340, crazy-alien, 241, 340);
.generate-fit-into(260, 340, diamond-dwarf, 260, 239);
.generate-fit-into(260, 340, dodgy-james, 237, 340);
.generate-fit-into(260, 340, dolpho, 175, 340);
.generate-fit-into(260, 340, dr-wutten, 178, 340);
{% endhighlight %}
To be combined with
{% highlight scss %}
.generate-fit-into(@w, @h, @key, @width, @height) {
    &.fit-into@{w}x@{h}.@{key} {
        @marginLeft:  (@w - @width) / 2;
        @marginRight: (@w - @width) / 2;
        width: unit(@width, px);
        height: unit(@height, px);
        margin-top:   unit(@h - @height, px);
        margin-left:  unit(@marginLeft,  px);
        margin-right: unit(@marginRight, px);
    }
}
{% endhighlight %}
output
{% highlight css %}
.fit-into260x340.ah-peku{
  width:199px;height:340px;
  margin-top:0;margin-left:30.5px;margin-right:30.5px;
}
.fit-into260x340.angry-alien{
  width:220px;height:340px;
  margin-top:0;margin-left:20px;margin-right:20px;
}
.fit-into260x340.betser{
  width:230px;height:340px;
  margin-top:0;margin-left:15px;margin-right:15px;
}
.fit-into260x340.bomby{
  width:211px;height:340px;
  margin-top:0;margin-left:24.5px;margin-right:24.5px;
}
.fit-into260x340.bulls-eye-jones{
  width:181px;height:340px;
  margin-top:0;margin-left:39.5px;margin-right:39.5px;
}
.fit-into260x340.cleosaga{
  width:192px;height:340px;
  margin-top:0;margin-left:34px;margin-right:34px;
}
.fit-into260x340.crabman{
  width:260px;height:293px;
  margin-top:47px;margin-left:0;margin-right:0;
}
.fit-into260x340.crazy-alien{
  width:241px;height:340px;
  margin-top:0;margin-left:9.5px;margin-right:9.5px;
}
.fit-into260x340.diamond-dwarf{
  width:260px;height:239px;
  margin-top:101px;margin-left:0;margin-right:0;
}
.fit-into260x340.dodgy-james{
  width:237px;height:340px;
  margin-top:0;margin-left:11.5px;margin-right:11.5px;
}
.fit-into260x340.dolpho{
  width:175px;height:340px;
  margin-top:0;margin-left:42.5px;margin-right:42.5px;
}
.fit-into260x340.dr-wutten{
  width:178px;height:340px;
  margin-top:0;margin-left:41px;margin-right:41px;
}
{% endhighlight %}

Now these classes can also be used for @2x images and they would still be fixed to display in the same CSS pixel dimensions as the “normal” ones.

