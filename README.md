补环境框架sdenv的专用jsdom版本

复刻自[jsdom](https://github.com/jsdom/jsdom)的24.0.0版本，使用方法与API参见[jsdom项目文档](https://github.com/jsdom/jsdom/blob/main/README.md)。

### 需要注意！

1. 由于HTML DOM中form元素下的节点如果定义了name值，则可通过直接在该form节点中取对应name对应的节点，在webidl中不好处理，因此在每次打包后需要手动修改文件：`lib/jsdom/living/generated/HTMLFormElement.js`

```git
 function makeWrapper(globalObject, newTarget) {
   let proto;
   if (newTarget !== undefined) {
     proto = newTarget.prototype;
   }

   if (!utils.isObject(proto)) {
     proto = globalObject[ctorRegistrySymbol]["HTMLFormElement"].prototype;
   }

-    return Object.create(proto);
+    return new globalObject.Proxy(Object.create(proto), {
+      get(target, propKey, receiver) {
+        if (typeof propKey === 'string' && target.children.namedItem(propKey)) {
+          return target.children.namedItem(propKey);
+        }
+        return globalObject.Reflect.get(target, propKey, receiver);
+      }
+    })
 }
```
