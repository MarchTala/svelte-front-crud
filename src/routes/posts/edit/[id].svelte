<script>
  import { onMount } from "svelte";
  import { page } from "$app/stores";
  import { goto } from "$app/navigation";
  import axios from "axios";

  let files = "";
  let title = "";
  let content = "";
  let validation = {};

  const fetchDataPost = async () => {
    try {
      const response = await axios.get(
        `http://localhost:8888/api/svelte/posts/${$page.params.id}`
      );

      if (response.data.status === 200 && response.data.result.length > 0) {
        title = response.data.result[0].title;
        content = response.data.result[0].content;
        files = response.data.result[0].image;
      }
    } catch (error) {
      console.error("Error fetching data:", error);
    }
  };

  onMount(async () => {
    fetchDataPost();
  });

  const updatePost = async () => {
    const formData = new FormData();

    formData.append("file", files[0]);
    formData.append("title", title);
    formData.append("content", content);

    try {
      await axios.post(
        `http://localhost:8888/api/svelte/update/${$page.params.id}`,
        formData
      );
      goto("/posts");
    } catch (error) {
      validation = error.response.data;
    }
  };
</script>

<div class="card border-0 rounded-3 shadow-sm">
  <div class="card-body">
    <form on:submit|preventDefault={updatePost}>
      <div class="form-group mb-3">
        <label class="form-label fw-bold" for="fileInput">Image</label>
        <input
          id="fileInput"
          type="file"
          accept="image/png, image/jpeg"
          name="file"
          bind:files
          class="form-control"
        />
      </div>

      <!-- {#if files}
        <div class="form-group mb-3">
          <img
            src={`http://localhost:8888/storage/svelte/thumbnails/${files}`}
            alt=""
            width="150"
            class="rounded-3"
          />
        </div>
      {/if} -->

      <div class="form-group mb-3">
        <label class="form-label fw-bold" for="titleInput">TITLE</label>
        <input
          id="titleInput"
          class="form-control"
          bind:value={title}
          type="text"
          placeholder="Title"
        />
      </div>
      {#if validation.title}
        <div class="alert alert-danger">
          {validation.title}
        </div>
      {/if}

      <div class="form-group mb-3">
        <label class="form-label fw-bold" for="contentInput">CONTENT</label>
        <textarea
          id="contentInput"
          class="form-control"
          rows={3}
          placeholder="Content"
          bind:value={content}
        />
      </div>
      {#if validation.content}
        <div class="alert alert-danger">
          {validation.content}
        </div>
      {/if}

      <button class="btn btn-primary border-0 shadow-sm" type="submit">
        UPDATE
      </button>
    </form>
  </div>
</div>
