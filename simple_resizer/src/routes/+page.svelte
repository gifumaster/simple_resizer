<script lang="ts">
let imageFile: File | null = null;
let previewUrl: string | null = null;
let resizedImageUrl: string | null = null;

function handleFileSelect(event: Event) {
    const input = event.target as HTMLInputElement;
    if (input.files && input.files[0]) {
        imageFile = input.files[0];
        previewUrl = URL.createObjectURL(imageFile);
    }
}

async function processImage() {
    if (!imageFile) return;

    const img = new Image();
    img.src = previewUrl!;
    
    await new Promise(resolve => img.onload = resolve);

    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d')!;

    // 画像の短い辺を基準に正方形にクロップ
    const size = Math.min(img.width, img.height);
    const x = 0;  // 左端からクロップ
    const y = 0;  // 上端からクロップ

    // 出力サイズを1024x1024に設定
    canvas.width = 1024;
    canvas.height = 1024;

    // 画像を描画
    ctx.drawImage(img, x, y, size, size, 0, 0, 1024, 1024);

    // 画像をBlobに変換
    const blob = await new Promise<Blob>(resolve => {
        canvas.toBlob(blob => resolve(blob!), 'image/png');
    });

    // URLを作成
    if (resizedImageUrl) {
        URL.revokeObjectURL(resizedImageUrl);
    }
    resizedImageUrl = URL.createObjectURL(blob);
}

function downloadImage() {
    if (!resizedImageUrl) return;
    
    const link = document.createElement('a');
    link.href = resizedImageUrl;
    link.download = 'resized-image.png';
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}

// コンポーネントのクリーンアップ
function cleanup() {
    if (previewUrl) URL.revokeObjectURL(previewUrl);
    if (resizedImageUrl) URL.revokeObjectURL(resizedImageUrl);
}
</script>

<svelte:head>
    <title>Simple Image Resizer</title>
</svelte:head>

<div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Simple Image Resizer</h1>
    
    <div class="space-y-4">
        <div class="space-y-2">
            <label class="block">
                画像を選択:
                <input 
                    type="file" 
                    accept="image/*" 
                    on:change={handleFileSelect} 
                    class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100"
                />
            </label>
        </div>

        {#if previewUrl}
            <div class="space-y-2">
                <h2 class="text-xl">プレビュー:</h2>
                <img src={previewUrl} alt="Preview" class="max-w-md border"/>
                <button 
                    on:click={processImage}
                    class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
                >
                    リサイズ
                </button>
            </div>
        {/if}

        {#if resizedImageUrl}
            <div class="space-y-2">
                <h2 class="text-xl">リサイズ後:</h2>
                <img src={resizedImageUrl} alt="Resized" class="max-w-md border"/>
                <button 
                    on:click={downloadImage}
                    class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600"
                >
                    ダウンロード
                </button>
            </div>
        {/if}
    </div>
</div>