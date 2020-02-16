title: dva记录
author: John Doe
date: 2019-06-02 23:28:45
tags:
---
```
export default {
 
  namespace: 'example',   
 
  state: {num:1},     //表示当前的example中的state状态，这里可以给初始值，这里num初始为1
 
 
  effects: { //这里是做异步处理的
    *addByONe({ param}, { call, put,select }) { //这里使用select
 
      const num = yield select(state => state.num)    //这里就获取到了当前state中的数据num
     //方式二： const num = yield select(({num}) =>num)
 
    //方式三： const num = yield select(_ =>_.num)
 
      let param1;
       param1 = num + param;   这里就可以使用num进行操作了
  
      yield put({
         type: 'save',   
         num:param1
      });
    }
   
 
  },
    
  //用来保存更新state值 上面的put方法调用这里的方法
  reducers: {
    save(state, action) { //这里的state是当前总的state，这里的action包含了上面传递的参数和type
      return { ...state, ...action.num }; //这里用ES6语法来更新当前state中num的值
    },
  },
 
```
  