<template>
  <div class="play-field">
    <div :class="{'play-field-row-15' : gridSize===15, 'play-field-row-13' : gridSize===13}" 
        v-for="rowIndex in gridSize" :key="rowIndex">
      <div
        :class="cellOwnerClasses(rowIndex - 1, cellIndex - 1)"
        v-for="cellIndex in gridSize"
        :key="gridSize * rowIndex + cellIndex"
        @click.stop="putLetter(rowIndex - 1, cellIndex - 1)"
      >
        <base-letter
          v-if="isCellBusy(rowIndex - 1, cellIndex - 1)"
          :letter="playField.cellGrid[rowIndex - 1][cellIndex - 1]"
          :showRemoveBtn="canClearCell(rowIndex - 1, cellIndex - 1)"
          @click.stop="removeLetter(rowIndex - 1, cellIndex - 1)"
        >
        </base-letter>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import { PlayField } from "../model/PlayField";
import BaseLetter from "./UI/BaseLetter.vue";

export default defineComponent({
  components: {
    BaseLetter
  },
  props: {
    playField: {
      type: Object as PropType<PlayField>,
      required: true
    },
  },
  emits: {
    "playfield-remove-letter": function(iRow: number, jCol: number) {
      return true;
    },
    "playfield-put-letter": function(iRow: number, jCol: number) {
      return true;
    }
  },
  computed: {
    gridSize(): number {
      return this.playField.gridSize;
    }
  },

  methods: {
    cellOwnerClasses(iRow: number, jCol: number) {
      if (this.playField.cellGrid[iRow][jCol].isInGo) {
        if (this.playField.cellGrid[iRow][jCol].idPlayer === 0)
          return { "cell-background-p0": true,
                   "play-field-cell-15" : this.gridSize === 15,
                   "play-field-cell-13" : this.gridSize === 13 };
        else if (this.playField.cellGrid[iRow][jCol].idPlayer === 1)
          return { "cell-background-p1": true,
                   "play-field-cell-15" : this.gridSize === 15,
                   "play-field-cell-13" : this.gridSize === 13 };
      }
      else if(this.playField.isLetterInPrevGo(this.playField.cellGrid[iRow][jCol])) {
        // слегка подсвечивать слова последнего завершённого хода
        if (this.playField.prevGoPlayerId === 1)
          return { "cell-background-p1-fix": true,
                   "play-field-cell-15" : this.gridSize === 15,
                   "play-field-cell-13" : this.gridSize === 13 };
        else if (this.playField.prevGoPlayerId === 0)
          return { "cell-background-p0-fix": true,
                   "play-field-cell-15" : this.gridSize === 15,
                   "play-field-cell-13" : this.gridSize === 13 };
      }
      return {
        "play-field-cell-15" : this.gridSize === 15,
        "play-field-cell-13" : this.gridSize === 13 
      };
    },

    isCellBusy(iRow: number, jCol: number): boolean {
      return !this.playField.cellGrid[iRow][jCol].isEmpty();
    },
    isCellEmpty(iRow: number, jCol: number) {
      return this.playField.cellGrid[iRow][jCol].isEmpty();
    },
    isCellInGo(iRow: number, jCol: number) {
      return this.playField.cellGrid[iRow][jCol].isInGo;
    },

    canClearCell(iRow: number, jCol: number) {
      if (this.playField.cellGrid[iRow][jCol].idPlayer === 0 || 
        this.playField.cellGrid[iRow][jCol].idPlayer === 2) // developer player
        return this.playField.cellGrid[iRow][jCol].isInGo;
      return false;  
    },


    putLetter(iRow: number, jCol: number) {
      if (!this.isCellInGo(iRow, jCol)) {
        this.$emit("playfield-put-letter", iRow, jCol);
      }
    },

    removeLetter(iRow: number, jCol: number) {
      if (this.canClearCell(iRow, jCol)) {
        this.$emit("playfield-remove-letter", iRow, jCol);
      }
    }
  }
});
</script>

<style scoped>
.play-field {
  display: -webkit-flex;
  display: flex;
  flex-direction: column;
  align-items:center;
}

.play-field-row-15 {
  display: -webkit-flex;
  display: flex;
  flex-direction: row;
  -webkit-flex: 0 0 var(--cell-15);
  -ms-flex: 0 0 var(--cell-15);
  flex: 0 0 var(--cell-15);
  height: var(--cell-15);
}

.play-field-cell-15 {
  -webkit-flex: 0 0 var(--cell-15);
  -ms-flex: 0 0 var(--cell-15);
  flex: 0 0 var(--cell-15);
  height: var(--cell-15);
  width: var(--cell-15);
  font-size: 1.8rem;
  border-right: 1px solid rgba(128, 128, 128, 0.5);
  border-bottom: 1px solid rgba(128, 128, 128, 0.5);
}

.play-field-row-13 {
  display: -webkit-flex;
  display: flex;
  flex-direction: row;
  -webkit-flex: 0 0 var(--cell-13);
  -ms-flex: 0 0 var(--cell-13);
  flex: 0 0 var(--cell-13);
  height: var(--cell-13);
}

.play-field-cell-13 {
  -webkit-flex: 0 0 var(--cell-13);
  -ms-flex: 0 0 var(--cell-13);
  flex: 0 0 var(--cell-13);
  height: var(--cell-13);
  width: var(--cell-13);
  font-size: 2rem;
  border-right: 1px solid rgba(128, 128, 128, 0.5);
  border-bottom: 1px solid rgba(128, 128, 128, 0.5);
}

.cell-background-default {
  background-color: white;
}

.cell-background-p0 {
  background-color: rgba(144, 238, 144, 1);
}

.cell-background-p0-fix {
  background-color: rgba(144, 238, 144, 0.7);
}

.cell-background-p1 {
  background-color: rgba(173, 216, 230, 1);
}

.cell-background-p1-fix {
  background-color: rgba(173, 216, 230, 0.7);
}

@media (orientation: portrait) {
  .play-field-cell-13:first-child {
    border-left: 1px solid rgba(128, 128, 128, 0.5);
  }

  .play-field-cell-15:first-child {
    border-left: 1px solid rgba(128, 128, 128, 0.5);
  }

}

</style>
