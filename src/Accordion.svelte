<script lang="ts" generics="T extends TVNode,S = string">

    import {onMount} from "svelte";
	import {setContext} from 'svelte';
	import AccordionNode from "./AccordionNode.svelte";	
	import { createEventDispatcher } from 'svelte';
	import {CustomEvent, TVNode} from './AccordionTypes';



	const dispatch = createEventDispatcher<{ "selectionChanged": T[] }>();
  
	export let disposition : string = "right";

    export let root:T & TVNode = undefined;
    
    export let nodeTemplate;

	export let searchTemplate = undefined;

    
    export let filter : (n:T & TVNode, pattern:string)=> boolean = undefined;

	export let searchPlaceholder = "";

	export let selectable : boolean = false;

	export let emptyTreeMessage : string;
    
	export let ref: string = "$xirglub!";

	export let tab : string = "25px";

    let search : string;

    let complexSearch: S; 

	export let complexFilter: (n:T, searchPattern:S) => boolean = undefined;

    let currentRoot;

		let selection = {}

	let selectNode = (selectedNode:T, selected) => {
		selection[selectedNode.id] = selected;	
		let allNodes = getAllChildren(currentRoot);
		let selectedNodes = allNodes.filter(x => isNodeSelected(x));		
		dispatch('selectionChanged',selectedNodes);	
	}

	let nodefilter = (node:T, search:string) => {
		if (search === undefined || search === null || search == '') {
			return node;
		}
		var children = node.children;
		if (children !== null && children !== undefined && children.length > 0) {
			var filtered = children.map(x => nodefilter(x as T, search)).filter(x => x!= null);
			if ( filter(node,search)) {
				return node;
			}
			else if (filtered.length > 0) {
				let newNode = Object.assign({}, node);
				newNode.children = filtered;				
				return newNode;
			}
			return null;
		}
		else {
			if (filter(node,search)) {
				return node;
			}
			return null;
		}
	}  

	let complexNodefilter = (node:T , searchPattern:S) => {		
		if (searchPattern === undefined || searchPattern === null || searchPattern == '') {				
			return node;
		}
		var children = node.children;
		if (children.length > 0) {

			var filtered = children.map(x => complexNodefilter(x as T, searchPattern)).filter(x => x!= null);
			if ( complexFilter(node,searchPattern)) {				
				return node;
			}
			else if (filtered.length > 0) {				
				let newNode = Object.assign({}, node);
				newNode.children = filtered;				
				return newNode;
			}
			return null;
		}
		else {
			if (complexFilter(node,searchPattern)) {				
				return node;
			}
			return null;
		}
	}  

	let onComplexFilterChanged = (e:CustomEvent<S>) => {
		currentRoot = complexNodefilter(root,e.detail);
	}

	let getNodeSelection = () => selection;

	let isNodeSelected = (node:T) => {		
		const id = node.id;
		if (selection.hasOwnProperty(id)) {
			return selection[id]
		}		
		return false;
	}
	
	setContext('selectNode', selectNode);

	setContext('getNodeSelection', getNodeSelection);

	setContext('isNodeSelected', isNodeSelected);
    
    $:{        
        search = search;
        if (filter && !complexFilter) {		
            currentRoot = nodefilter(root, search);						
        }        
    }

    onMount(async () => {
        currentRoot = root;
    })

	const getAllChildren = (n:T):T[] => {
		let children = n.children;
		let isnode = children && Array.isArray(children) && children !== null && children !== undefined && children.length > 0; 
		if (isnode) {				
			const subs = children.map(getAllChildren).reduce(function(a, b){ return a.concat(b); }, [n]);
			return subs;
		}
		else {
			return [n];
		}
	}

</script>

<svelte:head>

<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>


		<!-- <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/css/bootstrap.min.css" />
		<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.css"/>
		<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.min.js"/>
		<script src="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/js/bootstrap.min.js"/>	 -->
	
</svelte:head>
<div style="display:flex;flex-direction:column; width:fit-content;">
{#if filter && !complexFilter}
	<input type="text" bind:value={search} placeholder="{searchPlaceholder}"/>
{/if}
{#if complexFilter}
	<svelte:component this={searchTemplate} on:filterChanged={onComplexFilterChanged}/>
{/if}

{#if currentRoot}	
	<AccordionNode {ref} {selectable} node={currentRoot} nodeTemplate={nodeTemplate} tab={tab} disposition={disposition}/>
{:else}
	{#if emptyTreeMessage}
		<span style="font-style:italic;display:block">{emptyTreeMessage}</span>
	{:else}
	<span style="font-style:italic;display:block">empty</span>
	{/if}
{/if}
</div>