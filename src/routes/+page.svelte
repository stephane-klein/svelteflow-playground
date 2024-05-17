<script>
    import { writable } from "svelte/store";
    import { SvelteFlow, Controls, Background, BackgroundVariant, MiniMap } from "@xyflow/svelte";

    import "@xyflow/svelte/dist/style.css";

    const nodes = writable([
        {
            id: "1",
            type: "input",
            data: { label: "Input Node" },
            position: { x: 0, y: 0 },
        },
        {
            id: "2",
            type: "default",
            data: { label: "Node" },
            position: { x: 0, y: 150 },
        },
    ]);

    // same for edges
    const edges = writable([
        {
            id: "1-2",
            type: "default",
            source: "1",
            target: "2",
            label: "Edge Text",
        },
    ]);

    const snapGrid = [25, 25];
</script>

<div style:height="100vh">
    <SvelteFlow
        {nodes}
        {edges}
        {snapGrid}
        fitView
        on:nodeclick={(event) => console.log("on node click", event.detail.node)}
    >
        <Controls />
        <Background variant={BackgroundVariant.Dots} />
        <MiniMap />
    </SvelteFlow>
</div>
