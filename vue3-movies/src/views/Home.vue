<template>
  <main class="home bg-red-400 p-10">
    <div class="input-search text-center">
      <input
        type="text"
        class="lg:w-2/12 h-8 focus:outline-none rounded-lg px-2"
        placeholder="Search movie"
        v-model="movieName"
      />
      <button
        class="inline-block w-16 h-8 bg-blue-300 text-white ml-2 rounded-lg active:scale-95 focus:outline-none transition-all"
        @click="searchMovie"
      >
        搜索
      </button>
    </div>
    <div class="movie-list flex flex-wrap justify-between self-start p-4 mx-auto" style="max-width: 1400px;">
      <div v-if="movies.length === 0">未搜索到影片</div>
      <div
        class="item w-64 bg-purple-300 p-3 rounded-lg mb-6 text-white"
        v-for="movie in movies"
        :key="movie.id"
      >
        <!-- https://image.tmdb.org/t/p/w500/ajKpYK7XdzIYjy9Uy8nkgRboKyv.jpg -->
        <img
          :src="'https://image.tmdb.org/t/p/w1280' + movie.poster_path"
          alt=""
        />
        <h3 class="title text-lg font-bold">{{ movie.title }}</h3>
        <div class="pub-date">{{ movie.release_date }}</div>
      </div>
    </div>
  </main>
</template>

<script>
import { onMounted, reactive, ref, toRefs } from 'vue';
const popularApi =
  'https://api.themoviedb.org/3/discover/movie?sort_by=popularity.desc&api_key=04c35731a5ee918f014970082a0088b1&page=';
const searchApi =
  'https://api.themoviedb.org/3/search/movie?&api_key=04c35731a5ee918f014970082a0088b1&query=';
const bg = 'https://image.tmdb.org/t/p/w1280';
const testImg = 'https://image.tmdb.org/t/p/w1280/ajKpYK7XdzIYjy9Uy8nkgRboKyv.jpg'
export default {
  name: 'Home',
  data:() => ({
    bg: bg,
    testImg
  }),
  setup() {
    const page = ref(1);
    const searchTotalPage = ref(0);
    const movieName = ref('');
    const state = reactive({
      movies: []
    });
    let url = popularApi + page.value;
    onMounted(async () => {
      const data = await getData(url);
      state.movies.push(...data.results);
    });

    // 获取电影数据
    async function getData(url) {
      const data = await fetch(url)
        .then((res) => {
          return res.json();
        })
        .then((data) => {
          return data;
        });
      // 获取完之后 page + 1
      page.value += 1;
      return data;
    }
    // 获取电影查询数据
    async function searchMovie() {
      url = searchApi + movieName.value;
      movieName.value = '';
      const searchResult = await fetch(url)
        .then((res) => res.json())
        .then((data) => data);
      // console.log(searchResult)
      searchTotalPage.value = searchResult.totalpages;
      state.movies = searchResult.results;
    }

    return {
      page,
      ...toRefs(state),
      movieName,
      searchMovie
    };
  }
};
</script>
