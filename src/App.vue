<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const taches = ref([]);
const nom = ref('');

const contenu_input = ref('');
const categorie_input = ref('');

const taches_asc = computed(() => taches.value.sort((a, b) => {
  return b.createdAt - a.createdAt;
}));

const ajouterTache = () => {
  if (contenu_input.value.trim() === '' || categorie_input.value === null) {
    return;
  }
  taches.value.push({
    content: contenu_input.value,
    category: categorie_input.value,
    createdAt: new Date().getTime(),
    done: false,
  });

};

watch(taches, (newVal) => {
  localStorage.setItem('taches', JSON.stringify(newVal));
},
  { deep: true });

watch(nom, (newVal) => {
  localStorage.setItem('nom', newVal);
});

onMounted(() => {
  nom.value = localStorage.getItem('nom') || '';
  taches.value = JSON.parse(localStorage.getItem('taches')) || [];
});

</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Salut, <input type="text" placeholder="Nom ici" v-model="nom" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CRÉER UNE TÂCHE</h3>

      <form @submit.prevent="ajouterTache">
        <h4>Qu'est-ce qu'il y a sur votre liste de tâches ?</h4>
        <input type="text" placeholder="ex: faire une vidéo" v-model="contenu_input" />

        <h4>Choisissez une catégorie</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="categorie_input" />
            <span class="bubble business"></span>
            <div>Affaires</div>
          </label>


          <label>
            <input type="radio" name="category" value="personal" v-model="categorie_input" />
            <span class="bubble personal"></span>
            <div>Personnel</div>
          </label>

          {{ categorie_input }}

        </div>

        <input type="submit" value="Ajouter Tâche" />
      </form>

    </section>

    <section class="todo-list">
      <h3>LISTE DE TÂCHES</h3>
      <div class="list">
        <div v-for="tache in taches_asc" :key="tache.createdAt" :class="`todo-item ${tache.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="tache.done" />
            <span :class="`bubble ${tache.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="tache.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="taches.splice(taches.indexOf(tache), 1)">Supprimer</button>
          </div>

        </div>
      </div>
    </section>

  </main>
</template>
