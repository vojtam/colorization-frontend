      
<script lang="ts">
    import { WandSparkles } from 'lucide-svelte';
	let {
		src1,
		src2,
		caption1,
		caption2,
		fit = "cover"
	}: { src1: string; src2: string; caption1?: string; caption2?: string; fit: string } = $props();

	let sliderPercent = $state(50);
	let dragging = $state(false);

	let sliderElement: HTMLDivElement | undefined = $state();
	let sliderRect: DOMRect | undefined;

	function setSliderPercent(pageX: number) {
		if (!sliderRect) return;
		let percent = ((pageX - sliderRect.x) / sliderRect.width) * 100;
		sliderPercent = Math.min(Math.max(percent, 0), 100);
	}

	function handleInteractionStart(e: MouseEvent | TouchEvent) {
		e.preventDefault();
		sliderRect = sliderElement?.getBoundingClientRect();
		
		const pageX = 'touches' in e ? e.touches[0].pageX : e.pageX;
		
		setSliderPercent(pageX);
		dragging = true;
	}

	$effect(() => {
		if (dragging) {
			const handleMove = (e: MouseEvent | TouchEvent) => {
				if (e.cancelable) e.preventDefault();
				const pageX = 'touches' in e ? e.touches[0].pageX : e.pageX;
				setSliderPercent(pageX);
			};

			const handleEnd = () => {
				dragging = false;
			};

			document.addEventListener('mousemove', handleMove);
			document.addEventListener('touchmove', handleMove, { passive: false }); // {passive: false} is important for preventDefault
			document.addEventListener('mouseup', handleEnd);
			document.addEventListener('touchend', handleEnd);

			return () => {
				document.removeEventListener('mousemove', handleMove);
				document.removeEventListener('touchmove', handleMove);
				document.removeEventListener('mouseup', handleEnd);
				document.removeEventListener('touchend', handleEnd);
			};
		}
	});
</script>


<div class="imageSlider" bind:this={sliderElement}>
    {#if src2}
	<div class="imageWrapper">
		<img src={src1} alt="before" draggable="false" style="object-fit: {fit};" />
	</div>
	<div
		class="imageWrapper afterImage"
		style:clip-path="inset(0 0 0 {sliderPercent}%)"
	>
		<img src={src2} alt="after" draggable="false" style="object-fit: {fit};" />
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
			onmousedown={handleInteractionStart}
			ontouchstart={handleInteractionStart}
		></div>
    {/if}


	{#if caption1}<span class="imageLabel" style:left="0">{caption1}</span>{/if}

    {#if caption2 && src2}
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