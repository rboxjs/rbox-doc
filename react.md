# react中使用

## 监视容器自动渲染

> 推荐使用hooks的方式

#### hooks组件

* 用`useStores`来使用数据容器，传入多个数据容器，会按相同顺序返回对应的数据容器
* 当对应的容器数据发生变化后，页面会自动渲染


```typescript jsx
import {createStore} from 'rbox'
import {useStores} from 'rbox-react'

function userModel(){
    return createStore({
        uid:0,
        actions:{
            login(){
                this.core.updateData({
                    uid:1
                })
            }
        }
    })
}

function Page(){
    const [sUser,sArticles] = useStores([
        userModel(),
        articlesModel()
    ]);
    
    return <div>
        <div>uid:{sUser.uid}</div>
        <div onclick={sUser.actions.login}>
            登录
        </div>
    </div>
}

```

#### 类组件
* 类组件需要使用`observe`来监视组件props和this中的数据容器
* 当对应是容器数据发生变化时组件会自动渲染

```typescript jsx
import {observe} from 'rbox-react'

class _Page extends React.Component{
    sUser = userModel()
    
    render(){
        return <div>
            <div>uid:{this.sUser.uid}</div>
            <div onclick={this.sUser.actions.login}>
                登录
            </div>
        </div>
    }
}

@observe
class PageComponent extends React.Component{
    //...
}


//or
const PageComponent_1 = observe(_Page)

```

## 优化渲染

默认调用容器的`core.updateData`会自动触发渲染 <br />
这里会使用微任务来进行渲染组件，也就是`updateData`调用后立马再执行`updateData`不会渲染两次 <br />
有时调用`updateData`后并不需要渲染，我们可以使用以下方式进行优化

#### 使用 mergeUpdate

```typescript jsx
import {mergeUpdate} from 'rbox-react'

mergeUpdate(()=>{
    sUser.actions.login();
    sUser.actions.updateProfile()
})
```
