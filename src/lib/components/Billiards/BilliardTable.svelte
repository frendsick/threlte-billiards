<script lang="ts">
    import * as THREE from "three";
    import { T } from "@threlte/core";
    import { AutoColliders } from "@threlte/rapier";
    import { CSG } from "three-csg-ts";

    interface Props {
        position: [number, number, number];
    }
    let { position }: Props = $props();

    // https://www.dimensions.com/element/9-foot-billiards-pool-table
    const TABLE_LENGTH = 290;
    const TABLE_WIDTH = 163;
    const TABLE_HEIGHT = 81.3;

    // https://www.dimensions.com/element/billiard-balls
    const BALL_DIAMETER = 5.715;
    const BALL_RADIUS = BALL_DIAMETER / 2;

    const PLAYING_AREA_LENGTH = 254;
    const PLAYING_AREA_WIDTH = 127;

    const APRON_HEIGHT = 30;
    const PLAYING_AREA_DEPTH = BALL_RADIUS + 3;

    const TABLE_LEG_HEIGHT = TABLE_HEIGHT - APRON_HEIGHT;
    const TABLE_LEG_WIDTH = 20;

    const TABLE_LEG_MESH_POSITIONS = [
        [
            TABLE_WIDTH / 2 - TABLE_LEG_WIDTH / 2,
            -APRON_HEIGHT / 2 - TABLE_LEG_HEIGHT / 2,
            TABLE_LENGTH / 2 - TABLE_LEG_WIDTH / 2,
        ],
        [
            -TABLE_WIDTH / 2 + TABLE_LEG_WIDTH / 2,
            -APRON_HEIGHT / 2 - TABLE_LEG_HEIGHT / 2,
            TABLE_LENGTH / 2 - TABLE_LEG_WIDTH / 2,
        ],
        [
            -TABLE_WIDTH / 2 + TABLE_LEG_WIDTH / 2,
            -APRON_HEIGHT / 2 - TABLE_LEG_HEIGHT / 2,
            -TABLE_LENGTH / 2 + TABLE_LEG_WIDTH / 2,
        ],
        [
            TABLE_WIDTH / 2 - TABLE_LEG_WIDTH / 2,
            -APRON_HEIGHT / 2 - TABLE_LEG_HEIGHT / 2,
            -TABLE_LENGTH / 2 + TABLE_LEG_WIDTH / 2,
        ],
    ] as const;

    function tableLegMesh(x: number, y: number, z: number) {
        const tableLeg = new THREE.Mesh(
            new THREE.BoxGeometry(TABLE_LEG_WIDTH, TABLE_LEG_HEIGHT, TABLE_LEG_WIDTH),
        );
        tableLeg.position.set(x, y, z);
        tableLeg.updateMatrix();
        return tableLeg;
    }

    function tableMesh() {
        // Construct apron without playing area
        const apron = new THREE.Mesh(
            new THREE.BoxGeometry(TABLE_WIDTH, APRON_HEIGHT, TABLE_LENGTH),
        );
        apron.updateMatrix();

        // Cut playing area from apron
        const playingArea = new THREE.Mesh(
            new THREE.BoxGeometry(PLAYING_AREA_WIDTH, PLAYING_AREA_DEPTH, PLAYING_AREA_LENGTH),
        );
        playingArea.position.set(0, APRON_HEIGHT / 2 - PLAYING_AREA_DEPTH / 2, 0);
        playingArea.updateMatrix();
        const table = CSG.subtract(apron, playingArea);

        // Add legs to the table
        const tableWithLegs = TABLE_LEG_MESH_POSITIONS.reduce((table, position) => {
            return CSG.union(table, tableLegMesh(...position));
        }, table);

        return tableWithLegs;
    }

    const table = tableMesh();
</script>

<T.Group {position}>
    <AutoColliders shape="trimesh">
        <T.Mesh
            receiveShadow
            geometry={table.geometry}
            material={new THREE.MeshStandardMaterial()}
        />
    </AutoColliders>
</T.Group>
