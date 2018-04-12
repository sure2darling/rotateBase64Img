# rotateBase64Img
>It's time to show my fluent Chinese

>有不合理的地方欢迎小伙伴雅正

**js旋转base64图片90*N度，并产生新的base64数据**

项目开发中，衣食父母方希望上传图片的时候可以调整图片的方向，这也是一个合理要求，不合理又怎么样呢。

时间紧迫，我立刻阅览资料，寄希望与现成的插件，网上的思路是有了，就是利用canvas做图片旋转，然后用todataURL()函数生产base64数据，思路是对的，就是函数都不好用，然后自己决定做一个吧。

项目rotateBase64Img.html下载下即用。

具体函数的代码就贴在下面了，三个参数

1. src:图片链接，无论是url地址还是base64数据都可以
2. edg:旋转角度，注意必须是90°的倍数，否则代码报错，非90°的旋转的根据业务需要裁切裁切，用到的话小伙伴可以动动手修改一下函数
3. callback:因为img的load是个异步的，所以这才采取回调函数处理load成功的事件，回调的参数就是图片旋转后的base64的数据



        function rotateBase64Img(src, edg, callback) {

          var canvas = document.createElement("canvas");
          var ctx = canvas.getContext("2d");

          var imgW;//图片宽度
          var imgH;//图片高度
          var size;//canvas初始大小

          if (edg % 90 != 0) {
              console.error("旋转角度必须是90的倍数!");
              throw '旋转角度必须是90的倍数!';
          }
          (edg < 0) && (edg = (edg % 360) + 360)
          const quadrant = (edg / 90) % 4; //旋转象限
          const cutCoor = {sx: 0, sy: 0, ex: 0, ey: 0}; //裁剪坐标

          var image = new Image();
          image.crossOrigin = "anonymous"
          image.src = src;

          image.onload = function () {

              imgW = image.width;
              imgH = image.height;
              size = imgW > imgH ? imgW : imgH;

              canvas.width = size * 2;
              canvas.height = size * 2;
              switch (quadrant) {
                  case 0:
                      cutCoor.sx = size;
                      cutCoor.sy = size;
                      cutCoor.ex = size + imgW;
                      cutCoor.ey = size + imgH;
                      break;
                  case 1:
                      cutCoor.sx = size - imgH;
                      cutCoor.sy = size;
                      cutCoor.ex = size;
                      cutCoor.ey = size + imgW;
                      break;
                  case 2:
                      cutCoor.sx = size - imgW;
                      cutCoor.sy = size - imgH;
                      cutCoor.ex = size;
                      cutCoor.ey = size;
                      break;
                  case 3:
                      cutCoor.sx = size;
                      cutCoor.sy = size - imgW;
                      cutCoor.ex = size + imgH;
                      cutCoor.ey = size + imgW;
                      break;
              }


              ctx.translate(size, size);
              ctx.rotate(edg * Math.PI / 180);
              ctx.drawImage(image, 0, 0);

              var imgData = ctx.getImageData(cutCoor.sx, cutCoor.sy, cutCoor.ex, cutCoor.ey);
              if (quadrant % 2 == 0) {
                  canvas.width = imgW;
                  canvas.height = imgH;
              } else {
                  canvas.width = imgH;
                  canvas.height = imgW;
              }
              ctx.putImageData(imgData, 0, 0);
              callback(canvas.toDataURL())
          };
        }

函数有些大，不优雅，有什么不合理的地方欢迎小伙伴批评指正，共勉共勉。
