<template>
  <div :key="currentMangaKey">
    <div class="text-center" v-if="mangas">
      <div v-for="manga in mangas" :key="manga.id" @click="selectManga(manga)">
        <!-- <v-dialog v-model="dialog[manga.id]" width="500"> -->
        <!-- <template v-slot:activator="{ on, attrs }">
          <v-btn color="red lighten-2" dark v-bind="attrs" v-on="on">
            Click Me
          </v-btn>
        </template> -->

        <v-card>
          <v-card-title elevation="2" outlined shaped>
            {{ manga.titre }}
          </v-card-title>

          <v-card-text>{{ manga.chapitre }}</v-card-text>
        </v-card>
        <!-- </v-dialog> -->
      </div>
    </div>

    <div v-if="currentManga">
      <v-card>
        <Form
          :titremanga="this.currentManga.titre"
          :chapitremanga="this.currentManga.chapitre"
          :urlmanga="this.currentManga.url"
          :idmanga="this.currentManga.id"
          @formok="handleSubmit"
          @delete="handleDelete"
        />
      </v-card>
    </div>
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
