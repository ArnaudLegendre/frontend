<template>
  <div :key="currentMangaKey">
    <v-row class="d-flex justify-content-center" v-if="mangas">
      <v-col
        v-for="manga in mangas"
        :key="manga.id"
        @click="selectManga(manga)"
        @click.stop="dialog = true"
        cols="6"
        md="2"
      >
        <v-card>
          <v-card-title elevation="2" outlined shaped>
            {{ manga.titre }}
          </v-card-title>
          <v-img height="250" :src="manga.image ? manga.image : ''"></v-img>
          <v-card-text v-if="manga.chapitre < manga.dernierchapitre"
            >{{ manga.chapitre }}/{{ manga.dernierchapitre }}</v-card-text
          >
          <v-card-text v-else class="d-flex justify-space-between">
            <span>
              {{ manga.dernierchapitre }}
            </span>
            <v-icon class="ml-2 green--text">{{ icon }}</v-icon>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <v-row v-if="currentManga">
      <v-dialog v-model="dialog" content-class="white">
        <v-col>
          <Form
            :titremanga="this.currentManga.titre"
            :chapitremanga="this.currentManga.chapitre"
            :urlmanga="this.currentManga.url"
            :idmanga="this.currentManga.id"
            @formok="handleSubmit"
            @delete="handleDelete"
          />
        </v-col>
      </v-dialog>
    </v-row>
  </div>
</template>


<script>
import Form from "./Form";
export default {
  name: "Mangas",
  components: { Form },
  data() {
    return {
      mangas: undefined,
      error: null,
      headers: { "Content-Type": "application/json" },
      dialog: [],
      currentManga: undefined,
      currentMangaKey: 0,
      icon: "mdi-check-outline",
    };
  },
  methods: {
    parseJSON: function (resp) {
      return resp.json ? resp.json() : resp;
    },
    checkStatus: function (resp) {
      if (resp.status >= 200 && resp.status < 300) {
        return resp;
      }
      return this.parseJSON(resp).then((resp) => {
        throw resp;
      });
    },
    selectManga(manga) {
      this.currentManga = manga;
      this.currentMangaKey++;
    },
    async fetchdata() {
      try {
        const response = await fetch("http://localhost:8092/mangaas", {
          method: "GET",
          headers: this.headers,
        })
          .then(this.checkStatus)
          .then(this.parseJSON);
        this.mangas = response;
      } catch (error) {
        this.error = error;
      }
    },
    async handleSubmit(manga) {
      try {
        const response = await fetch(
          `http://localhost:8092/mangaas/${manga.id}`,
          {
            method: "PUT",
            headers: this.headers,
            body: JSON.stringify(manga),
          }
        )
          .then(this.checkStatus)
          .then(this.parseJSON);
        this.currentManga = undefined;
        this.fetchdata();
      } catch (error) {
        this.error = error;
      }
    },
    async handleDelete(id) {
      try {
        const response = await fetch(`http://localhost:8092/mangaas/${id}`, {
          method: "DELETE",
          headers: this.headers,
        })
          .then(this.checkStatus)
          .then(this.parseJSON);
        this.currentManga = undefined;
        this.fetchdata();
      } catch (error) {
        this.error = error;
      }
    },
  },
  async created() {
    await this.fetchdata();
  },
};
</script>
