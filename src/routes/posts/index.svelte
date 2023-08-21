<script>
  import { onMount } from "svelte";
  import axios from "axios";
  let posts = [];

  const fetchDataPosts = async () => {
    await axios
      .get("http://localhost:8888/api/svelte/posts")
      .then((response) => {
        if (response.data.status == 200) {
          posts = response.data.result;
        }
      });
  };

  onMount(async () => {
    fetchDataPosts();
  });

  const deletePost = async (id) => {
    await axios
      .delete(`http://localhost:8888/api/svelte/posts/${id}`)
      .then(() => {
        fetchDataPosts();
      });
  };
</script>

<div class="card border-0 rounded-3 shadow-sm">
  <div class="card-body">
    <a
      sveltekit:prefetch
      href="/posts/create"
      class="btn btn-success btn-md shadow-sm rounded-3 border-0 mb-3">Add</a
    >
    <table class="table table-striped table-bordered mb-0">
      <thead>
        <tr>
          <th scope="col">Image</th>
          <th scope="col">Title</th>
          <th scope="col">Content</th>
          <th scope="col">Actions</th>
        </tr>
      </thead>
      <tbody>
        {#each posts as post}
          <tr>
            <td class="text-center">
              <img
                src={`http://localhost:8888/storage/svelte/thumbnails/${post.image}`}
                width="150"
                class="rounded-3"
                alt=""
              />
            </td>
            <td>{post.title}</td>
            <td>{post.content}</td>
            <td class="text-center">
              <a
                sveltekit:prefetch
                href={`/posts/edit/${post.id}`}
                class="btn btn-sm btn-primary border-0 shadow-sm mb-3">EDIT</a
              >
              <button
                on:click={() => deletePost(post.id)}
                class="btn btn-sm btn-danger border-0 shadow-sm mb-3"
                >DELETE</button
              >
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>
