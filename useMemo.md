````js
let data=React.useMemo(()=>({number}),[number]);
````
````js
// 网上说useMemo原理全是这个代码，这个代码最大的问题就是hookIndex对不上，可取的思想states是个二维数组，数组里的每项元素又是个数组，用来保存要缓存的值和所依赖
let hookStates = [] 
let hookIndex = 0
function useMemo(factory,dependencies){
    if(hookStates[hookIndex]){ // 说明不是第一次渲染 
        let [lastMemo, lastDependencies] = hookStates[hookIndex];
        // 判断一下新的依赖数组中的每一项是否跟上次完全相等
        let same = dependencies.every((item, index) => item === lastDependencies[index]);
        if (same) {
            hookIndex++;
            return lastMemo;
        } else { //只要有一个依赖变量不一样的话
            let newMemo = factory();
            hookStates[hookIndex++] = [newMemo, dependencies];
            return newMemo;
        }
    } else {
        // 说明是第一次渲染 
        let newMemo = factory();
        hookStates[hookIndex++] = [newMemo, dependencies]; // [ [{number:1}, [1]] ]
        return newMemo;
    }
}
````