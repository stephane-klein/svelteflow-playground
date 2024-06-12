<script>
    import { onMount } from "svelte";
    import { writable } from "svelte/store";

    import { SvelteFlow, Controls, Background, BackgroundVariant, MiniMap, Position, useSvelteFlow } from "@xyflow/svelte";
    import ELK from "elkjs/lib/elk.bundled.js";

    import CustomNode from "./CustomNode.svelte";

    import "@xyflow/svelte/dist/style.css";
    import SideBar from "./Sidebar.svelte";

    const { fitView } = useSvelteFlow();

    const elk = new ELK();

    function getLayoutedElements(nodes, edges) {
        const graph = {
            id: "root",
            layoutOptions: {
                "elk.direction": "RIGHT",
                "elk.algorithm": "layered",
                "elk.layered.spacing.nodeNodeBetweenLayers": "40",
                "elk.spacing.nodeNode": "200",
            },
            children: nodes.map((node) => ({
                ...node,
                // Adjust the target and source handle positions based on the layout
                // direction.
                targetPosition: Position.Left,
                sourcePosition: Position.Right,

                // Hardcode a width and height for elk to use when layouting.
                width: 250,
                height: 80,
            })),
            edges: edges,
        };

        return elk
            .layout(graph)
            .then((layoutedGraph) => ({
                nodes: layoutedGraph.children.map((node) => ({
                    ...node,
                    // React Flow expects a position property on the node instead of `x`
                    // and `y` fields.
                    position: { x: node.x, y: node.y },
                })),

                edges: layoutedGraph.edges,
            }))
            .catch(console.error);
    }

    function applyLayout() {
        getLayoutedElements($nodes, $edges).then(({ nodes: layoutedNodes, edges: layoutedEdges }) => {
            $nodes = layoutedNodes;
            $edges = layoutedEdges;

            fitView();

            window.requestAnimationFrame(() => fitView());
        });
    }

    const nodeTypes = {
        customNode: CustomNode,
    };

    const nodes = writable([
        {
            id: "1",
            type: "customNode",
            data: {
                label: "Type 1",
                type: "type_1",
            },
            position: { x: -150, y: 0 },
        },
        {
            id: "2",
            type: "customNode",
            data: {
                label: "Type 2",
                type: "type_2",
            },
            position: { x: 150, y: 0 },
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
            type: "customNode",
            position,
            data: {
                label: "",
                type: type,
            },
            origin: [0.5, 0.0],
        };

        $nodes.push(newNode);
        $nodes = $nodes;
    };

    function exportData() {
        console.log("exportData");
        console.log($nodes);
        console.log($edges);
    }
    onMount(() => {
        applyLayout();
    });
</script>

<main>
    <SvelteFlow {nodes} {edges} {nodeTypes} {snapGrid} fitView on:dragover={onDragOver} on:drop={onDrop}>
        <Controls />
        <Background variant={BackgroundVariant.Dots} />
        <MiniMap />
    </SvelteFlow>
    <SideBar />
    <button on:click={exportData}>Export</button>
    <button on:click={applyLayout}>Apply layout</button>
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
