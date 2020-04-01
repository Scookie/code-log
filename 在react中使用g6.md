### 1.demo工程构建
```git
mkdir my-g6-demo
cd my-g6-demo
yarn init
yarn add umi @antv/g6
mkdir pages
cd pages
mkdir index.js
```
用编辑器打开index.js文件，粘贴如下代码
```javascript
import React,{useEffect} from 'react';
import G6 from '@antv/g6';
// 初始化数据
const data = {
  // 点集
  nodes: [{
    id: 'node1', // 节点的唯一标识
    x: 100,      // 节点横坐标，可以没有
    y: 200,      // 节点纵坐标，可以没有
    label: '节点1' // 节点文本
  },{
    id: 'node2',
    x: 300,
    y: 200,
    label: '节点2'
  },{
    id: 'node3',
    x: 300,
    y: 200,
    label: '节点3'
  }],
  // 边集
  edges: [
    // 表示一条从 node1 节点连接到 node2 节点的边
    {
      source: 'node1',  // 起始点 id
      target: 'node2',  // 目标点 id
      label: '我是1-2连线'   // 边的文本
    },{
      source: 'node2',  // 起始点 id
      target: 'node3',  // 目标点 id
      label: '我是2-3连线'   // 边的文本
    },{
      source: 'node3',  // 起始点 id
      target: 'node1',  // 目标点 id
      label: '我是3-1连线'   // 边的文本
    }
  ]
};
export default function() {
  const ref = React.useRef(null)
  let graph = null;
  
  useEffect(() => {
    if(!graph) {
      graph = new G6.Graph({
        container: ref.current,
        width: 1200,
        height: 800,
        // layout支持9+4种布局方式，参考https://www.yuque.com/antv/g6/agbmu2
        layout: {
          type: 'circular' // 圆形布局
        }
      })
    }
    
    graph.data(data);
    graph.render();
    // read 方法的功能相当于 data 和 render 方法的结合
    // graph.read(data);
  }, [])

  return (
    <div ref={ref}></div>
  );
}
```
执行命令`umi dev`把服务跑起来即可
解释一下useEffect，必须保证g6实例的container是渲染完成的真实dom
```javascript
function useEffect(effect: EffectCallback, deps?: DependencyList): void;

// 使用案例
useEffect(() => {
  console.log('在 dep 改变时触发,若无 dep 则,每次更新组件都会触发')
  return () => {
    console.log('在组件 unmount 时触发')
  };
});

// deps 必须是一个数组,但是如果是一个空数组时
useEffect(() => {
  console.log('效果等于 componentDidMount')
}, [])
// 即使有 deps ,他在初始化时也会触发一次
```


### 2.交互管理 mode
mode:用户在交互一张图时，可能由于意图不同而存在不同的交互模式，例如在编辑模式下点击节点需要弹出窗口让用户编辑，在查看模式下点击节点需要选中节点
<https://www.yuque.com/antv/g6/g6-mode>
```javascript
// 在图上定义了两个模式，分别是 default， edit 
// 其中 default 包含两个 Behavior：'drag-canvas' 和 'zoom-canvas'，都使用行为的默认参数
// 默认时 Graph 会使用 default 的 Mode ，可以拖动和缩放画布
// 当需要点击选中节点时，可以通过 graph.setMode('edit') 来切换到 edit 的 Mode
const graph = new G6.Graph({
  container: 'mountNode',
  width: 1200,
  height: 800,
  modes: {
   // 支持的 behavior
   default: [ 'drag-canvas', 'zoom-canvas' ],
   edit: [ 'click-select' ]
  }
});

graph.setMode('edit');
```


