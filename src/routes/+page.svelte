<script lang="ts">
    import { Canvas, T } from "@threlte/core";
    import { World } from "@threlte/rapier";
    import BilliardTable from "$lib/components/Billiards/BilliardTable.svelte";
    import { OrbitControls } from "@threlte/extras";
</script>

<div>
    <Canvas>
        <World>
            <T.PerspectiveCamera makeDefault position.x={300} position.y={200}>
                <OrbitControls target.x={25} />
            </T.PerspectiveCamera>
            <T.DirectionalLight castShadow position={[8, 20, -3]} />

            <BilliardTable position={[0, 0, 0]} />
        </World>
    </Canvas>
</div>

<style>
    div {
        height: 100%;
    }
</style>
