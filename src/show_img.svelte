<script lang="ts">
    let { imageSrc, emptyCaption } = $props<{ imageSrc: string | null, emptyCaption: string | null }>();
</script>

<div class="w-full h-full min-h-[250px] flex justify-center items-center rounded-md p-4">
    {#if imageSrc}
		<img
			src={imageSrc}
			alt="Uploaded"
			class="max-w-full max-h-[50vh] rounded-sm object-contain"
		/>
	{:else}
		<p class="text-gray-500 text-lg">{emptyCaption}</p>
	{/if}

</div>