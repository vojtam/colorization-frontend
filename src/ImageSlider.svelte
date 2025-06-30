      
<script lang="ts">
    import { WandSparkles } from 'lucide-svelte';
	let {
		src1,
		src2,
		caption1,
		caption2
	}: { src1: string; src2: string; caption1?: string; caption2?: string } = $props();

	let sliderPercent = $state(50);
	let dragging = $state(false);

	let sliderElement: HTMLDivElement | undefined = $state();
	let sliderRect: DOMRect | undefined;

	function setSliderPercent(pageX: number) {
		if (!sliderRect) return;
		let percent = ((pageX - sliderRect.x) / sliderRect.width) * 100;
		sliderPercent = Math.min(Math.max(percent, 0), 100);
	}


	$effect(() => {
		if (dragging) {
			const handleMousemove = (e: MouseEvent) => {
				setSliderPercent(e.pageX);
			};

			const handleMouseup = () => {
				dragging = false;
			};

			document.addEventListener('mousemove', handleMousemove);
			document.addEventListener('mouseup', handleMouseup);

			return () => {
				document.removeEventListener('mousemove', handleMousemove);
				document.removeEventListener('mouseup', handleMouseup);
			};
		}
	});
</script>

<div class="imageSlider" bind:this={sliderElement}>
    {#if src2}
	<div class="imageWrapper">
		<img src={src1} alt="before" draggable="false" />
	</div>
	<div
		class="imageWrapper afterImage"
		style:clip-path="inset(0 0 0 {sliderPercent}%)"
	>
		<img src={src2} alt="after" draggable="false" />
	</div>

    <div class="sliderHandle" style:left="{sliderPercent}%">
		<div class="iconWrapper">
			<WandSparkles color="white" size={24} />
		</div>
	</div>
    {/if}

    {#if src2}
        <div class="sliderHandle" style:left="{sliderPercent}%">
            <div class="iconWrapper">
                <WandSparkles color="white" size={24} />
            </div>
        </div>

        <div
            class="invisibleCover"
            style:cursor={dragging ? 'ew-resize' : 'pointer'}
            onmousedown={(e) => {
                e.preventDefault();
                sliderRect = sliderElement?.getBoundingClientRect();
                setSliderPercent(e.pageX);
                dragging = true;
            }}
        ></div>
    {/if}


	{#if caption1}<span class="imageLabel" style:left="0">{caption1}</span>{/if}

    {#if caption2 && src2}
		<!-- Also make the second caption conditional -->
		<span class="imageLabel" style:right="0">{caption2}</span>
	{/if}

</div>

<style>
	.imageSlider {
		display: block;
		width: 100%;
		height: 100%;
        max-height: 500px; 
		position: relative;
        overflow: hidden;
        background-color: #f0f0f0; 
		border-radius: 0.5rem; 
	}

	.imageWrapper {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		pointer-events: none; 
	}

	.imageWrapper img {
		display: block;
		width: 100%;
        height: 100%;
        object-fit: cover;
		user-select: none;
		/* -moz-user-select: none;
		-webkit-user-select: none;
		-webkit-user-drag: none; */
	}

	.afterImage {
		z-index: 2; 
	}

    .sliderHandle {
		position: absolute;
		top: 0;
		height: 100%;
		width: 4px; 
		background: white;
		transform: translateX(-50%); 
		pointer-events: none; 
		z-index: 100; 
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.sliderHandle .iconWrapper {
		background: rgba(0, 0, 0, 0.6);
		border-radius: 50%;
		padding: 8px;
		display: flex;
		align-items: center;
		justify-content: center;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
	}


	.imageSlider .invisibleCover {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 10;
	}

	.imageSlider .imageLabel {
		position: absolute;
		bottom: 0;
		padding: 8px;
		background-color: rgba(0, 0, 0, 42%);
		color: white;
		font-size: 12px;
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
		z-index: 5;
	}
</style>