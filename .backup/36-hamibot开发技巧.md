[hamibot开发技巧](https://github.com/cloudswave/blog/issues/36)

## 问题1：无法通过自带的布局范围分析工具轻易过滤找到想要的控件，如多多果园里的弹框的关闭按钮
- 尝试多切换几次界面，然后通过自带的布局范围分析工具查看控件边框情况
- 尝试找到控件附近的文本控件，通过它的bounds估算目标控件的位置，再使用boundsInside()或boundsContains()进一步匹配。举例获取多多果园里的弹框关闭按钮：
```
log(text("买菜得水滴").findOne().bounds()); // 打印附近某个文本的位置和大小
let v = boundsInside(850,600,1080,776); // 估算弹框关闭按钮的范围
// v = boundsContains(500, 300, 500, 300) // 给定一个点(500, 300), 寻找在这个点上的控件
let list = v.find();
log(list.length); // 如果长度大于1可以遍历
for(let i=0;i<list.length;i++){
   v = list[i];
   log(v.bounds(),v.className(),v.depth(),v.text()); // 打印具体信息分析是不是关闭按钮
}

let closeBtn = boundsInside(w * 2/3, 0, w, h/2).text("webp").filter(function(w) {
      let b = w.bounds();
      return b.bottom-b.top > 80 && b.bottom-b.top < 90;
});

textContains("记录").filter(function(w) {
  let b = w.bounds();
  return b.bottom-b.top > 0 && b.bottom <= h - 200 && b.top > recordBounds.bottom;
}).find().forEach(function(i){
  log(i.text(),i.id(),i.bounds(),i.depth(),i.className());
});
boundsInside(w * 2/3, 0, w, h/2).text("webp").find().forEach(function(i){
  log(i.text(),i.id(),i.bounds(),i.depth(),i.className());
});

```
- 遍历整个UI树尝试绘制每个控件的边框，未具体验证
