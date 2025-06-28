<script lang="ts">
    import ImgUpload from "../img_upload.svelte";
    import ShowImg from "../show_img.svelte";
    import { Loader2 } from "lucide-svelte";
    import { Button } from "$lib/components/ui/button/index.js";
    
    let isImageUploaded = false;

    let uploadedFile: File | null = null;
    let uploadedImageSrc: string | null = null;

    let colorizedImageUrl: string | null = null;
    let isLoading = false;
    let errorMessage = "";

    async function handleColorize() {
        if (!uploadedFile) {
            errorMessage = "Please upload a file first.";
            return;
        }

        isLoading = true;
        errorMessage = "";
        colorizedImageUrl = null;

        const formData = new FormData();
        formData.append("image_file", uploadedFile);

        try {
            const backendBaseUrl = import.meta.env.VITE_BACKEND_URL;
        
            const apiUrl = `${backendBaseUrl}/colorize`;
            console.log("Sending request to:", apiUrl);
            const response = await fetch(apiUrl, {
                method: "POST",
                body: formData,
            });

            if (!response.ok) {
                const errorData = await response.json().catch(() => ({ detail: "An unknown error occurred." }));
                throw new Error(`API Error: ${response.status} ${response.statusText}. ${errorData.detail}`);
            }
            const imageBlob = await response.blob();
            
            colorizedImageUrl = URL.createObjectURL(imageBlob);

        } catch (error: any) {
            console.error("Failed to colorize image:", error);
            errorMessage = error.message || "Failed to connect to the server.";
        } finally {
            isLoading = false;
        }
    }

</script>

<main class="w-screen bg-blue-100 h-screen flex flex-col  items-center">
    <div class="text-center font-display h-[25%] p-8 flex items-center justify-end flex-col">
        <h1 class="text-5xl  font-bold mb-4">Image Colorizer</h1>
        <p class="text-2xl">Add color to old family photos and historic images</p>
    </div>
    <ImgUpload bind:showImage={isImageUploaded} bind:file={uploadedFile} bind:imageSrc={uploadedImageSrc} />
    <div class="w-[80%] max-w-4xl grid grid-cols-1 md:grid-cols-2 gap-8 items-start">
        <div class="w-full">
            <h2 class="text-2xl font-semibold text-center mb-4">1. Upload Image</h2>
            <ShowImg imageSrc={uploadedImageSrc} emptyCaption="Your original image will appear here"/>
        </div>
        <div class="w-full">
            <h2 class="text-2xl font-semibold text-center mb-4">2. View Result</h2>
            <div class="w-full h-full min-h-[250px] flex justify-center items-center">
                {#if isLoading}
                    <div class="flex flex-col items-center gap-4 text-gray-500">
                        <Loader2 class="animate-spin" size={48} />
                        <span>Colorizing, please wait...</span>
                    </div>
                {:else if errorMessage}
                     <p class="text-red-500 text-center">{errorMessage}</p>
                {:else}
                    <ShowImg imageSrc={colorizedImageUrl} emptyCaption="Your colorized image will appear here"/>
                {/if}
            </div>
        </div>
    </div>
    {#if isImageUploaded}
        <div class="flex p-8 items-center justify-center">
            <Button disabled={isLoading} onclick={handleColorize} class="text-2xl p-12 cursor-pointer" variant="default">Colorize!</Button>
        </div>
    {/if}
</main>