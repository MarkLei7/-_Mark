# Front-End-Development-Learning
前端学习笔记&amp;练习代码

```js
function fn(obj={},keys=[]){
    let obj_t=obj
    while(keys.length){
        const key=keys.shift()
        obj_t=obj_t?.[key]
        // if(!obj_t[key]){
        //     throw new TypeError("不存在")
        // }
        // else{
        //    obj_t=obj_t[key]
        // }
    }
    return obj_t
}

const A={
    a:{b:"AB"},
    b:"B",
    c:"C"
}
fn(A,["a","b"])

```