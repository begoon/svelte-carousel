<script>
    import { swipe } from "svelte-gestures";
    import { files } from "./files.js";

    import { onMount } from "svelte";

    const images = files();

    let carousel;
    let cells = [];
    let cellCount = images.length;

    let cellWidth;
    let cellHeight;

    let orientation;
    let selectedIndex = 0;

    let rotation, radius, theta;

    onMount(() => {
        cells = carousel.querySelectorAll(".carousel-cell");

        cellWidth = carousel.offsetWidth;
        cellHeight = carousel.offsetHeight;

        detectOrientation();
    });

    function rotateCarousel() {
        const angle = theta * selectedIndex * -1;
        carousel.style.transform =
            "translateZ(" + -radius + "px) " + rotation + "(" + angle + "deg)";
    }

    function resize() {
        theta = 360 / cellCount;
        const size = orientation ? cellWidth : cellHeight;
        radius = Math.round(size / 2 / Math.tan(Math.PI / cellCount));
        for (let i = 0; i < cells.length; i++) {
            const cell = cells[i];
            if (i < cellCount) {
                // visible cell
                cell.style.opacity = 1;
                var cellAngle = theta * i;
                cell.style.transform =
                    rotation +
                    "(" +
                    cellAngle +
                    "deg) translateZ(" +
                    radius +
                    "px)";
            } else {
                // hidden cell
                cell.style.opacity = 0;
                cell.style.transform = "none";
            }
        }
        rotateCarousel();
    }

    function orient(horizontal = true) {
        orientation = horizontal;
        rotation = orientation ? "rotateY" : "rotateX";
        resize();
    }

    function previous() {
        selectedIndex--;
        rotateCarousel();
    }

    function next() {
        selectedIndex++;
        rotateCarousel();
    }

    function keydown(event) {
        switch (event.key) {
            case "ArrowLeft":
                orientation ? previous() : orient(!orientation);
                break;
            case "ArrowRight":
                orientation ? next() : orient(!orientation);
                break;
            case "ArrowUp":
                !orientation ? previous() : orient(!orientation);
                break;
            case "ArrowDown":
                !orientation ? next() : orient(!orientation);
                break;
        }
    }

    function swiper(direction) {
        console.log(direction);
        if (orientation) {
            if (direction === "left") next();
            if (direction === "right") previous();
        } else {
            if (direction === "top") previous();
            if (direction === "bottom") next();
        }
    }

    function detectOrientation() {
        console.log(window.screen.orientation.type);
        orientation = window.screen.orientation.type.startsWith("landscape");
        orient(orientation);
    }

    window.screen.orientation.addEventListener("change", detectOrientation);
</script>

<svelte:window on:keydown={keydown} />

<div
    class="swiper"
    use:swipe={{ timeframe: 500, minSwipeDistance: 200 }}
    on:swipe={(event) => swiper(event.detail.direction)}
>
    <div class="scene">
        <div class="carousel" bind:this={carousel}>
            {#each images as file, i}
                <div class="carousel-cell">
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <img src={file} draggable="false" />
                </div>
            {/each}
        </div>
    </div>
</div>

<div class="ranger">
    <input
        type="range"
        min="3"
        max={images.length}
        bind:value={cellCount}
        on:input={resize}
        style="background-color: #eee; appearance: none; height: 1rem; width: 100%;  border-radius: 1em"
    />
    <span class="ranger-value">{cellCount}</span>
</div>

<style>
    .ranger {
        position: fixed;
        top: 0;
        left: 50%;
        transform: translateX(-50%);
        width: 90vw;
    }
    @media screen and (min-width: 1024px) {
        .ranger {
            width: 50vw;
        }
    }
    .ranger input {
        width: 100%;
    }
    .ranger-value {
        position: absolute;
        top: 2px;
        left: 50%;
        color: white;
        transform: translateX(-50%);
        background-color: blue;
        padding: 0 0.4rem;
        font-size: 0.9rem;
    }

    .swiper {
        height: 100vh;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .scene {
        position: relative;
        width: 200px;
        height: 200px;
        perspective: 1000px;
    }

    .carousel {
        width: 100%;
        height: 100%;
        transform: translateZ(-1000px);
        transform-style: preserve-3d;
        transition: transform 1s;
    }
    .carousel-cell {
        position: absolute;
        width: 180px;
        height: 180px;
        left: 10px;
        top: 10px;
        border: 2px solid black;
        transition: transform 1s, opacity 1s;
    }
    .carousel img {
        width: 100%;
        height: auto;
    }

    .carousel-cell:nth-child(9n + 1) {
        background: hsla(0, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 2) {
        background: hsla(40, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 3) {
        background: hsla(80, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 4) {
        background: hsla(120, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 5) {
        background: hsla(160, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 6) {
        background: hsla(200, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 7) {
        background: hsla(240, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 8) {
        background: hsla(280, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(9n + 0) {
        background: hsla(320, 100%, 50%, 0.8);
    }
    .carousel-cell:nth-child(1) {
        transform: rotateY(0deg) translateZ(200px);
    }
    .carousel-cell:nth-child(2) {
        transform: rotateY(40deg) translateZ(200px);
    }
    .carousel-cell:nth-child(3) {
        transform: rotateY(80deg) translateZ(200px);
    }
    .carousel-cell:nth-child(4) {
        transform: rotateY(120deg) translateZ(200px);
    }
    .carousel-cell:nth-child(5) {
        transform: rotateY(160deg) translateZ(200px);
    }
    .carousel-cell:nth-child(6) {
        transform: rotateY(200deg) translateZ(200px);
    }
    .carousel-cell:nth-child(7) {
        transform: rotateY(240deg) translateZ(200px);
    }
    .carousel-cell:nth-child(8) {
        transform: rotateY(280deg) translateZ(200px);
    }
    .carousel-cell:nth-child(9) {
        transform: rotateY(320deg) translateZ(200px);
    }
</style>
