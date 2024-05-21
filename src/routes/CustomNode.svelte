<script>
    import { Handle, Position } from "@xyflow/svelte";

    export let isConnectable;
    export let data;
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
    } : { data?.label || "" }
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
</style>
