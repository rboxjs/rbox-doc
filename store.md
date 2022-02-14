# 容器

容器是用来储存数据的 <br />


``` typescript
//对于typescript，你需要设置noImplicitThis来获得this的类型检查

//tsconfig.json
{
  "compilerOptions": {
    "noImplicitThis": true
  }
}
```

## 基本使用

#### 创建容器

* 你可以使用`createStore`创建容器，执行后返回一个数据容器
* 对应的数据容器的会注入一些方法在`core属性上`，例如更新数据`userStore.core.updateData({uid:1})`

```typescript
const userStore = createStore({
    uid:0
});

console.log(userStore.uid)
```

#### 定义行为

* 行为方法需要放在`actions`属性里 <br />
* 在方法里使用`this.core.updateData`来更新数据，你不能直接使用`this.uid = 1`更新数据 <br />
* 之后在对应的数据容器里使用`userStore.actions.login()`触发行为


```typescript
const userStore = createStore({
    uid:0,
    actions:{
        login(){
            this.core.updateData({
                uid:1
            })
        }
    }
});

userStore.actions.login();

console.log(userStore.uid);
```

#### 监听数据更新

* 你可以使用`userStore.core.observeData`来监听数据的变化 <br />
* 回调中`previousData`对应的是上次数据快照 <br />
* 执行`observeData`后返回取消监听方法

```typescript
const unObserveData = userStore.core.observeData((previousData)=>{
    console.log(previousData.uid)
});

userStore.actions.login();

unObserveData()
```

## 继承

继承可以让你更好的复用代码

#### 基础容器

定义一个基础的列表容器

```typescript
const listModel = ()=>{
    return createStore({
        loading:false,
        list:[],
        actions:{
            setLoading(status){
                this.core.updateData({
                    loading:status
                })
            },
            async getPageData(){
                this.actions.setLoading(true)
                
                try{
                    const data = await this.actions.onGetPageData();
                    
                    this.core.updateData({
                        list:data
                    })
                }
                finally {
                    this.actions.setLoading(true)
                }
            }
        }
    })
}

```

#### 继承

* 你可以在`createStore`中的第二个参数传入要继承的基础容器
* 然后返回的容器既有基础容器的数据和行为，还有新定义的数据和行为
* 基础容器的行为被覆盖你仍可以通过`this.core.baseActions`访问基础容器的行为

```typescript
const articlesStore = createStore({
    type:"articles",
    actions:{
        onGetPageData(){
            return [
                {title:'1',content:"1"},
                {title:'2',content:"2"},
            ]
        }
    }
},listModel());

console.log(articlesStore)

type ArticlesStore = {
    loading:boolean,
    list:Array<any>,
    type:string,
    actions:{
        setLoading:(status:boolean)=>void,
        getPageData:()=>Promise<void>,
        onGetPageData:()=>Array<{title:string,content:string}>
    }
}

```