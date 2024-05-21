<script>
    import { writable } from "svelte/store";

    import { SvelteFlow, Controls, Background, BackgroundVariant, MiniMap, useSvelteFlow } from "@xyflow/svelte";

    import CustomNode from "./CustomNode.svelte";

    import "@xyflow/svelte/dist/style.css";
    import SideBar from "./Sidebar.svelte";

    const nodeTypes = {
        customNode: CustomNode
    };

    const nodes = writable([
        {
            id: "1",
            type: "default",
            data: { label: "Input Node" },
            position: { x: 0, y: 0 },
        },
        {
            id: "2",
            type: "customNode",
            data: { label: "Custom Node" },
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
        },
    ]);

    const snapGrid = [25, 25];
    const { screenToFlowPosition } = useSvelteFlow();
    const onDragOver = (event) => {
        event.preventDefault();

        if (event.dataTransfer) {
            event.dataTransfer.dropEffect = "move";
        }
    };

    const onDrop = (event) => {
        event.preventDefault();

        if (!event.dataTransfer) {
            return null;
        }

        const type = event.dataTransfer.getData("application/svelteflow");

        const position = screenToFlowPosition({
            x: event.clientX,
            y: event.clientY,
        });

        const newNode = {
            id: `${Math.random()}`,
            type,
            position,
            data: { label: `${type} node` },
            origin: [0.5, 0.0],
        };

        $nodes.push(newNode);
        $nodes = $nodes;
    };
</script>

<main>
    <SvelteFlow
        {nodes}
        {edges}
        {nodeTypes}
        {snapGrid}
        fitView
        on:dragover={onDragOver}
        on:drop={onDrop}
        on:nodeclick={(event) => console.log("on node click", event.detail.node)}
        isValidConnection={(connection) => {
            console.log("connection", connection);
            return true;
        }}
    >
        <Controls />
        <Background variant={BackgroundVariant.Dots} />
        <MiniMap />
    </SvelteFlow>
    <SideBar />
</main>

<style>
    :global(body) {
        margin: 0;
    }

    main {
        height: 100vh;
        display: flex;
        flex-direction: column-reverse;
    }
</style>
