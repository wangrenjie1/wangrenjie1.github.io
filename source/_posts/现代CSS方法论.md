---
title: 现代CSS方法论
date: 2019-06-25 08:51:22
tags: ['CSS']
categories: CSS
---

# BEM

BEM通过Block、Element、Modifier来描述页面。
- Block是页面中独立存在的区块，可以在不同场合下被重用。每个页面都可以看做是多个Block组成
- Element是构成Block的元素，只有在对应Block内部才具有意义，是依赖于Block的存在。
- Modifier是描述Block或Element的属性或状态。同一Block或Element可以有多个Modifier。
- Block__element--modifier  "__"表示块里内容 "--"表示状态
  
# ACSS
定义： 考虑如何设计一个系统的接口。原子(Atoms)是创建一个区块的最基本的特质，比如说表单按钮。分子(Molecules)是很多个原子(Atoms)的组合，比如说一个表单中包括了一个标签，输入框和按钮。生物(Organisms)是众多分子(Molecules)的组合物，比如一个网站的顶部区域，它包括了网站的标题、导航等。而模板(Templates)又是众多生物(Organisms)的结合体。比如一个网站页面的布局。而最后的页面就是特殊的模板。

- 例如 .m-10{margin:10px;} .w-50{width:50%}
  
# CSS workflow

- css预处理器
less sass stylus
将不认识的less,sass等变成css
- css后处理器
css压缩，美化，添加前缀

- 现代化的css
```css
:root{
    /* 定义全局变量 */
    --mainColor:#ccc;
    --fontSize:1rem;
}

:root{
    --centered:{
        display:flex;
        aligin-items:center;
        justify-content:center;
    }
}


body{
    color: var(--mainColor);
}
.centered{
    @apply --centered;
}

@custom-media --viewport-medium(width <= 50rem);

@media(--viewport-medium){
    body{
        font-size:calc(var(--fontSize) * 1.2)
    }
}

@custom-selector:--heading h1,h2,h3,h4,h5,h6;
:--heading{margin-top:0}

.foo{
    background-image:
        image-set(
            url(img/test.png) 1x,
            url(img/test-2x.png) 2x
        )
}

a {
color: var(--highlightColor);
transition: color 1s; /* autoprefixed ! */
}
a:hover { color: gray(255, 50%) }
a:active { color: rebeccapurple }
a:focus { background-color: rgb(255 153 0 / 33%); outline: 3px solid hsl(1turn 60% 50%); }
a:any-link { color: color(var(--highlightColor) blackness(+20%)) }

/* filters */
.blur {
filter: blur(4px);
}
.sepia {
filter: sepia(.8);
}

.hero:matches(main,.main){

}
a{
    color:rgb(0 0 100% / 90%);
    &:hover{
        color:rebeccapurple;
    }
}

```

- webpack 处理css
  - css-loader
  - style-loader 负责把样式写入style标签
  - 把css 配置成module
    1. css-loader?modules
    2. import index from "./index.css"
    3. index上就会有想要的calss

- webpack 提取css
  - mini-css-extract-plugin
                                
- postcss
  1. install postcss
  2. postcss-preset-env
  3. postcss.config.js

- style 自定义函数

```css
    :root{
        --number-var:1
    }
    div::before{
        counter-reset:number var(--number);
        content:counter(number)
    }
```

- 利用css Grace 可以兼容到IE6

- css-doodle

```css
<script src="https://cdnjs.cloudflare.com/ajax/libs/css-doodle/0.7.1/css-doodle.min.js"></script>
<css-doodle>
    :root{
        --customUnit:100%;
    }
    html,body{
        width:var(--customUnit);
        height:var(--customUnit);
        display:flex;
        align-items:center;
        justify-content:center;
    }
    body{
        /* background:#0a0c27; */
    
    }
    
    :doodle{
        @grid:1 x 10 / 61.8vmax;
        @place-cell:center;
        @size:calc(@index() * 10%);
        border-radius:50%;
        border-width:calc(@index()*1vmin);
        border-style:dashed;
        border-color:hsla(calc(20 * @index()),70%,68%,calc(3 / @index()* .8)
    }
    --d:@rand(20s,40s);
    --rf:@rand(360deg);
    --rt:calc(var(--rf) + @pick(1turn,-1turn));
    animation:spin var(--d) linear infinite;
    @keyframes spin{
        from{
            transform:rotate(var(--rf))
        }
        to{
           transform:rotate(var(--rt)) 
        }
    }
</css-doodle>
```

