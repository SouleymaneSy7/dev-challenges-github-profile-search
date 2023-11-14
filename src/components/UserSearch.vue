<template>
  <main class="main-container">
    <form class="form" @submit.prevent="getAllResquest()">
      <label for="form-search" class="form__label">
        <span class="form__label-icon">
          <img src="../assets/Search.svg" alt="Search svg icons" />
        </span>
        <input
          type="text"
          class="form__label--search"
          id="form-search"
          placeholder="Username"
          v-model="userSearch"
        />
      </label>
    </form>

    <section class="results">
      <div class="results__top">
        <div class="results__top__img">
          <img :src="usersData.avatar_url" alt="User Profile Picture" />
        </div>

        <div class="results__top__info">
          <div class="followers">
            <p class="followers__title">Followers</p>
            <p class="followers--numbers">{{ usersData.followers || 0 }}</p>
          </div>
          <div class="following">
            <p class="following__title">Following</p>
            <p class="following--numbers">{{ usersData.following || 0 }}</p>
          </div>
          <div class="location">
            <p class="location__title">Location</p>
            <p class="location--numbers">
              {{ usersData.location || "No Location" }}
            </p>
          </div>
        </div>
      </div>

      <div class="results__user">
        <h1 class="results__user__name">{{ usersData.name }}</h1>

        <p class="results__user__description">
          {{ usersData.bio || "No information" }}
        </p>
      </div>

      <div class="results__repositories">
        <div class="repositories" v-for="repos in repositoriesToShow">
          <a :href="repos.html_url" target="_blank">
            <h2 class="repositories__name">{{ repos.name }}</h2>
            <p class="repositories__description">
              {{ repos.description || "No Description" }}
            </p>

            <div class="flex">
              <div class="repositories__licences">
                <div>
                  <img src="../assets/Chield_alt.svg" alt="Chield Icons" />
                </div>
                <p>{{ repos.licence || "MIT" }}</p>
              </div>
              <div class="repositories__forks">
                <div>
                  <img src="../assets/Nesting.svg" alt="" />
                </div>

                <p>{{ repos.forks_count }}</p>
              </div>
              <div class="repositories__stars">
                <div>
                  <img src="../assets/Star.svg" alt="Star Icons" />
                </div>
                <p>{{ repos.stargazers_count }}</p>
              </div>
              <p class="repositories__updates">
                Updated {{ updateDate(repos.updated_at) }}
              </p>
            </div>
          </a>
        </div>
      </div>

      <button type="button" class="view-all" @click="showLessElements()">
        {{ showLess === true ? "view all repositories" : "Show Less" }}
      </button>
    </section>
  </main>
</template>

<script setup>
import axios from "axios";
import { computed, onBeforeMount, ref } from "vue";
import { formatDistanceToNow } from "date-fns";

const userSearch = ref("github");
const usersData = ref({});
const userRepositories = ref([]);
const showLess = ref(true);

// Request Config
const url = axios.create({
  baseURL: "https://api.github.com/users/",
});

onBeforeMount(() => {
  getAllResquest();
});

// Get User Information
const getUserInfo = () => {
  url({
    method: "get",
    url: `/${userSearch.value}`,
    responseType: "json",
  })
    .then((response) => {
      usersData.value = response.data;
    })
    .catch((error) => {
      console.error(error);
    });
};

// Get User Repositories
const getUserRepositories = () => {
  url({
    method: "get",
    url: `/${userSearch.value}/repos`,
    responseType: "json",
  })
    .then((response) => {
      userRepositories.value = response.data;
    })
    .catch((error) => {
      console.error(error);
    });
};

const getAllResquest = () => {
  getUserInfo();
  getUserRepositories();
};

// Show More or Less Repositories
const showLessElements = () => {
  showLess.value = !showLess.value;
};

const repositoriesToShow = computed(() => {
  if (showLess.value) {
    return userRepositories.value.slice(0, 4);
  } else {
    return userRepositories.value;
  }
});

// Get the Updated Date Distance and Formats
const updateDate = (date) => {
  let distance = formatDistanceToNow(new Date(date), {
    addSuffix: true,
  });

  return distance;
};
</script>
