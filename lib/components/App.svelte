<script>
    import { swipe } from "svelte-gestures";

    let direction;

    const directions = {
        left: () => {
            if (--cx < 0) cx = mx - 1;
        },
        right: () => {
            if (++cx >= mx) cx = 0;
        },
        top: () => {
            if (--cy < 0) cy = my - 1;
        },
        bottom: () => {
            if (++cy >= my) cy = 0;
        },
    };

    function select(x, y) {
        cx = x;
        cy = y;
    }

    import Arrow from "./Arrow.svelte";
    import { files } from "./files.js";
    import Carousel from "./Carousel.svelte";

    const images = files();

    const mx = images[0].length;
    let cx = Math.floor(mx / 2);

    const my = images.length;
    let cy = Math.floor(my / 2);

    let carousel = false;
</script>

<Carousel />
<div>Use arrows or swipe to switch the image.</div>
<div
    class="swiper"
    use:swipe={{ timeframe: 300, minSwipeDistance: 50 }}
    on:swipe={(event) => (direction = event.detail.direction)}
>
    <table>
        <tr><td colspan="3"><Arrow to="top" bind:direction /></td></tr>
        <tr>
            <td><Arrow to="left" bind:direction /></td>
            <td class="preview">
                <div class="preview">
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <img
                        draggable="false"
                        src={images[cy][cx]}
                        class="preview swipe-{direction}"
                        on:animationend={() => {
                            directions[direction]();
                            direction = null;
                        }}
                    />
                </div>
            </td>
            <td><Arrow to="right" bind:direction /></td>
        </tr>
        <tr><td colspan="3"><Arrow to="bottom" bind:direction /></td></tr>
    </table>
</div>

{#each images as names, y}
    <ul>
        {#each names as name, x}
            {@const current = x === cx && y === cy}
            <!-- svelte-ignore a11y-missing-attribute -->
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <img
                src={name}
                class="icon"
                class:current
                on:click={() => select(x, y)}
                on:tap={() => select(x, y)}
            />
        {/each}
    </ul>
{/each}

<style>
    * {
        margin: 0;
        padding: 0;
        --swiper-size: 200px;
    }
    div.swiper {
        width: 100%;
        aspect-ratio: 1;
        justify-content: center;
        align-items: center;
        box-sizing: bordaer-box;
        border: 1px solid black;
    }
    @media screen and (min-width: 1024px) {
        div.swiper {
            width: 300px;
        }
    }
    table {
        width: 100%;
        height: 100%;
        border-collapse: collapse;
    }
    td {
        text-align: center;
        vertical-align: middle;
        font-size: 40px;
        user-select: none;
        cursor: pointer;
    }
    td .preview {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100%;
    }
    .preview {
        position: relative;
        height: var(--swiper-size);
        width: var(--swiper-size);
        overflow: hidden;
    }
    img.preview {
        position: absolute;
        top: 0;
        left: 0;
        border: 1px solid black;
        box-sizing: border-box;
    }
    img.swipe-bottom {
        animation: bottom 1s ease-in-out forwards;
    }
    img.swipe-top {
        animation: top 1s ease-in-out forwards;
    }
    img.swipe-left {
        animation: left 1s ease-in-out forwards;
    }
    img.swipe-right {
        animation: right 1s ease-in-out forwards;
    }
    img.icon {
        display: inline-block;
        width: 30px;
        height: auto;
        margin: 2px;
        cursor: pointer;
    }
    @keyframes bottom {
        0% {
            margin-top: 0;
        }
        100% {
            margin-top: var(--swiper-size);
        }
    }
    @keyframes top {
        0% {
            margin-top: 0;
        }
        100% {
            margin-top: calc(-1 * var(--swiper-size));
        }
    }
    @keyframes left {
        0% {
            margin-left: 0;
        }
        100% {
            margin-left: calc(-1 * var(--swiper-size));
        }
    }
    @keyframes right {
        0% {
            margin-left: 0;
        }
        100% {
            margin-left: var(--swiper-size);
        }
    }
    img.current {
        outline: 2px solid red;
        pointer-events: none;
    }
</style>
