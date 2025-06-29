<script>
    import { onMount } from 'svelte';
    import Header from './components/Header.svelte';
    import FileUpload from './components/FileUpload.svelte';
    import ImageCropModal from './components/ImageCropModal.svelte';

    let selectedFile = null;
    let previewUrl = '';
    let error = '';
    let mergedImage = null;
    let isEditing = false;

    let unique = {}

    function handleFileSelected(event) {
        selectedFile = event.detail.file;
        previewUrl = event.detail.previewUrl;
        error = '';
        isEditing = true;
    }

    function handleError(event) {
        error = event.detail;
    }

    function handleProcessedImage(event) {
        mergedImage = event.detail.mergedImage; // Get the merged image from the event
        isEditing = false;
    }

    function downloadImage() {
        const link = document.createElement('a');
        link.href = mergedImage;
        link.download = 'profile_with_banner.jpg';
        link.click();
    }

    function reEdit() {
        isEditing = true; // Reopen the modal for editing
    }

    export function resetApp() {
        unique = {}
        selectedFile = null; // Clear the selected file
        previewUrl = ''; // Clear the preview URL
        error = ''; // Clear any errors
        mergedImage = null; // Clear the merged image
        isEditing = false; // Close the modal
    }

</script>

<style>    
    .merged-image-container {
        text-align: center;
        margin-top: 8px; /* reduced */
    }

    .merged-image {
        object-fit: cover;
        width: 200px;   /* smaller for mobile */
        height: 200px;
        border: 1px solid #ccc;
        border-radius: 8px;
    }

    @media (min-width: 640px) {
        .merged-image {
            width: 300px;
            height: 300px;
        }
        .merged-image-container {
            margin-top: 20px;
        }
    }
</style>

<main class="flex flex-col items-center justify-center min-h-screen max-h-screen overflow-auto">
    <div class="flex flex-col w-full max-w-4xl p-2 sm:p-4 min-h-screen sm:min-h-[60vh] items-center rounded-xl shadow-lg bg-base-200">
        <div class="w-full">
            <Header title="TGL Signal Banner Tool" />
        </div>
        <div class="flex flex-col md:flex-row items-center w-full flex-grow">
            <div class="flex flex-col flex-[3] items-center max-w-4xl w-full">
                <div class="container w-full max-w-xl">
                    <div class="flex flex-col justify-center card-body">
                        
                        {#if !mergedImage}
                        <h2 class="card-title">Upload je foto om te starten:</h2>
                            {#key unique}
                                <FileUpload 
                                    on:fileSelected={handleFileSelected}
                                    on:error={handleError}
                                />
                            {/key}
                        {/if}

                        {#if isEditing && previewUrl}
                            <ImageCropModal 
                                {previewUrl}
                                on:error={handleError}
                                on:imageProcessed={handleProcessedImage}
                            />
                        {/if}

                        {#if error}
                            <div class="alert alert-error shadow-lg">
                                <div>
                                    <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current flex-shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
                                    <span>{error}</span>
                                </div>
                            </div>
                        {/if}
                    

                    {#if mergedImage}
                        <div class="flex flex-col items-center merged-image-container gap-1">
                            <h3 class="font-bold text-lg">Klaar!</h3>
                            <!-- svelte-ignore a11y-img-redundant-alt -->
                            <img src={mergedImage} alt="Merged Image" class="merged-image" />
                            <div class="flex flex-col w-full max-w-xs gap-2">
                                <button class="btn btn-primary" on:click={downloadImage}>
                                    Download foto
                                </button>
                                <button class="btn btn-error" on:click={reEdit}>
                                    Verder aanpassen
                                </button>
                                <button class="btn btn-secondary" on:click={resetApp}>
                                    Reset
                                </button>
                            </div>
                        </div>
                    {/if} 
                </div>
            </div>
        </div>
        <div class="flex-1 flex flex-col">
            <div class="card w-full shadow-sm">
                <div class="card-body card-border flex flex-col text-emerald-500">
                    <div class="collapse collapse-arrow bg-base-100 border-base-300 border">
                        <input type="checkbox" />
                        <div class="collapse-title">Maak samen met ons een kleine vuist tegen de uitbuiting van ons digitale leven door Big Tech! 
                            <br>
                            <br>
                            <span class="font-bold">"Maar heeft dat zin dan?" (Ja, klik)</span>
                        </div>
                        <div class="collapse-content text-sm">
                            Absoluut - de waarde van sociale media platforms wordt alleen bepaald door het aantal gebruikers, dus we kunnen samen het tij keren.
                            <br>
                            <br>
                            Zie:<a class="underline" href="https://en.wikipedia.org/wiki/Network_effect" target="_blank">https://en.wikipedia.org/wiki/Network_effect</a>
                        </div>
                    </div>
                </div>
            </div>
        </div> 
    </div>
    <div class="alert alert-info w-full">
        <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M12 20a8 8 0 100-16 8 8 0 000 16z" />
        </svg>
        <span>
            NB: deze website is zo ontworpen dat je foto nergens wordt geüpload, maar op je eigen computer wordt bewerkt en opgeslagen. De publieke code staat op de 
            <a class="underline" href="https://github.com/thegreenland/signal-banner-app/" target="_blank">TGL GitHub</a>.
        </span>
    </div>
</main>