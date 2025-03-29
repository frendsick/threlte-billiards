<script lang="ts">
    import * as THREE from "three";
    import { T } from "@threlte/core";
    import { AutoColliders, RigidBody } from "@threlte/rapier";
    import { BALL_RADIUS } from "./common.js";

    interface Props {
        position: [number, number, number];
        rotation?: [number, number, number];
        color?: THREE.ColorRepresentation;
        isStriped?: boolean;
    }
    let { position, rotation, color, isStriped }: Props = $props();

    // Use shaders for striped balls
    const stripeColor = "#F2EDDC";
    const material = !isStriped
        ? new THREE.MeshStandardMaterial({ color })
        : new THREE.ShaderMaterial({
              uniforms: {
                  baseColor: { value: new THREE.Color(color) },
                  stripeColor: { value: new THREE.Color(stripeColor) },
              },
              vertexShader: `
                  varying vec2 vUv;
                  void main() {
                      vUv = uv;
                      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
                  }
              `,
              fragmentShader: `
                  varying vec2 vUv;
                  uniform vec3 baseColor;
                  uniform vec3 stripeColor;

                  void main() {
                      float stripe = step(0.2, abs(vUv.y - 0.5));
                      vec3 color = mix(baseColor, stripeColor, stripe);
                      gl_FragColor = vec4(color, 1.0);
                  }
              `,
          });
</script>

<RigidBody>
    <AutoColliders shape="ball">
        <T.Mesh
            {position}
            rotation={rotation || [0, 0, 0]}
            {material}
            receiveShadow
            geometry={new THREE.SphereGeometry(BALL_RADIUS)}
        />
    </AutoColliders>
</RigidBody>
