<script>
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher();
    let fileInput;
    let fileName = "";

    function handleFileSelect() {
        const file = fileInput.files[0];
        if (!file) return;

        // Only accept image files
        if (!file.type.startsWith("image/")) {
            dispatch("error", "Please select an image file.");
            return;
        }

        fileName = file.name;

        const reader = new FileReader();
        reader.onload = (e) => {
            dispatch("fileSelected", {
                file: file,
                previewUrl: e.target.result
            });
            console.log(previewUrl);
        };
        reader.readAsDataURL(file);
    }

    function triggerFileInput() {
        fileInput.click();
    }
</script>

<div class="form-control w-full">
    <label class="label">
        <span class="label-text">Alleen .png/.jpg</span>
    </label>
    <input
        bind:this={fileInput}
        type="file"
        class="hidden"
        accept="image/*"
        on:change={handleFileSelect}
    />
    <button type="button" class="btn btn-primary w-full mt-8" on:click={triggerFileInput}>
        Kies een foto
    </button>
    <span class="mt-2 block text-gray-500">{fileName || 'Geen bestand gekozen'}</span>
</div>
