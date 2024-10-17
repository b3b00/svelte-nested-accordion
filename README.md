

# This is not relevant !

wait for a real release and readme


## demo

[svelte-nested-accordion](https://svelte-nested-accordion.pages.dev/)


## installation

`npm i @bolduh/svelte-Accordion`

## usage

```js
<Accordion emptyTreeMessage="nothing to show" selectable root={root} childrenAccessor={accessor} nodeTemplate={Node} filter={nodefilter} ></Accordion>
```

The Accordion display a tree data structure that must inherit from TVNode
```ts
export interface TVNode {
    children : TVNode[]; // list of children (may be undefined if no children)
    id:any; // unique identifier
}
```

We will assume that `NodeType` is the node type of our Accordion for now on.

### Accordion attributes:

- root (`NodeType`): the full tree datastructure
- emptyTreeMessage (`string`) : message to display when filter do not return any node
- selectable (`boolean`, default is false) : if set add checkbox in front of every node/leaf
- nodeTemplate : a svelte component used to render nodes and leafs. it must accept an attribute `data` of the node type (`NodeType`)
- filter (`(node:NodeType, filter:string ) => boolean `): a function used to filter nodes on a simple text input. if not set no search widget is displayed. returns true if the `node` matches the `filter`.  
- for custom filters. The Accordion can accept custom filters. A filter must be mapped to a typescript interface, there is no constraint on the type. for now on this class will be `customFilter` 
  - complexFilter(`(node:NodeType, filter:CustomFilter) => boolean`). A function that returns true if the `node` matches the `filter`
  - filterTemplate : a svelte component displaying the custom filter. It must throw a `filterChanged` event whenever the filter change. the event payload must be an object of type `CustomFilter`


### Accordion events: 

The Accordion could raise a `selectionChanged` event whenever a node is selected / deselected (when node selection is enabled with `selectable` attribute). The event payload contains a list of `NodeType` : `NodeType[]`


```typescript

const onSelectionChanged = (e:CustomEvent<Disney[]>) => {				
		selectedNodes = e.detail;
	}

<Accordion  emptyTreeMessage="no data" on:selectionChanged={onSelectionChanged} selectable {root} nodeTemplate={Node2} {filter} ></Accordion>

```

## styling an Accordion

Styling accordion node may be done using : 
   - the `nodeClass` attribute that set a CSS class for accordion nodes.
   - the `style` slot that embed the CSS class.

```js
<Accordion emptyTreeMessage="Mikey Mouse" ref="style2" {root} nodeTemplate={Node2} {filter} disposition="left" nodeClass="reddy">
  <style slot="style">
    .reddy {
      border-bottom: thin solid red;
      border-left:thin dotted red;		
      border-right:thin dashed red;
      border-top:solid 5px red;
      padding:10px;
      border-radius:8px;
    }
    .reddy:hover{
      background-color:lightgreen;
    }
  </style>
</Accordion>
```