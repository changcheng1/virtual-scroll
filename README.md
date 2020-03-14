<!--
 * @Author: your name
 * @Date: 2020-03-11 16:00:39
 * @LastEditTime: 2020-03-11 16:10:45
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /webNotes/Users/changcheng/Downloads/virtual-scroll/README.md
 -->
# 大数据量虚拟列表优化
+ 参考插件 vue-virtual-scroll-list

> 分片加载会导致dom节点过多造成页面的卡顿

> 大数据量虚拟列表优化，只渲染当前的可是区域

```javaScript

<div id="container"></div>

    let total = 100000;
    let index = 0;
    function load() {
        index = index += 50
        if (index < total) {
            // setTimeout是一个宏任务  requestAnimationFrame 也是一个宏任务，可以配合浏览器刷新频率
            requestAnimationFrame(() => { // 分片渲染 因为setTimeout是一个宏任务，会等待ui渲染完成再执行下一个宏任务
                let fragment = document.createDocumentFragment()
                let li = document.createElement('li')
                for (var i = 0; i < 50; i++) {
                    li.innerHTML = i
                    fragment.appendChild(li)
                }
                document.getElementById('container').appendChild(fragment)
                load()
            })
        }
    }
    load()
```