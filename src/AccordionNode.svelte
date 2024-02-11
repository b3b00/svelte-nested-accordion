<style>
    .treemargin {
        margin-left: 25px;
    }
	
</style>
<script lang="ts" generics="T extends TVNode">

    import {onMount} from "svelte";
	import {getContext} from 'svelte';
	import {TVNode} from './AccordionTypes';
  import Accordion from "./Accordion.svelte";

    export let node;

	export let ref;
    
    export let nodeTemplate;

	export let selectable;

	export let selected = false;
	
    let child;
    let isNode;
	
	let selectNode = getContext<(n:any, selected:boolean) => void>('selectNode');
	let isNodeSelected = getContext<(n:any) => boolean>('isNodeSelected');
    
    onMount(async () => {        		
        child = node.children;        
        isNode = child !== undefined && child !== null && Array.isArray(child) && child.length > 0;    
	});  
	
	
	const getAllChildren = (n:T): T[] => {
		let children = n.children;
		let isnode = children !== undefined && children !== null && Array.isArray(children) && children.length > 0; 
		if (isnode) {				
			const subs = children.map(getAllChildren).reduce(function(a, b){ return a.concat(b); }, [n]);
			return subs;
		}
		else {
			return [n];
		}
	}
	
	const handleSelect = () => {						
		selectNode(node,selected);			
		if (isNode) {
			handleSelectNode();
		}
		node = node;
	}

	const handleSelectNode = () => {
		const all = getAllChildren(node);
		for(let i = 0; i < all.length; i++) {
			selectNode(all[i],selected);
		}
	}

</script>

{#if node.children !== null && node.children !== undefined && node.children.length > 0}
<div class="panel panel-default">
	<div class="panel-heading" role="tab" id="heading-1">
	<h4 class="panel-title">
	  <a role="button" data-toggle="collapse" data-parent="#accordion" href="#collapse-1" aria-expanded="true" aria-controls="collapse-1">
		<!-- here stand : 
			- heading
			- deploy/collapse button
			- select checkbox
		-->
			
			<svelte:component this={nodeTemplate} data={node}/>
			<span>🌈</span>
			{#if selectable}	
					<input type="checkbox" bind:checked={selected} on:change={handleSelect}/>
			{/if}
		Item 1
	  </a>
	</h4>
  </div>
  <div id="collapse-1" class="panel-collapse collapse in" role="tabpanel" aria-labelledby="heading-1">
	<div class="panel-body">
	<!-- here stand children nodes -->
		{#each node.children as subNode (subNode.id)}
			<svelte:self {ref} selected={isNodeSelected(subNode)} {selectable} node={subNode} {nodeTemplate}/>
		{/each}	
	</div>
</div>   
</div> 
{:else}
<div class="panel-heading" role="tab" id="heading-1">
	<h4 class="panel-title">
	  <a role="button" data-toggle="collapse" data-parent="#accordion" href="#collapse-1" aria-expanded="true" aria-controls="collapse-1">
		<!-- here stand : 
			- heading
			- deploy/collapse button
			- select checkbox
		-->
			
			<svelte:component this={nodeTemplate} data={node}/>
			<span>🌈</span>
			{#if selectable}	
					<input type="checkbox" bind:checked={selected} on:change={handleSelect}/>
			{/if}
		Item 1
	  </a>
	</h4>
  </div>
{/if}


