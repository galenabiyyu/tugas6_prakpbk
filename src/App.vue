<template>
  <q-layout view="hHh lpR fFf">
    <q-header>
      <q-toolbar>
        <q-toolbar-title>
          Article Management
        </q-toolbar-title>
      </q-toolbar>
    </q-header>
    
    <q-page-container>
      <q-page padding>
        <q-form @submit.prevent="save" class="q-gutter-md">
          <q-input v-model="form.title" label="Title" outlined />
          <q-input v-model="form.content" label="Content" outlined type="textarea" />
          <q-btn type="submit" label="Save" color="primary" />
        </q-form>
        
        <q-list bordered padding>
          <q-item v-for="article in articles" :key="article.id" clickable v-ripple>
            <q-item-section>
              <q-item-label>{{ article.title }}</q-item-label>
              <q-item-label caption>{{ article.content }}</q-item-label>
            </q-item-section>
            <q-item-section side>
              <q-btn icon="edit" @click="edit(article)" color="secondary" />
              <q-btn icon="delete" @click="remove(article.id)" color="negative" />
            </q-item-section>
          </q-item>
        </q-list>

        <q-footer class="q-pa-md text-center">
          <p>&copy; 2024 Integrasi Dengan API. All rights reserved.</p>
          <p>Contact Person: marifahmad145@gmail.com</p>
        </q-footer>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { reactive, ref, onMounted } from 'vue';
import axios from 'axios';
import { Quasar, QLayout, QHeader, QToolbar, QToolbarTitle, QPage, QForm, QInput, QBtn, QList, QItem, QItemSection, QItemLabel, QFooter, QPageContainer } from 'quasar';

const binId = '665aaffee41b4d34e4fcc399';  // Ganti dengan BIN ID Anda
const apiKey = '$2a$10$iqTmKWRsgTdg8e1AFMkOcekvaQ.MuvtBhqvODjFAhY9e99R1MJRy.'; // Ganti dengan API Key Anda
const apiUrl = `https://api.jsonbin.io/v3/b/${binId}`;

const headers = {
  'X-Master-Key': apiKey,
  'Content-Type': 'application/json',
};

const form = reactive({
  id: null,
  title: '',
  content: '',
});

const articles = ref([]);

async function load() {
  try {
    const response = await axios.get(apiUrl, { headers });
    articles.value = response.data.record.articles;
  } catch (error) {
    console.error('Error loading articles', error);
  }
}

async function save() {
  let updatedArticles;
  if (form.id) {
    updatedArticles = articles.value.map(article =>
      article.id === form.id ? { ...article, title: form.title, content: form.content } : article
    );
  } else {
    const newArticle = {
      id: Date.now(),
      title: form.title,
      content: form.content,
    };
    updatedArticles = [...articles.value, newArticle];
  }

  try {
    await axios.put(apiUrl, { articles: updatedArticles }, { headers });
    articles.value = updatedArticles;
    resetForm();
  } catch (error) {
    console.error('Error saving article', error);
  }
}

async function remove(id) {
  const updatedArticles = articles.value.filter(article => article.id !== id);

  try {
    await axios.put(apiUrl, { articles: updatedArticles }, { headers });
    articles.value = updatedArticles;
  } catch (error) {
    console.error('Error deleting article', error);
  }
}

function edit(article) {
  form.id = article.id;
  form.title = article.title;
  form.content = article.content;
}

function resetForm() {
  form.id = null;
  form.title = '';
  form.content = '';
}

onMounted(load);
</script>

<style scoped>
.q-page {
  max-width: 600px;
  margin: 20px auto;
}
</style>
