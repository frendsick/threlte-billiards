<script lang="ts">
    import BilliardBall from "./BilliardBall.svelte";
    import { BALL_DIAMETER, BALL_RADIUS } from "./common.js";

    interface Props {
        firstBallPosition: [number, number, number];
    }
    let { firstBallPosition }: Props = $props();

    function getRackedBalls() {
        const ballHeight = firstBallPosition[1];
        const rowOffset = Math.sqrt(BALL_DIAMETER ** 2 - BALL_RADIUS ** 2);

        let balls: { id: number; position: [number, number, number] }[] = [];
        let ballIndex = 0;
        for (let row = 0; row < 5; row++) {
            for (let i = 0; i <= row; i++) {
                let x = firstBallPosition[0] + (i - row / 2) * BALL_DIAMETER;
                let z = firstBallPosition[2] + row * rowOffset;
                balls.push({ id: ballIndex++, position: [x, ballHeight, z] });
            }
        }

        return balls;
    }
</script>

{#each getRackedBalls() as ball (ball.id)}
    <BilliardBall position={ball.position} />
{/each}
