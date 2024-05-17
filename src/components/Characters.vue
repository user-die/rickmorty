<template>
  <section>
    <select name="" id="" v-model="filter">
      <option value="">без фильтра</option>
      <option value="alive">жив</option>
      <option value="dead">умер</option>
      <option value="unknown">неизвестно</option>
    </select>

    <input type="text" v-model="name" placeholder="поиск по имени" />
    <div style="display: flex; gap: 5px; flex-wrap: wrap">
      <button
        class="btn"
        :class="{ active: button === currentPage }"
        v-for="button in Array.from({ length: countPages }, (_, i) => i + 1)"
        @click="
          () => {
            getCharacters(button);
          }
        "
      >
        {{ button }}
      </button>
    </div>
  </section>

  <article class="container">
    <article v-for="char in characters.data" class="card">
      <img :src="char.image" alt="" />
      <section class="section">
        <h2 style="color: white">{{ char.name }}</h2>
        <section style="display: flex; align-items: center; gap: 5px">
          <div
            class="status"
            :class="{
              alive: char.status === 'Alive',
              dead: char.status === 'Dead',
            }"
          ></div>
          <p style="color: white">{{ char.status }}</p>
          <p style="color: white">-</p>
          <p style="color: white">{{ char.species }}</p>
        </section>
        <p style="color: rgb(158, 158, 158)">Last known location:</p>
        <a class="location" :href="char.location.url">{{
          char.location.name
        }}</a>
        <p style="color: rgb(158, 158, 158)">First seen in:</p>
        <a class="location" :href="char.origin.url">{{ char.origin.name }}</a>
      </section>
    </article>
  </article>
</template>

<script setup>
import axios from "axios";
import { onMounted, reactive, ref, watch } from "vue";

let characters = reactive({ data: [] });
let countPages = ref(0);
let filter = ref("");
let name = ref("");
let currentPage = ref(1);

async function getCharacters(page, name, filter) {
  page = page || 1;
  name = name || "";
  filter = filter || "";
  let response = await axios(
    `https://rickandmortyapi.com/api/character/?page=${page}&name=${name}&status=${filter}`
  );
  countPages.value = response.data.info.pages;
  characters.data = response.data.results;
  currentPage.value = page;
}

onMounted(() => {
  getCharacters();
});

watch(name, async (newValue) => {
  getCharacters(1, newValue, filter.value);
});

watch(filter, async (newValue) => {
  getCharacters(1, name.value, newValue);
});
</script>

<style scoped>
.container {
  margin: 50px 50px;
  display: flex;
  gap: 30px;
  flex-wrap: wrap;
}

.card {
  background-color: rgb(32, 35, 41);
  border-radius: 10px;
  display: flex;

  img {
    width: 200px;
    height: 200px;
  }

  .section {
    display: flex;
    flex-direction: column;
    gap: 10px;
    width: 300px;
    height: 200px;
    padding: 0.75rem;
  }
}

.location {
  text-decoration: none;
  color: white;
}

.status {
  width: 9px;
  height: 9px;
  border-radius: 50%;
}

.alive {
  background-color: rgb(85, 204, 68);
}

.dead {
  background-color: rgb(214, 61, 46);
}

.btn {
  width: 20px;
  height: 20px;
}

.active {
  border: 2px solid red;
}

select {
  margin: 15px;
}
</style>
