
<script lang="ts" generics="T extends TVNode">

    import {onMount} from "svelte";
	import {getContext} from 'svelte';
	import {TVNode} from './AccordionTypes';
	import Fa from 'svelte-fa';
    import { faChevronDown, faChevronUp } from '@fortawesome/free-solid-svg-icons';

	export let disposition : string = "right";

	export let nodeClass = "defaultStyle";

    export let node;

	export let ref;
    
    export let nodeTemplate;

	export let selectable;

	export let selected = false;

	export let tab:string = "25px";

	let deployed = false;
	
    let child;
    let isNode;
	
	let selectNode = getContext<(n:any, selected:boolean) => void>('selectNode');
	let isNodeSelected = getContext<(n:any) => boolean>('isNodeSelected');
    
	let dispositionStyle : string = "float:right; padding-left:15px";
    onMount(async () => {        	
		dispositionStyle = disposition == "right" ? "float:right; padding-left:15px" : "float:left; padding-right:15px";		
        child = node.children;        
        isNode = child !== undefined && child !== null && Array.isArray(child) && child.length > 0; 
		deployed = true;   
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

	const clickNode = () => {
		deployed = !deployed;
	}


</script>
<slot name="style"></slot>
{#if node.children !== null && node.children !== undefined && node.children.length > 0}
<div class="panel panel-default">
	<div class={nodeClass}>
		{#if selectable}	
			<input type="checkbox" bind:checked={selected} on:change={handleSelect}/>
		{/if}
		{#if disposition == "right"}
			<div style="display:inline">
				<svelte:component this={nodeTemplate} data={node}/>
			</div>
		{/if}
		{#if deployed}
			<span style='cursor:pointer;display:inline;{dispositionStyle}' tabindex="-1" role="link" on:click={clickNode} on:keydown={clickNode}><Fa icon="{faChevronUp}"/></span>
		{:else}
			<span style='cursor:pointer;display:inline;{dispositionStyle}' tabindex="-1" role="link" on:click={clickNode} on:keydown={clickNode}><Fa icon="{faChevronDown}"/></span>
		{/if}
		{#if disposition == "left"}
			<div style="display:inline">
				<svelte:component this={nodeTemplate} data={node}/>
			</div>
		{/if}
	</div>
	<div class="node-body" style="display:{deployed ? "block" : "none"};margin-left: {tab};">
		{#each node.children as subNode (subNode.id)}
			<svelte:self {ref} selected={isNodeSelected(subNode)} {selectable} node={subNode} {nodeTemplate} tab={tab} {disposition} {nodeClass}>
				<slot name="style"></slot>
			</svelte:self>
		{/each}	
	</div>
</div>   
{:else}
<div class={nodeClass}>
	{#if selectable}	
		<input style="display:inline" type="checkbox" bind:checked={selected} on:change={handleSelect}/>
	{/if}
	<div style="display:inline">
		<svelte:component this={nodeTemplate} data={node}/>
	</div>
	
</div>


{/if}


