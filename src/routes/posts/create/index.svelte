<script>
    import axios from 'axios';
    import { goto } from '$app/navigation';

    let files   = '';
    let title   = '';
    let content = '';
    let validation = {};

    const storePost = ( async () => {
        const formData = new FormData();
        formData.append('file', files[0]);
        formData.append('title', title);
        formData.append('content', content);

        await axios.post('http://localhost:8888/api/svelte/posts', formData)
        .then(() => {
            goto("/posts");
        })
        .catch((error) => {
            validation = error.response.data
        })
    });
</script>

<div class="card border-0 rounded-3 shadow-sm">
    <div class="card-body">
        <form on:submit|preventDefault={storePost}>

            <div class="form-group mb-3">
                <label class="form-label fw-bold" for="fileInput">Image</label>
                <input type="file" accept="image/png, image/jpeg" name="file" bind:files class="form-control"/>
            </div>
            {#if validation.image}
                <div class="alert alert-danger">
                    {validation.image}
                </div>
            {/if}
            

            <div class="form-group mb-3">
                <label class="form-label fw-bold" for="titleInput">TITLE</label>
                <input class="form-control" bind:value={title} type="text" placeholder="Title" />
            </div>
            {#if validation.title}
                <div class="alert alert-danger">
                    {validation.title}
                </div>
            {/if}


            <div class="form-group mb-3">
                <label class="form-label fw-bold" for="contentInput">CONTENT</label>
                <textarea class="form-control" rows={3} placeholder="Content" bind:value={content} />
            </div>
            {#if validation.content}
                <div class="alert alert-danger">
                    {validation.content}
                </div>
            {/if}

            <button class="btn btn-primary border-0 shadow-sm" type="submit">
                Create
            </button>
        </form>
    </div>
</div>