<script lang="ts">

	import Accordion from './Accordion.svelte';
	import Filter from './Filter.svelte';
	import Node from './Node.svelte';
	import Node2 from './Node2.svelte';
		import {selectedNode} from './teststore.js'
	import {treeData, Disney, CharacterFilter, CharacterKind, Universe} from './data.js';
	import {CustomEvent} from './AccordionTypes';
	

	let name = 'world';
	$selectedNode = undefined

	let root = treeData;
	
	
	let filter = (node:Disney, search:string) :boolean => {
		const contains = node.name.toLocaleLowerCase().includes(search.toLocaleLowerCase());
		return contains;
	};

	let selectedNodes : Disney[] = [];

	const onSelectionChanged = (e:CustomEvent<Disney[]>) => {				
		selectedNodes = e.detail;
	}

	const characterFilter = (node:Disney, pattern:CharacterFilter) : boolean => {

		if (pattern.kind !== undefined) {
			if (node.kind != pattern.kind && pattern.kind != CharacterKind.All) {
				return false;
			}
		}
		if (pattern.universe !== undefined) {
			if (node.universe != pattern.universe && pattern.universe != Universe.All)	{
				return false;
			}			
		}
		if (pattern.name !== undefined && pattern.name !== null && pattern.name.length > 0) {
			return node.name.toLocaleLowerCase().includes(pattern.name);
		}
		return true;
	}
	
</script>

<h2>simple filter</h2>

{#if $selectedNode}
<b style="display:block">#{$selectedNode.id} {$selectedNode.name}</b>	
{/if}
<Accordion  tab="75px" emptyTreeMessage="Mickey Mouse" {root}  nodeTemplate={Node} {filter} searchPlaceholder="search the tree..."></Accordion>


<h2>simple filter - deploy on left</h2>

{#if $selectedNode}
<b style="display:block">#{$selectedNode.id} {$selectedNode.name}</b>	
{/if}
<Accordion tab="75px"  emptyTreeMessage="Mickey Mouse" {root}  nodeTemplate={Node} {filter} searchPlaceholder="search the tree..." disposition="left"></Accordion>

<h2>Select nodes</h2>


<div style="display:flex;flex-direction:row">
	<div>
	<Accordion tab="10px" emptyTreeMessage="no super hero to show" on:selectionChanged={onSelectionChanged} selectable {root} nodeTemplate={Node2} {filter} ></Accordion>
	</div>
	<div>
		{#if selectedNodes && selectedNodes.length > 0}
			<ul>
				{#each selectedNodes as node}
					<li>{node.id} - {node.name}</li>
				{/each}
			</ul>
		{:else}
			<i>no selected node</i>
		{/if}
	</div>
</div>






<h2>Custom filter</h2>



<Accordion tab="50px" emptyTreeMessage="Mikey Mouse"  {root} nodeTemplate={Node2} searchTemplate={Filter} complexFilter={characterFilter}></Accordion>


<h2>Styling</h2>



	<style>


:global(summary[ref="style1"]) {
  display: block;
}

:global(.tv-node[ref="style1"]::before) {
  margin-left: 1ch;
  display: inline-block;
  content: '➕';
  transition: 0.2s;
}
:global(details[ref="style1"][open] > .tv-node::before) {  
  content: '➖';
  transition: 0.2s;
}
:global(.tv-leaf[ref="style1"]::before) {
		content: '🦸🏻‍♂️'
	}


/**
second Accordion style
*/	
  

:global(summary[ref="style2"]) {
  display: block;
}

:global(.tv-node[ref="style2"]::before) {
  margin-left: 1ch;
  display: inline-block;
  content: '✈️';
  transition: 0.2s;
}
:global(details[ref="style2"][open] > .tv-node::before) {  
  content: '🛶';
  transition: 0.2s;
}
:global(.tv-leaf[ref="style2"]::before) {
		content: '🐧'
	}
  
</style>

<div style="display:flex;flex-direction:row">

<Accordion emptyTreeMessage="Mikey Mouse" ref="style1" {root} nodeTemplate={Node2} {filter} nodeClass="toto">
	<style slot="style">	
	.toto {
		border-bottom": thin solid black;
		border-left: thin dotted black;
		padding:10px;
		background-color:lightgrey;
		font-weight:bold;
	}
	.toto:hover {
		background-color:yellow;
	}
	</style>
</Accordion>
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

</div> 