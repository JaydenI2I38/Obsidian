
[lil-gui --github](https://github.com/georgealways/lil-gui)

## Init

```js
import GUI from 'lil-gui'
// import * as dat from 'lil-gui'

const gui = new GUI()
// const gui = new dat.GUI()

// add
gui.add(mesh.position, 'y')
```

## Construct
>[更多查看](https://lil-gui.georgealways.com/#GUI#constructor)


```js
const gui = new GUI({
	width: 300,
	title: 'xxx',
	closeFolders: true,
	// ...
})
```


## Base Types
### Range

```javascript
gui.add(mesh.position, 'y')
```
![[Pasted image 20241220093155.png]]

```javascript
gui.add(mesh.position, 'y', - 3, 3, 0.01)
```
![[Pasted image 20241220093136.png]]


换个写法
```javascript
gui
    .add(mesh.position, 'y')
    .min(- 3)
    .max(3)
    .step(0.01)
    .name('elevation') // 修改当前属性名 y -> elevation
```


可以对普通对象属性进行调整
```javascript
const myObject = {
    myVariable: 1337
}
gui.add(myObject, 'myVariable')
```
### Color

注意是`addColor`

>因为color属性不是字符串、布尔值或数字，是Three.js Color类的实例


```javascript
gui.addColor(material, 'color')
```


![[Pasted image 20241220094714.png]]

>当修改color后，会发现渲染的颜色和将当前hexColor值设置在材质上时，渲染是不一样的，这是因为`Three.js`应用了一些颜色管理来优化渲染

#### 解决方案
创建全局对象存储`color`

```javascript

const debugObject = {
	color: "#8acefe"
}

// ...
const material = new THREE.MeshBasicMaterial({ color: debugObject.color, wireframe: false })

// ...

gui
    .addColor(debugObject, 'color')
    .onChange(() =>
    {
        material.color.set(debugObject.color)
    })


```

![[Pasted image 20241220100032.png]]

### Text(待补充)
### Checkbox

```javascript
// visible
gui.add(mesh, 'visible')

// wireframe
gui.add(material, 'wireframe')
```

![[Pasted image 20241220094311.png]]
### Select(待补充)


### Function / Button

```javascript
debugObject.spin = () => {

    gsap.to(mesh.rotation, { duration: 1, y: mesh.rotation.y + Math.PI * 2 })

}

gui.add(debugObject, 'spin')
```
![[Pasted image 20241220100313.png]]


## Special

### Folders
```js
const cubeTweaks = gui.addFolder('Awesome cube')

cubeTweaks
	.add(/* ... */)
	// ...
```

![[Pasted image 20241220101514.png]]


```javascript
// 初始关闭当前文件夹
cubeTweaks.close()
```


![[Pasted image 20241220101610.png]]


## API
>[查看更多](https://lil-gui.georgealways.com/#API)

#### .hide()
```js
gui.hide() // 隐藏debugUI面板
```
#### .show()
>可以通过`gui._hidden` 获取当前是否隐藏

```js
gui.show();
gui.show( false ); // hide
gui.show( gui._hidden ); // toggle
```

```javascript
// 按h进行切换
window.addEventListener('keydown', (event) =>
{
    if(event.key == 'h')
        gui.show(gui._hidden)
})
```

#### .close()
```js
gui.close() // 折叠debugUI面板
```
