vue+css+js完成一个简单的图片预览组件

<!-- more -->

效果图:

![效果图](http://selfblog-1253589063.file.myqcloud.com/post/img_preview.png)

使用方法:

html
```html
<preview ref="preview"></preview>
```
javascript
```javascript
import preview from '路径';
fnPreviewImage(images) {
    this.$refs.preview.show(images);
},
```
