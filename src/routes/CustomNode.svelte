<script>
    import { Handle, Position } from "@xyflow/svelte";

    export let isConnectable;
</script>

<Handle type="target" position={Position.Left} {isConnectable} />

<div>
    Custom Node
</div>

<Handle type="source" position={Position.Right} id="a" {isConnectable} />

<style>
    :global(.svelte-flow__node-customNode) {
        padding: 10px;
        border-radius: 3px;
        font-size: 12px;
        border: 1px solid black;
        text-align: center;
    }
</style>
