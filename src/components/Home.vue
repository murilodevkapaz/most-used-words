<template>
  <v-container fluid>
    <v-form>
      <!--coloca a legendas dos arquivos-->
      <v-file-input
        multiple
        chips
        v-model="files"
        label="Selecione as Legendas"
        prepend-icon="mdi-message-text"
        outlined
        append-outer-icon="mdi-send"
        @click:append-outer="processSubititles"
      />
    </v-form>
    <div class="pills">
      <!--id-->
      <Pill
        v-for="word in groupedWords"
        :key="word.name"
        :name="word.name"
        :amount="word.amount"
      />
    </div>
  </v-container>
</template>

<script>
import { ipcRenderer } from "electron";
import Pill from "./Pill";
export default {
  components: { Pill },
  data: function () {
    return {
      files: [],
      groupedWords: [],
    };
  },
  methods: {
    processSubititles() {
      console.log(this.files);
      try {
        const paths = this.files.map((f) => f.path);
        ipcRenderer.send("process-subtitles", paths);
      } catch (e) {
        console.log(e);
      }

      try {
        ipcRenderer.on("process-subtitles", (event, resp) => {
          this.groupedWords = resp;
        });
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style>
.pills {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
</style>