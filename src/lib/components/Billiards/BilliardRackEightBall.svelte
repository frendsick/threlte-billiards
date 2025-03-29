<script lang="ts">
    import type { ColorRepresentation } from "three";
    import BilliardBall from "./BilliardBall.svelte";
    import { BALL_COLORS, BALL_DIAMETER, BALL_RADIUS } from "./common.js";

    interface Props {
        firstBallPosition: [number, number, number];
    }
    let { firstBallPosition }: Props = $props();

    function randomiseBallsForRack() {
        // Randomise balls exept for the black ball
        const balls = BALL_COLORS.flatMap((color) => [
            { color, isStriped: false },
            { color, isStriped: true },
        ])
            .map((ball) => ({ ball, sort: Math.random() }))
            .sort((a, b) => a.sort - b.sort)
            .map(({ ball }) => ball);

        // Add black ball to its position
        balls.splice(4, 0, { color: "black", isStriped: false });

        // Ensure corner balls are different types (one solid, one striped)
        const [corner1, corner2] = [10, 14];
        if (balls[corner1].isStriped === balls[corner2].isStriped) {
            const swapIndex = balls.findIndex(
                (ball) => ball.isStriped !== balls[corner1].isStriped,
            );
            [balls[swapIndex], balls[corner2]] = [balls[corner2], balls[swapIndex]];
        }

        return balls;
    }

    function getRackedBalls() {
        const ballHeight = firstBallPosition[1];
        const rowOffset = Math.sqrt(BALL_DIAMETER ** 2 - BALL_RADIUS ** 2);
        const randomBalls = randomiseBallsForRack();

        let balls: {
            id: number;
            position: [number, number, number];
            color: ColorRepresentation;
            isStriped: boolean;
        }[] = [];
        let ballIndex = 0;
        for (let row = 0; row < 5; row++) {
            for (let i = 0; i <= row; i++) {
                const x = firstBallPosition[0] + (i - row / 2) * BALL_DIAMETER;
                const z = firstBallPosition[2] + row * rowOffset;
                let ball = randomBalls[ballIndex];

                balls.push({
                    id: ballIndex++,
                    position: [x, ballHeight, z],
                    color: ball.color,
                    isStriped: ball.isStriped,
                });
            }
        }

        return balls;
    }
</script>

{#each getRackedBalls() as ball (ball.id)}
    <BilliardBall
        position={ball.position}
        color={ball.color}
        isStriped={ball.isStriped}
        rotation={[
            // Randomize spawn orientation
            Math.random() * Math.PI * 2,
            Math.random() * Math.PI * 2,
            Math.random() * Math.PI * 2,
        ]}
    />
{/each}
