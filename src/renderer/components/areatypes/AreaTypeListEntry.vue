<template>
  <v-card flat hover color="white">
    <v-container @click="onAreaTypeClicked">
      <v-layout row>
        <v-flex xs2>
          <v-card-media class="pt-2 pl-1">
            <font-awesome-icon class="grey--text" :icon="areaType.icon" size="3x"/>
          </v-card-media>
        </v-flex>
        <v-flex xs10>
          <div>
            <div class="title ellipsis mb-1">{{ areaType.name }}</div>
            <div class="atdesc">{{ areaType.description }}</div>
          </div>
        </v-flex>
      </v-layout>
    </v-container>
  </v-card>
</template>

<script>
import Utils from "../common/Utils";

export default {
  data: () => ({}),

  components: {
    Utils
  },

  props: ["areaType", "areaTypes"],

  methods: {
    // Callen when card is clicked.
    onAreaTypeClicked: function() {
      Utils.routeTo(this, "/areatypes/" + this.areaType.token);
    }
  }
};
</script>

<style scoped>
.atdesc {
  height: 40px;
  overflow-y: hidden;
}
</style>
