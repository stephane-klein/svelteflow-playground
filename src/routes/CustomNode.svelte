<script>
    import { Handle, Position, useNodes, useEdges } from "@xyflow/svelte";

    export let id;
    export let isConnectable;
    export let data;

    const nodes = useNodes();
    const edges = useEdges();

    function deleteNode() {
        $nodes = $nodes.filter((node) => node.id !== id);
        $edges = $edges.filter((edge) => edge.source !== id && edge.target !== id);
    }
</script>

<Handle
    type="target"
    position={Position.Left}
    id={data.type}
    isValidConnection={(event) => {
        return event.sourceHandle != event.targetHandle
    }}
    {isConnectable}
/>

<div>
    {
        (
            {
                "type_1": "Type 1",
                "type_2": "Type 2"
            }[data.type]
        )
    } :
    <input type="text" bind:value={data.label} />

    <button on:click={deleteNode}>x</button>
</div>


<Handle
    type="source"
    position={Position.Right}
    id={data.type}
    isValidConnection={(event) => {
        return event.sourceHandle != event.targetHandle
    }}
    {isConnectable}
/>

<style>
    :global(.svelte-flow__node-customNode) {
        padding: 10px;
        border-radius: 3px;
        font-size: 12px;
        border: 1px solid black;
        text-align: center;
    }
    :global(.svelte-flow .svelte-flow__handle.connectingto) {
        background: #ff6060;
    }

    :global(.svelte-flow .svelte-flow__handle.valid) {
        background: #55dd99;
    }
</style>
