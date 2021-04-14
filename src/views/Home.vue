<template>
  <div class="home">
    <Form
      :titremanga="this.titre"
      :chapitremanga="this.chapitre"
      :urlmanga="this.url"
      @formok="handleSubmit"
    />
  </div>
</template>

<script>
// @ is an alias to /src
import Form from "./Form";
export default {
  name: "Home",
  components: { Form },
  data() {
    return {
      titre: "",
      url: "",
      chapitre: null,

      error: null,
      headers: { "Content-Type": "application/json" },
    };
  },
  methods: {
    parseJSON(resp) {
      return resp.json ? resp.json() : resp;
    },
    checkStatus(resp) {
      if (resp.status >= 200 && resp.status < 300) {
        return resp;
      }
      return this.parseJSON(resp).then((resp) => {
        throw resp;
      });
    },
    async handleSubmit(manga) {
      
      try {
        const response = await fetch("http://localhost:8092/mangaas", {
          method: "POST",
          headers: this.headers,
          body: JSON.stringify(manga),
        })
          .then(this.checkStatus)
          .then(this.parseJSON);
          
      } catch (error) {
        this.error = error;
      }
    },
    // async getRss(url){
    //   const data = await fetch(url,{mode:'cors'})
    //   console.log(url)
    //   console.log(data)

    // }
  },
};
</script>
