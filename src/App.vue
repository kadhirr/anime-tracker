<script>
export default {
  data(){
    return {
      query: '',
      my_anime: [],
      search_results: []
    }
  },
  methods: {
    handleInput(e){
      if(!e.target.value){
        this.search_results = [];
      }
    },
    addAnime(anime){
      this.search_results = [];
      this.query = '';

      this.my_anime.push({
        id: anime.mal_id,
        title: anime.title,
        image: anime.images.jpg.image_url,
        total_episodes: anime.episodes,
        watched_episodes: 0
      });
      localStorage.setItem('my_anime', JSON.stringify(this.my_anime));
    },
    increaseWatch(anime){
      anime.watched_episodes++;
      localStorage.setItem('my_anime', JSON.stringify(this.my_anime));
    },
    decreaseWatch(anime){
      anime.watched_episodes--;
      localStorage.setItem('my_anime', JSON.stringify(this.my_anime));
    },
    searchAnime(){
      const url= `https://api.jikan.moe/v4/anime?q=${this.query}`;
      fetch(url)
        .then(res => res.json())
        .then(res => {
          this.search_results = res.data;
        })
    }

  },
  computed:{
    my_anime_asc(){
      return this.my_anime.sort( (a,b) => {
        return a.title.localeCompare(b.title)
      } )
    }
  },
  mounted(){
    this.my_anime = JSON.parse(localStorage.getItem('my_anime')) || [];
  }
}

</script>

<template>
  <main>
    <h1 class="text-6xl m-4 font-bangers">My Anime Tracker</h1>
    <form @submit.prevent="searchAnime">
      <input type="text" placeholder="Anime Name" v-model="query" @input="handleInput" class="shadow-2 focus:outline-none rounded focus:ring-blue-500 border-2 p-4 focus:ring-2">
      <button class="text-white bg-teal-600 rounded m-4 p-2" type="submit">Search</button>
    </form>
    <div v-if="search_results.length > 0">
      <div v-for="anime in search_results" class="flex items-center justify-around p-2 border-2 m-4 bg-slate-200 border-stone-700">
        <img :src="anime.images.jpg.image_url" class="rounded border-fuchsia-400 border-8">
        <div class="details flex">
          <h3 class="m-2 font-extrabold text-xl" >{{anime.title}}</h3>  
          <p :title="anime.synopsis" v-if="anime.synopsis" class="m-2">
            {{anime.synopsis.slice(0,120)}}...
          </p>
          <span class="flex-1"></span>
          <button @click="addAnime(anime)" class="rounded bg-pink-500 text-white py-1 px-3 m-2 hover:scale-105 hover:transition-all">Add to My Anime</button>
        </div>
      </div>
    </div>
    <div class="myanime" v-if="my_anime.length > 0" >
      <h2 class="font-mono text-4xl m-4">My Anime</h2>
      <div v-for="anime in my_anime_asc" class="anime flex items-center justify-around p-2 border-2 m-4 bg-slate-200 border-stone-700">
        <img :src="anime.image" class="w-32">
        <h3 class="text-2xl font-bangers">{{anime.title}}</h3>
        <span class="episodes text-2xl">
          <b>{{anime.watched_episodes}}</b>/{{anime.total_episodes}}
        </span>
        <button v-if="anime.total_episodes !== anime.watched_episodes" @click="increaseWatch(anime)"
        class="rounded bg-pink-500 text-white py-4 px-8 hover:scale-105 hover:transition-all">+</button>
        <button v-if="anime.watched_episodes > 0" @click="decreaseWatch(anime)" class="rounded bg-pink-500 text-white py-4 px-8 hover:scale-105 hover:transition-all">-</button>
      </div>
    </div>
  </main>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