### 3.设置node和edge样式
设置默认的node和edge样式，如果每一个node/edge样式存在差异那么遍历data中的node和edge一一设置，style不可局部改变属性，会被全部覆盖，详情请访问如下内容。
<https://www.yuque.com/antv/g6/wdkcm3>
```javascript
graph = new G6.Graph({
  container: ref.current,
  width: 1200,
  height: 800,
  modes: {
    default: [ 'drag-canvas', 'zoom-canvas' ],
    edit: [ 'click-select' ]
  },
  // 节点在默认状态下的样式配置（style）和其他配置
  defaultNode: {
    // 内置node类型 https://www.yuque.com/antv/g6/internal-node
    type: 'rect',
    labelCfg: {
      style: {
        fill: '#000000A6',
        fontSize: 10
      }
    },
    style: {
      stroke: '#72CC4A',
      width: 150
    }
  },
  // 边在默认状态下的样式配置（style）和其他配置
  defaultEdge: {
    // 内置edge类型 https://www.yuque.com/antv/g6/internal-edge
    type: 'polyline'
  },
  // layout支持9+4种布局方式，参考https://www.yuque.com/antv/g6/agbmu2
  layout: {
    type: 'circular'
  }
})
```


### 4.配置不同 state 的样式并添加事件
<https://www.yuque.com/antv/g6/fqnn9w>
```javascript
const graph = new G6.Graph({
  container: ref.current,
  width: 1200,
  height: 800,
  nodeStateStyles: {
    hover: {                // hover 状态为 true 时的样式
      fill: '#d3adf7'
    },
    click: {              // click 状态为 true 时的样式
      fill: '#00f',
      stroke: 'steelblue'
    }
  },
  edgeStateStyles: {
    hover: {                // hover 状态为 true 时的样式
      fill: '#f00'
    },
    running: {              // running 状态为 true 时的样式
      stroke: 'steelblue'
    }
  }
});

graph.on('node:mouseenter', evt => {
  const { item } = evt
  graph.setItemState(item, 'hover', true)
})

graph.on('node:mouseleave', evt => {
  const { item } = evt
  graph.setItemState(item, 'hover', false)
})

graph.on('node:click', evt => {
  // 先将所有当前有 click 状态的节点的 click 状态置为 false
  const clickNodes = graph.findAllByState('node', 'click');
  clickNodes.forEach(cn => {
    graph.setItemState(cn, 'click', false);
  });
  const nodeItem = evt.item;
  // 设置目标节点的 click 状态 为 true
  graph.setItemState(nodeItem, 'click', true);
})

graph.on('edge:mouseenter', evt => {
  const { item } = evt
  graph.setItemState(item, 'hover', true)
})

graph.on('edge:mouseleave', evt => {
  const { item } = evt
  graph.setItemState(item, 'hover', false)
})
```


### 5.更新数据
```javascript
const newData = {
  nodes: [
    {
      id: '1',
      label: '前端'
    },
    {
      id: '2',
      label: '产品'
    },
    {
      id: '3',
      label: '运维'
    },
    {
      id: '4',
      label: '后端'
    },
    {
      id: '5',
      label: '设计'
    },
    {
      id: '6',
      label: '销售'
    },
    {
      id: '7',
      label: '测试'
    },
  ],
  edges: [
    {
      source: '1',
      target: '2',
      label: '评审'
    },
    {
      source: '1',
      target: '3',
      label: '部署'
    },
    {
      source: '2',
      target: '5',
      label: '沟通'
    },
    {
      source: '5',
      target: '6',
      label: '需求'
    },
    {
      source: '3',
      target: '4',
      label: '部署'
    },
    {
      source: '4',
      target: '7',
      label: '测试'
    },
  ]
};
graph.changeData(newData);
```


### 6.自定义 Node/Edge/Behavior/Layout
<https://www.yuque.com/antv/g6/api-global>
* G6.registerNode(nodeName, options, extendNodeName)
* G6.registerEdge(edgeName, options, extendEdgeName)
* G6.registerBehavior(behaviorName, behavior)
* G6.registerLayout(layoutName, layout)


> 参考文献：<https://www.yuque.com/antv/g6/xim703>