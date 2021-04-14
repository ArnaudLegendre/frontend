<template>
  <div class="home">
    <v-form v-on:submit="handleSubmit($event, objectmanga)" ref="form">
      <v-container>
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field
              label="titre"
              required
              v-model="objectmanga.titre"
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              label="url"
              required
              v-model="objectmanga.url"
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              type="number"
              label="chapitre"
              required
              v-model="objectmanga.chapitre"
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12" md="3">
            <v-btn type="submit" v-if="this.objectmanga.id">Editer</v-btn>
            <v-btn v-if="this.objectmanga.id" @click="$emit('delete', objectmanga.id)">SUpprimer</v-btn>
            <v-btn type="submit" v-else>Ajouter</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Form",
  components: {},
  props: {
    titremanga: String,
    chapitremanga: Number,
    urlmanga: String,
    idmanga: { default: undefined },
  },
  data() {
    return {
      objectmanga: {
        titre: "",
        url: "",
        chapitre: "",
        id: undefined,
      },
      error: null,
      headers: { "Content-Type": "application/json" },
    };
  },
  methods: {
    handleSubmit(e, manga) {
      e.preventDefault();
      this.$emit("formok", manga);
      this.objectmanga={}
    },
  },
  created() {
    this.objectmanga.titre = this.titremanga;
    this.objectmanga.url = this.urlmanga;
    this.objectmanga.chapitre = this.chapitremanga;
    if (this.idmanga) {
      this.objectmanga.id = this.idmanga;
    }
  },
};
</script>
