<template>
  <div class="buttons-container">
    <div class="mini-btn-container">
      <command-button class="mini-button"
        :class="{'btn-highlight': gridSize === 15}" 
        :disabled="!changeGridSizeEnabled"
        @command="toggleGridSize(15)">
        15
      </command-button>
      <command-button class="mini-button" 
        :class="{'btn-highlight': gridSize === 13}" 
        :disabled="!changeGridSizeEnabled"
        @command="toggleGridSize(13)">
        13
      </command-button>
    </div>
    <command-button
      :disabled="!finishEnabled"
      @command="$emit('finish-the-game')">
      <span>Завершить</span>
    </command-button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import CommandButton from "./UI/CommandButton.vue"

export default defineComponent({
  name: "GameCommmadPanel",

  components: {
    CommandButton
  },
  
  props: {
    finishEnabled: {
      type: Boolean,
      required: true
    },
    changeGridSizeEnabled: {
      type: Boolean,
      required: true
    },
    gridSize: {
      type: Number,
      required: true
    }  
  },
  emits: {
    "finish-the-game": function () { return true},
    "set-grid-size": function(gridSize: number) {
      return true;
    },
  },

  methods: {

    toggleGridSize(gridSize: number) {
      this.$emit("set-grid-size", gridSize);
    }

  },
})
</script>

<style scoped>

.buttons-container {
  display: -webkit-flex;
  display: flex;
}

.mini-btn-container{
  display: inline-flex;
  justify-content: center;
}

.btn-command.mini-button {
  min-width: 1em;
  padding-left: 0.15em;
  padding-right: 0.15em;
  width: auto;
}

@media (orientation: landscape){

  .buttons-container {
    flex-direction: column;
    align-items: center;
    font-size: 2vmax;
    padding-top: 0.1em;
    gap: 0.2em;
  }

  .mini-btn-container{
    gap: 1em;
    width: 100%;
  }
} /* @media (orientation: landscape) */

@media (orientation: portrait){
  .buttons-container {
    flex-direction: row;
    align-items: center;
    justify-content: space-around;
    font-size: 2vmax;
    margin-top: 0.2em;
  }

  .mini-btn-container{
    gap: 1em;
    width: 100%;
  }
} /* @media (orientation: portrait) */

</style>
