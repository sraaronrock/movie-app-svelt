<script>
  import { fly } from "svelte/transition";
  import MovieItem from "./Movie/Item.svelte";

  const APIKEY = `YOUR MOVIEDB API KEY`;
  const BASEURL = `https://api.themoviedb.org/3`;
  const APISETTINGS = `?api_key=${APIKEY}&language=en-US`;

  const movies = (async () => {
    const URL = `${BASEURL}/discover/movie${APISETTINGS}&sort_by=popularity.desc`;
    const response = await fetch(URL);

    return await response.json();
  })();

  let likedMovies = [];

  function toggleLike(event) {
    const movie = event.detail;

    let index = likedMovies.findIndex(m => m.id === movie.id);

    if (index >= 0) {
      likedMovies.splice(index, 1);
      return;
    } else {
      likedMovies.push(movie);
    }
    likedMovies = likedMovies;
  }

  $: like = id => {
    let index = likedMovies.findIndex(m => m.id === id);

    return index >= 0;
  };
</script>

<style>
  main {
    text-align: center;
    padding: 0;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }

    .panel {
      height: 100vh;
      overflow: auto;
    }
  }
</style>

<svelte:head>
  <title>Movies Svelte</title>
  <link
    rel="stylesheet"
    href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
    crossorigin="anonymous" />
</svelte:head>

<main class="container">
  <div class="row">
    <div class="col-12 col-md-6 col-lg-8 border panel">
      <h2>Popular movies</h2>
      <div class="row">
        {#await movies}
          <div class="col-12">
            <p>Cargando datos ...</p>
          </div>
        {:then data}
          {#each data.results as movie}
            <div class="col-12 col-md-6 col-lg-4 p-2">
              <MovieItem
                like={like(movie.id)}
                id={movie.id}
                title={movie.title}
                overview={movie.overview}
                cover={movie.poster_path}
                on:onToggleLike={toggleLike} />
            </div>
          {/each}
        {/await}
      </div>

    </div>
    <div class="col-12 col-md-6 col-lg-4 border panel">
      <h2>My Top Favourite Films</h2>
      <div class="row">
        {#if likedMovies.length}
          {#each likedMovies as movie, i (movie.id)}
            <div
              in:fly={{ duration: 300, y: 20 }}
              out:fly={{ duration: 800, y: -20 }}
              class="col-12 col-md-6 col-lg-4 p-2">
              <MovieItem
                like={like(movie.id)}
                id={movie.id}
                title={movie.title}
                overview=""
                cover={movie.cover}
                on:onToggleLike={toggleLike} />
            </div>
          {/each}
        {:else}
          <div class="col-12">
            <p>Add films ...</p>
          </div>
        {/if}
      </div>
    </div>
  </div>
</main>
