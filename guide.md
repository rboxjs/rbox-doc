# 快速开始

`推荐使用typescript获得更好的体验`

#### 安装依赖

`npm install rbox --save` <br />
`npm install rbox-react --save` <br />
#### 定义数据

```typescript
import {createStore} from 'rbox'

const articlesModel = ()=>{
    return createStore({
        loading:false,
        list:[],
        actions:{
            async getPageData(){
                this.core.updateData({
                    loading:true
                })
                try{
                    const data = await fetch('/api/articles');
                    
                    this.core.updateData({
                        list:data
                    })
                }
                finally {
                    this.core.updateData({
                        loading:false
                    }) 
                }
            }
        }
    })
}
```
#### 在react中使用

```typescript jsx
import React,{useEffect} from 'react'
import {useStores} from 'rbox-react'

const ArticlesComponent = ()=>{
    const [sArticles] = useStores([
        articlesModel()
    ])

    useEffect(()=>{
        sArticles.actions.getPageData();
    },[])
    
    if(sArticles.loading){
        return <div>loading...</div>
    }
    
    return sArticles.list.map(item=>{
        return <div>
            <h3>{item.title}</h3>
            <div>{item.content}</div>
        </div>
    })
}
```