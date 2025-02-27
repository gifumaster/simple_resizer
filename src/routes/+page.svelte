<script>
import ImageUploader from '$lib/ImageUploader.svelte';
</script>

<ImageUploader />

<div class="instructions mt-8 p-4 bg-gray-100 rounded-lg">
    <h2 class="text-xl font-bold mb-4">使用手順</h2>
    <img src="/thumb.png" alt="サンプル画像" class="mb-4 rounded-lg shadow-md max-w-md">
    <ol class="list-decimal pl-6 space-y-2">
        <li><a href="https://vrchat.com/home/world/wrld_96460268-8c89-438f-b8f2-2ea2f1ed1ce7/info" target="_blank" class="text-blue-600 hover:underline">こちらのワールド</a>にアクセス。</li>
        <li>上のパターンの写真をとり、撮影する</li>
        <li>できた画像をドラッグアンドドロップ</li>
        <li><a href="https://vrchat.com/home/avatars" target="_blank" class="text-blue-600 hover:underline">VRChatのアバターページ</a>でサムネイルを更新する。</li>
    </ol>
</div>

<style>
    .instructions {
        max-width: 800px;
        margin: 0 auto;
    }
</style>
