+++
title = "Generate image using html2canvas"
tags = [ "JavaScript" ]
+++
需求：点击“分享”按钮，动态生成一张包含分享信息和二维码的图片，让用户在微信分享图片。

如果不考虑用服务器生成图片，在客户端浏览器生成的话，可以使用 canvas 来生成。

html2canvas 能将 HTML 页面“截图”成为 canvas ，但是有很多效果是不能显示的，
如过渡颜色的背景，阴影等等。

可以用 iframe 加载生成分享信息的页面，当加载完成后就进行“截图”。

```js
var iframe = $('<iframe />', {
  load: function () {
    var w = this.contentWindow;
    if (!w || typeof w.getScreenshot !== 'function') return;
    w.getScreenshot().then(function (canvas) {
      $('#target-img').empty().append($('<img />', {
        src: canvas.toDataURL('image/png'),
      }).attr({
        width: canvas.width/2,
        height: canvas.height/2
      }));
    });
  }
}).hide().appendTo('body');

// ...

$('#btn').click(function () {
  iframe[0].src = '<the-page-url>';
});
```

截图时可根据不同需求对 canvas 进行修改：

```js
window.getScreenshot = function () {
  var canvas = document.createElement('canvas');

  // reset position
  document.body.scrollLeft = 0;
  document.body.scrollTop = 0;

  // retina support
  var w = document.body.scrollWidth;
  var h = document.body.scrollHeight;
  canvas.width = w * 2;
  canvas.height = h * 2;
  canvas.style.width = w + 'px';
  canvas.style.height = h + 'px';
  var ctx = canvas.getContext('2d');
  ctx.scale(2, 2);

  // html2canvas can't draw gradient background, so draw one manually
  var gradient = ctx.createRadialGradient(w/2, 100, 500, w/2, 100, 0);
  gradient.addColorStop(0, '#c11204');
  gradient.addColorStop(1, '#ff355a');
  ctx.fillStyle = gradient;
  ctx.fillRect(0, 0, w, h);

  return html2canvas(document.body, {
    canvas: canvas,
    width: w,
    height: h
  });
};
```

如果网页上有图像跨域问题，可通过如下方式解决：

```js
// <img class="poster" id="image" data-src="https://dn-foobar.qbox.me/poster/example?imageView2/1/w/212/h/304">
(function () {
  // convert image url to data-uri, so that no cross origin problem for html2canvas
  var img = new Image();
  img.setAttribute('crossOrigin', 'anonymous');
  img.onload = function () {
    var canvas = document.createElement('canvas');
    canvas.width = img.width;
    canvas.height = img.height;
    var ctx = canvas.getContext("2d");
    ctx.drawImage(img, 0, 0);
    document.querySelector('#image').src = canvas.toDataURL('image/jpeg');
  };
  img.src = document.querySelector('#image').dataset.src;
})();
```
