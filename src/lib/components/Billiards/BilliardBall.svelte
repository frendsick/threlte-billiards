<script lang="ts">
    import * as THREE from "three";
    import { T } from "@threlte/core";
    import { AutoColliders, RigidBody } from "@threlte/rapier";
    import { BALL_RADIUS } from "./common.js";

    interface Props {
        position: [number, number, number];
    }
    let { position }: Props = $props();
</script>

<RigidBody>
    <AutoColliders shape="ball">
        <T.Mesh
            {position}
            receiveShadow
            geometry={new THREE.SphereGeometry(BALL_RADIUS)}
            material={new THREE.MeshStandardMaterial()}
        />
    </AutoColliders>
</RigidBody>
